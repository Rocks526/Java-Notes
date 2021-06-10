# 一：Excel工具包介绍

### 1.1 Excel介绍

Excel是一种常见的数据存储、统计的工具，在项目中经常会涉及到`将数据从数据库导出到excel`和`将数据从excel导入数据库`的需求。

Excel版本众多，常见的有两种：

- xls：2003版本后缀，最大支持65535行数据
- xlsx：2007版本后缀，最大支持104万行数据


### 1.2 Excel工具包介绍

Java针对Excel处理的工具包有很多，常用的有以下几种：

- Apache POI：apache提供的office处理工具包，包括excel、doc、ppt等的处理，支持sax和dom两种方式
- Easy POI：针对Apache POI的封装简化包，提供易于操作的API，提供对象和excel映射的能力
- Easy Excel：alibaba推出的excel处理包，基于Apache POI封装而成，流式处理数据，并且删除style相关属性，减少内存消耗

# 二：Apache POI

### 2.1 POI提供的类

- HSSF：提供读写Microsoft Excel `xls`格式文档的功能。
- XSSF：提供读写Microsoft Excel OOXML `xlsx`格式文档的功能。
- HWPF：提供读写Microsoft Word `doc97`格式文档的功能。
- XWPF：提供读写Microsoft Word `doc2003`格式文档的功能。

- HSLF：提供读写Microsoft `PowerPoint`格式文档的功能。

- HDGF：提供读Microsoft `Visio`格式文档的功能。
- HPBF：提供读Microsoft `Publisher`格式文档的功能。
- HSMF：提供读Microsoft `Outlook`格式文档的功能。

> 以下测试都需要进行初始化数据：

```java
    private List<User> users;

    private final int NUMBER = 50000;

    @BeforeEach
    public void initUsers(){
        users = new ArrayList<>(NUMBER);
        for (int i=0;i<NUMBER;i++){
            users.add(User.builder().id((long)i).userName(RandomStringUtils.randomAlphanumeric(8,12))
                    .password(RandomStringUtils.randomAlphanumeric(8,12))
                    .nickName(StringGenerator.getRandomString(3,5))
                    .tel(RandomStringUtils.randomNumeric(12))
                    .desc(StringGenerator.getRandomString(15,30))
                    .birthday(LocalDate.now().withMonth(RandomUtils.nextInt(1,12)).withDayOfMonth(RandomUtils.nextInt(1,29)))
                    .createTime(LocalDateTime.now())
                    .updateTime(LocalDateTime.now()).build());
        }
    }
```

```java
package com.rocks;

import org.apache.commons.lang3.RandomUtils;
import org.apache.commons.lang3.StringUtils;
import java.io.UnsupportedEncodingException;
import java.util.Collection;
import java.util.Random;

/**
 * 字符串生成器
 * @author lizhaoxuan
 */
public class StringGenerator {

    /**
     * 将list的元素全部连接成字符串
     * @return
     */
    public static String buildStringFromStrList(Collection<String> list){
        return StringUtils.join(list);
    }

    /**
     * 将list的元素全部连接成字符串
     * @return
     */
    public static String buildStringFromStrList(Collection<String> list,String separator){
        return StringUtils.join(list,separator);
    }

    /**
     * 生成随机汉字
     * @return
     */
    public static String getRandomString(Integer size) {
        StringBuilder result = new StringBuilder();
        for (int i=0;i<size;i++){
            String str = "";
            int highPos;
            int lowPos;
            Random random = new Random();
            highPos = (176 + Math.abs(random.nextInt(39)));
            lowPos = (161 + Math.abs(random.nextInt(93)));
            byte[] b = new byte[2];
            b[0] = (Integer.valueOf(highPos)).byteValue();
            b[1] = (Integer.valueOf(lowPos)).byteValue();
            try {
                str = new String(b, "GBK");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
            result.append(str);
        }
        return result.toString();
    }

    /**
     * 生成随机汉字
     * @return
     */
    public static String getRandomString(Integer min,Integer max) {
        StringBuilder result = new StringBuilder();
        int size = RandomUtils.nextInt(min,max);
        for (int i=0;i<size;i++){
            String str = "";
            int highPos;
            int lowPos;
            Random random = new Random();
            highPos = (176 + Math.abs(random.nextInt(39)));
            lowPos = (161 + Math.abs(random.nextInt(93)));
            byte[] b = new byte[2];
            b[0] = (Integer.valueOf(highPos)).byteValue();
            b[1] = (Integer.valueOf(lowPos)).byteValue();
            try {
                str = new String(b, "GBK");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
            result.append(str);
        }
        return result.toString();
    }

}
```

> Maven依赖，注意poi依赖要求3.16，部分版本不存在SAXHelper类

```xml
    <dependencies>
        <!--      Apache POI    -->
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi</artifactId>
            <version>3.16</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml-schemas</artifactId>
            <version>3.16</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>3.16</version>
        </dependency>

        <!-- Junit -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.5.2</version>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
    </dependencies>
```

