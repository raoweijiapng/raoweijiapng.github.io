---
title: easyUI网页查询Excel表格下载(2)
tags: easyUI Excel
categories: easyUI
---

* TOC
{:toc}

## Excel表格下载

- 生成excel表格的工具类

  ```
	import java.io.IOException;
	import java.io.InputStream;
	import java.text.DecimalFormat;
	import java.text.SimpleDateFormat;
	import java.util.ArrayList;
	import java.util.List;
	import java.util.Map;

	import org.apache.poi.hssf.usermodel.HSSFCell;
	import org.apache.poi.hssf.usermodel.HSSFCellStyle;
	import org.apache.poi.hssf.usermodel.HSSFFont;
	import org.apache.poi.hssf.usermodel.HSSFRow;
	import org.apache.poi.hssf.usermodel.HSSFSheet;
	import org.apache.poi.hssf.usermodel.HSSFWorkbook;
	import org.apache.poi.hssf.util.HSSFColor;
	import org.apache.poi.ss.usermodel.Cell;
	import org.apache.poi.ss.usermodel.CellStyle;
	import org.apache.poi.ss.usermodel.Font;
	import org.apache.poi.ss.usermodel.IndexedColors;
	import org.apache.poi.ss.usermodel.Row;
	import org.apache.poi.ss.usermodel.Sheet;
	import org.apache.poi.ss.usermodel.Workbook;
	import org.apache.poi.ss.util.CellRangeAddress;
	import org.apache.poi.xssf.usermodel.XSSFCellStyle;
	import org.apache.poi.xssf.usermodel.XSSFFont;
	public class ExcelUtil {
		private final static String excel2003L =".xls";    //2003- 版本的excel
		private final static String excel2007U =".xlsx";   //2007+ 版本的excel
		
		private HSSFWorkbook wb = null;  
	   
	    private HSSFSheet sheet = null;
		public ExcelUtil() {
			// TODO Auto-generated constructor stub
		}
	    /** 
	     * @param wb 
	     * @param sheet 
	     */  
	    public ExcelUtil(HSSFWorkbook wb, HSSFSheet sheet)  
	    {  
	        this.wb = wb;  
	        this.sheet = sheet;  
	    }  
		
		/**
		 * 描述：获取IO流中的数据，组装成List<List<Object>>对象
		 * @param in,fileName
		 * @return
		 * @throws IOException 
		 */
		public  List<List<Object>> getBankListByExcel(InputStream in,String fileName) throws Exception{
			List<List<Object>> list = null;
			
			//创建Excel工作薄
			Workbook work = this.getWorkbook(in,fileName);
			if(null == work){
				throw new Exception("创建Excel工作薄为空！");
			}
			Sheet sheet = null;
			Row row = null;
			Cell cell = null;
			
			list = new ArrayList<List<Object>>();
			//遍历Excel中所有的sheet
			for (int i = 0; i < work.getNumberOfSheets(); i++) {
				sheet = work.getSheetAt(i);
				if(sheet==null){continue;}
				
	//			System.out.println("sheet.getFirstRowNum():"+sheet.getFirstRowNum()+"--sheet.getLastRowNum():"+sheet.getLastRowNum());
				//遍历当前sheet中的所有行
				for (int j = sheet.getFirstRowNum(); j < sheet.getLastRowNum()+1; j++) {
					row = sheet.getRow(j);
					//if(row==null||row.getFirstCellNum()==j){continue;}
					if(row==null){continue;}
					//遍历所有的列
					List<Object> li = new ArrayList<Object>();
					for (int y = row.getFirstCellNum(); y < row.getLastCellNum(); y++) {
						cell = row.getCell(y);
						li.add(this.getCellValue(cell));
					}
					list.add(li);
				}
			}
			work.close();
			return list;
		}
		
		/**
		 * 描述：根据文件后缀，自适应上传文件的版本 
		 * @param inStr,fileName
		 * @return
		 * @throws Exception
		 */
		public  Workbook getWorkbook(InputStream inStr,String fileName) throws Exception{
			Workbook wb = null;
			String fileType = fileName.substring(fileName.lastIndexOf("."));
			if(excel2003L.equals(fileType)){
				wb = new HSSFWorkbook(inStr);  //2003-
			}else if(excel2007U.equals(fileType)){
				//wb = new XSSFWorkbook(inStr);  //2007+
				//wb = WorkbookFactory.create(inStr);
			}else{
				throw new Exception("解析的文件格式有误！");
			}
			return wb;
		}

		/**
		 * 描述：对表格中数值进行格式化
		 * @param cell
		 * @return
		 */
		@SuppressWarnings("deprecation")
		public  Object getCellValue(Cell cell){
			Object value = null;
			DecimalFormat df = new DecimalFormat("0");  //格式化number String字符
			SimpleDateFormat sdf = new SimpleDateFormat("yyy-MM-dd");  //日期格式化
			DecimalFormat df2 = new DecimalFormat("0.00");  //格式化数字
			
			switch (cell.getCellType()) {
			case Cell.CELL_TYPE_STRING:
				value = cell.getRichStringCellValue().getString();
				break;
			case Cell.CELL_TYPE_NUMERIC:
				if("General".equals(cell.getCellStyle().getDataFormatString())){
					value = df.format(cell.getNumericCellValue());
				}else if("m/d/yy".equals(cell.getCellStyle().getDataFormatString())){
					value = sdf.format(cell.getDateCellValue());
				}else{
					value = df2.format(cell.getNumericCellValue());
				}
				break;
			case Cell.CELL_TYPE_BOOLEAN:
				value = cell.getBooleanCellValue();
				break;
			case Cell.CELL_TYPE_BLANK:
				value = "";
				break;
			default:
				break;
			}
			return value;
		}
		
		
		/**
	     * 创建excel文档，
	     * @param list 数据
	     * @param keys list中map的key数组集合
	     * @param columnNames excel的列名
	     * */
	    @SuppressWarnings("deprecation")
		public static Workbook createWorkBook(List<Map<String, Object>> list,String []keys,String columnNames[]) {
	        // 创建excel工作簿
	        Workbook wb = new HSSFWorkbook();
	        // 创建第一个sheet（页），并命名
	        Sheet sheet = wb.createSheet(list.get(0).get("sheetName").toString());
	        // 手动设置列宽。第一个参数表示要为第几列设；，第二个参数表示列的宽度，n为列高的像素数。
	        for(int i=0;i<keys.length;i++){
	            sheet.setColumnWidth((short) i, (short) (256 * 35));
	        }

	        // 创建第一行
	        Row row = sheet.createRow((short) 0);

	        // 创建两种单元格格式
	        CellStyle cs = wb.createCellStyle();
	        CellStyle cs2 = wb.createCellStyle();

	        // 创建两种字体
	        Font f = wb.createFont();
	        Font f2 = wb.createFont();

	        // 创建第一种字体样式（用于列名）
	        f.setFontHeightInPoints((short) 10);
	        f.setColor(IndexedColors.BLACK.getIndex());
	        f.setBoldweight(Font.BOLDWEIGHT_BOLD);

	        // 创建第二种字体样式（用于值）
	        f2.setFontHeightInPoints((short) 10);
	        f2.setColor(IndexedColors.BLACK.getIndex());

	//        Font f3=wb.createFont();
	//        f3.setFontHeightInPoints((short) 10);
	//        f3.setColor(IndexedColors.RED.getIndex());

	        // 设置第一种单元格的样式（用于列名）
	        cs.setFont(f);
	        cs.setBorderLeft(CellStyle.BORDER_THIN);
	        cs.setBorderRight(CellStyle.BORDER_THIN);
	        cs.setBorderTop(CellStyle.BORDER_THIN);
	        cs.setBorderBottom(CellStyle.BORDER_THIN);
	        cs.setAlignment(CellStyle.ALIGN_CENTER);

	        // 设置第二种单元格的样式（用于值）
	        cs2.setFont(f2);
	        cs2.setBorderLeft(CellStyle.BORDER_THIN);
	        cs2.setBorderRight(CellStyle.BORDER_THIN);
	        cs2.setBorderTop(CellStyle.BORDER_THIN);
	        cs2.setBorderBottom(CellStyle.BORDER_THIN);
	        cs2.setAlignment(CellStyle.ALIGN_CENTER);
	        //设置列名
	        for(int i=0;i<columnNames.length;i++){
	            Cell cell = row.createCell(i);
	            cell.setCellValue(columnNames[i]);
	            cell.setCellStyle(cs);
	        }
	        //设置每行每列的值
	        for (short i = 1; i < list.size(); i++) {
	            // Row 行,Cell 方格 , Row 和 Cell 都是从0开始计数的
	            // 创建一行，在页sheet上
	            Row row1 = sheet.createRow((short) i);
	            // 在row行上创建一个方格
	            for(short j=0;j<keys.length;j++){
	                Cell cell = row1.createCell(j);
	                cell.setCellValue(list.get(i).get(keys[j]) == null?" ": list.get(i).get(keys[j]).toString());
	                cell.setCellStyle(cs2);
	            }
	        }
	        return wb;
	    }

	    
	    /** 
	     * 合并单元格后给合并后的单元格加边框 
	     *  
	     * @param region 
	     * @param cs 
	     */  
	    public void setRegionStyle(CellRangeAddress region, XSSFCellStyle cs)  
	    {  
	  
	        int toprowNum = region.getFirstRow();  
	        for (int i = toprowNum; i <= region.getLastRow(); i++)  
	        {  
	            HSSFRow row = sheet.getRow(i);  
	            for (int j = region.getFirstColumn(); j <= region.getLastColumn(); j++)  
	            {  
	                HSSFCell cell = row.getCell(j);// XSSFCellUtil.getCell(row,  
	                                                // (short) j);  
	                cell.setCellStyle(cs);  
	            }  
	        }  
	    }  
	  
	    /** 
	     * 设置表头的单元格样式 
	     *  
	     * @return 
	     */  
	    @SuppressWarnings("deprecation")
	    //标题单元格格式
		public HSSFCellStyle getHeadStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格的背景颜色为淡蓝色  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false);
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setBoldweight(XSSFFont.BOLDWEIGHT_BOLD);  
	        font.setFontName("宋体"); 
	        font.setFontHeight((short) 300);  
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        return cellStyle;  
	    }  
	    //导出时间单元格格式
	    @SuppressWarnings("deprecation")
	  	public HSSFCellStyle getTimeStyle()  
	      {  
	          // 创建单元格样式  
	          HSSFCellStyle cellStyle = wb.createCellStyle();  
	          // 设置单元格的背景颜色为淡蓝色  
	          // 设置单元格居中对齐  
	          cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	          // 设置单元格垂直居中对齐  
	          cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	          // 创建单元格内容显示不下时自动换行  
	          cellStyle.setWrapText(false);
	          // 设置单元格字体样式  
	          HSSFFont font = wb.createFont();  
	          // 设置字体加粗  
	          font.setFontName("宋体"); 
	          font.setFontHeight((short) 217);  
	          cellStyle.setFont(font);  
	          // 设置单元格边框为细线条  
	       
	          return cellStyle;  
	      }  
	  
	    /** 
	     * 设置表体的单元格样式 
	     *  
	     * @return 
	     */  
	    //表格体内的单元格格式
	    @SuppressWarnings("deprecation")
		public HSSFCellStyle getBodyStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格最后一列单元格格式（不包含该列最后一个单元格）
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getBodyRightStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格最后一行第一个单元格格式
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getBodyLeftLastStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格列属性的单元格格式（不包括改行的第一个单元格和最后一个单元格）
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getTitleStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_MEDIUM);  
	        return cellStyle;  
	    }
	    //表格列属性第一个单元格的格式
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getTitleLeftStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_MEDIUM);  
	        return cellStyle;  
	    }
	    //表格最后一行的单元格格式（不包括第一个和最后一个单元格）
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getLastStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格最后一行的最后一个单元格的格式
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getLastBodyRightStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格第一列的单元格格式（不包括该列的第一个和最后一个）
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getBodyLeftStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_DASHED);  
	        return cellStyle;  
	    }
	    //表格属性行最后一个单元格格式
	    @SuppressWarnings("deprecation")
	    public HSSFCellStyle getTitleRightStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false); 
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        // 设置字体加粗  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 217);
	        cellStyle.setFont(font);  
	        // 设置单元格边框为细线条  
	        cellStyle.setBorderLeft(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderBottom(XSSFCellStyle.BORDER_DASHED);  
	        cellStyle.setBorderRight(XSSFCellStyle.BORDER_MEDIUM);  
	        cellStyle.setBorderTop(XSSFCellStyle.BORDER_MEDIUM);  
	        return cellStyle;  
	    }
	    
	    /** 
	     * 设置表体的单元格样式 
	     *  
	     * @return 
	     */  
	    @SuppressWarnings("deprecation")
		public HSSFCellStyle getDkqdStyle()  
	    {  
	        // 创建单元格样式  
	        HSSFCellStyle cellStyle = wb.createCellStyle();  
	        // 设置单元格居中对齐  
	        cellStyle.setAlignment(XSSFCellStyle.ALIGN_CENTER);  
	        // 设置单元格垂直居中对齐  
	        cellStyle.setVerticalAlignment(XSSFCellStyle.VERTICAL_CENTER);  
	        // 创建单元格内容显示不下时自动换行  
	        cellStyle.setWrapText(false);  
	        // 设置单元格字体样式  
	        HSSFFont font = wb.createFont();  
	        cellStyle.setAlignment(HSSFCellStyle.ALIGN_CENTER); // 居中
	        // 设置字体加粗  
	        font.setBoldweight(XSSFFont.BOLDWEIGHT_BOLD);  
	        font.setFontName("宋体");  
	        font.setFontHeight((short) 200);  
	        cellStyle.setFont(font);
	        // 设置单元格边框为细线条
	        cellStyle.setBorderBottom(HSSFCellStyle.BORDER_THIN); //下边框
	        cellStyle.setBorderLeft(HSSFCellStyle.BORDER_THIN);//左边框
	        cellStyle.setBorderTop(HSSFCellStyle.BORDER_THIN);//上边框
	        cellStyle.setBorderRight(HSSFCellStyle.BORDER_THIN);//右边框
	        return cellStyle;  
	    }
	    
	    
	}

  ```

