---
layout: post
title: 📝 Character Set and Encoding
categories: codetips
---

# 字符集与编码

## 字符集和编码

字符集(Character Set)是字符的集合，定义系统能处理哪些字符；编码(Encoding)则规定这些字符在计算机内部的表示方式。

这里字符是抽象的概念，编码将其与二进制数据进行映射。由于编码通常依赖于字符集，实践中两者经常是绑定或互指的。常见的汉字编码方案 `GB2312`，其全名为《信息交换用汉字编码字符集·基本集》；而 HTML 中的 `<meta charset="encoding">` 标签也混用了字符集（`charset`）和编码的概念。

既然是集合，字符集就会有超集和子集。如 1995 年发布的 `GBK`(《汉字内码扩展规范》)，就是 `GB2312` （发布于 1980 年）的超集；而 2000 年发布的 `GB18030`（《信息技术 中文编码字符集》）又是 `GBK` 的超集。

## 代码页

代码页就是字符集加上编码。

Windows 提供了[很多代码页](https://docs.microsoft.com/en-us/windows/win32/intl/code-page-identifiers)选项，用于支持不同的语言文字。每个代码页有一个编号，简体中文对应的编号为 `936`，其对应的字符集为 `GBK`。你可以在命令行中执行命令`reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage /f ACP`，查看系统当前设置的代码页编号：

```
C:\>reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage /f ACP

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage
    ACP    REG_SZ    936

搜索结束: 找到 1 匹配。

C:>
```

代码页是 Windows 系统的术语。在 Linux 系统中，类似的概念是 [`locale`](https://www.gnu.org/software/libc/manual/html_node/Locales.html)，不过当前 Linux 一般默认编码方式都是 `utf-8`，对应的字符集是 Unicode。

## ANSI 编码

ANSI 编码代表 Windows 系统中当前代码页对应的编码，亦即系统默认编码。

这个术语经常在 Windows 平台使用，但实际上是被误用的。理论上，它应该等同于 [ANSI](https://www.ansi.org/) 在 1986 年发布的 [US-ASCII](http://www.columbia.edu/kermit/ascii.html)。现实中，它可以代表任意编码，甚至可以与 `ASCII` 完全不兼容，只要设置为系统默认编码。

## Unicode

Unicode 是一套标准，包含多语言统一的字符集及其相关编码，以及在这个字符集上进行文本处理的相关规则。

Unicode 当前版本(12.1)共规定了 137,929 个字符。它们不仅囊括了当前全球使用的主要语言文字（如拉丁字母，阿拉伯文字，简繁汉字等），还包含了很多符号（货币符号，标点符号，数学符号，几何图形，[emoji](https://emojipedia.org/)等），甚至还有仅在史料上使用的文字（如楔形文字，埃及象形文字等）。

在 Unicode 中，每个字符被分配了一个数值(`Code Point`，代码点)和一个名称。比如字母`A`的名称是`LATIN CAPITAL LETTER A`(大写拉丁字母A)。它对应的数值是 65，通常写作 `U+0041`(41是十六进制数，等于10进制的65)。除此之外，Unicode 还定义了各个字符的一系列属性，比如是否是大写字母，是否代表数字，书写方向（左到右还是右到左），宽度（半角还是全角）等。基于这些属性，Unicode 提供了大小写转换，文本换行，双向书写显示等相关算法。

Unicode 字符集被分为十七个子集(`Plane`，平面或位面)，每个子集最多可包含 65536 个字符，因此总共可以有 1,114,112 个字符。其中第一个子集(Plane 0)包含最常用的字符，被称为 `BMP`(`Basic Multilingual Plane`, 基本多文种平面)。 BMP 中为 UTF-16 中的代理对(`Surrogate Pair`)保留了 2048 个位置，只剩下 63488 个有效字符空间（因此 Unicode 中实际最多有1,112,064个字符）。BMP 中的字符可以用四位十六进制数(U+xxxx)表示，其它的字符需要五位或更多。

## UTF-8, UTF-16, UTF-32

这些是 Unicode 标准中规定的几种编码方式。UTF 是 Unicode Transformation Format 的缩写。

`UTF-8` 是一种变长编码，以字节为基本单位，单个字符占用的字节数可能是 1 （U+0000 ~ U+007F，128个位置，所有的 ASCII 字符），2（U+0080 ~ U+07FF, 1920个位置，主要是各种字母和符号），3（U+0800 ~ U+FFFF，63488个位置，BMP中所有其它字符，包括绝大部分常用汉字）或4（U+10000 ~ U+10FFFF，1048576个位置，所有其他字符）。 UTF-8 的主要优势在于兼容 ASCII，面向字节因而无需考虑字节顺序。现在 UTF-8 是互联网上使用率最高的编码方式。

`UTF-16` 是另一种变长编码，以 16 位(bit)为基本单位，单个字符可占用单位数可能为 1 （BMP 中的所有字符）或 2（所有其它字符，这两个单位被称为`代理对`）。相对于另外两种编码，它最大的优势是存储大量东亚文本（中日韩）时占用空间较少。由于基本单位不是字节，而是 16 位，不同的系统通信时需要考虑字节序(Byte Order)问题。Windows, JavaScript, Java 等内部使用了 UTF-16。

`UTF-32` (又称 `UCS-4`) 是一种定长编码，每个字符使用 32 位来表示。它的优势在于实现和处理简单，劣势在于空间效率低。它同样需要考虑字节序问题。

在 Unicode 标准早期版本中，唯一的编码方式是 16 位定长编码(`UCS-2`)，Windows 和 JavaScript 等采用了这种简单高效的标准。后来 Unicode 字符集扩充，16 位空间不足以容纳所有的字符，不得不产生了 UTF-16 的代理对机制，定长编码尴尬地转变成了变长编码。而 Linux 由于反应较慢，躲过一劫，后来逐渐转向了 UTF-8 编码。

## 字节序 (Byte Order)

不同系统中处理数值时可能采用的大小端序(`Endianness`)不同。比如在小端序（`Little-endian`）的机器上，32 位数字 1 在内存中表示为 4 个字节：`01 00 00 00`；而在大端序(`Big-endian`)的机器上则表示为 `00 00 00 01`。常见的 x86/x64, ARM 等处理器架构使用小端序，OpenRISC, SPARC等则使用大端序。不同系统间通信时，或读取其它系统存储的文件时，需要考虑字节顺序问题。

Unicode 编码中可以使用字符 `U+FEFF` 来作为字节序标记(`Byte Order Mark`)，需要时在字符序列前添加该字符。该字符本身不被视为文本的一部分，但通过分析它的表示方式，处理程序可以判断编码时的字节序，进而做出相应的处理。`U+FEFF` 名称为`ZERO WIDTH NO-BREAK SPACE`(零宽度非间断空白)，原本是有实际意义的。后来这个意义被新字符 `U+2060` (`WORD JOINER`) 取代，`U+FEFF` 现在仅用于标明字节序。

| 编码   | 大端字节序标记 | 小端字节序标记 |
| :----- | :------------- | :------------- |
| UTF-8  | EF BB BF       | EF BB BF       |
| UTF-16 | FE FF          | FF FE          |
| UTF-32 | 00 00 FE FF    | FF FE 00 00    |

UTF-8 编码的基本单位是字节，无需考虑字节序问题。尽管可以添加字节序标记 `EF BB BF`(字符 `U+FEFF` 在 UTF-8 中的表示方式)，但标准推荐仅在特殊情况下使用。Windows 下记事本保存为 UTF-8 格式时，会自动添加 BOM，用以与系统默认编码（即记事本所称的 ANSI编码）区分。而 Linux/macOS 等其它操作系统上的软件，一般只支持无字节序标记的 UTF-8 编码文件，这样导致文件交换出现不少问题。 从 Windows 10 v1903 起，记事本也在保存为 UTF-8 编码时默认不附加字节序标记了。

## 字素(Grapheme)与字素簇(Grapheme Cluster)

字素是文本在书写时最小的单位，可以被理解为单独的“字”。

在 Unicode 标准中，字符（`Character`）一般指代码点（Code Point）。通常，一个字素就是一个字符。但是，也有些字素是由多个字符序列组合而成的，这样的字符序列被称为`字素簇`。比如字母 é 可以用字母 e (`U+0065`) 加上重音符(`U+0301`) 组合而成。像重音符这样用于修饰前一个字符的字符，被称为组合字符（`Combining Character`）。可以使用多个[组合字符](http://www.zalgotextgenerator.com/unicode)来修饰同一个字符，这就是有一段时间内各个社区很流行的越界文字的技术根源。

> 超̷̪͓̫͕̳̝̔͐̋͌͑͗́̒̕͟͞越͓̻̗̙̙̠͖̔̆̌͑͐̽̊代̷͉̘̲̺̤͈̀͑̒͗̄͘̕͜码̵̨̟͖͎̉̿͌͜͞͞͞，越界文字̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗̗

如同前面的例子所示，含重音符的拉丁字母可以使用基本字符加上重音修饰字符来表示。为保持与旧软件系统的兼容性，这种情况下 Unicode 中实际还包含了预先组合好的单个字符。即，某些字素可以有多个表示方式。上面的 é 既可以用字符序列 `U+0065 U+0301` 表示，也可以用单个 `U+00E9` 表示。这样也带来了新的问题，在字符串比较，排序等操作前需要首先进行正规化(`Normalization`)。正规化即把所有*可用单个字符表示的*字符序列替换为对应的单个字符。

## Collation

Collation 是字符间的排序规则。在一个 Collation 中，对应字符集里所有的字符都有确定的排序先后关系，因而构成了全序关系。

文本排序的规则可以有很多，如字母表顺序（Alphabetical order），数值顺序等；针对汉字还有部首笔画顺序等。同一字符，在不同的语言中，所处的排序位置也有可能不同。排序时，还要考虑是否忽略大小写，是否忽略重音/音调等，因此排序规则是很复杂的。Unicode 标准中提供了所有字符的默认的排序规则（Default Unicode Collation Element Table, DUCET），该规则也可根据不同的情况进行定制。开源项目[ICU(International Components for Unicode)](http://site.icu-project.org/)里提供了各种语言的各种排序规则。

## MySQL 与 UTF-8

在 MySQL 数据库中，存在多个与 Unicode 相关的字符集和排序规则。

一般情况下应该使用 `utf8mb4` 字符集，这才是 MySQL 中真正的`UTF-8`编码(如第一节所言，这里字符集和编码又一次互指了)。相应的所谓 `utf8` 字符集是非标准的，其中单个字符最多只能编码为 3 个字节，因此很多字符无法保存。

MySQL 中排序规则应该使用 `utf8mb4_unicode_ci`，而不是 `utf8mb4_general_ci`。后者性能稍微高于前者，但算法不符合 Unicode 标准，可能在处理某些语言文字时出现错误的结果。`utf8mb4_unicode_ci`对应 Unicode v4.0，较新版本的 MySQL 还同时支持 v5.2(`utf8mb4_unicode_520_ci`)，v9.0(`utf8mb4_0900_ai_ci`)。