### 2.2 XLS文件的写入操作

```java
    /**
     * 生成xls文件      xls文件只支持65535行 超过会抛出异常：IllegalArgumentException: Invalid row number (65536) outside allowable range (0..65535)
     * 面向接口编程 Workbook、Sheet都是接口，xls实现类为HSSFWorkbook xlsx实现类为XSSF sax模式实现类为SXSSF
     */
    @Test
    @SneakyThrows
    public void HSSFWrite(){
        long startTime = System.currentTimeMillis();
        // 文件位置
        String xlsFilePath = startTime + ".xls";
        // 创建工作簿
        Workbook workbook = new HSSFWorkbook();
        // 创建工作表
        Sheet sheet = workbook.createSheet("用户信息统计");
        // 写入数据
        int currentRowNumber = 0;
        for (User user : users) {
            Row row = sheet.createRow(currentRowNumber);
            row.createCell(0).setCellValue(user.getId());
            row.createCell(1).setCellValue(user.getUserName());
            row.createCell(2).setCellValue(user.getPassword());
            row.createCell(3).setCellValue(user.getNickName());
            row.createCell(4).setCellValue(user.getTel());
            row.createCell(5).setCellValue(user.getDesc());
            row.createCell(6).setCellValue(user.getBirthday().toString());
            row.createCell(7).setCellValue(user.getCreateTime().toString());
            row.createCell(8).setCellValue(user.getUpdateTime().toString());
            currentRowNumber ++;
        }
        // 保存文件
        workbook.write(new FileOutputStream(xlsFilePath));
        long endTime = System.currentTimeMillis();
        System.out.printf("build user excel with row:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",sheet.getLastRowNum(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(users));
        // 5W万行 9列 dom方式写入xls文件 xls文件12m 耗时和内存占用如下:
        // build user excel with row:49999, cost time: 2149 ms, cost memory: 93.4 MB, user memory:27 MB
        // build user excel with row:49999, cost time: 3208 ms, cost memory: 93.4 MB, user memory:27 MB
        // build user excel with row:49999, cost time: 2096 ms, cost memory: 93.4 MB, user memory:27 MB
        // build user excel with row:49999, cost time: 2316 ms, cost memory: 93.4 MB, user memory:27 MB
    }
```

### 2.3 XLS文件的读取操作

```java
    /**
     * dom解析xls文件
     */
    @Test
    @SneakyThrows
    public void HSSFRead(){
        ArrayList<User> userList = new ArrayList<>(NUMBER);
        long startTime = System.currentTimeMillis();
        // 读取的文件
        String xlsFilePath = "1623139603515.xls";
        // 创建工作簿
        // Workbook workbook = new HSSFWorkbook(new FileInputStream(xlsFilePath));
        // 自动根据文件名创建对应的WorkBook
        Workbook workbook = WorkbookFactory.create(new FileInputStream(xlsFilePath));
        // 获取工作表
        Sheet sheet = workbook.getSheetAt(0);
        // 获取行
        for (int i=0;i<sheet.getLastRowNum();i++){
            Row row = sheet.getRow(i);
            // 获取单元格 组装对象
            userList.add(User.builder().id((long) row.getCell(0).getNumericCellValue())
                    .userName(row.getCell(1).getStringCellValue())
                    .password(row.getCell(2).getStringCellValue())
                    .nickName(row.getCell(3).getStringCellValue())
                    .tel(row.getCell(4).getStringCellValue())
                    .desc(row.getCell(5).getStringCellValue())
                    .birthday(LocalDate.parse(row.getCell(6).getStringCellValue()))
                    .createTime(LocalDateTime.parse(row.getCell(7).getStringCellValue()))
                    .updateTime(LocalDateTime.parse(row.getCell(8).getStringCellValue()))
                    .build());
        }
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",userList.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(userList));
        // 5W万行 9列 dom方式读取xls文件 xls文件12m 耗时和内存占用如下:
        // parse user from excel with size:49999, cost time: 1905 ms, cost memory: 106.8 MB , user memory:27 MB
        // parse user from excel with size:49999, cost time: 2055 ms, cost memory: 106.8 MB , user memory:27 MB
        // parse user from excel with size:49999, cost time: 1901 ms, cost memory: 106.8 MB , user memory:27 MB
    }
```

### 2.4 XLS文件的sax解析读取操作

```java
    /**
     * xls的sax解析测试
     */
    @SneakyThrows
    @Test
    public void HSSFReadWithSax(){
        long startTime = System.currentTimeMillis();
        // 读取的文件
        String xlsFilePath = "1623139603515.xls";
        // 解析文档
        HSSFDemoListener listener = new HSSFDemoListener(new FileInputStream(xlsFilePath), 10);
        listener.process();
        // 获取解析后的数据
        List<ArrayList<String>> result = listener.getResult();
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",result.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(listener), RamUsageEstimator.humanSizeOf(result));
        // 5W万行 9列 sax方式读取xls文件 xls文件12m 耗时和内存占用如下:  (listener监听器的内存占用包括了用户数据)
        // parse user from excel with size:50000, cost time: 1475 ms, cost memory: 57.7 MB , user memory:22.2 MB
        // parse user from excel with size:50000, cost time: 1712 ms, cost memory: 57.7 MB , user memory:22.2 MB
        // parse user from excel with size:50000, cost time: 1690 ms, cost memory: 57.7 MB , user memory:22.2 MB
    }
```

