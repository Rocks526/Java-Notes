# 一：Java类型体系介绍

### 1.1 Type介绍

Java在Jdk1.5版本引入了泛型，为了支持泛型相关的操作，也引入了Java类型体系。

> 早期所有Java的类型，除了基本类型之外都属于Class类型，引入泛型后，新增了一些泛型的所属类型，用于实现对泛型的特殊操作。

Type是Java编程语言中所有类型的公共高级接口(官方解释)，也就是Java中所有类型的"爹；其中，"所有类型"的描述尤为值得关注。它并不是我们平常工作中经常使用的int、String、List、Map等数据类型，而是从Java语言角度来说，对基本类型、引用类型向上的抽象。

### 1.2 Type分类

Type体系中类型的包括：

- 原始类型(Class)：泛型之外的所有类型
- 参数化类型(ParameterizedType)：泛型，例如List\<String>
- 参数化数组类型(GenericArrayType)：泛型数组，例如List\<String>[]
- 类型变量(TypeVariable)：引用泛型，例如List\<T>
- 通配符类型(WildcardType)：未知泛型，例如List<? extends Integer>

### 1.3 Type继承结构

![image-20211208150531673](http://rocks526.top/lzx/image-20211208150531673.png)



# 二：Java类型源码与测试

### 2.1 Type

```java
package java.lang.reflect;

/**
 * Type is the common superinterface for all types in the Java
 * programming language. These include raw types, parameterized types,
 * array types, type variables and primitive types.
 *
 * Type是Java所有类型的通用顶级接口，包括原始类型、参数化类型、数组类型、类型变量、基元类型等。
 *
 * @since 1.5
 */
public interface Type {
    /**
     * Returns a string describing this type, including information
     * about any type parameters.
     *
     * 返回类型信息描述，默认实现是toString方法
     *
     * @implSpec The default implementation calls {@code toString}.
     *
     * @return a string describing this type
     * @since 1.8
     */
    default String getTypeName() {
        return toString();
    }
}
```

Type是个空接口，只定义一个getTypeName方法，通过多态提高了程序的扩展性。

### 2.2 ParameterizedType

- 源码

```java
package java.lang.reflect;


/**
 * ParameterizedType represents a parameterized type such as
 * Collection&lt;String&gt;.
 *
 * <p>A parameterized type is created the first time it is needed by a
 * reflective method, as specified in this package. When a
 * parameterized type p is created, the generic type declaration that
 * p instantiates is resolved, and all type arguments of p are created
 * recursively. See {@link java.lang.reflect.TypeVariable
 * TypeVariable} for details on the creation process for type
 * variables. Repeated creation of a parameterized type has no effect.
 *
 * <p>Instances of classes that implement this interface must implement
 * an equals() method that equates any two instances that share the
 * same generic type declaration and have equal type parameters.
 *
 * ParameterizedType表示参数化类型，例如List<String>
 *
 * @since 1.5
 */
public interface ParameterizedType extends Type {
    /**
     * Returns an array of {@code Type} objects representing the actual type
     * arguments to this type.
     *
     * <p>Note that in some cases, the returned array be empty. This can occur
     * if this type represents a non-parameterized type nested within
     * a parameterized type.
     *
     * @return an array of {@code Type} objects representing the actual type
     *     arguments to this type
     * @throws TypeNotPresentException if any of the
     *     actual type arguments refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     actual type parameters refer to a parameterized type that cannot
     *     be instantiated for any reason
     * @since 1.5
     *
     * 返回实际类型数组，例如：
     * List<String>返回String
     * Map<String,Integer>返回[String,Integer]
     * List<T>返回TypeVariable
     *
     */
    Type[] getActualTypeArguments();

    /**
     * Returns the {@code Type} object representing the class or interface
     * that declared this type.
     *
     * @return the {@code Type} object representing the class or interface
     *     that declared this type
     * @since 1.5
     *
     * 返回原类型，例如：
     * List<String>返回List
     * Map<String,Integer>返回Map
     * List<T>返回List
     *
     */
    Type getRawType();

    /**
     * Returns a {@code Type} object representing the type that this type
     * is a member of.  For example, if this type is {@code O<T>.I<S>},
     * return a representation of {@code O<T>}.
     *
     * <p>If this type is a top-level type, {@code null} is returned.
     *
     * @return a {@code Type} object representing the type that
     *     this type is a member of. If this type is a top-level type,
     *     {@code null} is returned
     * @throws TypeNotPresentException if the owner type
     *     refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if the owner type
     *     refers to a parameterized type that cannot be instantiated
     *     for any reason
     * @since 1.5
     *
     * 返回所属类型，如果是内部类，返回父类，如果是父类，则返回null
     *
     */
    Type getOwnerType();
}
```

- 测试

```java
package type;

import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;
import java.util.Set;

/**
 * ParameterizedType : 参数化类型测试
 * 例如：List<String>，Map<String,Integer>，Set<T>
 */
public class ParameterizedTypeTest<T> {

    private List<T> list;
    private List<Set<T>> list2;
    private Set<T> set;
    private Map<String, T> map;
    private Map.Entry<String, Boolean> map2;

    public static void main(String[] args) throws NoSuchFieldException {
        // 不同类型泛型测试
        getActualTypeTest();
        // getActualTypeArguments方法测试
        getActualTypeArgumentsTest();
        // getRawType方法测试
        getRawTypeTest();
        // getOwnerType方法测试
        getOwnerTypeTest();
    }

    private static void getOwnerTypeTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getOwnerTypeTest-----------------------------------------");
        Field[] declaredFields = ParameterizedTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            Type genericType = declaredField.getGenericType();
            ParameterizedType parameterizedType = (ParameterizedType) genericType;
            // 获取拥有者 ==> 内部类的父类
            System.out.println(parameterizedType.getOwnerType());
        }
    }

    private static void getRawTypeTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getRawTypeTest-----------------------------------------");
        Field[] declaredFields = ParameterizedTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            Type genericType = declaredField.getGenericType();
            ParameterizedType parameterizedType = (ParameterizedType) genericType;
            System.out.println("field [" + declaredField.getName() + "] ==> " + parameterizedType.getRawType());
        }
    }

    private static void getActualTypeArgumentsTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getActualTypeArgumentsTest-----------------------------------------");
        Field[] declaredFields = ParameterizedTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            Type genericType = declaredField.getGenericType();
            // 转换真实类型
            ParameterizedType parameterizedType = (ParameterizedType) genericType;
            // 获取泛型类型 ==> 可能多个
            Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
            for (Type actualTypeArgument : actualTypeArguments) {
                if (actualTypeArgument instanceof Class){
                    // 具体类型
                    System.out.println("field [" + declaredField.getName() + "] ==> " + ((Class<?>) actualTypeArgument).getName());
                }else {
                    // 非具体类型
                    System.out.println("field [" + declaredField.getName() + "] ==> " + actualTypeArgument.getClass().getName());
                }
            }
        }
    }

    private static void getActualTypeTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getActualTypeTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = ParameterizedTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取Type实际类型  ==>  sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl
            System.out.println("field [" + declaredField.getName() + "] ==> " + genericType.getClass().getName());
        }
    }


}
```

![image-20211208153718873](http://rocks526.top/lzx/image-20211208153718873.png)

### 2.3 GenericArrayType

- 源码

```java
package java.lang.reflect;

/**
 * {@code GenericArrayType} represents an array type whose component
 * type is either a parameterized type or a type variable.
 * @since 1.5
 *
 * 泛型数组类型，例如List<String>[]
 *
 */
public interface GenericArrayType extends Type {
    /**
     * Returns a {@code Type} object representing the component type
     * of this array. This method creates the component type of the
     * array.  See the declaration of {@link
     * java.lang.reflect.ParameterizedType ParameterizedType} for the
     * semantics of the creation process for parameterized types and
     * see {@link java.lang.reflect.TypeVariable TypeVariable} for the
     * creation process for type variables.
     *
     * @return  a {@code Type} object representing the component type
     *     of this array
     * @throws TypeNotPresentException if the underlying array type's
     *     component type refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if  the
     *     underlying array type's component type refers to a
     *     parameterized type that cannot be instantiated for any reason
     * 返回数组的元素类型，例如：
     * List<String>[]返回List<String>
     * List<String>[][]返回List<String>[]
     */
    Type getGenericComponentType();
}
```

- 测试

```java
package type;

import java.lang.reflect.Field;
import java.lang.reflect.GenericArrayType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;

/**
 * GenericArrayType ==> 泛型数组类型
 * 例如：List<String>[]、Map<String,Integer>[]
 */
public class GenericArrayTypeTest<T> {

    private List<String>[] list1;
    private String[] list2;
    private String[][] list3;
    private List<T>[] list4;
    private List<String>[][] list5;
    private Map<String,T>[] map;

    public static void main(String[] args) {
        // 类型测试
        getActualTypeTest();
        // getGenericComponentType方法测试
        getGenericComponentTypeTest();
    }

    private static void getGenericComponentTypeTest() {
        System.out.println("--------------------------getGenericComponentTypeTest----------------------------");
        // 获取所有字段
        Field[] declaredFields = GenericArrayTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取类型
            Type genericType = declaredField.getGenericType();
            if (genericType instanceof GenericArrayType){
                GenericArrayType genericArrayType = (GenericArrayType) genericType;
                System.out.println("field [" + declaredField.getName() + "] ==> " + genericArrayType.getGenericComponentType().getClass().getName() + " ==> " + genericArrayType.getGenericComponentType());
            }
        }
    }

    private static void getActualTypeTest() {
        System.out.println("--------------------------getActualTypeTest----------------------------");
        // 获取所有字段
        Field[] declaredFields = GenericArrayTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取类型
            Type genericType = declaredField.getGenericType();
            System.out.println("field [" + declaredField.getName() + "] ==> " + genericType.getClass().getName());
        }
    }

}
```

![image-20211208153757642](http://rocks526.top/lzx/image-20211208153757642.png)

### 2.4 TypeVariable

- 源码

```java
package java.lang.reflect;

/**
 * TypeVariable is the common superinterface for type variables of kinds.
 * A type variable is created the first time it is needed by a reflective
 * method, as specified in this package.  If a type variable t is referenced
 * by a type (i.e, class, interface or annotation type) T, and T is declared
 * by the nth enclosing class of T (see JLS 8.1.2), then the creation of t
 * requires the resolution (see JVMS 5) of the ith enclosing class of T,
 * for i = 0 to n, inclusive. Creating a type variable must not cause the
 * creation of its bounds. Repeated creation of a type variable has no effect.
 *
 * <p>Multiple objects may be instantiated at run-time to
 * represent a given type variable. Even though a type variable is
 * created only once, this does not imply any requirement to cache
 * instances representing the type variable. However, all instances
 * representing a type variable must be equal() to each other.
 * As a consequence, users of type variables must not rely on the identity
 * of instances of classes implementing this interface.
 *
 * @param <D> the type of generic declaration that declared the
 * underlying type variable.
 *
 * @since 1.5
 *
 * 类型变量，例如：
 * List<T>的T
 * Map<String,E>里的E
 *
 */
public interface TypeVariable<D extends GenericDeclaration> extends Type, AnnotatedElement {
    /**
     * Returns an array of {@code Type} objects representing the
     * upper bound(s) of this type variable.  Note that if no upper bound is
     * explicitly declared, the upper bound is {@code Object}.
     *
     * <p>For each upper bound B: <ul> <li>if B is a parameterized
     * type or a type variable, it is created, (see {@link
     * java.lang.reflect.ParameterizedType ParameterizedType} for the
     * details of the creation process for parameterized types).
     * <li>Otherwise, B is resolved.  </ul>
     *
     * @throws TypeNotPresentException  if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     * @return an array of {@code Type}s representing the upper
     *     bound(s) of this type variable
     * 获取变量的上限，例如：
     * List<T extends String>返回String
     * List<E> 返回Object
    */
    Type[] getBounds();

    /**
     * Returns the {@code GenericDeclaration} object representing the
     * generic declaration declared this type variable.
     *
     * @return the generic declaration declared for this type variable.
     *
     * @since 1.5
     *
     * 获取泛型定义的实体，例如类、方法、构造方法
     *
     */
    D getGenericDeclaration();

    /**
     * Returns the name of this type variable, as it occurs in the source code.
     *
     * @return the name of this type variable, as it appears in the source code
     *
     * 返回泛型的名称，例如List<T>返回T
     *
     */
    String getName();

    /**
     * Returns an array of AnnotatedType objects that represent the use of
     * types to denote the upper bounds of the type parameter represented by
     * this TypeVariable. The order of the objects in the array corresponds to
     * the order of the bounds in the declaration of the type parameter.
     *
     * Returns an array of length 0 if the type parameter declares no bounds.
     *
     * @return an array of objects representing the upper bounds of the type variable
     * @since 1.8
     * 返回类型变量的上限
     */
     AnnotatedType[] getAnnotatedBounds();
}
```

- 测试

```java
package type;

import java.io.Serializable;
import java.lang.reflect.*;
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * TypeVariable ==> 类型变量测试
 * 例如：List<T>里的T
 */
public class TypeVariableTest <T,E extends Number & Serializable & Comparable> {

    private List<T> list1;
    private List<E> list2;
    private Map<T,E> map;

    public static void main(String[] args) throws NoSuchFieldException {
        // 类型测试
        getActualTypeTest();
        // getBounds方法测试
        getBoundsTest();
        // getGenericDeclaration方法测试
        getGenericDeclarationTest();
        // getName方法测试
        getNameTest();
        // getAnnotatedBounds方法测试
        getAnnotatedBoundsTest();
    }

    private static void getAnnotatedBoundsTest() {
        System.out.println("--------------------------------getAnnotatedBoundsTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = TypeVariableTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = ((ParameterizedType) genericType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof TypeVariable){
                        TypeVariable typeVariable = (TypeVariable) actualTypeArgument;
                        // 获取Type实际类型
                        AnnotatedType[] annotatedBounds = typeVariable.getAnnotatedBounds();
                        for (AnnotatedType annotatedBound : annotatedBounds) {
                            Type type = annotatedBound.getType();
                            System.out.println("field [" + declaredField.getName() + "] ==> " + type);
                        }
                    }
                }
            }
        }
    }

    private static void getNameTest() {
        System.out.println("--------------------------------getNameTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = TypeVariableTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = ((ParameterizedType) genericType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof TypeVariable){
                        TypeVariable typeVariable = (TypeVariable) actualTypeArgument;
                        // 获取Type实际类型
                        System.out.println("field [" + declaredField.getName() + "] ==> " + typeVariable.getName());
                    }
                }
            }
        }
    }

    private static void getGenericDeclarationTest() {
        System.out.println("--------------------------------getGenericDeclarationTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = TypeVariableTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof TypeVariable){
                        TypeVariable typeVariable = (TypeVariable) actualTypeArgument;
                        // 获取Type实际类型
                        System.out.println("field [" + declaredField.getName() + "] ==> " + typeVariable.getGenericDeclaration());
                    }
                }
            }
        }
    }

    private static void getBoundsTest() {
        System.out.println("--------------------------------getBoundsTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = TypeVariableTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = ((ParameterizedType) genericType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof TypeVariable){
                        TypeVariable typeVariable = (TypeVariable) actualTypeArgument;
                        // 获取Type实际类型
                        System.out.println("field [" + declaredField.getName() + "] ==> " + Arrays.stream(typeVariable.getBounds()).collect(Collectors.toList()));
                    }
                }
            }
        }
    }

    private static void getActualTypeTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getActualTypeTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = TypeVariableTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = ((ParameterizedType) genericType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    // 获取Type实际类型
                    System.out.println("field [" + declaredField.getName() + "] ==> " + actualTypeArgument.getClass().getName());
                }
            }
        }
    }

}
```

![image-20211208153953809](http://rocks526.top/lzx/image-20211208153953809.png)

### 2.5 WildcardType

- 源码

```java
package java.lang.reflect;

/**
 * WildcardType represents a wildcard type expression, such as
 * {@code ?}, {@code ? extends Number}, or {@code ? super Integer}.
 *
 * 通配符泛型，例如List<? extends Integer>
 *
 * @since 1.5
 */
public interface WildcardType extends Type {
    /**
     * Returns an array of {@code Type} objects representing the  upper
     * bound(s) of this type variable.  Note that if no upper bound is
     * explicitly declared, the upper bound is {@code Object}.
     *
     * <p>For each upper bound B :
     * <ul>
     *  <li>if B is a parameterized type or a type variable, it is created,
     *  (see {@link java.lang.reflect.ParameterizedType ParameterizedType}
     *  for the details of the creation process for parameterized types).
     *  <li>Otherwise, B is resolved.
     * </ul>
     *
     * @return an array of Types representing the upper bound(s) of this
     *     type variable
     * @throws TypeNotPresentException if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     * 返回通配符泛型变量的上限，例如List<? extends Integer>返回Integer，List<? super Integer>返回Object
     */
    Type[] getUpperBounds();

    /**
     * Returns an array of {@code Type} objects representing the
     * lower bound(s) of this type variable.  Note that if no lower bound is
     * explicitly declared, the lower bound is the type of {@code null}.
     * In this case, a zero length array is returned.
     *
     * <p>For each lower bound B :
     * <ul>
     *   <li>if B is a parameterized type or a type variable, it is created,
     *  (see {@link java.lang.reflect.ParameterizedType ParameterizedType}
     *  for the details of the creation process for parameterized types).
     *   <li>Otherwise, B is resolved.
     * </ul>
     *
     * @return an array of Types representing the lower bound(s) of this
     *     type variable
     * @throws TypeNotPresentException if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     * 返回通配符泛型变量的下限，例如List<? extends Integer>返回[]，List<? super Integer>返回Integer
     */
    Type[] getLowerBounds();
    // one or many? Up to language spec; currently only one, but this API
    // allows for generalization.
}
```

- 测试

```java
package type;

import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.lang.reflect.WildcardType;
import java.util.Arrays;
import java.util.List;
import java.util.Map;

/**
 * WildcardType ==> 通配符泛型测试
 * 例如：List<? extends Number>里的?就是WildcardType
 */
public class WildcardTypeTest {

    private List<? extends Number> list1;
    private List<? extends WildcardTypeTest> list2;
    private List<? super WildcardTypeTest> list3;
    private Map<String, ? extends Comparable> map;

    public static void main(String[] args) throws NoSuchFieldException {
        // 类型测试
        getActualTypeTest();
        // getUpperBounds方法测试
        getUpperBoundsTest();
        // getLowerBounds方法测试
        getLowerBoundsTest();
    }

    private static void getLowerBoundsTest() {
        System.out.println("--------------------------------getLowerBoundsTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = WildcardTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof WildcardType){
                        // 获取通配符变量上限
                        System.out.println("field [" + declaredField.getName() + "] ==> " + Arrays.toString(((WildcardType) actualTypeArgument).getLowerBounds()));
                    }
                }
            }
        }
    }

    private static void getUpperBoundsTest() {
        System.out.println("--------------------------------getUpperBoundsTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = WildcardTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    if (actualTypeArgument instanceof WildcardType){
                        // 获取通配符变量上限
                        System.out.println("field [" + declaredField.getName() + "] ==> " + Arrays.toString(((WildcardType) actualTypeArgument).getUpperBounds()));
                    }
                }
            }
        }
    }

    private static void getActualTypeTest() throws NoSuchFieldException {
        System.out.println("--------------------------------getActualTypeTest-----------------------------------------");
        // 获取字段
        Field[] declaredFields = WildcardTypeTest.class.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            // 获取泛型类型，返回接口
            Type genericType = declaredField.getGenericType();
            // 获取泛型类型里的变量
            if (genericType instanceof ParameterizedType){
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    // 获取Type实际类型
                    System.out.println("field [" + declaredField.getName() + "] ==> " + actualTypeArgument.getClass().getName());
                }
            }
        }
    }

}
```

![image-20211208153845663](http://rocks526.top/lzx/image-20211208153845663.png)

