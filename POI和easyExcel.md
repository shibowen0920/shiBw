#### POI

***

##### POI

- 写

```java
/**
 * @author: shiBw
 * @description: excel写
 * @time: 2020/7/2 16:52
 */
public class ExcelWriteTest {

    String PATH = "G:\\github\\shiro-demo\\POI";

    @Test
    public void testWrite03() throws Exception {
        //创建工作簿
        Workbook workbook = new HSSFWorkbook();
        //创建工作表
        Sheet sheet = workbook.createSheet("shiBw001");
        //创建一行
        Row row1 = sheet.createRow(0);
        //创建单元格(0是开始)
        //(1,1)
        Cell cell1 = row1.createCell(0);
        cell1.setCellValue("写入数据001");
        //(1,2)
        Cell cell2 = row1.createCell(1);
        cell2.setCellValue("写入数据002");

        Row row2 = sheet.createRow(1);
        Cell cell21 = row2.createCell(0);
        cell21.setCellValue("统计时间：");
        Cell cell22 = row2.createCell(1);
        String time = new DateTime().toString("yyyy-MM-dd");
        cell22.setCellValue(time);

        //生成一张表（IO流）03使用xls
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "shiBw.xls");
        workbook.write(fileOutputStream);
        fileOutputStream.close();
        System.out.println("文件生成完毕");
    }

    @Test
    public void testWrite07() throws Exception {
        //创建工作簿
        Workbook workbook = new SXSSFWorkbook();
        //创建工作表
        Sheet sheet = workbook.createSheet("shiBw002");
        //创建一行
        Row row1 = sheet.createRow(0);
        //创建单元格(0是开始)
        //(1,1)
        Cell cell1 = row1.createCell(0);
        cell1.setCellValue("写入数据001");
        //(1,2)
        Cell cell2 = row1.createCell(1);
        cell2.setCellValue("写入数据002");

        Row row2 = sheet.createRow(1);
        Cell cell21 = row2.createCell(0);
        cell21.setCellValue("统计时间：");
        Cell cell22 = row2.createCell(1);
        String time = new DateTime().toString("yyyy-MM-dd");
        cell22.setCellValue(time);

        //生成一张表（IO流）07使用xlsx
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "shiBw02.xlsx");
        workbook.write(fileOutputStream);
        fileOutputStream.close();
        System.out.println("文件生成完毕");
    }

    @Test
    public void testWrite03BigData() throws Exception {
        //时间差
        long begin = System.currentTimeMillis();

        Workbook workbook = new HSSFWorkbook();
        Sheet sheet = workbook.createSheet();
        //03最大是65536,超过会报错
        for (int rowNum = 0; rowNum < 65536; rowNum++) {
            Row row = sheet.createRow(rowNum);
            for(int cellNum = 0;cellNum < 10; cellNum++){
                Cell cell = row.createCell(cellNum);
                cell.setCellValue(cellNum);
            }
        }
        System.out.println("over");
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "bigDATA03.xls");
        workbook.write(fileOutputStream);
        fileOutputStream.close();
        long end = System.currentTimeMillis();
        System.out.println((end-begin)/1000);
    }

    //耗时较长
    @Test
    public void testWrite07BigData() throws Exception {
        //时间差
        long begin = System.currentTimeMillis();

        Workbook workbook = new XSSFWorkbook();
        Sheet sheet = workbook.createSheet();
        //03最大是65536,超过会报错
        for (int rowNum = 0; rowNum < 100000; rowNum++) {
            Row row = sheet.createRow(rowNum);
            for(int cellNum = 0;cellNum < 10; cellNum++){
                Cell cell = row.createCell(cellNum);
                cell.setCellValue(cellNum);
            }
        }
        System.out.println("over");
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "bigDATA07.xlsx");
        workbook.write(fileOutputStream);
        fileOutputStream.close();
        long end = System.currentTimeMillis();
        System.out.println((end-begin)/1000);
    }
    //快
    @Test
    public void testWrite07BigDataS() throws Exception {
        //时间差
        long begin = System.currentTimeMillis();

        Workbook workbook = new SXSSFWorkbook();
        Sheet sheet = workbook.createSheet();
        //03最大是65536,超过会报错
        for (int rowNum = 0; rowNum < 100000; rowNum++) {
            Row row = sheet.createRow(rowNum);
            for(int cellNum = 0;cellNum < 10; cellNum++){
                Cell cell = row.createCell(cellNum);
                cell.setCellValue(cellNum);
            }
        }
        System.out.println("over");
        FileOutputStream fileOutputStream = new FileOutputStream(PATH + "bigDATA07S.xlsx");
        workbook.write(fileOutputStream);
        //清除临时文件
        ((SXSSFWorkbook)workbook).dispose();
        fileOutputStream.close();
        long end = System.currentTimeMillis();
        System.out.println((end-begin)/1000);
    }
}
```