```java
package com.rocks.poi;

import org.apache.poi.hssf.eventusermodel.*;
import org.apache.poi.hssf.eventusermodel.dummyrecord.LastCellOfRowDummyRecord;
import org.apache.poi.hssf.eventusermodel.dummyrecord.MissingCellDummyRecord;
import org.apache.poi.hssf.model.HSSFFormulaParser;
import org.apache.poi.hssf.record.*;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.poifs.filesystem.POIFSFileSystem;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.io.PrintStream;
import java.util.ArrayList;
import java.util.List;

/**
 * @author lizhaoxuan
 * @Description 示例代码 完整代码参考文档：https://www.cnblogs.com/lzjdm/p/10407846.html
 * @date 2021/06/08
 */
public class HSSFDemoListener implements HSSFListener {

    private int minColumns;
    private POIFSFileSystem fs;
    private PrintStream output;


    public List<ArrayList<String>> getData() {
        return data;
    }

    // 当前行
    private int curRow = 0;

    // 存储行记录的容器
    private List<String> rowlist = new ArrayList<String>();

    //样式记录容器
    private List<String> rowType = new ArrayList<String>();
    @SuppressWarnings("unchecked")
//    private ArrayList boundSheetRecords = new ArrayList();

    private String sheetName;
    //Excel数据
    private List<ArrayList<String>> data = new ArrayList<ArrayList<String>>();

    private int lastRowNumber;
    private int lastColumnNumber;

    private int currentSheetChildPage = 1;
    /** Should we output the formula, or the value it has? */
    private boolean outputFormulaValues = true;

    /** For parsing Formulas */
    private EventWorkbookBuilder.SheetRecordCollectingListener workbookBuildingListener;
    private HSSFWorkbook stubWorkbook;

    // Records we pick up as we process
    private SSTRecord sstRecord;
    private FormatTrackingHSSFListener formatListener;

    /** So we known which sheet we're on */
    private int sheetIndex = -1;
    private BoundSheetRecord[] orderedBSRs;
    private List boundSheetRecords = new ArrayList<>();

    // For handling formulas with string results
    private int nextRow;
    private int nextColumn;
    private boolean outputNextStringRecord;

    /**
     * Creates a new XLS -> CSV converter
     * @param fs The POIFSFileSystem to process
     * @param output The PrintStream to output the CSV to
     * @param minColumns The minimum number of columns to output, or -1 for no minimum
     */
    public HSSFDemoListener(POIFSFileSystem fs, PrintStream output, int minColumns) {
        this.fs = fs;
        this.output = output;
        this.minColumns = minColumns;
    }

    /**
     * Creates a new XLS -> CSV converter
     * @param filename The file to process
     * @param minColumns The minimum number of columns to output, or -1 for no minimum
     */
    public HSSFDemoListener(InputStream is, int minColumns) throws IOException, FileNotFoundException {
        this(
                new POIFSFileSystem(is),
                System.out, minColumns
        );
    }

    /**
     * Initiates the processing of the XLS file to CSV
     */
    public void process() throws IOException {
        MissingRecordAwareHSSFListener listener = new MissingRecordAwareHSSFListener(this);
        formatListener = new FormatTrackingHSSFListener(listener);

        HSSFEventFactory factory = new HSSFEventFactory();
        HSSFRequest request = new HSSFRequest();

        if(outputFormulaValues) {
            request.addListenerForAllRecords(formatListener);
        } else {
            workbookBuildingListener = new EventWorkbookBuilder.SheetRecordCollectingListener(formatListener);
            request.addListenerForAllRecords(workbookBuildingListener);
        }

        factory.processWorkbookEvents(request, fs);
    }

    /**
     * Main HSSFListener method, processes events, and outputs the
     *  CSV as the file is processed.
     */
    @Override
    public void processRecord(Record record) {
        int thisRow = -1;
        int thisColumn = -1;
        String thisStr = null;
        String value = null;


        switch (record.getSid()) {
            //---------add start---------
            case FontRecord.sid://字体记录
                /*FontRecord font = (FontRecord) record;

                short boldWeight = font.getBoldWeight();
                short fontHeight = font.getFontHeight();
                short colorPaletteIndex = font.getColorPaletteIndex();
                cellStyle = "style='";index++;
                cellStyle += "font-weight:" + boldWeight + ";"; //
                cellStyle += "font-size: " + fontHeight / 2 + "%;"; //
    */
                break;
            case FormatRecord.sid://单元格样式记录
                /*FormatRecord format = (FormatRecord) record;*/
                break;
            case ExtendedFormatRecord.sid://扩展单元格样式记录
                /*ExtendedFormatRecord extendedFormat = (ExtendedFormatRecord) record;
                short borderTop = extendedFormat.getBorderTop();
                short borderRight = extendedFormat.getBorderRight();
                short borderBottom = extendedFormat.getBorderBottom();
                short leftBorderPaletteIdx = extendedFormat.getLeftBorderPaletteIdx();

                short alignment = extendedFormat.getAlignment();
                short verticalAlignment = extendedFormat.getVerticalAlignment();

                index++;
                alignStyle = "align='" + convertAlignToHtml(alignment) + "' ";
                alignStyle += "valign='" + convertVerticalAlignToHtml(verticalAlignment) + "' ";//

                StringBuffer sb = new StringBuffer();
                sb.append(getBorderStyle(0, borderTop));
                sb.append(getBorderStyle(1, borderRight));
                sb.append(getBorderStyle(2, borderBottom));
                sb.append(getBorderStyle(3, leftBorderPaletteIdx));
                cellStyle += sb.toString();*/
                break;
            //---------add end---------
            case BoundSheetRecord.sid://遍历所有boundSheetRecord,每个sheet对应一个boundSheetRecord
                boundSheetRecords.add(record);
                break;
            case BOFRecord.sid://type=5为workbook的开始
                BOFRecord br = (BOFRecord) record;
                if (br.getType() == BOFRecord.TYPE_WORKSHEET) {
                    // 如果有需要，则建立子工作薄
                    if (workbookBuildingListener != null && stubWorkbook == null) {
                        stubWorkbook = workbookBuildingListener.getStubHSSFWorkbook();
                    }

                    sheetIndex++;
                    if (orderedBSRs == null) {
                        orderedBSRs = BoundSheetRecord.orderByBofPosition(boundSheetRecords);
                    }
                    sheetName = orderedBSRs[sheetIndex].getSheetname();
                    /*if(currentSheetIndex!=-1 && sheetIndex > currentSheetIndex){
                        if(data.size()>0){
                            String writeSheetName = orderedBSRs[sheetIndex-1].getSheetname();
                            String sheetDir = dirPath + "/" + writeSheetName;
                            String htmlPath = sheetDir + "/" + fileName.substring(0, fileName.lastIndexOf(".")) + "_"
                                    + writeSheetName + "_" + currentSheetChildPage + ".html";
                            writeHtml(writeSheetName, htmlPath);
                            data.clear();
                            currentSheetChildPage=1;
                        }
                    }
                    currentSheetIndex = sheetIndex;*/
                }
                break;

            case EOFRecord.sid:
                /*if(sheetIndex!=-1){
                    if(data.size()>0){
                        String sheetDir = dirPath + "/_a"+ (sheetIndex+1) + "-" + sheetName;
                        String htmlPath = sheetDir + "/" + fileName.substring(0, fileName.lastIndexOf(".")) + "_"
                                + sheetName + "_" + currentSheetChildPage + ".html";
                        boolean writeHtml = writeHtml(orderedBSRs[sheetIndex].getSheetname(), htmlPath);
                        data.clear();
                        if(writeHtml) currentSheetChildPage++;
                    }
                }*/
                currentSheetChildPage = 1;
                break;
            case SSTRecord.sid://存储了xls所有文本单元格值，通过索引获取
                sstRecord = (SSTRecord) record;
                break;

            case BlankRecord.sid:
                BlankRecord brec = (BlankRecord) record;
                thisRow = brec.getRow();
                thisColumn = brec.getColumn();
                thisStr = "";
                rowlist.add(thisColumn, thisStr);
                //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                break;
            case BoolErrRecord.sid: // 单元格为布尔类型
                BoolErrRecord berec = (BoolErrRecord) record;
                thisRow = berec.getRow();
                thisColumn = berec.getColumn();
                thisStr = berec.getBooleanValue() + "";
                rowlist.add(thisColumn, thisStr);
                //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                break;

            case FormulaRecord.sid: // 单元格为公式类型
                FormulaRecord frec = (FormulaRecord) record;
                thisRow = frec.getRow();
                thisColumn = frec.getColumn();
                if (outputFormulaValues) {
                    if (Double.isNaN(frec.getValue())) {
                        // Formula result is a string
                        // This is stored in the next record
                        outputNextStringRecord = true;
                        nextRow = frec.getRow();
                        nextColumn = frec.getColumn();
                    } else {
                        thisStr = formatListener.formatNumberDateCell(frec);
                    }
                } else {
                    thisStr = '"' + HSSFFormulaParser.toFormulaString(stubWorkbook, frec.getParsedExpression()) + '"';
                }
                rowlist.add(thisColumn, thisStr);
                //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                break;
            case StringRecord.sid:// 单元格中公式的字符串
                if (outputNextStringRecord) {
                    // String for formula
                    StringRecord srec = (StringRecord) record;
                    thisStr = srec.getString();
                    thisRow = nextRow;
                    thisColumn = nextColumn;
                    outputNextStringRecord = false;
                }
                break;
            case LabelRecord.sid:
                LabelRecord lrec = (LabelRecord) record;
                curRow = thisRow = lrec.getRow();
                thisColumn = lrec.getColumn();
                value = lrec.getValue().trim();
                value = value.equals("") ? " " : value;
                this.rowlist.add(thisColumn, value);
                //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                break;
            case LabelSSTRecord.sid: // 单元格为字符串类型
                LabelSSTRecord lsrec = (LabelSSTRecord) record;
                curRow = thisRow = lsrec.getRow();
                thisColumn = lsrec.getColumn();
                if (sstRecord == null) {
                    rowlist.add(thisColumn, " ");
                    //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                } else {
                    value = sstRecord.getString(lsrec.getSSTIndex()).toString().trim();
                    value = value.equals("") ? " " : value;
                    rowlist.add(thisColumn, value);
                    //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                }
                break;
            case NumberRecord.sid: // 单元格为数字类型
                NumberRecord numrec = (NumberRecord) record;
                curRow = thisRow = numrec.getRow();
                thisColumn = numrec.getColumn();
                value = formatListener.formatNumberDateCell(numrec).trim();
                value = value.equals("") ? " " : value;
                // 向容器加入列值
                rowlist.add(thisColumn, value);
                //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
                break;
            default:
                break;
        }

        // 遇到新行的操作
        if (thisRow != -1 && thisRow != lastRowNumber) {
            lastColumnNumber = -1;
        }

        // 空值的操作
        if (record instanceof MissingCellDummyRecord) {
            MissingCellDummyRecord mc = (MissingCellDummyRecord) record;
            curRow = thisRow = mc.getRow();
            thisColumn = mc.getColumn();
            rowlist.add(thisColumn, " ");
            //rowType.add(thisColumn,cellStyle + "' " + alignStyle);
        }

        // 更新行和列的值
        if (thisRow > -1){
            lastRowNumber = thisRow;
        }
        if (thisColumn > -1){
            lastColumnNumber = thisColumn;
        }

        // 行结束时的操作
        if (record instanceof LastCellOfRowDummyRecord) {
            if (minColumns > 0) {
                // 列值重新置空
                if (lastColumnNumber == -1) {
                    lastColumnNumber = 0;
                }
            }
            lastColumnNumber = -1;

            // 每行结束时， 调用getRows() 方法(打印内容)
            //rowReader.getRows(sheetIndex, curRow, rowlist);

            ArrayList<String> list = new ArrayList<>();
            list.addAll(rowlist);
            data.add(list);
               /* if(data.size()==2000){
                    String sheetDir = dirPath + "/_a"+ (sheetIndex+1)+ "-" + sheetName;
                    String htmlPath = sheetDir + "/" + fileName.substring(0, fileName.lastIndexOf(".")) + "_"
                            + sheetName + "_" + currentSheetChildPage + ".html";
                    boolean writeHtml = writeHtml(orderedBSRs[sheetIndex].getSheetname(), htmlPath);
                    data.clear();
                    if(writeHtml) currentSheetChildPage++;
                }*/
                /*List<String> styleList = new ArrayList<>();
                styleList.addAll(rowType);
                styleData.add(styleList);
    */
            // 清空容器
            rowlist.clear();
        }
    }

    public List<ArrayList<String>> getResult(){
        return data;
    }

}
```

