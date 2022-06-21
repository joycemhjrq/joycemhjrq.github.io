---
layout: post
title: java 多个excel的合并
categories: java
tags: excel
---

## 多个excel合并

{% highlight java %}

package com.goodhope.sheet;

import java.io.File;

import jxl.Sheet;
import jxl.Workbook;
import jxl.format.Alignment;
import jxl.format.Border;
import jxl.write.Label;
import jxl.write.VerticalAlignment;
import jxl.write.WritableCellFormat;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;

public class ResultSheet {

    public static File copyfile = new File("../last.xls");
    public static File resultfile = new File("../result.xls");

    public static WritableWorkbook resultbook;
    public static WritableSheet resultsheet;

    public static void main(String[] args) throws Exception {
        resultbook = Workbook.createWorkbook(resultfile);
        String[] sheetName = { "5个线程", "10个线程", "50个线程", "200个线程",
                "Success Rate", "Average Time", "Through Put", "90% Line" };
        String[] files = { "../5.xls", "../10.xls", "../50.xls", "../200.xls" };

        // 4个工作表：
        for (int i = 0; i < 4; i++) {
            Sheet copysheet = Workbook.getWorkbook(copyfile).getSheet(i);
            File file = new File(files[i]);
            Sheet worksheet = Workbook.getWorkbook(file).getSheet(0);
            resultsheet = resultbook.createSheet(sheetName[i], i);
            WriteWorkSheet(copysheet, 7);
            // 其它行列
            WriteOtherRows(worksheet, resultsheet);
            Workbook.getWorkbook(file).close();
        }

        // 4个比较表
        for (int i = 4; i < 8; i++) {
            Sheet copysheet = Workbook.getWorkbook(copyfile).getSheet(i);
            resultsheet = resultbook.createSheet(sheetName[i], i);
            WriteWorkSheet(copysheet, 10);
            // 其它行列：
            WriteCompareSheetRows(copysheet, resultsheet, i);
        }

        resultbook.write();
        resultbook.close();
    }

    public static void WriteWorkSheet(Sheet copysheet, int rowNum)
            throws Exception {
        // 第一行
        for (int i = 0; i < rowNum; i++) {
            resultsheet.setColumnView(i, copysheet.getColumnWidth(i));
            resultsheet.setRowView(0, copysheet.getRowHeight(0));
            WritableCellFormat cellformat = new WritableCellFormat();
            cellformat.setBackground(copysheet.getCell(i, 0).getCellFormat()
                    .getBackgroundColour());
            cellformat.setBorder(Border.BOTTOM, copysheet.getCell(i, 0)
                    .getCellFormat().getBorderLine(Border.TOP));
            cellformat.setAlignment(Alignment.CENTRE);
            cellformat
                    .setVerticalAlignment(jxl.format.VerticalAlignment.CENTRE);
            Label label = new Label(i, 0,
                    copysheet.getCell(i, 0).getContents(), cellformat);
            resultsheet.addCell(label);
        }
        // 第一、二列
        for (int i = 0; i <= 1; i++) {
            for (int j = 1; j < 30; j++) {
                resultsheet.setRowView(j, copysheet.getRowHeight(j));
                WritableCellFormat cellformat = new WritableCellFormat();
                cellformat.setBackground(copysheet.getCell(i, j)
                        .getCellFormat().getBackgroundColour());
                cellformat.setBorder(Border.BOTTOM, copysheet.getCell(i, j)
                        .getCellFormat().getBorderLine(Border.TOP));
                cellformat.setAlignment(Alignment.CENTRE);
                cellformat
                        .setVerticalAlignment(jxl.format.VerticalAlignment.CENTRE);
                Label label = new Label(i, j, copysheet.getCell(i, j)
                        .getContents(), cellformat);
                resultsheet.addCell(label);
            }
        }
        //合并单元格：如果合并后面的列不为空,那么就把后面格的内容清空,格式也是按前一个单元格的格式
         resultsheet.mergeCells(0, 1, 0, 8);//合并第1行第0列到第8行第0列的单元格
         resultsheet.mergeCells(0, 9, 0, 10);
         resultsheet.mergeCells(0, 13, 1, 13);
         resultsheet.mergeCells(0, 14, 0, 15);
         resultsheet.mergeCells(0, 16, 0, 17);
         resultsheet.mergeCells(0, 18, 0, 20);
         resultsheet.mergeCells(0, 21, 0, 25);
         resultsheet.mergeCells(0, 26, 0, 27);
         resultsheet.mergeCells(0, 28, 1, 28);
         resultsheet.mergeCells(0, 29, 1, 29);
    }

    public static void WriteOtherRows(Sheet worksheet, WritableSheet resultsheet)
            throws Exception {
        for (int i = 1; i < 5; i++)
            for (int j = 1; j < 30; j++) {
                WritableCellFormat cellformat = new WritableCellFormat();
                cellformat.setAlignment(Alignment.CENTRE);
                Label label = new Label(i + 1, j, worksheet.getCell(i, j + 1)
                        .getContents(), cellformat);
                resultsheet.addCell(label);
            }
    }

    public static void WriteCompareSheetRows(Sheet copysheet,
            WritableSheet resultsheet, int sheetNum) throws Exception {
        for (int i = 2; i <= 8; i += 2)
            for (int j = 1; j < 30; j++) {
                WritableCellFormat cellformat = new WritableCellFormat();
                //设置水平居中
                cellformat.setAlignment(Alignment.CENTRE);
                //设置垂直居中
                cellformat
                        .setVerticalAlignment(jxl.format.VerticalAlignment.CENTRE);
                Label label = new Label(i, j, copysheet.getCell(i + 1, j)
                        .getContents(), cellformat);
                resultsheet.addCell(label);
            }
        Sheet copyresultsheet;
        int m = 0;
        for (int i = 3; i < 10; i += 2) {
            copyresultsheet = resultbook.getSheet(m);
            m++;
            for (int j = 1; j < 30; j++) {
                WritableCellFormat cellformat = new WritableCellFormat();
                cellformat.setAlignment(Alignment.CENTRE);
                cellformat
                        .setVerticalAlignment(jxl.format.VerticalAlignment.CENTRE);
                cellformat.setBackground(copysheet.getCell(i, 0)
                        .getCellFormat().getBackgroundColour());
                Label label = new Label(i, j, copyresultsheet.getCell(
                        sheetNum - 2, j).getContents(), cellformat);
                resultsheet.addCell(label);
            }
        }

    }
}

{% endhighlight %}

哦哦哦，还是没有实现完全自动化！！！

还有一个： worksheet.getCell(i, j )：

这里  i：代表列

j：代表行