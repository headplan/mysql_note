# 浅谈MySQL中utf8和utf8mb4的区别

#### **一.什么是UTF8MB4?**

我们在使用PhpMyAdmin管理面板时，可以在首页看到名为“Server connection collation”（服务器连接排序规则）的选项，用来选择所使用的字符集。对于我们常用的UTF8，却有utf8和utf8mb4两种，这是为什么呢？

![](http://7xomln.com1.z0.glb.clouddn.com/utf8-and-utf8mb4/server-connection-collation.jpg)

原来，MySQL在5.5.3版本之后增加了这个utf8mb4的编码，mb4就是most bytes 4的意思，专门用来兼容四字节的unicode。其实，utf8mb4是utf8的超集，理论上原来使用utf8，然后将字符集修改为utf8mb4，也会不会对已有的utf8编码读取产生任何问题。当然，为了节省空间，一般情况下使用utf8也就够了。

#### 二.为什么会有UTF8MB4?

既然utf8应付日常使用完全没有问题，那为什么还要使用utf8mb4呢? 低版本的MySQL支持的utf8编码，最大字符长度为 3 字节，如果遇到 4 字节的字符就会出现错误了。三个字节的 UTF-8 最大能编码的 Unicode 字符是 0xFFFF，也就是 Unicode 中的基本多文平面（BMP）。也就是说，任何不在基本多文平面的 Unicode字符，都无法使用MySQL原有的 utf8 字符集存储。这些不在BMP中的字符包括哪些呢？最常见的就是Emoji 表情（Emoji 是一种特殊的 Unicode 编码，常见于 ios 和 android 手机上），和一些不常用的汉字，以及任何新增的 Unicode 字符等等。

#### 三.扩展阅读:UTF-8编码

理论上将， UTF-8 格式使用一至六个字节，最大能编码 31 位字符。最新的 UTF-8 规范只使用一到四个字节，最大能编码21位，正好能够表示所有的 17个 Unicode 平面。

而utf8 则是 Mysql 早期版本中支持的一种字符集，只支持最长三个字节的 UTF-8字符，也就是 Unicode 中的基本多文本平面。这可能是因为在MySQL发布初期，基本多文种平面之外的字符确实很少用到。而在MySQL5.5.3版本后，要在 Mysql 中保存 4 字节长度的 UTF-8 字符，就可以使用 utf8mb4 字符集了。例如可以用utf8mb4字符编码直接存储emoj表情，而不是存表情的替换字符。

为了获取更好的兼容性，应该总是使用 utf8mb4 而非 utf8，事实上，最新版的phpmyadmin默认字符集就是utf8mb4。诚然，对于 CHAR 类型数据，使用utf8mb4 存储会多消耗一些空间。