***

- 读

```java
public class ExcelReadTest {

    String PATH = "G:\\github\\shiro-demo\\";

    @Test
    public void testRead03() throws Exception {

        FileInputStream fileInputStream = new FileInputStream(PATH + "POIbigDATA03.xls");

        //创建工作簿_使用excel能操作的 这边都可以操作
        Workbook workbook = new HSSFWorkbook(fileInputStream);
        //得到表
        Sheet sheet = workbook.getSheet("Sheet0");
        //得到行
        Row row = sheet.getRow(0);
        Cell cell = row.getCell(3);
        //读取值的时候需要注意类型
        cell.setCellType(Cell.CELL_TYPE_STRING);
        //字符串类型
        System.out.println(cell.getStringCellValue());

        fileInputStream.close();
    }

    @Test
    public void testRead07() throws Exception {

        FileInputStream fileInputStream = new FileInputStream(PATH + "POIbigDATA07.xlsx");

        //创建工作簿_使用excel能操作的 这边都可以操作
        Workbook workbook = new XSSFWorkbook(fileInputStream);
        //得到表
        Sheet sheet = workbook.getSheet("Sheet0");
        //得到行
        Row row = sheet.getRow(0);
        Cell cell = row.getCell(3);
        //读取值的时候需要注意类型
//        cell.setCellType(Cell.CELL_TYPE_STRING);
        //字符串类型
        System.out.println(cell.getNumericCellValue());

        fileInputStream.close();
    }
}
```

***

- **按类型读**

```java
@Test
public void testReadType() throws Exception {

    FileInputStream fileInputStream = new FileInputStream(PATH + "03.xls");

    //创建工作簿_使用excel能操作的 这边都可以操作
    Workbook workbook = new HSSFWorkbook(fileInputStream);
    //得到表
    Sheet sheet = workbook.getSheetAt(0);

    Row rowTitle = sheet.getRow(0);

    if(rowTitle != null){
        int cellcount = rowTitle.getPhysicalNumberOfCells();
        for (int i = 0;i < cellcount;i++ ){
            Cell cell = rowTitle.getCell(i);
            if(cell != null){
                int cellType = cell.getCellType();
                String stringCellValue = cell.getStringCellValue();
                System.out.print(stringCellValue+"   |   ");
            }
        }

        int physicalNumberOfRows = sheet.getPhysicalNumberOfRows();
        for (int i = 1; i < physicalNumberOfRows; i++) {
            Row row = sheet.getRow(i);
            if(row != null){
                int cellCount = rowTitle.getPhysicalNumberOfCells();
                for (int cellNum = 0; cellNum < cellCount; cellNum++) {
                    System.out.print("["+(i+1)+"-"+(cellNum+1)+"]   |   ");
                    Cell cell = row.getCell(cellNum);
                    //匹配列的类型
                    if(cell!=null){
                        int cellType = cell.getCellType();
                        String cellValue = "";
                        switch (cellType){
                            case HSSFCell.CELL_TYPE_STRING:
                                System.out.println("[String]");
                                cellValue = cell.getStringCellValue();
                                break;
                            case HSSFCell.CELL_TYPE_NUMERIC:
                                if (HSSFDateUtil.isCellDateFormatted(cell)) {
                                    System.out.println("[日期]");
                                    Date dateCellValue = cell.getDateCellValue();
                                    cellValue = new DateTime(dateCellValue).toString("yyyy-MM-dd");
                                }else{
                                    System.out.println("[NUMERIC]");
                                    cell.setCellType(HSSFCell.CELL_TYPE_STRING);
                                    cellValue = String.valueOf(cell.getStringCellValue());
                                }
                                break;
                        }
                        System.out.println(cellValue);
                    }
                }
            }
        }
        fileInputStream.close();
    }
}
```

***

- **公式计算**

  ```JAVA
  @Test
  public void testReadFormula() throws Exception {
  
      FileInputStream fileInputStream = new FileInputStream(PATH + "03.xls");
  
      //创建工作簿_使用excel能操作的 这边都可以操作
      Workbook workbook = new HSSFWorkbook(fileInputStream);
  
      Sheet Sheet = workbook.getSheetAt(1);
      Row row = Sheet.getRow(5);
      Cell cell = row.getCell(0);
      FormulaEvaluator formulaEvaluator = new HSSFFormulaEvaluator((HSSFWorkbook)workbook);
  
      int cellType = cell.getCellType();
      String formula = cell.getCellFormula();
      System.out.println(formula);
  
      CellValue evaluate = formulaEvaluator.evaluate(cell);
      String cellValue = evaluate.formatAsString();
      System.out.println(cellValue);
  }
  ```
