# 一：Java集合框架体系结构

### 1.1 集合框架介绍

早在 Java 2 中，Java 就提供了特设类，比如：Dictionary, Vector, Stack, 和 Properties 这些类用来存储和操作对象组。虽然这些类都非常有用，但是它们缺少一个核心的，统一的主题。由于这个原因，使用 Vector 类的方式和使用 Properties 类的方式有着很大不同。因此出现了集合/容器框架设计，要满足以下几个目标：

- 该框架必须是高性能的，基本集合（动态数组，链表，树，哈希表）的实现也必须是高效的
- 该框架允许不同类型的集合，以类似的方式工作，具有高度的互操作性
- 对一个集合的扩展和适应必须是简单的

为此，整个集合框架就围绕一组标准接口而设计，你可以直接使用这些接口的标准实现，诸如： LinkedList, HashSet, 和 TreeSet 等，除此之外你也可以通过这些接口实现自己的集合。

### 1.2 集合框架体系结构图

![img](http://rocks526.top/lzx/2243690-9cd9c896e0d512ed.gif)

整个集合体系结构分为两大类：

- 集合（Collection）：存储一个元素的集合
- 哈希表（Map）：存储键/值对映射

Collection 接口又有 3 种子类型，列表（List）、集合（Set）和 队列（Queue），再下面是一些抽象类，最后是具体实现类，常用的有 ArrayList、LinkedList、HashSet、LinkedHashSet、HashMap、LinkedHashMap 等等。

集合框架是一个用来代表和操纵集合的统一架构，所有的集合框架都包含如下内容：

- 接口：是代表集合的抽象数据类型，例如 Collection、List、Set、Map 等，之所以定义多个接口，是为了以不同的方式操作集合对象
- 实现（类）：是集合接口的具体实现，从本质上讲，它们是可重复使用的数据结构，例如：ArrayList、LinkedList、HashSet、HashMap
- 算法：是实现集合接口的对象里的方法执行的一些有用的计算，例如：搜索和排序，这些算法被称为多态，那是因为相同的方法可以在相似的接口上有着不同的实现

Java 集合框架提供了一套性能优良，使用方便的接口和类，java集合框架位于java.util包中。

### 1.3 集合接口

集合框架定义了一些核心接口，具体如下：

| 接口            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Iterable\<T>    | 迭代器接口，表明此容器可以遍历迭代                           |
| Collection\<T>  | 所有集合类型容器的顶级接口，代表存储一组`不唯一、无序`的元素集合 |
| RandomAccess    | 标记接口，实现此接口代表容器支持根据下标随机访问，在实现某些算法时会有不同的处理逻辑 |
| List\<T>        | List接口是一个`有序`的 Collection，使用此接口能够精确的控制每个元素插入的位置，能够通过索引来访问List中的元素，第一个元素的索引为 0，而且`允许有相同的元素` |
| Set\<T>         | Set 具有与 Collection 完全一样的接口，只是行为上不同，Set `不保存重复`的元素，并且内部存储`无序`。 |
| SortedSet\<T>   | 继承于Set接口，实现保存`有序`的集合。                        |
| Map\<K,V>       | Map 接口存储一组`键值对象`，提供key（键）到value（值）的映射 |
| Map.Entry\<K,V> | 描述在一个Map中的`一个元素`（键/值对）。是一个 Map 的内部接口 |
| SortedMap\<K,V> | 继承于 Map，使 Key 保持`升序`排列                            |
| Comparable\<T>  | 排序接口，实现此接口，代表`支持排序`                         |
| Comparator\<T>  | `排序器`，内部定义元素之间的排序逻辑                         |

注：部分算法在排序容器内元素时，要求元素都实现Comparable或者传入一个显式的Comparator类。

### 1.4 集合实现类

Java提供了一套实现了容器框架接口的标准类，其中一些是具体类，这些类可以直接拿来使用，而另外一些是抽象类，提供了接口的部分实现，具体如下：

| 容器类                     | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| AbstractCollection\<T>     | 实现了集合接口的一些通用方法                                 |
| AbstractList\<T>           | 继承于AbstractCollection，并且实现了大部分List接口的通用方法 |
| AbstractSequentialList\<T> | 继承于AbstractList ，提供了对数据元素的链式访问而不是随机访问 |
| ArrayList\<T>              | 实现了List的接口，基于数组实现了`动态扩容`、`随机访问`和`顺序遍历`等特性 |
| LinkedList\<T>             | 实现了List的接口，基于链表实现了`动态扩容`、`顺序遍历`等特性 |
| AbstractSet\<T>            | 继承于AbstractCollection，并且实现了大部分Set接口通用方法    |
| HashSet\<T>                | 实现了Set接口，`不允许出现重复元素`，`不保证集合中元素的顺序`，`允许包含值为null的元素` |
| LinkedHashSet\<T>          | 实现了Set接口，继承于HashSet，底层`通过链表实现了元素的排序（访问顺序）`，`适合LRU` |
| TreeSet\<T>                | 实现了SortedSet接口，`支持根据某种排序算法排序`              |
| AbstractMap\<K,V>          | 实现了Map接口的通用方法                                      |
| HashMap\<K,V>              | 继承自AbstractMap，根据键的HashCode值存储数据，`具有O(1)的访问速度`，`允许NULL值` |
| TreeMap\<K,V>              | 继承自AbstractMap，实现了SortedMap接口，`支持key根据某种顺序排列` |
| LinkedHashMap\<K,V>        | 继承自HashMap，底层`通过链表实现了元素的排序（访问顺序）`，`适合LRU` |

注：这里只列举了常用的容器类，都是非线程安全的，线程安全的容器在Java并发章节介绍。

### 1.5 算法

Java集合框架除了提供上面的集合标准接口定义、高性能容器实现之外，还提供了两个算法工具类。

-  Collections：容器工具类，支持排序、查找、线程安全、不可变容器等通用方法
-  Arrays：数组工具类，支持排查、查找、拷贝、切割等方法

注：这两个工具类提供的功能类似，不过一个是针对容器类的，一个是针对数组的。

# 二：Java集合核心类介绍

注：此处只介绍整个集合框架体系的一些标准接口和抽象类，具体的实现子类源码解析参考Jdk源码解析章节。

### 2.1 Iterable && Iterator 接口

- 介绍

Iterable接口`提供容器遍历的能力`，可以返回一个迭代器Iterator，Jdk8之后提供了增强for循环和Spliterator切分器。

- 源码

```java
package java.lang;

import java.util.Iterator;
import java.util.Objects;
import java.util.Spliterator;
import java.util.Spliterators;
import java.util.function.Consumer;

/**
 * 1.使用此接口，代表支持遍历
 * 2.可以通过iterator获取一个迭代器进行遍历
 * 3.也可以通过for循环遍历
 * 4.Jdk1.8之后，引入了增强forEach遍历，可以传入一个函数表达式进行遍历
 */
public interface Iterable<T> {

    /**
     * 获取一个遍历的迭代器
     */
    Iterator<T> iterator();

    /**
     * Jdk8引入的函数式编程，简化代码，增强for循环
     * @param action
     */
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }

    /**
     * 获取一个切分器，Jdk8引入
     */
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }

}
```

```java
package java.util;

import java.util.function.Consumer;

/**
 * 集合上的迭代器，用于遍历集合元素
 */
public interface Iterator<E> {

    /**
     * 是否还有下一个元素
     */
    boolean hasNext();

    /**
     * 返回下一个元素
     */
    E next();

    /**
     * 从集合中移除刚刚返回的元素
     */
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    /**
     * Jdk8引入函数式编程优化迭代器
     *
     *      while(hasNext()){
     *          handler == > next();
     *      }
     *
     *  将迭代器遍历这种编程范式隐藏在此方法内，简化代码
     * @param action
     */
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

### 2.2 Collection接口

- 介绍

 集合的通用`顶级接口`，声明一个集合的基础方法。

- 源码

```java
package java.util;

import java.util.function.Predicate;
import java.util.stream.Stream;
import java.util.stream.StreamSupport;


/**
 * 集合的通用顶级接口，声明一个集合的基础方法
 */
public interface Collection<E> extends Iterable<E> {

    // =================== 查询操作 ====================================

    /**
     * 返回集合元素个数
     */
    int size();

    /**
     * 集合是否是空的
     */
    boolean isEmpty();

    /**
     * 集合是否包含某个元素
     */
    boolean contains(Object o);

    /**
     * 获取集合的迭代器
     */
    Iterator<E> iterator();

    /**
     * 返回包含此集合所有元素的数组
     */
    Object[] toArray();

    /**
     * 返回包含此集合所有元素的数组，传入返回数组的类型，要求与集合类型一致
     */
    <T> T[] toArray(T[] a);


    // =================== 变更操作 ====================================

    /**
     * 向集合添加一个元素
     * 操作失败抛出UnsupportedOperationException
     * 参数不合法抛出IllegalStateException
     */
    boolean add(E e);

    /**
     * 从集合删除一个元素，如果存在的话
     */
    boolean remove(Object o);


    // =================== 批量操作 ====================================

    /**
     * 判断是否包含参数集合的所有元素
     */
    boolean containsAll(Collection<?> c);

    /**
     * 将指定集合的元素全部添加到此集合中
     */
    boolean addAll(Collection<? extends E> c);

    /**
     * 删除此集合中包含的所有参数集合的元素
     */
    boolean removeAll(Collection<?> c);

    /**
     * 删除此集合中满足给定条件的所有元素，返回是否有元素被删除
     */
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }

    /**
     * 仅保留此集合中包含参数集合元素的元素
     * 即保留交集部分
     */
    boolean retainAll(Collection<?> c);

    /**
     * 清空整个集合
     */
    void clear();


    // =================== 比较 和 Hash 操作 ====================================

    /**
     * 判断指定对象与此集合是否相等
     */
    boolean equals(Object o);

    /**
     * 生成HashCode
     */
    int hashCode();

    /**
     * 创建切分器
     */
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

    /**
     * 创建一个Stream流
     */
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    /**
     * 返回一个并行流
     */
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}
```

### 2.3 AbstractCollection抽象类

- 介绍

Collection接口的抽象实现，`实现了一些通用方法`：isEmpty、contains、toArray、remove、containsAll、addAll、removeAll、retainAll等，大多`采用迭代器遍历实现`，`复杂度O(N)`，效率不是很高。

- 源码

```java
package java.util;


