---
layout: post
title: junit Task
categories: junit
tags: junit
---

<http://ant.apache.org/manual/Tasks/junit.html>

Description

This task runs tests from the JUnit testing framework. The latest version of the framework can be found at http://www.junit.org. This task has been tested with JUnit 3.0 up to JUnit 3.8.2; it won't work with versions prior to JUnit 3.0. It also works with JUnit 4.0, including "pure" JUnit 4 tests using only annotations and no JUnit4TestAdapter.

Note: This task depends on external libraries not included in the Apache Ant distribution. See Library Dependencies for more information.

Note: You must have junit.jar available. You can do one of:

    1. Put both junit.jar and ant-junit.jar in ANT_HOME/lib.
    2. Do not put either in ANT_HOME/lib, and instead include their locations in your CLASSPATH environment variable.
    3. Add both JARs to your classpath using -lib.
    4. Specify the locations of both JARs using a element in a in the build file.
    5. Leave ant-junit.jar in its default location in ANT_HOME/lib but include junit.jar in the passed to . (since Ant 1.7)

See the FAQ for details.

Tests are defined by nested test or batchtest tags (see nested elements).

<b>Parameters</b>

<table border="1" cellpadding="2" cellspacing="0">
<tbody>
<tr>
<td valign="top" width="12%"><b>Attribute</b></td>
<td valign="top" width="78%"><b>Description</b></td>
<td valign="top" width="10%"><b>Required</b></td>
</tr>
<tr>
<td valign="top">printsummary</td>
<td valign="top">Print one-line statistics for each testcase. Can
take the values <code>on</code>, <code>off</code>, and
<code>withOutAndErr</code>. <code>withOutAndErr</code> is the same
as <code>on</code> but also includes the output of the test as
written to <code>System.out</code> and
<code>System.err</code>.</td>
<td align="center" valign="top">No; default is
<code>off</code>.</td>
</tr>
<tr>
<td valign="top">fork</td>
<td valign="top">Run the tests in a separate VM.</td>
<td align="center" valign="top">No; default is
<code>off</code>.</td>
</tr>
<tr>
<td valign="top">forkmode</td>
<td valign="top">Controls how many Java Virtual Machines get
created if you want to fork some tests. Possible values are
"perTest" (the default), "perBatch" and "once". "once" creates only
a single Java VM for all tests while "perTest" creates a new VM for
each TestCase class. "perBatch" creates a VM for each nested and
one collecting all nested s. Note that only tests with the same
settings of <code>filtertrace</code>, <code>haltonerror</code>,
<code>haltonfailure</code>, <code>errorproperty</code> and
<code>failureproperty</code> can share a VM, so even if you set
<code>forkmode</code> to "once", Ant may have to create more than a
single Java VM. This attribute is ignored for tests that don't get
forked into a new Java VM. <em>since Ant 1.6.2</em></td>
<td align="center" valign="top">No; default is
<code>perTest</code>.</td>
</tr>
<tr>
<td valign="top">haltonerror</td>
<td valign="top">Stop the build process if an error occurs during
the test run.</td>
<td align="center" valign="top">No; default is
<code>off</code>.</td>
</tr>
<tr>
<td valign="top">errorproperty</td>
<td valign="top">The name of a property to set in the event of an
error.</td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">haltonfailure</td>
<td valign="top">Stop the build process if a test fails (errors are
considered failures as well).</td>
<td align="center" valign="top">No; default is
<code>off</code>.</td>
</tr>
<tr>
<td valign="top">failureproperty</td>
<td valign="top">The name of a property to set in the event of a
failure (errors are considered failures as well).</td>
<td align="center" valign="top">No.</td>
</tr>
<tr>
<td valign="top">filtertrace</td>
<td valign="top">Filter out Junit and Ant stack frames from error
and failure stack traces.</td>
<td align="center" valign="top">No; default is
<code>on</code>.</td>
</tr>
<tr>
<td valign="top">timeout</td>
<td valign="top">Cancel the individual tests if they don't finish
in the given time (measured in milliseconds). Ignored if
<code>fork</code> is disabled. When running multiple tests inside
the same Java VM (see forkMode), timeout applies to the time that
all tests use together, not to an individual test.</td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">maxmemory</td>
<td valign="top">Maximum amount of memory to allocate to the forked
VM. Ignored if <code>fork</code> is disabled.
<strong>Note</strong>: If you get <code>java.lang.OutOfMemoryError:
Java heap space</code> in some of your tests then you need to raise
the size like <code>maxmemory="128m"</code></td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">jvm</td>
<td valign="top">The command used to invoke the Java Virtual
Machine, default is 'java'. The command is resolved by
<code>java.lang.Runtime.exec()</code>. Ignored if <code>fork</code>
is disabled.</td>
<td align="center" valign="top">No; default is
<code>java</code>.</td>
</tr>
<tr>
<td valign="top">dir</td>
<td valign="top">The directory in which to invoke the VM. Ignored
if <code>fork</code> is disabled.</td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">newenvironment</td>
<td valign="top">Do not propagate the old environment when new
environment variables are specified. Ignored if <code>fork</code>
is disabled.</td>
<td align="center" valign="top">No; default is
<code>false</code>.</td>
</tr>
<tr>
<td valign="top">includeantruntime</td>
<td valign="top">Implicitly add the Ant classes required to run the
tests and JUnit to the classpath in forked mode.</td>
<td align="center" valign="top">No; default is
<code>true</code>.</td>
</tr>
<tr>
<td valign="top">showoutput</td>
<td valign="top">Send any output generated by tests to Ant's
logging system as well as to the formatters. By default only the
formatters receive the output.</td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">outputtoformatters</td>
<td valign="top"><em>Since Ant 1.7.0.</em><br>
Send any output generated by tests to the test formatters. This is
"true" by default.</td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">tempdir</td>
<td valign="top">Where Ant should place temporary files. <em>Since
Ant 1.6</em>.</td>
<td align="center" valign="top">No; default is the project's base
directory.</td>
</tr>
<tr>
<td valign="top">reloading</td>
<td valign="top">Whether or not a new classloader should be
instantiated for each test case.<br>
Ignore if <code>fork</code> is set to true. <em>Since Ant
1.6</em>.</td>
<td align="center" valign="top">No; default is
<code>true</code>.</td>
</tr>
<tr>
<td valign="top">clonevm</td>
<td valign="top">If set to true true, then all system properties
and the bootclasspath of the forked Java Virtual Machine will be
the same as those of the Java VM running Ant. Default is "false"
(ignored if fork is disabled). <em>since Ant 1.7</em></td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">logfailedtests</td>
<td valign="top">When Ant executes multiple tests and doesn't stop
on errors or failures it will log a "FAILED" message for each
failing test to its logging system. If you set this option to
false, the message will not be logged and you have to rely on the
formatter output to find the failing tests. <em>since Ant
1.8.0</em></td>
<td align="center" valign="top">No</td>
</tr>
<tr>
<td valign="top">enableTestListenerEvents<wbr></td>
<td valign="top">Whether Ant should send fine grained information
about the running tests to Ant's logging system at the verbose
level. Such events may be used by custom test listeners to show the
progress of tests.<br>
Defaults to <code>false</code>.<br>
Can be overridden by a <a href="http://ant.apache.org/manual/Tasks/junit.html#enabletestlistenerevents">
magic property</a>.<br>
<em>since Ant 1.8.2</em> - <strong>Ant 1.7.0 to 1.8.1 behave as if
this attribute was true by default.</strong></td>
<td align="center" valign="top">No</td>
</tr>
</tbody>
</table>