### 2.5 XLSX文件的写入操作

```java
 /**
     * xlsx写入测试
     */
    @SneakyThrows
    @Test
    public void XSSFWrite(){
        long startTime = System.currentTimeMillis();
        // 文件位置
        String xlsxFilePath = startTime + ".xlsx";
        // 创建工作簿
        Workbook workbook = new XSSFWorkbook();
        // 创建工作表
        Sheet sheet = workbook.createSheet("用户信息统计");
        // 写入数据
        int currentRowNumber = 0;
        for (User user : users) {
            Row row = sheet.createRow(currentRowNumber);
            row.createCell(0).setCellValue(user.getId());
            row.createCell(1).setCellValue(user.getUserName());
            row.createCell(2).setCellValue(user.getPassword());
            row.createCell(3).setCellValue(user.getNickName());
            row.createCell(4).setCellValue(user.getTel());
            row.createCell(5).setCellValue(user.getDesc());
            row.createCell(6).setCellValue(user.getBirthday().toString());
            row.createCell(7).setCellValue(user.getCreateTime().toString());
            row.createCell(8).setCellValue(user.getUpdateTime().toString());
            currentRowNumber ++;
        }
        // 保存文件
        workbook.write(new FileOutputStream(xlsxFilePath));
        long endTime = System.currentTimeMillis();
        System.out.printf("build user excel with row:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",sheet.getLastRowNum(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(users));
        // 5W万行 9列 dom方式写入xlsx文件 xlsx文件6m 耗时和内存占用如下:
        // build user excel with row:49999, cost time: 20602 ms, cost memory: 445.6 MB , user memory:27 MB
        // build user excel with row:49999, cost time: 19476 ms, cost memory: 445.7 MB , user memory:27 MB
        // build user excel with row:49999, cost time: 23243 ms, cost memory: 445.6 MB , user memory:27 MB
    }
```

