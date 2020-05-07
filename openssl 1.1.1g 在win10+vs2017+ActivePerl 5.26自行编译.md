# openssl 1.1.1g编译 win10+vs2017+ActivePerl-5.26


**主要过程：**
1.下载openssl 1.1.1g源码
2.安装ActivePerl 5.26
3.perl Configure VC-WIN64A
4.nmake

 **主要大坑：**
1.cmdm命令行输入>perl Configure VC-WIN64A 报错说找不到文件
2.按照网上的nmake  –f  ms\ntdll.mak 以及nmake  –f  ms\ntdll.mak 也找不到文件无法执行

**解决过程：**
对于1，报错内容如下：
_Configuring OpenSSL version 1.1.1g (0x1010107fL) for VC-WIN64A
_Using os-specific seed configuration
Can't locate Win32/Console.pm in @INC (you may need to install the Win32::Console module) (@INC contains: C:/Users/wangle1/Downloads/openssl-1.1.1g.tar/openssl-1.1.1g/openssl-1.1.1g/util/perl C:/Perl64/site/lib C:/Perl64/lib) at C:/Perl64/lib/ActivePerl/Config.pm line 400.
Compilation failed in require at C:/Perl64/site/lib/ExtUtils/MakeMaker.pm line 9.
BEGIN failed--compilation aborted at C:/Perl64/site/lib/ExtUtils/MakeMaker.pm line 9.
Compilation failed in require at C:/Perl64/lib/IPC/Cmd.pm line 235.__

在报错目录中查找发现已经有了 C:/Perl64/site/lib/ExtUtils/MakeMaker.pm但是提示找不到
再看第一个错误位置C:/Perl64/lib/ActivePerl/Config.pm line 400
网上查找方案建议注释掉400行的这个函数内的code，**直接修改无法生效**，需要将Config.pm的read only 勾去掉，然后可以用nodepad++修改并保存

code如下：
_my $console;
sub _warn {
    #my($msg) = @_;
    #unless (-t STDOUT) {
	#print "\n$msg\n";
	#return;
    #}
    #require Win32::Console;
    #unless ($console) {
	#$console = Win32::Console->new(Win32::Console::STD_OUTPUT_HANDLE());
    #}
    #my($col,undef) = $console->Size;
    #print "\n";
    #my $attr = $console->Attr;
    #$console->Attr($Win32::Console::FG_RED | $Win32::Console::BG_WHITE);
    #for (split(/\n/, "$msg")) {
	#$_ .= " " while length() < $col-1;
	#print "$_\n";
    #}
    #$console->Attr($attr);
    #print "\n";
}_
再次执行->**perl Configure VC-WIN64A**
提示正常
继续下一步-> **nmake** 顺利编译，就是时间有点长

对于2，在网上查了很多资料，一般建议 此时执行 nmake  –f  ms\ntdll.mak 以及nmake  –f  ms\ntdll.mak 
可能由于版本不同，对于1.1.1g的ms文件夹中已经没有上述2个文件，因此无序指定，直接nmake即可


前前后后百度谷歌尝试也有快3消失了，特此记录。最大感受就是：如果第三方库版本号和网上教程不一样，务必注意由于版本升级带来的编译方式不匹配问题，最直接的办法就是先仔细看网上教程中命令行提到的文件，然后在要编译的源码文件中查找是否有对应的，如果没有，则说明这个教程不适合你。


下面2个是查找资料过程中觉得比较好的教程，虽然不适合我的版本，但还是贴出来供大家参考吧。

https://blog.csdn.net/astroyang/article/details/96828458?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5

https://blog.csdn.net/tianse12/article/details/72844231
