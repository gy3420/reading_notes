#### 8.从源文件得可执行文件
* 编译器-》操作系统，编程语言，CPU架构共同决定
* 链接将目标文件连接到obj文件，比如引用库函数printf,那么他在所属的lib文件(目标文件的集合)，链接时直接抽取lib的目标文件，连接在obj里。
并且lib文件也不用公开源码了。有一些目标文件并不是存储在库文件里的，比如API函数，他在lib中，但lib里存储两个信息，非实体，
一是告诉该api在某个dll里，二是dll在哪里的信息。我们把这样的lib叫导入库，这样程序运行时，结合导入库文件，
直接在dll调出函数与exe结合，相反存储目标文件实体的是静态链接库。

来我们看个图：

源文件.c->编译成 .obj->加启动用的(main函数).obj->静态链接库(库函数)->导入库(比如API之类由动态链接库提供)
-》可执行的exe-》运行的exe（执行时将动态链接库中抽出目标文件）

可执行的exe文件中实际分配给的是虚拟的地址，运行时在转换成实际的内存。
exe链接后的形式是这样的， 再配置信息，(变量组基址)变量1,2,3。。。(函数组基址)函数1,2,3.。。
实际运行时基址转化为实际分配的内存。加载到内存中为 复制exe部分的用于变量的空间和用于函数的空间，运行时分配的堆和栈。

叠加链接：将不会同时运行的函数，交替加载到同一个内存时运行，在内存不足时经常用叠加链接。****