### 2.6 XLSX文件的读取操作

```java
 /**
     * xlsx读取
     */
    @SneakyThrows
    @Test
    public void XSSFRead(){
        ArrayList<User> userList = new ArrayList<>(NUMBER);
        long startTime = System.currentTimeMillis();
        // 读取的文件
        String xlsxFilePath = "1623139668133.xlsx";
        // 创建工作簿
        // Workbook workbook = new XSSFWorkbook(new FileInputStream(xlsxFilePath));
        // 自动根据文件名创建对应的WorkBook
        Workbook workbook = WorkbookFactory.create(new FileInputStream(xlsxFilePath));
        // 获取工作表
        Sheet sheet = workbook.getSheetAt(0);
        // 获取行
        for (int i=0;i<sheet.getLastRowNum();i++){
            Row row = sheet.getRow(i);
            // 获取单元格 组装对象
            userList.add(User.builder().id((long) row.getCell(0).getNumericCellValue())
                    .userName(row.getCell(1).getStringCellValue())
                    .password(row.getCell(2).getStringCellValue())
                    .nickName(row.getCell(3).getStringCellValue())
                    .tel(row.getCell(4).getStringCellValue())
                    .desc(row.getCell(5).getStringCellValue())
                    .birthday(LocalDate.parse(row.getCell(6).getStringCellValue()))
                    .createTime(LocalDateTime.parse(row.getCell(7).getStringCellValue()))
                    .updateTime(LocalDateTime.parse(row.getCell(8).getStringCellValue()))
                    .build());
        }
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",userList.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(userList));
        // 5W万行 9列 dom方式读取xlsx文件 xls文件6m 耗时和内存占用如下:
        // parse user from excel with size:49999, cost time: 15798 ms, cost memory: 481.5 MB , user memory:27 MB
        // parse user from excel with size:49999, cost time: 12009 ms, cost memory: 481.5 MB , user memory:27 MB
        // parse user from excel with size:49999, cost time: 15328 ms, cost memory: 481.5 MB , user memory:27 MB
    }
```