/**
 * 集合的基础抽象类，可以直接基于此类进行扩展
 */
public abstract class AbstractCollection<E> implements Collection<E> {

    protected AbstractCollection() {
        // 唯一构造器，只能由子类调用
    }

    // ======================= 查询操作 ===================================

    /**
     * 返回一个迭代器，用于遍历集合
     */
    public abstract Iterator<E> iterator();

    /**
     * 返回集合元素个数
     */
    public abstract int size();

    /**
     * 判断集合是否为空，默认实现为判断size是否为0
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /**
     * 判断是否包含某个元素，默认实现为通过迭代器遍历，挨个对比
     */
    public boolean contains(Object o) {
        Iterator<E> it = iterator();
        if (o==null) {
            while (it.hasNext())
                if (it.next()==null)
                    return true;
        } else {
            while (it.hasNext())
                if (o.equals(it.next()))
                    return true;
        }
        return false;
    }

    /**
     * 返回一个数组，默认实现通过迭代器遍历实现，返回数组顺序和迭代器遍历一致
     */
    public Object[] toArray() {
        // 创建size大小数据
        Object[] r = new Object[size()];
        Iterator<E> it = iterator();
        for (int i = 0; i < r.length; i++) {
            // 迭代器元素比size小，直接截断，以size为准
            if (! it.hasNext()) // fewer elements than expected
                return Arrays.copyOf(r, i);
            r[i] = it.next();
        }
        // 如果迭代器元素个数大于size，则通过finishToArray方法扩容数组，容纳迭代器后续元素
        return it.hasNext() ? finishToArray(r, it) : r;
    }


    /**
     * 将集合返回为数组个数，泛型通过参数传入
     * 实现逻辑与toArray类似，返回元素个数以迭代器为准
     */
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        // Estimate size of array; be prepared to see more or fewer elements
        int size = size();
        T[] r = a.length >= size ? a :
                  (T[])java.lang.reflect.Array
                  .newInstance(a.getClass().getComponentType(), size);
        Iterator<E> it = iterator();

