---
layout: post
title: javascript 把table转成excel
categories: javascript
tags: 
    - javascript
    - table
    - excel
---

## javascript 把table转成excel

{% highlight html %}

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
 "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
 <html xmlns="http://www.w3.org/1999/xhtml">
 <head>
     <meta http-equiv="Content-Type" content="text/html; charset=gb2312"/>
     <title>WEB页面导出为EXCEL文档的方法</title>
 </head>
 <body>
 <table id="tableExcel" width="100%" border="1" cellspacing="0" cellpadding="0">
     <tr>
         <td colspan="5" align="center">WEB页面导出为EXCEL文档的方法</td>
     </tr>
     <tr>
         <td>列标题1</td>
         <td>列标题2</td>
         <td>列标题3</td>
         <td>列标题4</td>
         <td>列标题5</td>
     </tr>
     <tr>
         <td>aaa</td>
         <td>bbb</td>
         <td>ccc</td>
         <td>ddd</td>
         <td>eee</td>
     </tr>
     <tr>
         <td>AAA</td>
         <td>BBB</td>
         <td>CCC</td>
         <td>DDD</td>
         <td>EEE</td>
     </tr>
     <tr>
         <td>FFF</td>
         <td>GGG</td>
         <td>HHH</td>
         <td>III</td>
         <td>JJJ</td>
     </tr> 
 </table>
 <input type="button" onclick="javascript:method1('tableExcel');" value="第一种方法导入到EXCEL"/>
 <input type="button" onclick="javascript:method2('tableExcel');" value="第二种方法导入到EXCEL"/>
 <input type="button" onclick="javascript:getXlsFromTbl('tableExcel',null);" value="第三种方法导入到EXCEL"/>
 <SCRIPT type="text/javascript" LANGUAGE="javascript">
 function method1(tableid) {//整个表格拷贝到EXCEL中
    var curTbl = document.getElementByIdx(tableid);
     var oXL = new ActiveXObject("Excel.Application");
     //创建AX对象excel
     var oWB = oXL.Workbooks.Add();
     //获取workbook对象
        var oSheet = oWB.ActiveSheet;
     //激活当前sheet
     var sel = document.body.createTextRange();
     sel.moveToElementText(curTbl);
     //把表格中的内容移到TextRange中
    sel.select();
     //全选TextRange中内容
    sel.execCommand("Copy");
     //复制TextRange中内容 
     oSheet.Paste();
     //粘贴到活动的EXCEL中      
     oXL.Visible = true;
     //设置excel可见属性
}
function method2(tableid) //读取表格中每个单元到EXCEL中
{
     var curTbl = document.getElementByIdx(tableid);
     var oXL = new ActiveXObject("Excel.Application");
     //创建AX对象excel
     var oWB = oXL.Workbooks.Add();
     //获取workbook对象
    var oSheet = oWB.ActiveSheet;
     //激活当前sheet
     var Lenr = curTbl.rows.length;
     //取得表格行数
    for (i = 0; i < Lenr; i++)
     {
         var Lenc = curTbl.rows(i).cells.length;
         //取得每行的列数
        for (j = 0; j < Lenc; j++)
         {
             oSheet.Cells(i + 1, j + 1).value = curTbl.rows(i).cells(j).innerText;
             //赋值
        }
     }
     oXL.Visible = true;
     //设置excel可见属性
}
function getXlsFromTbl(inTblId, inWindow) {
     try {
         var allStr = "";
         var curStr = "";
         //alert("getXlsFromTbl");
         if (inTblId != null && inTblId != "" && inTblId != "null") {
             curStr = getTblData(inTblId, inWindow);
         }
         if (curStr != null) {
             allStr += curStr;
         }
         else {
             alert("你要导出的表不存在！");
             return;
         }
         var fileName = getExcelFileName();
         doFileExport(fileName, allStr);
     }
     catch(e) {
         alert("导出发生异常:" + e.name + "->" + e.description + "!");
     }
 }
 function getTblData(inTbl, inWindow) {
     var rows = 0;
     //alert("getTblData is " + inWindow);
     var tblDocument = document;
     if (!!inWindow && inWindow != "") {
         if (!document.all(inWindow)) {
             return null;
         }
         else {
             tblDocument = eval_r(inWindow).document;
         }
     }
     var curTbl = tbldocument.getElementByIdx(inTbl);
     var outStr = "";
     if (curTbl != null) {
         for (var j = 0; j < curTbl.rows.length; j++) {
             //alert("j is " + j);
             for (var i = 0; i < curTbl.rows[j].cells.length; i++) {
                 //alert("i is " + i);
                 if (i == 0 && rows > 0) {
                     outStr += " \t";
                     rows -= 1;
                 }
                 outStr += curTbl.rows[j].cells[i].innerText + "\t";
                 if (curTbl.rows[j].cells[i].colSpan > 1) {
                     for (var k = 0; k < curTbl.rows[j].cells[i].colSpan - 1; k++) {
                         outStr += " \t";
                     }
                 }
                 if (i == 0) {
                     if (rows == 0 && curTbl.rows[j].cells[i].rowSpan > 1) {
                         rows = curTbl.rows[j].cells[i].rowSpan - 1;
                     }
                 }
             }
             outStr += "\r\n";
         }
     }
     else {
         outStr = null;
         alert(inTbl + "不存在!");
     }
     return outStr;
 }
 function getExcelFileName() {
     var d = new Date();
     var curYear = d.getYear();
     var curMonth = "" + (d.getMonth() + 1);
     var curDate = "" + d.getDate();
     var curHour = "" + d.getHours();
     var curMinute = "" + d.getMinutes();
     var curSecond = "" + d.getSeconds();
     if (curMonth.length == 1) {
         curMonth = "0" + curMonth;
     }
     if (curDate.length == 1) {
         curDate = "0" + curDate;
     }
     if (curHour.length == 1) {
         curHour = "0" + curHour;
     }
     if (curMinute.length == 1) {
         curMinute = "0" + curMinute;
     }
     if (curSecond.length == 1) {
         curSecond = "0" + curSecond;
     }
     var fileName = "leo_zhang" + "_" + curYear + curMonth + curDate + "_"
             + curHour + curMinute + curSecond + ".csv";
     //alert(fileName);
     return fileName;
 }
 function doFileExport(inName, inStr) {
     var xlsWin = null;
     if (!!document.all("glbHideFrm")) {
         xlsWin = glbHideFrm;
     }
     else {
         var width = 6;
         var height = 4;
         var openPara = "left=" + (window.screen.width / 2 - width / 2)
                 + ",top=" + (window.screen.height / 2 - height / 2)
                 + ",scrollbars=no,width=" + width + ",height=" + height;
         xlsWin = window.open("", "_blank", openPara);
     }
     xlsWin.document.write(inStr);
     xlsWin.document.close();
     xlsWin.document.execCommand('Saveas', true, inName);
     xlsWin.close();
 }
 </SCRIPT>
 </body>
 </html> 

 {% endhighlight %}