---
layout: post
title: ant java main 互传参数
categories: ant
tags: ant
---

## Ant的数据类型

在构建文件中为了标识文件或文件组，经常需要使用数据类型。数据类型包含在org.apache.tool.ant.types包中。下面镜简单介绍构建文件中一些常用的数据类型。

### 1. argument 类型

由Ant构建文件调用的程序，可以通过<arg>元素向其传递命令行参数，如apply,exec和java任务均可接受嵌套<arg>元素，可以为各自的过程调用指定参数。以下是<arg>的所有属性。

    (1).values 是一个命令参数。如果参数种有空格，但又想将它作为单独一个值，则使用此属性。
    (2).file表示一个参数的文件名。在构建文件中，此文件名相对于当前的工作目录。
    (3).line表示用空格分隔的多个参数列表。
    (4).path表示路径。

如果只传一个参数，这个参数包含有空格的话，可以用value，如果要传多个参数就用line，参数之间用空格格开

### 2.ervironment 类型

由Ant构建文件调用的外部命令或程序，<env>元素制定了哪些环境变量要传递给正在执行的系统命令，<env>元素可以接受以下属性。

    (1).file表示环境变量值得文件名。此文件名要被转换位一个绝对路径。
    (2).path表示环境变量的路径。Ant会将它转换为一个本地约定。
    (3).value 表示环境变量的一个直接变量。
    (4).key 表示环境变量名。

注意  file path 或 value只能取一个。

### 3.filelist类型

Filelist 是一个支持命名的文件列表的数据类型，包含在一个filelist类型中的文件不一定是存在的文件。以下是其所有的属性。

    (1).dir是用于计算绝对文件名的目录。
    (2).files 是用逗号分隔的文件名列表。
    (3).refid 是对某处定义的一个<filelist>的引用。

注意  dir 和 files 都是必要的，除非指定了refid(这种情况下，dir和files都不允许使用)。

### 4.fileset类型

Fileset 数据类型定义了一组文件，并通常表示为<fileset>元素。不过，许多ant任务构建成了隐式的fileset,这说明他们支持所有的fileset属性和嵌套元素。以下为fileset 的属性列表。

    (1).dir表示fileset 的基目录。
    (2).casesensitive的值如果为false，那么匹配文件名时，fileset不是区分大小写的，其默认值为true.
    (3).defaultexcludes 用来确定是否使用默认的排除模式，默认为true。
    (4).excludes 是用逗号分隔的需要派出的文件模式列表。
    (5).excludesfile 表示每行包含一个排除模式的文件的文件名。
    (6).includes 是用逗号分隔的，需要包含的文件模式列表。
    (7).includesfile 表示每行包括一个包含模式的文件名。

### 5.patternset 类型

Fileset 是对文件的分组，而patternset是对模式的分组，他们是紧密相关的概念。

<patternset>支持4个属性：includes excludex includexfile 和 excludesfile,与fileset相同。Patternset 还允许以下嵌套元素：include,exclude,includefile 和 excludesfile.

### 6.filterset 类型

Filterset定义了一组过滤器，这些过滤器将在文件移动或复制时完成文件的文本替换。

主要属性如下：

    (1).begintoken 表示嵌套过滤器所搜索的记号，这是标识其开始的字符串。
    (2).endtoken表示嵌套过滤器所搜索的记号这是标识其结束的字符串。
    (3).id是过滤器的唯一标志符。
    (4).refid是对构建文件中某处定义一个过滤器的引用。

### 7.Path类型

Path元素用来表示一个类路径，不过它还可以用于表示其他的路径。在用作揖个属性时，路经中的各项用分号或冒号隔开。在构建的时候，此分隔符将代替当前平台中所有的路径分隔符，其拥有的属性如下。

    (1).location 表示一个文件或目录。Ant在内部将此扩展为一个绝对路径。
    (2).refid 是对当前构建文件中某处定义的一个path的引用。
    (3).path表示一个文件或路径名列表。

### 8.mapper类型

Mapper类型定义了一组输入文件和一组输出文件间的关系，其属性如下。

    (1).classname 表示实现mapper类的类名。当内置mapper不满足要求时，用于创建定制mapper.
    (2).classpath表示查找一个定制mapper时所用的类型路径。
    (3).classpathref是对某处定义的一个类路径的引用。
    (4).from属性的含义取决于所用的mapper.
    (5).to属性的含义取决于所用的mapper.
    (6).type属性的取值为identity，flatten glob merge  regexp  其中之一，它定义了要是用的内置mapper的类型。

Ant 的运行

安装好Ant并且配置好路径之后，在命令行中切换到构建文件的目录，输入Ant命令就可以运行Ant.若没有指定任何参数，Ant会在当前目录下查询build.xml文件。如果找到了就用该文件作为构建文件。如果使用了 –find 选项，Ant 就会在上级目录中找构建文件，直至到达文件系统得跟目录。如果构建文件的名字不是build.xml ，则Ant运行的时候就可以使用 –buildfile file ,这里file 指定了要使用的构建文件的名称，示例如下：

Ant如下说明了表示当前目录的构建文件为build.xml 运行 ant 执行默认的目标。

Ant –buildfile test.xml：使用当前目录下的test.xml 文件运行Ant ,执行默认的目标   