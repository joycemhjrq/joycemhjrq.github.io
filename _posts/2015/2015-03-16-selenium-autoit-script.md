---
layout: post
title: 关于Java（selenium）调用autoIt脚本的问题  
categories: autoIt
tags: 
- selenium
- autoIt
---

先来段autoit代码:

{% highlight java %}

Sleep($CmdLine[1]) 

{% endhighlight %}

编译成exe文件，文件名为sleep.exe，放在c盘根目录下，代码的功能很简单，就是根据传入的时间休眠。cmd下执行：c:\sleep.exe 2000，表示休眠2秒。 
我们在Java中调用这个程序:

{% highlight java %}

long time1 = System.currentTimeMillis();
try {
        Runtime.getRuntime().exec("c://sleep.exe 10000");
        long time2 = System.currentTimeMillis();
        System.out.println((time2 - time1) + "毫秒。");
} catch (Exception e) {
        e.printStackTrace();
}

{% endhighlight %}

用上面这段代码去调用的话，java不会等待autoit的执行，sleep启动后，继续往下执行，这样几乎没有实际的应用价值。我们通常都是要等autoit去把该做的事情都做完再做后面的事情。

{% highlight java %}

long time1 = System.currentTimeMillis();
try {
        Runtime.getRuntime().exec("c://sleep.exe 10000").waitFor();
        long time2 = System.currentTimeMillis();
        System.out.println((time2 - time1) + "毫秒。");
} catch (Exception e) {
        e.printStackTrace();
}

{% endhighlight %}

我们在代码中加入waitFor()，可以达到上面的效果，但是又带来了另外一个问题，玩意autoit执行出了问题，一直不退出怎么办？程序就hang住了。

今天在某QQ群中向高人讨教了一番，知道了如何控制autoit的超时问题，我们可以利用检查内存中是否存在au3进程的方式来做判断程序是否执行完毕，同时加一个超时控制，超时则继续往后执行，下面是demo 代码：

Java代码：

{% highlight java %}

import java.io.BufferedReader;
import java.io.File;
import java.io.InputStreamReader;
 
public class Runau3 {
    public static void main(String[] args) {
            // 执行au3脚本
            // 休眠的那个脚本，脚本所在的目录（秒），等待时间，脚本的休眠时间（毫秒）
            runAu3("sleep.exe", "c://", 5, "10000");
    }

    /**
     * 
     * @param filename
     *            au3的文件名，必须编译成exe
     * @param filepath
     *            au3的文件的路径
     * @param timeout
     *            超时时间（秒）
     * @param args
     *            au3的参数
     */
    public static void runAu3(String filename, String filepath, int timeout,
                    String... args) {
            try {
                    // 如果发现进程，先杀了
                    if (findProcess(filename)) {
                            killProcess(filename);
                    }
                    // 拼装命令
                    String cmd = filename;
                    for (String arg : args) {
                            cmd += " " + arg;
                    }

                    // 执行au3脚本
                    Runtime.getRuntime()
                                    .exec("cmd /C " + cmd, null, new File(filepath));
                    int count = 0;
                    while (findProcess(filename) && count < timeout) {
                            System.out.println(count);
                            Thread.sleep(1000);
                            count++;
                    }
                    // 如果进程未结束，杀了
                    if (findProcess(filename)) {
                            killProcess(filename);
                    }
            } catch (Exception e) {
                    e.printStackTrace();
            }

    }

    /**
     * 查找进程
     * @param processname
     * @return
     */
    public static boolean findProcess(String processname) {
            BufferedReader bufferedreader = null;
            try {
                    Process proc = Runtime.getRuntime().exec(
                                    "tasklist /fi \" imagename eq " + processname + " \" ");
                    bufferedreader = new BufferedReader(new InputStreamReader(proc
                                    .getInputStream()));
                    String line = null;
                    while ((line = bufferedreader.readLine()) != null) {
                            if (line.contains(processname)) {
                                    return true;
                            }
                    }
                    return false;
            } catch (Exception ex) {
                    ex.printStackTrace();
                    return false;
            } finally {
                    if (bufferedreader != null) {
                            try {
                                    bufferedreader.close();
                            } catch (Exception ex) {
                            }
                    }
            }
    }

    /**
     * 杀进程
     * @param processname
     */
    public static void killProcess(String processname) {
            BufferedReader bufferedreader = null;
            try {
                    Process proc = Runtime.getRuntime().exec(
                                    "taskkill /F /IM " + processname);
                    bufferedreader = new BufferedReader(new InputStreamReader(proc
                                    .getInputStream()));
                    String line = null;
                    while ((line = bufferedreader.readLine()) != null) {
                            System.out.println(line);
                    }
            } catch (Exception ex) {
                    ex.printStackTrace();
            } finally {
                    if (bufferedreader != null) {
                            try {
                                    bufferedreader.close();
                            } catch (Exception ex) {
                            }
                    }
            }
    }
}

{% endhighlight %}

end