        for (int i = 0; i < r.length; i++) {
            if (! it.hasNext()) { // fewer elements than expected
                if (a == r) {
                    r[i] = null; // null-terminate
                } else if (a.length < i) {
                    return Arrays.copyOf(r, i);
                } else {
                    System.arraycopy(r, 0, a, 0, i);
                    if (a.length > i) {
                        a[i] = null;
                    }
                }
                return a;
            }
            r[i] = (T)it.next();
        }
        // more elements than expected
        return it.hasNext() ? finishToArray(r, it) : r;
    }

    // 数组最大长度，超过此长度，Jvm可能会有问题
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;


    /**
     * 迭代器元素大于size，对数组进行扩容，将迭代器后续元素融入
     */
    @SuppressWarnings("unchecked")
    private static <T> T[] finishToArray(T[] r, Iterator<?> it) {
        int i = r.length;
        while (it.hasNext()) {
            int cap = r.length;
            if (i == cap) {
                int newCap = cap + (cap >> 1) + 1;
                // overflow-conscious code
                if (newCap - MAX_ARRAY_SIZE > 0)
                    newCap = hugeCapacity(cap + 1);
                r = Arrays.copyOf(r, newCap);
            }
            r[i++] = (T)it.next();
        }
        // trim if overallocated
        return (i == r.length) ? r : Arrays.copyOf(r, i);
    }

    // 数组容量扩充
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError
                ("Required array size too large");
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

    // ===================== 变更操作 ==========================================

    /**
     * 新增一个元素，默认实现不支持此操作
     */
    public boolean add(E e) {
        throw new UnsupportedOperationException();
    }

    /**
     * 移除一个元素，默认实现是通过迭代器遍历查找并移除
     */
    public boolean remove(Object o) {
        Iterator<E> it = iterator();
        if (o==null) {
            while (it.hasNext()) {
                if (it.next()==null) {
                    it.remove();
                    return true;
                }
            }
        } else {
            while (it.hasNext()) {
                if (o.equals(it.next())) {
                    it.remove();
                    return true;
                }
            }
        }
        return false;
    }


    // ======================== 批量操作 =====================================


    /**
     * 判断是否包含目标集合的所有元素，默认for循环调用contain
     */
    public boolean containsAll(Collection<?> c) {
        for (Object e : c)
            if (!contains(e))
                return false;
        return true;
    }

    /**
     * 批量新增元素，默认for调用add
     */
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }


    /**
     * 批量移除元素，默认通过迭代器遍历，contain对比实现
     */
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<?> it = iterator();
        while (it.hasNext()) {
            if (c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }


    /**
     * 保留集合与参数集合的交集部分，默认迭代器遍历 + contain方法实现
     */
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<E> it = iterator();
        while (it.hasNext()) {
            if (!c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }


    /**
     * 清空集合，默认迭代器遍历删除
     */
    public void clear() {
        Iterator<E> it = iterator();
        while (it.hasNext()) {
            it.next();
            it.remove();
        }
    }


    //  ============== 字符串转换 ==========================================

    /**
     * 集合输出，默认为： [v1, v2, v3 .....]
     */
    public String toString() {
        Iterator<E> it = iterator();
        if (! it.hasNext())
            return "[]";

        StringBuilder sb = new StringBuilder();
        sb.append('[');
        for (;;) {
            E e = it.next();
            sb.append(e == this ? "(this Collection)" : e);
            if (! it.hasNext())
                return sb.append(']').toString();
            sb.append(',').append(' ');
        }
    }

}
```

### 2.4 RandomAccess接口

- 介绍

 标记接口，实现此接口`代表容器支持根据下标随机访问`。

- 源码

```java
package java.util;


/**
 * 标记接口，实现此接口代表容器支持根据下标随机访问
 * 用于在通用算法中，根据下标随机访问的特性进行优化
 * 例如针对查找的算法，如果支持RandomAccess接口，即可通过二分查找实现
 */
public interface RandomAccess {
}
```

### 2.5 List接口

- 介绍

 List通用接口，代表`有序`、`可重复`集合。

继承自Collection，扩容了部分`下标相关操作`、`迭代器操作`、`集合切分`的方法。

- 源码

```java
package java.util;

import java.util.function.UnaryOperator;


/**
 * List通用接口，代表有序、可重复集合
 *
 */
public interface List<E> extends Collection<E> {

    // ================= 查询操作 ===========================================

    // 返回集合元素个数
    int size();

    // 判断是否为空
    boolean isEmpty();

    // 判断集合是否包含元素
    boolean contains(Object o);

    // 返回一个迭代器
    Iterator<E> iterator();

    // 转换数组
    Object[] toArray();

    // 转换泛型数组
    <T> T[] toArray(T[] a);


    // =========================== 变更操作 =====================================

    // 新增元素，添加到末尾!!!
    boolean add(E e);

    // 列表删除第一个匹配项
    boolean remove(Object o);


    // =========================== 批量操作 =====================================

    // 是否包含参数集合的所有元素
    boolean containsAll(Collection<?> c);

    // 批量新增，追加到末尾!!!
    boolean addAll(Collection<? extends E> c);

    // 从指定索引开始批量插入
    boolean addAll(int index, Collection<? extends E> c);

    // 批量删除
    boolean removeAll(Collection<?> c);

    // 保留集合交集部分
    boolean retainAll(Collection<?> c);

    // 将每个元素根据传入的函数式方法，替换为目标元素，通过迭代器遍历实现
    default void replaceAll(UnaryOperator<E> operator) {
        Objects.requireNonNull(operator);
        final ListIterator<E> li = this.listIterator();
        while (li.hasNext()) {
            li.set(operator.apply(li.next()));
        }
    }


    /**
     * 集合排序，传入比较器参数
     * 默认采用转数组、排序数组、迭代器遍历替换元素的方式实现
     */
    @SuppressWarnings({"unchecked", "rawtypes"})
    default void sort(Comparator<? super E> c) {
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }

    // 清空集合
    void clear();


    // =========================== 哈希操作 =====================================

    // 判断集合是否相等，默认比较引用
    boolean equals(Object o);

    // 生成集合的hashcode方法
    int hashCode();


    // =============== 根据下标访问操作 ================================

    // 获取指定下标的元素
    E get(int index);

    // 设置指定下标的元素值
    E set(int index, E element);

    // 给指定下标插入元素，会导致后续元素全部搬移
    void add(int index, E element);

    // 移除指定下标的元素，会导致元素搬移
    E remove(int index);


    // ===================== 查找操作 ========================================

    // 返回该元素第一次出现的索引下标
    int indexOf(Object o);

    // 返回该元素在集合中的最后的下标
    int lastIndexOf(Object o);


    // ============================ List迭代器 =====================================


    // 定制的遍历List的迭代器，支持双向遍历
    ListIterator<E> listIterator();

    // 从指定的下标开始返回一个迭代器
    ListIterator<E> listIterator(int index);

    // ================= 视图操作 ======================================

    // 返回集合指定下标之间的元素作为一个只读视图，注意只读！！！
    List<E> subList(int fromIndex, int toIndex);

    // 创建一个切分器
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.ORDERED);
    }
}
```

```java
package java.util;

/**
 * 用于List的迭代器，支持在双向遍历，并且支持遍历过程中进行更新、删除等
 */
public interface ListIterator<E> extends Iterator<E> {

    // ========================= 查询操作 ===================================

    // 向后遍历，是否还有下一个元素
    boolean hasNext();

    // 向后遍历，返回下一个元素
    E next();

    // 向前遍历，是否还有前一个元素
    boolean hasPrevious();

    // 返回前一个元素
    E previous();

    // 返回下一个元素的下标
    int nextIndex();

    // 返回上一个元素的下标
    int previousIndex();


    // ======================== 更新操作 ========================================

    // 从列表中删除当前游标指向的元素
    void remove();

    // 替换当前游标指向的元素
    void set(E e);

    // 在当前游标后面新增一个元素
    void add(E e);
}
```

### 2.6 AbstractList抽象类

- 介绍

List接口的抽象实现，实现`部分通用接口（迭代器、contain、下标检查等）`，减少底层实现类的开发量。

- 源码

```java
package java.util;


