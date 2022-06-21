---
layout: post
title: java stream
categories: java
tags: stream
---

[原文地址](http://blog.xujif.com/archives/java-stream/)

这一系列是个教程，既是自己学习，也是为了某个懒人能够好好学习。

流（stream）的概念源于UNIX中管道（pipe）的概念。在UNIX中，管道是一条不间断的字节流，用来实现程序或进程间的通信，或读写外围设备、外部文件、网络等。Stream为各种设备抽象了一种数据交流方式。

简单说，stream像一个一个先进先出的数据队列，当从头部读取数据时，则这部分数据出列。从尾部写入数据时，队列变长。

java中，Stream主要分为input及output Stream。我们写console程序时，就会经常用到3个stream (System.in, System.out, System.err)，其中除了in是InputStream，其他两个都是OutputStream (PrintStream继承自 OutputStream).

java.io命名空间里，就有各种Stream的实现（还有各种reader和wirter）。常用的有

	BufferedInputStream
	BufferedOutputStream
	ByteArrayInputStream
	ByteArrayOutputStream
	DataInputStream
	DataOutputStream
	FileInputStream
	FileOutputStream
	InputStreamReader
	OutputStream
	OutputStreamWriter
	PipedInputStream
	PipedOutputStream
	PrintStream
	PrintWriter
	StringBufferInputStream

## InputStream类

InputStream处于Input stream类层次的最顶层，它主要具有以下几种方法：

## read方法

read方法用于从指定的输入流读取以字节为单位的数据，读取后同时把数据指针往后移动。

（1） int read(byte buff[n])：从指定输入流中读取n个字节填充到buff中，该方法返回读取的实际字节数，如果读取的实际字节数小于n，一般是因为已读到指定输入流的末尾；

（2） int read()：即不带参数，该方法每次一个字节从指定的输入流中读取数据。返回值也是int类型，但它并不代表读取的字节数，而是从流中读取的数据的本身，因数据本身是byte类型的，所以一般要强制进行转化；如果读到流的末尾返回的值是-1；

（3） int read(byte buff[n],int start, int len):从指定流读取数据，从start开始，填充len个字节到buff中，返回值为实际的填充数，如果返回值 

## skip方法

skip 方法类似于C语言中的lseek都是用于定位的。Skip方法定义：long skip(long n)，该方法使指定流中的当前位置移动n个字节，n的值可以是负值用于向前移，skip方法返回值为实际移动的字节数，由于种种原因，如已到流尾或者其它原因返回的值往往小于n。对于读取文件来说，小于n的原因最大的原因是读到了文件尾，也可能是网络流中数据还没到达缓冲区。

## available方法

available方法用于计算指定流中当前有多少字节，如果指定的流是文件流，那么就返回文件的大小。Available返回的值是int类型。有的输入流可能没有能力返回字节数（比如网络流，并不能准确知道还有多少数据未到达），如果对这些输入流使用avaiable方法，返回值为0。

## close方法

关闭Stream，这样就不能对这个Stream进行上述操作，一个好的习惯就是使用完Stream后立马关闭，不要等到GC去回收。

## OutputStream

OutputStream位于Output Stream类层次的最顶层，它是一个抽象类，它规定了Output Stream类的基本功能。

## write

write方法与InputStream的read方法相对应，它有三个形态：

1. write(byte[])：将指定byte数组中的数据输出到指定Stream；
1. write(byte[],int,int)：将指定byte数组中的数据从第二个参数开始，输出第三个参数指定的长度到指定的Stream；
1. wirte(int)；将一个int值输出到指定的Stream； 

## flush和close

有些输出流在输出时先放在缓冲中，可以使用flush将这些数据真正写入指定的输出流中。Close用于关闭指定的输出流。 一般output流close的时候会自动flush. 

其他的Stream类型都是继承这两个，扩展了一些功能，使用方法大同小异。

{% highlight java %}

import java.io.*;

public class Main {


public static void main(String[] args) throws IOException {

	ReadFIle("d:\\test.txt");

	//下面的方法段演示了OutputStream的作用，功能是复制上面的文件内容至一个新文件
	CopyFile("d:\\test.txt", "d:\\test2.txt");

}

public static void ReadFIle(String filePath) throws IOException
{
	FileInputStream fStream = new FileInputStream(filePath);
	int c;
	int count = 0; //记录读取的字节数
	System.out.println("逐字节读取演示：如果为中文可能会乱码，因为编码问题，下同");
	while ( ( c = fStream.read()) != -1){  //read返回int型是为了确定文件末尾的magicNumber问题
	count ++;
	System.out.print((char)c);
}
System.out.print('\n');
System.out.println("（在读一遍）缓冲读取演示：");


fStream.skip(-count); //回到文件流开头

byte[] buffer = new byte[count / 3];   //使用文件字节数的1/3作为缓冲，体会缓冲的使用方法

int n = 0;
while((n= fStream.read(buffer,0,buffer.length)) != -1){
for (int i=0; i//因为读取出来的是byte型的，所以要先转成char输出.
{
	System.out.print((char)buffer[i]);
}
}
fStream.close();

}

public static void CopyFile(String srcFilePath,String dstFilePath) throws IOException
{
	System.out.println("源文件:" + srcFilePath);
	System.out.println("目标文件:" + dstFilePath);
	FileInputStream fStream = new FileInputStream(srcFilePath);
	File dstFile = new File(dstFilePath);
	if(!dstFile.exists())
	dstFile.createNewFile();               //如果文件不存在则创建新文件

	FileOutputStream fOutputStream = new FileOutputStream(dstFile);
	byte[] buffer = new byte[10];   //使用10字节作为缓冲，体会缓冲的使用方法
	int n = 0;
	int count = 0;
	do{
		n= fStream.read(buffer,0,buffer.length);
		System.out.print('.');    //打印一个点，显示正在复制中
		if(n == -1)
		break;
		count += n;
		fOutputStream.write(buffer);
		}while(true);

		System.out.print('\n');
		System.out.printf("复制完成,一共 %d 字节\n",count);
		fOutputStream.close();
		fStream.close();

	}

}

{% endhighlight %}
 