### 2.7 XLSX文件的sax解析读取操作

```java
 /**
     * xlsx的sax解析测试
     */
    @SneakyThrows
    @Test
    public void XSSFReadWithSax(){
        long startTime = System.currentTimeMillis();
        // 读取的文件
        String xlsxFilePath = "1623139668133.xlsx";
        // 解析文档
        XSSFDemoListener listener = new XSSFDemoListener(OPCPackage.open(xlsxFilePath, PackageAccess.READ), 10);
        listener.process();
        // 获取解析后的数据
        List<ArrayList<String>> result = listener.getResult();
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",result.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(listener), RamUsageEstimator.humanSizeOf(result));
        // 5W万行 9列 sax方式读取xlsx文件 xls文件6m 耗时和内存占用如下:  (listener监听器的内存占用包括了用户数据)
        // parse user from excel with size:50000, cost time: 24976 ms, cost memory: 41.2 MB , user memory:41.2 MB
        // parse user from excel with size:50000, cost time: 18220 ms, cost memory: 23.3 MB , user memory:23.3 MB
        // parse user from excel with size:50000, cost time: 23682 ms, cost memory: 23.3 MB , user memory:23.3 MB
    }
```

# 三：Easy Poi

### 3.1 EasyPoi介绍

easypoi功能如同名字easy，主打的功能就是容易，让一个没见接触过poi的人员就可以方便的写出Excel导出，Excel模板导出，Excel导入，Word模板导出等。通过简单的注解和模板语言(熟悉的表达式语法)，完成以前复杂的写法。

- 基于注解的导入导出,修改注解就可以修改Excel
- 支持常用的样式自定义
- 基于map可以灵活定义的表头字段
- 支持一对多的导出,导入
- 支持模板的导出,一些常见的标签,自定义标签
- 支持HTML/Excel转换
- 支持word的导出,支持图片,Excel

> Excel自适应xls和xlsx两种格式,word只支持docx模式
>
> 官网：http://easypoi.mydoc.io

### 3.2 Maven依赖

```xml
        <dependency>
            <groupId>org.jeecg</groupId>
            <artifactId>easypoi-base</artifactId>
            <version>2.1.3</version>
        </dependency>
        <dependency>
            <groupId>org.jeecg</groupId>
            <artifactId>easypoi-web</artifactId>
            <version>2.1.3</version>
        </dependency>
        <dependency>
            <groupId>org.jeecg</groupId>
            <artifactId>easypoi-annotation</artifactId>
            <version>2.1.3</version>
        </dependency>
```

