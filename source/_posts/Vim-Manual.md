
title: vim使用总结笔记
date: 2017-5-13 20:18
tags: vim
---
<font style="color:black; background:yellow">Toggle Vrapper,sublime Text,IdeaVim,Chrome具有vim插件</font>
===

chrome 的vimium 使用shift+? 进行提示  具体参考里面的快捷键
---

1.[vimium](https://github.com/582033/vim) 的基本入门使用 `* j [japan在 k Korea在下面] `
	* u pageUp  d pageDown
	* o 查找或者打开页面
	* x 退出  X还原
	* f 查询 --->很关键
	* 有个很重要的是f6在地址栏获取焦点,tab键可以取消默认的搜索焦点然后按f
	* shift +J 往左  K 往右
	* g0 第一个tab  t 开启一个新的tab页面
	* g$ 最后一个tab
	* H 返回history L 跳转当前
	* t 新开一个tab
	* T 查找开启的tab
	* 但如果完全失灵了 可以使用ctrl +W 实现关闭浏览器窗口/chrome



<!--more-->



[vim 初心者-Master的使用和dotfile](https://github.com/grigio/vim-sublime)
---

1. vim8.0在window可以通过git bash自带的练习

	* vim + selfNote.md 定位到bottom 底部
	* vim +12 第12 行打开   +/ettercap 不能有空格
	* **a** append 在光标后面插入
	* i 在光标前面插入insert  [A在行尾] [I 在首部][O 在行上方插入一行]
	* o 在光标所在行的下方 新建一行 转化为输入模式
	* 编辑模式转化成插入模式
	* 插入模式 转化成 编辑模式esc
	* 编辑转化末行模式 : 10,20d   !ls/etc
	* :wq ==:x 保存退出
* 编辑模式下ZZ 保存退出
1. 以逐一字符为单位移动 h 左  l 右
	* j [japan在K Korea在下面]
	* :help tutor 查看帮助文档
2. 以单词为单位移动
	1. w 移至下一个档次的词首   3w
	2. e 跳至当前或者下一个单词的词尾
	3. b跳至当前或前一个单词的词首
3. 行内跳转
	1. 0 绝对行首 开头
	2. $ 绝对行尾
	3. ^ 行首的第一个非空白字符
4. 行间跳转
5. `4G` 跳转到第4行
	G 最后一行
	末行模式直接输入: 50G向下跳转就可以了
6. 翻屏
	1. ctrl f 向下翻屏
	2. ctrl b 向上翻屏
	3. ctrl d 向下翻半屏
	4. crtl u 向上翻半屏
7. 删除单个字符
	* x 删除光标所在处的单个字符  3x 删除光标后面3个字符
8. 删除命令d
	d 命令跟跳转命令组合使用
	3dw de db
	2dd 删除当前光标所在2行
9. 末行模式下删除,删除的可以撤销u,存在寄存器中,最后一次的d可以哟哦你过来粘贴
	. 表示当前行  1,+3d 表示向下共删除4行
	$ 最后一行
	: .,$-1 从光标行删除到末行

10. 粘贴命令p
	p如果删除或复制的是整行,则粘贴在光标所在行的下方,如果复制或删除的是非整行,则粘贴在后面 P 相反
11.  复制命令y
	用法同d 命令
	3yy  基本和d 相同
12. 修改 先删除 内容,在进入insert 插入模式
	c 用法同d 命令  c$  删除到末尾并进入插入模式
13. 替换使用r 6ra 替换单个字符
	R 进入替换模式,一直进入修改
14. 撤销标记操作u
	1. u撤销前一次编辑操作 连续u 可撤销n<50次 默认情况下
	2.  3u 直接撤销最近3次的编辑操作
	3.  ctrl r 撤销最近一次的撤销操作
15. 重复前一次编辑操作 [乐芙兰.] .
16. 可视化模式
	1. v 按字符选取
	2. V 按矩形选取
17. 查找替换末行模式使用
	/Pattern
	?pattern 模糊匹配
	n 向上  N 向下匹配
18. 查找并替换s 命令 gi 全局忽略大小写
	起始地址,结束地址@pattern@string@gi
	: .,$-1s/id/ID/gi
---



        ```shell
        1、vim 单文件中查找方法

        正常模式下使用 / 或 ? 命令执行向后搜索或向前搜索

        /love	从光标位置向前搜索关键词 love
        ?love	从光标位置向后搜索关键词 love

        正常模式下使用 n 或 N 命令执行 向前查找 或 向后查找 下一个关键词

        :set ic		忽略大小写命令，ic 为 ignore case 缩写
        :set is		边输入边显示匹配结果，is 是 incsearch 命令的缩写
        :set hls	高亮显示匹配结果，hls 为 highlight search 缩写

        可将上述设置放入.vimrc 文件中，设为默认搜索设置

        :set noic nois nohls	将取消上述设置

        :noh	暂时取消高亮显示，不会影响下次高亮显示匹配结果


        2、vim 多文件查找方法

        正常模式下使用 :vim 或 :vimgrep 命令执行多文件搜索

        搜索的时候涉及到选取文件问题，这里主要用到 * 作为通配符
        ** 表示文件夹及子文件夹下的所有文件

        搜索 love 关键词
        :vim/love/*						当前文件夹下的所有文件
        :vim/love/**					当前文件夹及子目录下的所有文件
        :vim/love/**/*.php				当前文件夹及子目录下的所有 php 文件
        :vim/love/*.php aa/**/*.php		当前文件夹下的 php 文件和 aa 目录及子目录下的 php 文件

        使用 ** 和 * 可任意组合出想要搜索的文件

        :cw			查看搜索结果
        :ccl		关闭搜索结果
        :cn			查找下一个
        :cp			查找上一个


        3、vim 单文件替换方法

        单文件中使用 :s 命令进行替换操作

        替换操作会涉及几个常用的标记
        % 所有行  g 单行内所有匹配项  i 忽略大小写  c  确认是否替换

        关键词 old 替换为 new
        :s/old/new				将光标所在行第一个 old 替换为 new
        :s/old/new/g			将光标所在行所有 old 替换为 new
        :%s/old/new/gc			全文执行替换,询问是否替换
        :3,10s/old/new/gic		将第3行到第10行内容替换，忽略大小写，且每个询问是否替换

        g i c 可自由组合




    	4. vim 多文件操作
    	设置vim中默认使用选择缓冲区寄存器 “*：

    	set clipboard = unnamed
    	则可以直接通过y,p和系统选择缓冲区进行数据交换。

    	Vimer们对高效的追求永无止境。还有其他更好地方法吗？

    	你可能想到了，vim寄存器。对，使用vim寄存器 “+p 粘贴即可。根本不用考虑是否自动缩进，是否paste模式，直接原文传递！:

    	"+p
    	要说vim寄存器，就要从vim文件间的复制粘贴说起。

    	Vim中，若要复制当前行，普通模式下按 yy 即可，在要粘贴的地方按 p 。这是vim将复制内容保存到了自己的寄存器中的缘故。如果在其他地方执行yy，新的内容将覆盖掉原寄存器中内容。如果想保存原寄存器中内容而同时增加新 的内容呢？这时就要在yy前增加标签了。标签以双引号开始，跟着的是标签名称，可以是数字0-9，也可以是26个字母，然后就是复制操作，这样就把复制内 容保存到该标签寄存器里。通过下面命令显示所有寄存器内容：:

    	:reg
    	其中注意两个特殊的寄存器：”* 和 “+。这两个寄存器是和系统相通的，前者关联系统选择缓冲区，后者关联系统剪切板。通过它们可以和其他程序进行数据交换。

    	备注：
    	若寄存器列表里无”* 或 “+ 寄存器，则可能是由于没有安装vim的图形界面所致。Debian/Ubuntu下可以通过安装vim-gnome解决。

    	$ sudo apt-get install vim-gnome
    	选择缓冲区和系统剪切板啥子区别？让我们继续研究。

    	选择缓冲区和剪切板
    	不同于Windows，Linux系统里存在两个剪切板：一个叫做选择缓冲区(X11 selection buffer)，另一个才是剪切板(clipboard)。

    	选择缓冲区是实时的，当使用鼠标或键盘选择内容时，内容已经存在于选择缓冲区了，这或许就是选择缓冲区的由来吧。

    	使用下面的命令查看选择缓冲区的内容：:

    	$ xclip -out
    	如果没有xclip命令，Debian/Ubuntu下可以通过如下命令安装：:

    	$ sudo apt-get install xclip
    	可以使用鼠标中键或键入Shift+Insert来粘贴选择缓冲区的内容。但对于有些GUI程序，比如gedit，只能通过鼠标中键调用选择缓冲区的内容，使用Shift+Insert的话，调用的是剪切板的内容。

    	剪切板和Windows的剪切板类似，在选择文字内容后，执行Ctrl + c或在菜单里选择‘复制’的话，这时内容才存放到剪切板里。

    	使用下面的命令查看剪切板的内容：:

    	$ xclip -out -sel clipboard
    	而使用剪切板的内容，则是Ctrl+v。 但在有些情况下，比如gnome-terminal，不能直接使用Ctrl+c，Ctrl+v，这时就要用Shift+Ctrl+c，Shift+Ctrl+v代替。

    	原格式粘贴
    	好了，了解了选择缓冲区和剪切板，下面就是实现保留格式粘贴的完美解决方案：

        ```



vim文件间复制粘贴的完美方案，文件传输的中转使用选择缓冲区。
---




1. **vim 编辑多个文件`:vsplit   :sp 水平分割 垂直切分窗口  Ctrl+6  两文件间的切换`**
	* 在终端里输入
	vim file1 file2 ... filen便可以打开所有想要打开的文件
	* vim已经启动
	输入 	:` e  filelocation like  : e /etc/nginx.conf`
	可以再打开一个文件，并且此时vim里会显示出file文件的内容。
	* 同时显示多个文件：
	:sp         //水平切分窗口
	:vsplit     //垂直切分窗口

	* Ctrl+6  //两文件间的切换
	:bn      //下一个文件
	:bp      //上一个文件
	:ls       //列出打开的文件，带编号
	:b1~n  //切换至第n个文件
	对于用(v)split在多个窗格中打开的文件，这种方法只会在当前窗格中切换不同的文件。
	* 在窗格间切换的方法
	Ctrl+w+方向键——切换到前／下／上／后一个窗格
	Ctrl+w+h/j/k/l ——同上
	Ctrl+ww——依次向后切换到下一个窗格中
2. 多文件复制粘贴使用寄存器
	* +y3y 复制三行
	* +p  粘贴  gp最后一行
3. who |wc -l  5人同时登陆在线  last
   tail -f catalina.out 打印log

    ```

        方案一：
        选择文本内容
        vim普通模式下按 “*p 将选择缓冲区中内容粘贴进来
        方案二：
        复制文件内容
        vim普通模式下按 “+ p 将剪切板内容粘贴进来

    ```


注意git配置windows& unix(macos) 之间差异
---



1.Git设置

git config --global core.autocrlf false
git config --global core.safecrlf true

2.AutoCRLF提交时转换为LF，检出时转换为CRLF
git config --global core.autocrlf true

