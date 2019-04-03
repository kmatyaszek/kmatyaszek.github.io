---
title: "Visual Studio 2013 designer cache & disk space leak"
tags:
  - WPF
  - VS2013
---
I have recently encountered a problem with using VS2013 designer when I develop WPF application. Problem is with a designer cache which is not cleared out. After about three months I have ended up with 0 bytes on my C drive and I was not able to do anything. When I started searching for the possible cause of this situation I found information about the bug in VS2013 connected with the correctly delete cache data from disk. In my case, about 30GB of storage was being used by VS2013 designer cache.

If you have the same problem you can just delete everything from the directory:
<pre><code class="accesslog">%LOCALAPPDATA%\Microsoft\VisualStudio\12.0\Designer\ShadowCache\</code></pre>

But if you don't want to lose again all space on your hard drive you can create a script and run it via TaskScheduler. Please follow the below steps.


<ol>
  <li>Create a batch script with the following code.
<pre><code class="Bash">set cacheLocation="%LOCALAPPDATA%\Microsoft\VisualStudio\12.0\Designer\ShadowCache"
cd /d %cacheLocation%
for /F "delims=" %%i in ('dir /b') do (rmdir "%%i" /s/q || del "%%i" /s/q)</code></pre></li>
  <li>Open TaskScheduler (press the <strong>Windows key + R</strong> to open the Run box, type <strong>taskschd.msc</strong> and press Enter).
  <img src="/images/posts/mmc_2018-08-13_04-51-36.png" class="align-center" alt=""></li>
  <li>From Actions menu select Create Basic Task, fill name and description and click Next button.
  <img src="/images/posts/mmc_2018-08-13_05-02-13.png" class="align-center" alt="">
  <img src="/images/posts/mmc_2018-08-13_05-07-40.png" class="align-center" alt=""></li>
  <li>Next, you can choose when do you want the task to start. In my case, I selected "When I log on".
  <img src="/images/posts/mmc_2018-08-13_05-03-42.png" class="align-center" alt=""></li>
  <li>In the next step, you can select what action will be performed. In this step select "Start a program".
  <img src="/images/posts/mmc_2018-08-13_05-03-50.png" class="align-center" alt=""></li>
  <li>After that select location of your batch script created in step 1 and click Next button.
  <img src="/images/posts/mmc_2018-08-13_05-04-07.png" class="align-center" alt=""></li>
  <li>In the last page, you can see a summary of your action in the previous pages. Click finish if everything is fine.
  <img src="/images/posts/mmc_2018-08-13_05-04-22.png" class="align-center" alt=""></li>
</ol>