/**
 * List接口的抽象实现，实现部分通用接口，减少底层实现类的开发量
 */
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {

    // 唯一构造方法，只能子类调用
    protected AbstractList() {
    }

    // 集合添加元素，添加的末尾，减少元素搬移工作
    public boolean add(E e) {
        // 调用add(index, value)实现
        add(size(), e);
        return true;
    }

    // 获取指定下标的元素
    abstract public E get(int index);

    // 设置指定下标的元素
    public E set(int index, E element) {
        throw new UnsupportedOperationException();
    }

    // 给指定下标新增一个元素
    public void add(int index, E element) {
        throw new UnsupportedOperationException();
    }

    // 移除指定下标的元素
    public E remove(int index) {
        throw new UnsupportedOperationException();
    }

    // ============================ 查找操作 =========================================

    // 查找目标元素在集合中出现的第一个下标
    public int indexOf(Object o) {
        ListIterator<E> it = listIterator();
        if (o==null) {
            while (it.hasNext())
                if (it.next()==null)
                    return it.previousIndex();
        } else {
            while (it.hasNext())
                if (o.equals(it.next()))
                    return it.previousIndex();
        }
        return -1;
    }

    // 查找目标元素在集合中出现的最后一个下标
    public int lastIndexOf(Object o) {
        // 从尾向前遍历
        ListIterator<E> it = listIterator(size());
        if (o==null) {
            while (it.hasPrevious())
                if (it.previous()==null)
                    return it.nextIndex();
        } else {
            while (it.hasPrevious())
                if (o.equals(it.previous()))
                    return it.nextIndex();
        }
        return -1;
    }


    // ========================= 批量操作 ============================

    // 清空集合
    public void clear() {
        removeRange(0, size());
    }

    // 批量新增
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);
        boolean modified = false;
        for (E e : c) {
            add(index++, e);
            modified = true;
        }
        return modified;
    }


    // ========================= 迭代器 ===================================

    // 返回List迭代器
    public Iterator<E> iterator() {
        return new Itr();
    }

    // 返回List迭代器
    public ListIterator<E> listIterator() {
        // 从头开始遍历
        return listIterator(0);
    }

    // 从指定下标返回迭代器
    public ListIterator<E> listIterator(final int index) {
        rangeCheckForAdd(index);

        return new ListItr(index);
    }

    // List迭代器的实现
    private class Itr implements Iterator<E> {

        // 游标
        int cursor = 0;

        // 最近一次调用next或prev的元素的下标，如果删除，则置为-1
        int lastRet = -1;

        // 检测是否存在并发修改，实现fast-fail机制
        int expectedModCount = modCount;

        public boolean hasNext() {
            return cursor != size();
        }

        // 获取下一个元素
        public E next() {
            checkForComodification();
            try {
                int i = cursor;
                // 获取值
                E next = get(i);
                // 更新游标和上一个元素下标
                lastRet = i;
                cursor = i + 1;
                return next;
            } catch (IndexOutOfBoundsException e) {
                // 检查是否并发导致
                checkForComodification();
                // 非并发导致，抛出异常
                throw new NoSuchElementException();
            }
        }

        public void remove() {
            // 上次操作元素下标检查
            if (lastRet < 0)
                throw new IllegalStateException();
            // 并发操作检查
            checkForComodification();

            try {
                // 移除指定下标的元素
                AbstractList.this.remove(lastRet);
                if (lastRet < cursor)
                    // 游标更新
                    cursor--;
                // 删除成功，lastRet置为-1
                lastRet = -1;
                // list调用remove更新了modCount，因此更新expectedModCount
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException e) {
                // lastRet是之前访问过的元素，因此必然存在，如果下标越界，必然是其他线程操作了
                throw new ConcurrentModificationException();
            }
        }

        // 快速失败策略，每次遍历之前检查是否存在并发操作
        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }

    // Itr的增强
    private class ListItr extends Itr implements ListIterator<E> {

        // 支持从指定下标开始遍历
        ListItr(int index) {
            cursor = index;
        }

        // 判断前面是否还有元素
        public boolean hasPrevious() {
            return cursor != 0;
        }

        // 向前遍历
        public E previous() {
            checkForComodification();
            try {
                int i = cursor - 1;
                E previous = get(i);
                lastRet = cursor = i;
                return previous;
            } catch (IndexOutOfBoundsException e) {
                checkForComodification();
                throw new NoSuchElementException();
            }
        }

        // 当前元素的下标
        public int nextIndex() {
            return cursor;
        }

        // 上个元素的下标
        public int previousIndex() {
            return cursor-1;
        }

        // 更新刚刚遍历的元素
        public void set(E e) {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                AbstractList.this.set(lastRet, e);
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        // 新增元素
        public void add(E e) {
            checkForComodification();

            try {
                int i = cursor;
                AbstractList.this.add(i, e);
                lastRet = -1;
                cursor = i + 1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }

    // 集合根据范围切分，返回只读的视图
    public List<E> subList(int fromIndex, int toIndex) {
        return (this instanceof RandomAccess ?
                new RandomAccessSubList<>(this, fromIndex, toIndex) :
                new SubList<>(this, fromIndex, toIndex));
    }

    // ===================== 比较和哈希 ===================================


    // 相等比较，要求两个集合元素和顺序完全一致则相等
    public boolean equals(Object o) {
        // 引用判断
        if (o == this)
            return true;
        // 类型判断
        if (!(o instanceof List))
            return false;

        // 获取迭代器
        ListIterator<E> e1 = listIterator();
        ListIterator<?> e2 = ((List<?>) o).listIterator();
        // 逐一对比
        while (e1.hasNext() && e2.hasNext()) {
            E o1 = e1.next();
            Object o2 = e2.next();
            if (!(o1==null ? o2==null : o1.equals(o2)))
                return false;
        }
        return !(e1.hasNext() || e2.hasNext());
    }

    // 计算哈希值
    public int hashCode() {
        int hashCode = 1;
        // 和每个元素都有关
        for (E e : this)
            hashCode = 31*hashCode + (e==null ? 0 : e.hashCode());
        return hashCode;
    }

    // 移除指定范围的元素
    protected void removeRange(int fromIndex, int toIndex) {
        // 迭代器移除
        ListIterator<E> it = listIterator(fromIndex);
        for (int i=0, n=toIndex-fromIndex; i<n; i++) {
            it.next();
            it.remove();
        }
    }

    // 此集合修改的次数，用来实现fast-fail
    protected transient int modCount = 0;

    // 下标合法性校验
    private void rangeCheckForAdd(int index) {
        if (index < 0 || index > size())
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    // 地址越界提示
    private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size();
    }
}

// 切分集合，从当前的实现来看，切分的子集合对api的支持依赖于原集合
class SubList<E> extends AbstractList<E> {

    // 原集合的引用
    private final AbstractList<E> l;
    // 偏移量，即原集合截取的开始下标
    private final int offset;
    // 大小
    private int size;

    SubList(AbstractList<E> list, int fromIndex, int toIndex) {
        // 参数校验
        if (fromIndex < 0)
            throw new IndexOutOfBoundsException("fromIndex = " + fromIndex);
        if (toIndex > list.size())
            throw new IndexOutOfBoundsException("toIndex = " + toIndex);
        if (fromIndex > toIndex)
            throw new IllegalArgumentException("fromIndex(" + fromIndex +
                                               ") > toIndex(" + toIndex + ")");
        // 保留原集合引用
        l = list;
        // 开始下标
        offset = fromIndex;
        // 长度
        size = toIndex - fromIndex;
        // 修改次数
        this.modCount = l.modCount;
    }

    // 设置指定下标元素
    public E set(int index, E element) {
        rangeCheck(index);
        checkForComodification();
        return l.set(index+offset, element);
    }

    // 获取元素
    public E get(int index) {
        rangeCheck(index);
        checkForComodification();
        return l.get(index+offset);
    }

    // 获取大小
    public int size() {
        checkForComodification();
        return size;
    }

    // 新增元素
    public void add(int index, E element) {
        rangeCheckForAdd(index);
        checkForComodification();
        l.add(index+offset, element);
        this.modCount = l.modCount;
        size++;
    }

    // 移除元素
    public E remove(int index) {
        rangeCheck(index);
        checkForComodification();
        E result = l.remove(index+offset);
        this.modCount = l.modCount;
        size--;
        return result;
    }

    // 范围移除
    protected void removeRange(int fromIndex, int toIndex) {
        checkForComodification();
        l.removeRange(fromIndex+offset, toIndex+offset);
        this.modCount = l.modCount;
        size -= (toIndex-fromIndex);
    }

    // 批量新增
    public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }

    // 指定下标开始批量新增
    public boolean addAll(int index, Collection<? extends E> c) {
        // 范围检查
        rangeCheckForAdd(index);
        int cSize = c.size();
        if (cSize==0)
            return false;
        
        // 并发检查
        checkForComodification();
        l.addAll(offset+index, c);
        this.modCount = l.modCount;
        size += cSize;
        return true;
    }

    // 迭代器
    public Iterator<E> iterator() {
        return listIterator();
    }

    // 迭代器
    public ListIterator<E> listIterator(final int index) {
        checkForComodification();
        rangeCheckForAdd(index);

        return new ListIterator<E>() {
            // 创建新的迭代器，要计算offset
            private final ListIterator<E> i = l.listIterator(index+offset);

            public boolean hasNext() {
                return nextIndex() < size;
            }

            public E next() {
                if (hasNext())
                    return i.next();
                else
                    throw new NoSuchElementException();
            }

            public boolean hasPrevious() {
                return previousIndex() >= 0;
            }

            public E previous() {
                if (hasPrevious())
                    return i.previous();
                else
                    throw new NoSuchElementException();
            }

            public int nextIndex() {
                return i.nextIndex() - offset;
            }

            public int previousIndex() {
                return i.previousIndex() - offset;
            }

            public void remove() {
                i.remove();
                SubList.this.modCount = l.modCount;
                size--;
            }

            public void set(E e) {
                i.set(e);
            }

            public void add(E e) {
                i.add(e);
                SubList.this.modCount = l.modCount;
                size++;
            }
        };
    }

    // 切分集合
    public List<E> subList(int fromIndex, int toIndex) {
        return new SubList<>(this, fromIndex, toIndex);
    }

    // 下标检查
    private void rangeCheck(int index) {
        if (index < 0 || index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    // 下标检查
    private void rangeCheckForAdd(int index) {
        if (index < 0 || index > size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size;
    }

    private void checkForComodification() {
        if (this.modCount != l.modCount)
            throw new ConcurrentModificationException();
    }
}

// 实际作用和SubList一致，多了一个RandomAccess接口标识
class RandomAccessSubList<E> extends SubList<E> implements RandomAccess {
    RandomAccessSubList(AbstractList<E> list, int fromIndex, int toIndex) {
        super(list, fromIndex, toIndex);
    }

    public List<E> subList(int fromIndex, int toIndex) {
        return new RandomAccessSubList<>(this, fromIndex, toIndex);
    }
}
```

### 2.7 AbstractSequentialList抽象类

- 介绍

`不支持随机访问特性的集合`，`根据下标进行操作的相关方法全部通过迭代器实现`。

- 源码

```java
package java.util;

/**
 * 不支持随机访问特性的集合，根据下标进行操作的相关方法全部通过迭代器实现
 */
public abstract class AbstractSequentialList<E> extends AbstractList<E> {
    
    protected AbstractSequentialList() {
    }

    // 获取指定下标元素，根据迭代器获取
    public E get(int index) {
        try {
            return listIterator(index).next();
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

    // 设置指定下标的元素，根据迭代器更新
    public E set(int index, E element) {
        try {
            ListIterator<E> e = listIterator(index);
            E oldVal = e.next();
            e.set(element);
            return oldVal;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

    // 给指定下标添加元素，根据迭代器实现
    public void add(int index, E element) {
        try {
            listIterator(index).add(element);
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }

    // 移除指定下标的元素，根据迭代器实现
    public E remove(int index) {
        try {
            ListIterator<E> e = listIterator(index);
            E outCast = e.next();
            e.remove();
            return outCast;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }


    // ===================== 批量操作 =================================
    
    // 指定下标开始，批量新增
    public boolean addAll(int index, Collection<? extends E> c) {
        try {
            boolean modified = false;
            ListIterator<E> e1 = listIterator(index);
            Iterator<? extends E> e2 = c.iterator();
            while (e2.hasNext()) {
                e1.add(e2.next());
                modified = true;
            }
            return modified;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }


    // ========================= 迭代器 ====================================

    // 返回迭代器
    public Iterator<E> iterator() {
        return listIterator();
    }

    // 返回list的迭代器
    public abstract ListIterator<E> listIterator(int index);
}
```

### 2.8 Set接口

- 介绍

Set接口代表：`不包含重复元素`、`无序`的集合。

Set继承自Collection，并`没有对Collection的方法进行扩充`，与Collection完全一致。

- 源码

```java
package java.util;

/**
 * 不包含重复元素、无序的集合
 */
public interface Set<E> extends Collection<E> {

    // ===================== 查询操作 ==========================================

    // 返回集合大小
    int size();

    // 是否为空
    boolean isEmpty();

    // 是否包含某个元素
    boolean contains(Object o);

    // 获取迭代器
    Iterator<E> iterator();

    // 转换数组
    Object[] toArray();

    // 转换泛型数组
    <T> T[] toArray(T[] a);


    // ========================= 变更操作 =====================================

    // 集合增加一个元素
    boolean add(E e);

    // 移除一个元素
    boolean remove(Object o);


    // ========================= 批量操作 =====================================

    // 是否包含参数集合的所有元素
    boolean containsAll(Collection<?> c);

    // 批量添加
    boolean addAll(Collection<? extends E> c);

    // 与参数集合取交集
    boolean retainAll(Collection<?> c);

    // 移除参数集合的所有元素
    boolean removeAll(Collection<?> c);

    // 清空集合
    void clear();


    // ========================= 比较与哈希操作 =====================================

    // 判断是否相等
    boolean equals(Object o);

    // 获取哈希值
    int hashCode();

    // 获取切分器
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.DISTINCT);
    }
}
```

### 2.9 AbstractSet抽象类

- 介绍

 Set接口的抽象实现，继承自AbstractCollection，大部分方法继承了AbstractCollection的实现。

- 源码

```java
package java.util;

/**
 * Set接口的抽象实现，继承自AbstractCollection，大部分方法继承了AbstractCollection的实现
 */
public abstract class AbstractSet<E> extends AbstractCollection<E> implements Set<E> {

    protected AbstractSet() {
    }

    // ======================== 比较和哈希 ===================================

    // 比较方法
    public boolean equals(Object o) {
        // 引用判断
        if (o == this)
            return true;
        // 类型判断
        if (!(o instanceof Set))
            return false;
        // 元素大小判断
        Collection<?> c = (Collection<?>) o;
        if (c.size() != size())
            return false;
        try {
            // 调用containsAll判断，不关注顺序
            return containsAll(c);
        } catch (ClassCastException unused)   {
            return false;
        } catch (NullPointerException unused) {
            return false;
        }
    }

    // 计算哈希值
    public int hashCode() {
        int h = 0;
        // 每个元素的哈希值累计
        Iterator<E> i = iterator();
        while (i.hasNext()) {
            E obj = i.next();
            if (obj != null)
                h += obj.hashCode();
        }
        return h;
    }

    // 移除参数的所有元素
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;

        // 检查大小，遍历小的集合，contains方法对比，remove移除
        if (size() > c.size()) {
            for (Iterator<?> i = c.iterator(); i.hasNext(); )
                modified |= remove(i.next());
        } else {
            for (Iterator<?> i = iterator(); i.hasNext(); ) {
                if (c.contains(i.next())) {
                    i.remove();
                    modified = true;
                }
            }
        }
        return modified;
    }

}
```

### 2.10 SortedSet接口

- 介绍

Set接口的扩充，`不允许重复`，`有序`的集合。

继承自Set接口，扩容了`最大`、`最小`、`获取范围`相关的方法。

- 源码

```java
package java.util;

/**
 * 支持排序的集合，通过Comparator排序
 */
public interface SortedSet<E> extends Set<E> {

    // 返回集合用于排序的比较器
    Comparator<? super E> comparator();

    // 取出集合中范围元素的集合，要求实现为视图模式
    SortedSet<E> subSet(E fromElement, E toElement);

    // 取出集合中范围元素的集合，要求实现为视图模式 （小于或等于该元素）
    SortedSet<E> headSet(E toElement);

    // 取出集合中范围元素的集合，要求实现为视图模式 （大于或等于该元素）
    SortedSet<E> tailSet(E fromElement);

    // 返回最小的元素
    E first();

    // 返回最大的元素
    E last();

    // 切分器
    @Override
    default Spliterator<E> spliterator() {
        return new Spliterators.IteratorSpliterator<E>(
                this, Spliterator.DISTINCT | Spliterator.SORTED | Spliterator.ORDERED) {
            @Override
            public Comparator<? super E> getComparator() {
                return SortedSet.this.comparator();
            }
        };
    }
}
```

### 2.11 Map接口

- 介绍

Map接口是哈希表的顶级接口，定义了哈希表的统一行为，在Jdk1.8之后新增了很多复合操作用来简化代码。

- 源码

```java
package java.util;

import java.util.function.BiConsumer;
import java.util.function.BiFunction;
import java.util.function.Function;
import java.io.Serializable;

/**
 * 哈希类型顶级接口，支持KV存储，用来替代以前的Dictionary
 */
public interface Map<K,V> {

    // ======================== 查询操作 ================================

    // 返回KV个数
    int size();

    // 是否为空
    boolean isEmpty();

    // key里是否包含某个元素
    boolean containsKey(Object key);

    // value里是否包含某个元素
    boolean containsValue(Object value);

    // 根据K获取V
    V get(Object key);

    // ========================= 变更操作 =====================================

    // 新增KV对
    V put(K key, V value);


    // 移除KV对
    V remove(Object key);


    // ========================= 批量操作 =====================================

    // 批量新增
    void putAll(Map<? extends K, ? extends V> m);

    // 清空集合
    void clear();


    // ========================= 视图操作 =====================================

    // 返回包含所有K的集合视图
    Set<K> keySet();

    // 返回包含所有V的集合视图 ==> K不可重复，所以是Set，V可以重复，所以是Collection
    Collection<V> values();


    // 返回所有KV对的集合视图
    Set<Map.Entry<K, V>> entrySet();

    // KV对
    interface Entry<K,V> {

        // 获取K
        K getKey();

        // 获取V
        V getValue();

        // 修改V
        V setValue(V value);

        // 判断KV对是否相等
        boolean equals(Object o);

        // 哈希值
        int hashCode();

        // 获取一个Key比较器
        public static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getKey().compareTo(c2.getKey());
        }

        // 获取一个Value比较器
        public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getValue().compareTo(c2.getValue());
        }

        // 返回一个比较器，用给定的比较器比较Key
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
        }

        // 返回一个比较器，用给定的比较器比较Value
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
        }
    }

    // ======================= 比较和哈希 =============================================

    // 是否相等
    boolean equals(Object o);

    // 哈希值
    int hashCode();

    // ========================= 支持默认值的操作 ==============================

    // 获取value，不存在则返回defaultValue
    default V getOrDefault(Object key, V defaultValue) {
        V v;
        return (((v = get(key)) != null) || containsKey(key))
            ? v
            : defaultValue;
    }

    // 增强for循环，使用函数表达式处理map元素
    default void forEach(BiConsumer<? super K, ? super V> action) {
        Objects.requireNonNull(action);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
            action.accept(k, v);
        }
    }


    // 批量修改value，根据函数表达式处理
    default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        Objects.requireNonNull(function);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }

            // ise thrown from function is not a cme.
            v = function.apply(k, v);

            try {
                entry.setValue(v);
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
        }
    }

    // key不存在时设置value，并返回当前值
    default V putIfAbsent(K key, V value) {
        V v = get(key);
        if (v == null) {
            v = put(key, value);
        }

        return v;
    }

    // 包含key，并且value等于期望值时，再移除
    default boolean remove(Object key, Object value) {
        Object curValue = get(key);
        // 不相等或者key不存在
        if (!Objects.equals(curValue, value) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        remove(key);
        return true;
    }

    // 包含key，并且value等于期望值时，再替换
    default boolean replace(K key, V oldValue, V newValue) {
        Object curValue = get(key);
        if (!Objects.equals(curValue, oldValue) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        put(key, newValue);
        return true;
    }

    // key存在时再替换
    default V replace(K key, V value) {
        V curValue;
        if (((curValue = get(key)) != null) || containsKey(key)) {
            curValue = put(key, value);
        }
        return curValue;
    }

    /**
     * 复合操作，当key不存在时，对key进行函数式计算，获得value，再put
     *      * <pre> {@code
     *      * if (map.get(key) == null) {
     *      *     V newValue = mappingFunction.apply(key);
     *      *     if (newValue != null)
     *      *         map.put(key, newValue);
     *      * }
     *      * }</pre>
     */
    default V computeIfAbsent(K key,
            Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        if ((v = get(key)) == null) {
            V newValue;
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
    }

    /**
     * 复合操作，当key存在时，根据key和旧的value进行函数式计算，生成新的value
     *  如果新的value为null，则执行remove操作，反之执行put操作
     *
     *      * if (map.get(key) != null) {
     *      *     V oldValue = map.get(key);
     *      *     V newValue = remappingFunction.apply(key, oldValue);
     *      *     if (newValue != null)
     *      *         map.put(key, newValue);
     *      *     else
     *      *         map.remove(key);
     *      * }
     *      * }</pre>
     */
    default V computeIfPresent(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue;
        if ((oldValue = get(key)) != null) {
            V newValue = remappingFunction.apply(key, oldValue);
            if (newValue != null) {
                put(key, newValue);
                return newValue;
            } else {
                remove(key);
                return null;
            }
        } else {
            return null;
        }
    }

    /**
     * 复合操作，基于key和旧的value计算新的value，
     * 1. 如果新值不为null则put
     * 2. 如果新值为null，则删除旧值，如果旧值不存在，则不处理
     */
    default V compute(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue = get(key);

        V newValue = remappingFunction.apply(key, oldValue);
        if (newValue == null) {
            // 新值为null，删除旧值
            if (oldValue != null || containsKey(key)) {
                remove(key);
                return null;
            } else {
                // 旧值不存在
                return null;
            }
        } else {
            // 替换旧值
            put(key, newValue);
            return newValue;
        }
    }


    /**
     * 复合操作，如果旧值不存在，则使用value为新值，反之根据旧值和value计算新值
     * 如果新值为null，则remove，反之put
     */
    default V merge(K key, V value,
            BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        V oldValue = get(key);
        V newValue = (oldValue == null) ? value :
                   remappingFunction.apply(oldValue, value);
        if(newValue == null) {
            remove(key);
        } else {
            put(key, newValue);
        }
        return newValue;
    }
}
```

### 2.12 AbstractMap抽象类

- 介绍

哈希表抽象实现，`通过迭代器实现了一些通用方法，效率较低`，`提供了两个Entry实现`。

- 源码

```java
package java.util;
import java.util.Map.Entry;

/**
 * 哈希表抽象实现，实现一些通用方法
 */
public abstract class AbstractMap<K,V> implements Map<K,V> {

    protected AbstractMap() {
    }

    // ======================== 查询操作 ====================================

    // 返回集合个数
    public int size() {
        return entrySet().size();
    }

    // 集合是否为空
    public boolean isEmpty() {
        return size() == 0;
    }

    // 是否包含value
    public boolean containsValue(Object value) {
        // 迭代器计算
        Iterator<Entry<K,V>> i = entrySet().iterator();
        if (value==null) {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (e.getValue()==null)
                    return true;
            }
        } else {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (value.equals(e.getValue()))
                    return true;
            }
        }
        return false;
    }

    // 是否包含key
    public boolean containsKey(Object key) {
        Iterator<Map.Entry<K,V>> i = entrySet().iterator();
        if (key==null) {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (e.getKey()==null)
                    return true;
            }
        } else {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (key.equals(e.getKey()))
                    return true;
            }
        }
        return false;
    }

    // 根据k获取v，迭代器计算
    public V get(Object key) {
        Iterator<Entry<K,V>> i = entrySet().iterator();
        if (key==null) {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (e.getKey()==null)
                    return e.getValue();
            }
        } else {
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                if (key.equals(e.getKey()))
                    return e.getValue();
            }
        }
        return null;
    }


    // =================== 变更操作 ========================================

    // 设置kv
    public V put(K key, V value) {
        throw new UnsupportedOperationException();
    }

    // 移除kv
    public V remove(Object key) {
        Iterator<Entry<K,V>> i = entrySet().iterator();
        Entry<K,V> correctEntry = null;
        if (key==null) {
            while (correctEntry==null && i.hasNext()) {
                Entry<K,V> e = i.next();
                if (e.getKey()==null)
                    correctEntry = e;
            }
        } else {
            while (correctEntry==null && i.hasNext()) {
                Entry<K,V> e = i.next();
                if (key.equals(e.getKey()))
                    correctEntry = e;
            }
        }

        V oldValue = null;
        if (correctEntry !=null) {
            oldValue = correctEntry.getValue();
            i.remove();
        }
        return oldValue;
    }


    // =================== 批量操作 ====================================

    // 批量新增
    public void putAll(Map<? extends K, ? extends V> m) {
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet())
            put(e.getKey(), e.getValue());
    }

    // 清空集合
    public void clear() {
        entrySet().clear();
    }


    // ============================ 视图相关操作 ==============================

    // 所有key的集合
    transient Set<K>        keySet;
    // 所有values的集合
    transient Collection<V> values;

    // 获取所有key的集合
    public Set<K> keySet() {
        Set<K> ks = keySet;
        if (ks == null) {
            ks = new AbstractSet<K>() {
                public Iterator<K> iterator() {
                    return new Iterator<K>() {
                        // 根据entrySet计算
                        private Iterator<Entry<K,V>> i = entrySet().iterator();

                        public boolean hasNext() {
                            return i.hasNext();
                        }

                        public K next() {
                            return i.next().getKey();
                        }

                        public void remove() {
                            i.remove();
                        }
                    };
                }

                public int size() {
                    return AbstractMap.this.size();
                }

                public boolean isEmpty() {
                    return AbstractMap.this.isEmpty();
                }

                public void clear() {
                    AbstractMap.this.clear();
                }

                public boolean contains(Object k) {
                    return AbstractMap.this.containsKey(k);
                }
            };
            keySet = ks;
        }
        return ks;
    }


    // 获取所有value集合
    public Collection<V> values() {
        Collection<V> vals = values;
        if (vals == null) {
            vals = new AbstractCollection<V>() {
                public Iterator<V> iterator() {
                    return new Iterator<V>() {
                        // entrySet迭代计算
                        private Iterator<Entry<K,V>> i = entrySet().iterator();

                        public boolean hasNext() {
                            return i.hasNext();
                        }

                        public V next() {
                            return i.next().getValue();
                        }

                        public void remove() {
                            i.remove();
                        }
                    };
                }

                public int size() {
                    return AbstractMap.this.size();
                }

                public boolean isEmpty() {
                    return AbstractMap.this.isEmpty();
                }

                public void clear() {
                    AbstractMap.this.clear();
                }

                public boolean contains(Object v) {
                    return AbstractMap.this.containsValue(v);
                }
            };
            values = vals;
        }
        return vals;
    }

    // 所有键值对
    public abstract Set<Entry<K,V>> entrySet();


    // ========================= 比较和哈希操作 =============================

    // 比较
    public boolean equals(Object o) {
        // 引用
        if (o == this)
            return true;

        // 类型
        if (!(o instanceof Map))
            return false;
        Map<?,?> m = (Map<?,?>) o;
        // 大小
        if (m.size() != size())
            return false;

        try {
            // 迭代器遍历，挨个对比
            Iterator<Entry<K,V>> i = entrySet().iterator();
            while (i.hasNext()) {
                Entry<K,V> e = i.next();
                K key = e.getKey();
                V value = e.getValue();
                if (value == null) {
                    if (!(m.get(key)==null && m.containsKey(key)))
                        return false;
                } else {
                    if (!value.equals(m.get(key)))
                        return false;
                }
            }
        } catch (ClassCastException unused) {
            return false;
        } catch (NullPointerException unused) {
            return false;
        }

        return true;
    }

    // 哈希值
    public int hashCode() {
        int h = 0;
        Iterator<Entry<K,V>> i = entrySet().iterator();
        while (i.hasNext())
            // 迭代器遍历，元素挨个计算
            h += i.next().hashCode();
        return h;
    }

    // map输出
    public String toString() {
        Iterator<Entry<K,V>> i = entrySet().iterator();
        if (! i.hasNext())
            return "{}";

        StringBuilder sb = new StringBuilder();
        sb.append('{');
        for (;;) {
            Entry<K,V> e = i.next();
            K key = e.getKey();
            V value = e.getValue();
            sb.append(key   == this ? "(this Map)" : key);
            sb.append('=');
            sb.append(value == this ? "(this Map)" : value);
            if (! i.hasNext())
                return sb.append('}').toString();
            sb.append(',').append(' ');
        }
    }

    // 浅克隆
    protected Object clone() throws CloneNotSupportedException {
        AbstractMap<?,?> result = (AbstractMap<?,?>)super.clone();
        result.keySet = null;
        result.values = null;
        return result;
    }

    // 测试是否相等 SimpleEntry和SimpleImmutableEntry使用
    private static boolean eq(Object o1, Object o2) {
        return o1 == null ? o2 == null : o1.equals(o2);
    }


    // 简单Entry实现
    public static class SimpleEntry<K,V>
        implements Entry<K,V>, java.io.Serializable
    {
        private static final long serialVersionUID = -8499721149061103585L;

        // kv
        private final K key;
        private V value;

        // 构造函数
        public SimpleEntry(K key, V value) {
            this.key   = key;
            this.value = value;
        }

        public SimpleEntry(Entry<? extends K, ? extends V> entry) {
            this.key   = entry.getKey();
            this.value = entry.getValue();
        }

        // 获取k
        public K getKey() {
            return key;
        }

        // 获取v
        public V getValue() {
            return value;
        }

        // 更新value，返回旧的value
        public V setValue(V value) {
            V oldValue = this.value;
            this.value = value;
            return oldValue;
        }

        // 相等比较
        public boolean equals(Object o) {
            // 类型校验
            if (!(o instanceof Map.Entry))
                return false;
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            // 比较kv
            return eq(key, e.getKey()) && eq(value, e.getValue());
        }

        // 哈希值
        public int hashCode() {
            return (key   == null ? 0 :   key.hashCode()) ^
                   (value == null ? 0 : value.hashCode());
        }

        // 输出
        public String toString() {
            return key + "=" + value;
        }

    }

    // 不可变Entry实现
    public static class SimpleImmutableEntry<K,V>
        implements Entry<K,V>, java.io.Serializable
    {
        private static final long serialVersionUID = 7138329143949025153L;

        // kv : value也被final修饰
        private final K key;
        private final V value;

        public SimpleImmutableEntry(K key, V value) {
            this.key   = key;
            this.value = value;
        }

        public SimpleImmutableEntry(Entry<? extends K, ? extends V> entry) {
            this.key   = entry.getKey();
            this.value = entry.getValue();
        }

        // 获取key
        public K getKey() {
            return key;
        }

        // 获取v
        public V getValue() {
            return value;
        }

        // 不允许更新value
        public V setValue(V value) {
            throw new UnsupportedOperationException();
        }

        // 相等比较
        public boolean equals(Object o) {
            if (!(o instanceof Map.Entry))
                return false;
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            return eq(key, e.getKey()) && eq(value, e.getValue());
        }

        // 哈希值
        public int hashCode() {
            return (key   == null ? 0 :   key.hashCode()) ^
                   (value == null ? 0 : value.hashCode());
        }

        // 输出
        public String toString() {
            return key + "=" + value;
        }

    }

}
```

### 2.13 SortedMap接口

- 介绍

Map接口的扩展，`支持Key排序的Map`，并扩展了一些`根据Key大小切分范围`的方法。

- 源码

```java
package java.util;