- mapper.xml文件的代码

  ```
    <select id="selectproduceListByNumber" resultType="int">
		select count(*) from t_productmsg where 1=1
		<if test="producetongxuntypeselect != '' and producetongxuntypeselect != '全部'">
			and comunicateMode = #{producetongxuntypeselect}
		</if>
		<if test="producetypeselect != '' and producetypeselect != '全部'">
			and productType like '%${producetypeselect}%'
		</if>
		<if test="produceordernumber != ''">
			and code = #{produceordernumber}
		</if>
		<if test="produceusername != ''">
			and customerName like '%${produceusername}%'
		</if>
		<if test="producemeter != '' and producemeter != '全部'">
			and meterSize like '%${producemeter}%'
		</if>  
	</select>
	<select id="selectproduceList" resultType="T_productmsg">
		select * from t_productmsg where 1=1
		<if test="producetongxuntypeselect != '' and producetongxuntypeselect != '全部'">
			and comunicateMode = #{producetongxuntypeselect}
		</if>
		<if test="producetypeselect != '' and producetypeselect != '全部'">
			and productType like '%${producetypeselect}%'
		</if>
		<if test="produceordernumber != ''">
			and code = #{produceordernumber}
		</if>
		<if test="produceusername != ''">
			and customerName like '%${produceusername}%'
		</if>
		<if test="producemeter != '' and producemeter != '全部'">
			and meterSize like '%${producemeter}%'
		</if>
	</select>
  ```

