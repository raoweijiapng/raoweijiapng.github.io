---
title: easyUI网页查询Excel表格下载(1)
tags: easyUI Excel
categories: easyUI
---

* TOC
{:toc}

## Excel表格下载

- 前端html代码(最好使用form表单提交请求)

  ```
    <form id="exportproduceExcel" style="float: left;">
		<div style="display: none">
			<input id="produce_producetongxuntypeselect" name="producetongxuntypeselect" class="easyui-textbox">
			<input id="produce_producetypeselect" name="producetypeselect" class="easyui-textbox">	
			<input id="produce_produceordernumber" name="produceordernumber" class="easyui-textbox">
			<input id="produce_produceusername" name="produceusername" class="easyui-textbox">
			<input id="produce_producemeter" name="producemeter" class="easyui-textbox">			
		</div>
	</form>

	<a href="javascript:void(0)" class="easyui-linkbutton c6" onclick="exportproduceExcel()">导出设备信息</a>
  ```

- 前端js代码

  ```
    //导出设备信息列表
	function exportproduceExcel(){
		//取前端输入框的值		
		var produce_tongxuntype_select = $("#produce_tongxuntype_select").combobox("getText");
		var produce_type_select = $("#produce_type_select").combobox("getText");	   	
	   	var produce_order_number = $("#produce_order_number").textbox("getValue");
		var produce_user_name = $("#produce_user_name").textbox("getValue");
	   	var produce_meter = $("#produce_meter").combobox("getText");
	   	//把得到的条件放入form表单
	   	$("#produce_producetongxuntypeselect").textbox("setValue",produce_tongxuntype_select);
		$("#produce_producetypeselect").textbox("setValue",produce_type_select);		
		$("#produce_produceordernumber").textbox("setValue",produce_order_number);
		$("#produce_produceusername").textbox("setValue",produce_user_name);
		$("#produce_producemeter").textbox("setValue",produce_meter);
		//form表单的提交
	   	$('#exportproduceExcel').form('submit', {
	   	 	url: BASE_PATH+"/userPlat/exportproduceExcelBy.do",
	        success: function(data) {
	        	debugger;
	        	var res = JSON.parse(data);
	        	if(res.type == 0){
	        		$.messager.alert('提示',res.msg);
	        	}	        
	        }
	    });
	}	
  ```

- 后台controller的代码

  ```
    @RequestMapping("/exportproduceExcelBy")
	@ResponseBody
	public String exportproduceExcelBy(HttpServletRequest request,HttpServletResponse response,
			HttpSession session)
			throws Exception {
		//获得form表单的提交的数据并去掉首尾的空格
		String producetongxuntypeselect = request.getParameter("producetongxuntypeselect").trim();
		String producetypeselect = request.getParameter("producetypeselect").trim();		
		String produceordernumber = request.getParameter("produceordernumber").trim();
		String produceusername = request.getParameter("produceusername").trim();
		String producemeter = request.getParameter("producemeter").trim();
		//获得在这些条件下得到的数据的总量
		int	total = userPlatService.selectproduceListByNumber(producetongxuntypeselect,producetypeselect,produceordernumber,produceusername,producemeter);			
		//给前端的提示信息
		JSONObject jsonObject = new JSONObject();
		//判断总量是否超过60000条,老版本的最多下载这些
		if(total>=60000) { 
			jsonObject.put("type",0);
			jsonObject.put("msg","信息量太大,请精确查找后导出!"); 
			return jsonObject.toString(); 
		}
		//执行下载方法
		userPlatService.exportproduceExcelBy(request,response,producetongxuntypeselect,producetypeselect,produceordernumber,produceusername,producemeter);	 
		//返回下载成功的信息
		jsonObject.put("type",1);
		return jsonObject.toString();
	}
  ```