// 支持排序的map
public interface SortedMap<K,V> extends Map<K,V> {

    // 返回用于key排序的比较器
    Comparator<? super K> comparator();

    // map切分，获取范围kv对 ==> k在fromKey和toKey之间
    SortedMap<K,V> subMap(K fromKey, K toKey);

    // map切分，获取范围kv对 ==> k小于等于toKey
    SortedMap<K,V> headMap(K toKey);

    // map切分，获取范围kv对 ==> k大于等于fromKey
    SortedMap<K,V> tailMap(K fromKey);
    
    // 最小的key
    K firstKey();

    // 最大的key
    K lastKey();

    // 所有的key集合
    Set<K> keySet();

    // 所有的value集合
    Collection<V> values();

    // 所有kv对集合
    Set<Map.Entry<K, V>> entrySet();
}
```

### 2.14 Queue接口

- 介绍

Queue接口是队列的顶级接口，定义了队列的统一行为。

- 源码

```java
package java.util;

/**
 * 队列接口
 */
public interface Queue<E> extends Collection<E> {

    // 队尾添加元素，失败时抛出异常
    boolean add(E e);

    // 队尾添加元素，失败时返回false
    boolean offer(E e);

    // 队头取出元素，失败时抛出异常
    E remove();

    // 队头取出元素，失败时返回null
    E poll();