### 3.3 Excel注解方式

- @Excel ：作用到filed上面,是对Excel一列的一个描述
- @ExcelCollection：表示一个集合,主要针对一对多的导出
- @ExcelEntity：表示一个继续深入导出的实体,但他没有太多的实际意义,只是告诉系统这个对象里面同样有导出的字段
- @ExcelIgnore：和名字一样表示这个字段被忽略跳过这个导出
- @ExcelTarget：这个是作用于最外层的对象,描述这个对象的id,以便支持一个对象可以针对不同导出做出不同处理

### 3.4 实体准备

```java
package com.rocks;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;
import org.jeecgframework.poi.excel.annotation.Excel;
import java.time.LocalDate;
import java.time.LocalDateTime;

/**
 *  测试实体
 *  @author: lizhaoxuan
 *  @Date: 2021/5/20
 */
@Data
@AllArgsConstructor
@Accessors(chain = true)
@Builder
@NoArgsConstructor
public class User {

    /**
     * 用户ID
     */
    @Excel(name = "编号", orderNum = "0")
    private Long id;

    /**
     * 用户名
     */
    @Excel(name = "用户名", orderNum = "1")
    private String userName;

    /**
     * 密码
     */
    @Excel(name = "密码", orderNum = "2")
    private String password;

    /**
     * 昵称
     */
    @Excel(name = "昵称", orderNum = "3")
    private String nickName;

    /**
     * 手机号
     */
    @Excel(name = "手机号", orderNum = "4")
    private String tel;

    /**
     * 描述
     */
    @Excel(name = "描述", orderNum = "5")
    private String desc;

    /**
     * 生日
     */
    @Excel(name = "生日", orderNum = "6", format = "yyyy-MM-dd HH:mm:ss")
    private LocalDate birthday;

    /**
     * 创建时间
     */
    @Excel(name = "创建时间", orderNum = "7", format = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createTime;

    /**
     * 更新时间
     */
    @Excel(name = "更新时间", orderNum = "8", format = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime updateTime;

}
```

```java
package com.rocks.easyPoi;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;
import org.jeecgframework.poi.excel.annotation.Excel;

import java.time.LocalDate;
import java.time.LocalDateTime;

/**
 * @author lizhaoxuan
 * @Description 将User的LocalDate属性替换为string
 * @date 2021/06/10
 */
@Data
@AllArgsConstructor
@Accessors(chain = true)
@Builder
@NoArgsConstructor
public class EasyPoiUser {


    /**
     * 用户ID
     */
    @Excel(name = "编号", orderNum = "0")
    private Long id;

    /**
     * 用户名
     */
    @Excel(name = "用户名", orderNum = "1")
    private String userName;

    /**
     * 密码
     */
    @Excel(name = "密码", orderNum = "2")
    private String password;

    /**
     * 昵称
     */
    @Excel(name = "昵称", orderNum = "3")
    private String nickName;

    /**
     * 手机号
     */
    @Excel(name = "手机号", orderNum = "4")
    private String tel;

    /**
     * 描述
     */
    @Excel(name = "描述", orderNum = "5")
    private String desc;

    /**
     * 生日
     */
    @Excel(name = "生日", orderNum = "6", format = "yyyy-MM-dd HH:mm:ss")
    private String birthday;

    /**
     * 创建时间
     */
    @Excel(name = "创建时间", orderNum = "7", format = "yyyy-MM-dd HH:mm:ss")
    private String createTime;

    /**
     * 更新时间
     */
    @Excel(name = "更新时间", orderNum = "8", format = "yyyy-MM-dd HH:mm:ss")
    private String updateTime;

    /**
     * 性别
     */
    @Excel(name = "性别", replace = {"男_1","女_0"})
    private Integer sex;

}
```

```java
    private List<User> users;

    private final int NUMBER = 50000;

    @BeforeEach
    public void initUsers(){
        users = new ArrayList<>(NUMBER);
        for (int i=0;i<NUMBER;i++){
            users.add(User.builder().id((long)i).userName(RandomStringUtils.randomAlphanumeric(8,12))
                    .password(RandomStringUtils.randomAlphanumeric(8,12))
                    .nickName(StringGenerator.getRandomString(3,5))
                    .tel(RandomStringUtils.randomNumeric(12))
                    .desc(StringGenerator.getRandomString(15,30))
                    .birthday(LocalDate.now().withMonth(RandomUtils.nextInt(1,12)).withDayOfMonth(RandomUtils.nextInt(1,29)))
                    .createTime(LocalDateTime.now())
                    .updateTime(LocalDateTime.now()).build());
        }
    }
```

### 3.5 示例代码