- 后端service实现类方法的代码

  ```
    @Override
	public void exportproduceExcelBy(HttpServletRequest request,HttpServletResponse response,String producetongxuntypeselect,String producetypeselect,
			String produceordernumber,String produceusername,String	producemeter) {
		ServletOutputStream outputStream = null;
		String fileName = "";
		try {
			//时间的格式
			SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			//转换当前获取时间的格式
			String time = df.format(new Date());
			//生成的excel表格的表头
			String[] titles = {"订单号","客户名称","产品（订单系统内的产品）","产品id(订单系统内的产品)",
					"产品类型","口径","箱号","表号","厂家编码",	"温度传感器","流量系数1","流量系数2","流量系数3",
					...
					...
					"衡流阀设置流量点","衡流阀睡眠时间段","上传分界点",	"频点","基准频率",	"无线状态","无线功率"
			};
			//请求响应的输出流
			outputStream = response.getOutputStream();
			fileName = new String(("设备信息统计").getBytes(), "ISO8859_1");
			// 组装附件名称和格式
			response.setHeader("Content-disposition", "attachment; filename=" + fileName + time + ".xls");
			//数据库查询的结果
			List<T_productmsg>	list = userPlatMapper.selectproduceList(producetongxuntypeselect,producetypeselect,produceordernumber,produceusername,producemeter);
			
			// 创建一个workbook 对应一个excel应用文件
			HSSFWorkbook workBook = new HSSFWorkbook();
			// 在workbook中添加一个sheet,对应Excel文件中的sheet
			HSSFSheet sheet = workBook.createSheet("设备信息统计");
			ExcelUtil exportUtil = new ExcelUtil(workBook, sheet);

			//exportUtil的格式
			HSSFCellStyle headStyle = exportUtil.getHeadStyle();
			HSSFCellStyle timeStyle = exportUtil.getTimeStyle();
			HSSFCellStyle bodyStyle = exportUtil.getBodyStyle();
			HSSFCellStyle titleStyle = exportUtil.getTitleStyle();
			HSSFCellStyle lastStyle = exportUtil.getLastStyle();
			HSSFCellStyle titleRightStyle = exportUtil.getTitleRightStyle();
			HSSFCellStyle bodyrightStyle = exportUtil.getBodyRightStyle();
			HSSFCellStyle LastBodyRightStyle = exportUtil.getLastBodyRightStyle();
			HSSFCellStyle titleLeftStyle = exportUtil.getTitleLeftStyle();
			HSSFCellStyle bodyLeftStyle = exportUtil.getBodyLeftStyle();
			HSSFCellStyle bodyLeftLastStyle = exportUtil.getBodyLeftLastStyle();

			//和title里的数量一致
			for (int h = 0; h <= 103; h++) {
				//设置列宽度
				sheet.setColumnWidth((short) h, (short) 5600);
			}

			//表格的第一行
			HSSFRow titleRow = sheet.createRow(0);
			titleRow.setHeight((short) 500);			
			HSSFCell titlecell = titleRow.createCell(1);
			titlecell.setCellValue("设备信息报表");
			//表格的第二行
			HSSFRow timeRow = sheet.createRow(1);
			timeRow.setHeight((short) 450);
			HSSFCell timecell = timeRow.createCell(1);
			Date date = new Date();
			SimpleDateFormat ss = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			timecell.setCellValue("设备统计(导出时间:" + ss.format(date));
			
			//合并单元格
    		//参数1：起始行 参数2：终止行 参数3：起始列 参数4：终止列
			sheet.addMergedRegion(new CellRangeAddress(0, 0, 1, 6));
			sheet.addMergedRegion(new CellRangeAddress(1, 1, 1, 10));
			//设置第一行和第二行的格式
			titlecell.setCellStyle(headStyle);
			timecell.setCellStyle(timeStyle);

			// 构建表头
			HSSFRow headRow = sheet.createRow(2);
			headRow.setHeight((short) 450);
			HSSFCell cell = null;
			for (int i = 0; i < titles.length; i++) {
				if (i == 0) {
					cell = headRow.createCell(i);
					cell.setCellValue(titles[i]);
					cell.setCellStyle(titleLeftStyle);
				} else if (i == titles.length - 1) {
					cell = headRow.createCell(i);
					cell.setCellValue(titles[i]);
					cell.setCellStyle(titleRightStyle);
				} else {
					cell = headRow.createCell(i);
					cell.setCellValue(titles[i]);
					cell.setCellStyle(titleStyle);
				}
			}
			// 构建表体数据
			for (int j = 0; j < list.size(); j++) {
				if (j == list.size() - 1) {
					HSSFRow bodyRow = sheet.createRow(j + 3);
					bodyRow.setHeight((short) 450);
					
					cell = bodyRow.createCell(0);
					cell.setCellValue(list.get(j).getCode());
					cell.setCellStyle(lastStyle);
					
					cell = bodyRow.createCell(1);
					cell.setCellValue(list.get(j).getCustomerName());
					cell.setCellStyle(lastStyle);
					
					......
					......
					
					cell = bodyRow.createCell(112);
					cell.setCellValue(list.get(j).getWirelessPower());
					cell.setCellStyle(lastStyle);
					
				} else {
					HSSFRow bodyRow = sheet.createRow(j + 3);
					bodyRow.setHeight((short) 450);
					
					cell = bodyRow.createCell(0);
					cell.setCellValue(list.get(j).getCode());
					cell.setCellStyle(bodyLeftStyle);
					
					cell = bodyRow.createCell(1);
					cell.setCellValue(list.get(j).getCustomerName());
					cell.setCellStyle(bodyLeftStyle);
					
					cell = bodyRow.createCell(2);
					cell.setCellValue(list.get(j).getProduct());
					cell.setCellStyle(bodyLeftStyle);
					
					......
					......
					
					cell = bodyRow.createCell(112);
					cell.setCellValue(list.get(j).getWirelessPower());
					cell.setCellStyle(bodyLeftStyle);	
				}
			}
			//浏览器下载excel
			workBook.write(outputStream);
			outputStream.flush();
			outputStream.close();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				outputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}		
	}
  ```