    // 查看队头元素，失败时抛出异常
    E element();

    // 查看队头元素，失败时返回null
    E peek();
}
```

### 2.15 Deque接口

- 介绍

Deque接口是Queue接口的扩展，`双端队列`，支持从队列的两端进行操作，并且支持`栈模型`。

- 源码

```java
package java.util;

/**
 * 双端队列接口
 */
public interface Deque<E> extends Queue<E> {

    // 队头添加元素，失败抛出异常
    void addFirst(E e);

    // 队尾添加元素，失败抛出异常
    void addLast(E e);

    // 队头添加元素，失败返回false
    boolean offerFirst(E e);

    // 队尾添加元素，失败返回false
    boolean offerLast(E e);

    // 队头取出元素，失败抛出异常
    E removeFirst();

    // 队尾取出元素，失败抛出异常
    E removeLast();

    // 队头取出元素，失败返回null
    E pollFirst();

    // 队尾取出元素，失败返回null
    E pollLast();

    // 查看队头元素，失败抛出异常
    E getFirst();

    // 查看队尾元素，失败抛出异常
    E getLast();

    // 查看队头元素，失败返回null
    E peekFirst();

    // 查看队尾元素，失败返回null
    E peekLast();


    // 删除包含指定元素的第一个匹配项
    boolean removeFirstOccurrence(Object o);