```java
 /**
     * 导出xls测试
     */
    @Test
    @SneakyThrows
    public void exportXlsTest(){
        // xls文件名
        long startTime = System.currentTimeMillis();
        String xlsFilePath = startTime + ".xls";
        // 格式信息
        ExportParams exportParams = new ExportParams("", "人员统计信息", ExcelType.HSSF);
        exportParams.setCreateHeadRows(false);
        Workbook workbook = ExcelExportUtil.exportExcel(exportParams,User.class, users);
        workbook.write(new FileOutputStream(xlsFilePath));
        long endTime = System.currentTimeMillis();
        System.out.printf("build user excel with row:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",users.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(users));
        // build user excel with row:0, cost time: 4118 ms, cost memory: 100.9 MB , user memory:195.4 KB
        // build user excel with row:0, cost time: 4299 ms, cost memory: 100.9 MB , user memory:195.4 KB
        // build user excel with row:0, cost time: 3580 ms, cost memory: 100.9 MB , user memory:195.4 KB
        // 注意: 耗时和内存消耗 都比apache poi稍多 导出完成后会自动清理掉导出list的数据
    }


    /**
     * 导出xlsx测试
     */
    @Test
    @SneakyThrows
    public void exportXlsxTest(){
        // xls文件名
        long startTime = System.currentTimeMillis();
        String xlsxFilePath = startTime + ".xlsx";
        // 格式信息
        ExportParams exportParams = new ExportParams("", "人员统计信息", ExcelType.XSSF);
        exportParams.setCreateHeadRows(false);
        Workbook workbook = ExcelExportUtil.exportExcel(exportParams,User.class, users);
        workbook.write(new FileOutputStream(xlsxFilePath));
        long endTime = System.currentTimeMillis();
        System.out.printf("build user excel with row:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",users.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(workbook), RamUsageEstimator.humanSizeOf(users));
        // build user excel with row:0, cost time: 6884 ms, cost memory: 3 MB , user memory:195.4 KB
        // build user excel with row:0, cost time: 6726 ms, cost memory: 3 MB , user memory:195.4 KB
        // build user excel with row:0, cost time: 7057 ms, cost memory: 3 MB , user memory:195.4 KB
        // 注意: 导出xlsx文件时 内存消耗和时间都比apache poi少很多
    }


    /**
     * xls导入测试
     * easyPoi导入时 解析excel后会生成对象list 底层通过反射创建对象 要求对象必须存在无参构造方法 对象的引用类型属性也必须存在空参构造方法
     * LocalDate系列通过静态方法of和now创建实例 无空参构造 会出现创建对象异常
     */
    @Test
    @SneakyThrows
    public void importXlsTest(){
        long startTime = System.currentTimeMillis();
        // xls文件名
        String xlsFilePath = "1623210450434.xls";
        // 格式信息
        ImportParams importParams = new ImportParams();
        importParams.setNeedSave(false);
        importParams.setHeadRows(1);
        List<EasyPoiUser> userList = ExcelImportUtil.importExcel(new FileInputStream(xlsFilePath), EasyPoiUser.class, importParams);
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",userList.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(importParams), RamUsageEstimator.humanSizeOf(userList));
        // parse user from excel with size:49999, cost time: 3158 ms, cost memory: 136 bytes , user memory:2.5 MB
        // parse user from excel with size:49999, cost time: 2875 ms, cost memory: 136 bytes , user memory:2.5 MB
        // parse user from excel with size:49999, cost time: 2712 ms, cost memory: 136 bytes , user memory:2.5 MB
    }


    /**
     * xlsx导入测试
     * easyPoi导入时 解析excel后会生成对象list 底层通过反射创建对象 要求对象必须存在无参构造方法 对象的引用类型属性也必须存在空参构造方法
     * LocalDate系列通过静态方法of和now创建实例 无空参构造 会出现创建对象异常
     */
    @Test
    @SneakyThrows
    public void importXlsxTest(){
        long startTime = System.currentTimeMillis();
        // xls文件名
        String xlsxFilePath = "1623210611390.xlsx";
        // 格式信息
        ImportParams importParams = new ImportParams();
        importParams.setNeedSave(false);
        importParams.setHeadRows(1);
        List<EasyPoiUser> userList = ExcelImportUtil.importExcel(new FileInputStream(xlsxFilePath), EasyPoiUser.class, importParams);
        long endTime = System.currentTimeMillis();
        System.out.printf("parse user from excel with size:%d, cost time: %d ms, cost memory: %s , user memory:%s %n",userList.size(),endTime-startTime, RamUsageEstimator.humanSizeOf(importParams), RamUsageEstimator.humanSizeOf(userList));
        // parse user from excel with size:49999, cost time: 16038 ms, cost memory: 136 bytes , user memory:2.5 MB
        // parse user from excel with size:49999, cost time: 14740 ms, cost memory: 136 bytes , user memory:2.5 MB
        // parse user from excel with size:49999, cost time: 13878 ms, cost memory: 136 bytes , user memory:2.5 MB
    }
```

> 一对多、html和excel互转等功能参考官方文档。

# 四：Easy Excel





























