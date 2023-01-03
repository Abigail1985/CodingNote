[environment variables - What does "LC_ALL=C" do? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do)


在shell脚本中常见到如下语句:
export LC_ALL=C
这一句的作用是为了去除所有本地化的设置，让命令能正确执行。

Linux的运行环境

在Linux中通过locale来设置程序运行的不同语言环境，locale由ANSI C提供支持。

locale的命名规则为<语言>_<地区>.<字符集编码>，如zh_CN.UTF-8，zh代表中文，CN代表大陆地区，UTF-8表示字符集。在locale环境中，有一组变量，代表国际化环境中的不同设置：

LC_COLLATE
定义该环境的排序和比较规则
LC_CTYPE
用于字符分类和字符串处理，控制所有字符的处理方式，包括字符编码，字符是单字节还是多字节，如何打印等。是最重要的一个环境变量。
LC_MONETARY
货币格式
LC_NUMERIC
非货币的数字显示格式
LC_TIME
时间和日期格式
LC_MESSAGES
提示信息的语言。另外还有一个LANGUAGE参数，它与LC_MESSAGES相似，但如果该参数一旦设置，则LC_MESSAGES参数就会失效。LANGUAGE参数可同时设置多种语言信息，如LANGUANE=“zh_CN.GB18030:zh_CN.GB2312:zh_CN”。
LANG
LC_*的默认值，是最低级别的设置，如果LC_*没有设置，则使用该值。类似于 LC_ALL。
LC_ALL
它是一个宏，如果该值设置了，则该值会覆盖所有LC_*的设置值。注意，LANG的值不受该宏影响。
C"是系统默认的locale，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的locale就是C或POSIX。
查看本地默认设置

在shell控制台中输入locale就可以查看本地默认设置, 例如

```
$ locale
LANG=en_GB.UTF-8
LANGUAGE=
LC_CTYPE="en_GB.UTF-8"
LC_NUMERIC="en_GB.UTF-8"
LC_TIME="en_GB.UTF-8"
LC_COLLATE="en_GB.UTF-8"
LC_MONETARY="en_GB.UTF-8"
LC_MESSAGES="en_GB.UTF-8"
LC_PAPER="en_GB.UTF-8"
LC_NAME="en_GB.UTF-8"
LC_ADDRESS="en_GB.UTF-8"
LC_TELEPHONE="en_GB.UTF-8"
LC_MEASUREMENT="en_GB.UTF-8"
LC_IDENTIFICATION="en_GB.UTF-8"
LC_ALL=
```