    // 删除包含指定元素的最后一个匹配项
    boolean removeLastOccurrence(Object o);

    // =================== 队列方法 ============================================

    boolean add(E e);

    boolean offer(E e);

    E remove();

    E poll();

    E element();

    E peek();


    // ======================== 栈方法 =============================

    // 入栈
    void push(E e);

    // 出栈
    E pop();


    // ======================== 集合方法 ====================================

    boolean remove(Object o);

    boolean contains(Object o);

    public int size();

    // 正向返回迭代器
    Iterator<E> iterator();

    // 相反的顺序返回迭代器
    Iterator<E> descendingIterator();

}
```

### 2.16 BlockingQueue接口

- 介绍

基于Queue接口的扩展，`阻塞队列`，`支持对队列的操作进行超时等待，用于实现生产者-消费者模型`。

- 源码

```java
package java.util.concurrent;

import java.util.Collection;
import java.util.Queue;

/**
 * 阻塞队列接口，对队列接口的扩展，用于实现生产者-消费者模型
 */
public interface BlockingQueue<E> extends Queue<E> {

    // 往队列里添加元素，失败抛出异常
    boolean add(E e);

    // 往队列里添加元素，失败返回false
    boolean offer(E e);

    // 往队列里添加元素，一直等待，可被中断
    void put(E e) throws InterruptedException;

