1.控制命令



Ctrl + l：清屏（与clear命令效果相同）

Ctrl + o：执行当前命令，并选择上一条命令

Ctrl + s：阻止屏幕输出(当前正在执行的命令不在打印信息)

Ctrl + q：允许屏幕输出(使用Ctrl+s命令后，可以用Ctrl+q恢复)

Ctrl + c：终止当前正在执行的命令

Ctrl + z：挂起命令，把当前进程转到后台运行，使用fg命令恢复。

Ctrl + d : 退出当前 Shell S、Q、C、Z 是由终端设备处理的，可用 stty 命令设置。



2.编辑命令光标移动



Ctrl + a ：移到命令行首

Ctrl + e ：移到命令行尾

Ctrl + f ：前移（向右移动）一个字符

Ctrl + b ：后退（向左移动）一个字符

Alt + f ：前移（向右移动）一个单词

Alt + b ：后退（向左移动）一个单词

Ctrl + xx：在命令行首和光标之间移动



3.文本修改



tab : 自动补全命令

Ctrl + u ：从光标处删除至命令行首

Ctrl + k ：从光标处删除至命令行尾

Ctrl + w ：从光标处删除至字首

Alt + d ：从光标处删除至字尾

Ctrl + d ：删除光标处（或光标后）的字符（如果光标前后都没有字符，即命令行为空的时候，则会退出shell）

Ctrl + h ：删除光标前的字符(与backspace键相同)

Alt + Backspace：与 Ctrl + w 类似，分隔符有些差别

Ctrl + y ：粘贴至光标后



4.改变大小写



Alt + c ：从光标处更改为首字母大写的单词

Alt + u ：从光标处更改为全部大写的单词

Alt + l ：从光标处更改为全部小写的单词



5.交换字符、单词位置

Ctrl + t ：交换光标处和之前的字符（ESC+t相同）

Alt + t ：交换光标处和之前的单词



6.重新执行命令



Ctrl + p：历史中的上一条命令

Ctrl + n：历史中的下一条命令

Alt + .：使用上一条命令的最后一个参数（会直接在当前光标位置显示）

Ctrl + r：搜索之前使用过的命令

Ctrl + g：从历史搜索模式退出



7.Bang (!) 命令



Bang命令算不上快捷键键，但是使用可以快捷的进行一些操作，比如重新执行之前命令、修改上一条命令并执行等等。



!cc：执行最近的以cc开头的命令，如!l会执行ls命令



!$：打印上一条命令的最后一个参数，并回车执行。与Alt + .相似，但是会自动执行



!*：上一条命令的所有参数 !cc:p：仅打印以!cc的输出，但不执行，如!l:p会显示ls



!: p ： 打 印 输 出 ! :p：打印输出!:p：打印输出!的输出



^blah：删除上一条命令中第一个blah，然后执行



blahfoo：将上一条命令中的 blah 替换为 foo，然后执行



blahfoo^：将上一条命令中所有的 blah 都替换为 foo，然后执行



1_ctrl键组合



ctrl+a:光标移到行首。

ctrl+e:光标移到行尾。



ctrl+b:光标左移一个字母, 向行首移动一个字符



ctrl+f: 光标右移,向行尾移动一个字符



ctrl+c:杀死当前进程。

ctrl+d:退出当前 Shell,从Shell提示中注销并关闭，使用该快捷键就不必键入exit

ctrl+h:删除光标前一个字符，同 backspace 键相同。

ctrl+l:清屏，相当于clear。

ctrl+r:搜索之前打过的命令。会有一个提示，根据你输入的关键字进行搜索bash的history



ctrl+k:清除光标后至行尾的内容。

ctrl+u: 清除光标前至行首间的所有内容。

ctrl+w: 移除光标前的一个单词

ctrl+t: 交换光标位置前的两个字符(包括光标所在的当前位置)

ctrl+y: 粘贴或者恢复上次的删除

ctrl+d: 删除光标所在字母;注意和backspace以及ctrl+h的区别，这2个是删除光标前的字符

ctrl+z : 把当前进程转到后台运行，使用’ fg ‘命令恢复。比如top -d1 然后ctrl+z ，到后台，然后fg,重新恢复



2_esc组合

esc+d: 删除光标后的一个词

esc+f: 往右跳一个词

esc+b: 往左跳一个词

esc+t: 交换光标位置前的两个单词。



4_ Shell 脚本

$0为程序名称，$1为第一个参数，$2为第二个参数，依次类推，直到$9为第九个参数。