By using the errorproperty and failureproperty attributes, it is possible to perform setup work (such as starting an external server), execute the test, clean up, and still fail the build in the event of a failure.

The filtertrace attribute condenses error and failure stack traces before reporting them. It works with both the plain and XML formatters. It filters out any lines that begin with the following string patterns:

    "junit.framework.TestCase"
    "junit.framework.TestResult"
    "junit.framework.TestSuite"
    "junit.framework.Assert."
    "junit.swingui.TestRunner"
    "junit.awtui.TestRunner"
    "junit.textui.TestRunner"
    "java.lang.reflect.Method.invoke("
    "sun.reflect."
    "org.apache.tools.ant."
    "org.junit."
    "junit.framework.JUnit4TestAdapter"

Nested Elements

The task supports a nested element that represents a PATH like structure.

As of Ant 1.7, this classpath may be used to refer to junit.jar as well as your tests and the tested code.
jvmarg

If fork is enabled, additional parameters may be passed to the new VM via nested elements. For example:

  ...

would run the test in a VM without JIT.

allows all attributes described in Command-line Arguments.
sysproperty

Use nested elements to specify system properties required by the class. These properties will be made available to the VM during the execution of the test (either ANT's VM or the forked VM, if fork is enabled). The attributes for this element are the same as for environment variables.

  ...

would run the test in ANT's VM and make the basedir property available to the test.