    // 往队列里添加元素，超时后返回false
    boolean offer(E e, long timeout, TimeUnit unit)
        throws InterruptedException;

    // 从队列里取出元素，一直等待，可被中断
    E take() throws InterruptedException;

    // 从队列里取出元素，超时后返回null
    E poll(long timeout, TimeUnit unit)
        throws InterruptedException;

    // 返回队列剩余容量
    int remainingCapacity();


    // 移除指定元素，失败返回false
    boolean remove(Object o);

    // 判断队列是否包含某个元素
    public boolean contains(Object o);

    // 将队列所有元素导出到参数集合里，返回导出的数量
    int drainTo(Collection<? super E> c);

    // 将队列所有元素导出到参数集合里，第二个参数指定要导出的数量，结果返回导出的数量
    int drainTo(Collection<? super E> c, int maxElements);
}
```

### 2.17 AbstractQueue抽象类

- 介绍

队列的抽象实现，基于基础操作，实现了一些复合操作的逻辑。

- 源码

```java
package java.util;


/**
 * 队列的抽象实现，一些基础操作由实现类实现，抽象类基于基础操作实现了一些复合操作的逻辑
 */
public abstract class AbstractQueue<E>
    extends AbstractCollection<E>
    implements Queue<E> {

    protected AbstractQueue() {
    }

    // 调用offer添加，offer子类实现
    public boolean add(E e) {
        if (offer(e))
            return true;
        else
            throw new IllegalStateException("Queue full");
    }

    // 调用poll删除，poll子类实现
    public E remove() {
        E x = poll();
        if (x != null)
            return x;
        else
            throw new NoSuchElementException();
    }

    
    // 调用peek实现，peek子类实现
    public E element() {
        E x = peek();
        if (x != null)
            return x;
        else
            throw new NoSuchElementException();
    }

    // 一直循环弹出
    public void clear() {
        while (poll() != null)
            ;
    }

    // 循环add
    public boolean addAll(Collection<? extends E> c) {
        if (c == null)
            throw new NullPointerException();
        if (c == this)
            throw new IllegalArgumentException();
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }

}
```

# 三：Java常用容器总结

| 容器                  | 底层结构                        | 应用场景             | 有序 | 重复 | NULL | 线程安全 | 备注 |
| --------------------- | ------------------------------- | -------------------- | ---- | ---- | ---- | -------- | ---- |
| ArrayList             | 数组                            | 列表                 | √    | √    | √    | ×        |      |
| LinkedList            | 双向链表                        | 列表、队列、栈       | √    | √    | √    | ×        |      |
| Vector                | 数组                            | 列表                 |      |      |      |          |      |
| Stack                 | 数组                            | 列表、栈             |      |      |      |          |      |
| CopyOnWriteArrayList  | 数组                            | 列表                 |      |      |      |          |      |
| HashSet               | 数组                            | 集合                 |      |      |      |          |      |
| LinkedHashSet         | 链表                            | 有序集合（访问顺序） |      |      |      |          |      |
| TreeSet               | 红黑树                          | 有序集合（支持排序） |      |      |      |          |      |
| ConcurrentSkipListSet | 跳表                            |                      |      |      |      |          |      |
| CopyOnWriteArraySet   | 数组                            |                      |      |      |      |          |      |
| HashMap               | 数组 + 链表 + 红黑树            | 哈希表               |      |      |      |          |      |
| HashTable             | 数组 + 链表                     |                      |      |      |      |          |      |
| LinkedHashMap         | 数组 + 链表 + 红黑树 + 双向链表 |                      |      |      |      |          |      |
| ConcurrentHashMap     | 哈希表 + 链表 + 红黑树          |                      |      |      |      |          |      |
| ConcurrentSkipListMap | 哈希表 + 链表 + 红黑树 + 跳表   |                      |      |      |      |          |      |
| ArrayBlockingQueue    | 数组 + 队列                     |                      |      |      |      |          |      |
| LinkedBlockingQueue   | 链表 + 队列                     |                      |      |      |      |          |      |
| LinkedBlockingDeque   | 链表 + 队列                     |                      |      |      |      |          |      |
| LinkedTransferQueue   |                                 |                      |      |      |      |          |      |
| PriorityBlockingQueue |                                 |                      |      |      |      |          |      |
| SynchronousQueue      |                                 |                      |      |      |      |          |      |



