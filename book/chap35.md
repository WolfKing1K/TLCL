---
layout: book
title: 字符串和数字
---

Computer programs are all about working with data. In past chapters, we have focused on
processing data at the file level. However, many programming problems need to be
solved using smaller units of data such as strings and numbers.

所有的计算机程序都是用来和数据打交道的。在过去的章节中，我们专注于处理文件级别的数据。
然而，许多程序问题需要使用更小的数据单位来解决，比方说字符串和数字。

In this chapter, we will look at several shell features that are used to manipulate strings
and numbers. The shell provides a variety of parameter expansions that perform string
operations. In addition to arithmetic expansion (which we touched upon in Chapter 7),
there is a common command line program called bc, which performs higher level math.

在这一章中，我们将查看几个用来操作字符串和数字的 shell 功能。shell 提供了各种执行字符串操作的参数展开功能。
除了算术展开（在第七章中接触过），还有一个常见的命令行程序叫做 bc，能执行更高级别的数学运算。

### Parameter Expansion

### 参数展开

Though parameter expansion came up in Chapter 7, we did not cover it in detail because
most parameter expansions are used in scripts rather than on the command line. We have
already worked with some forms of parameter expansion; for example, shell variables.
The shell provides many more.

尽管参数展开在第七章中出现过，但我们并没有详尽地介绍它，因为大多数的参数展开会用在脚本中，而不是命令行中。
我们已经使用了一些形式的参数展开；例如，shell 变量。shell 提供了更多方式。

#### Basic Parameters

#### 基本参数

The simplest form of parameter expansion is reflected in the ordinary use of variables.
For example:

最简单的参数展开形式反映在平常使用的变量上。例如：

$a

when expanded, becomes whatever the variable a contains. Simple parameters may also
be surrounded by braces:

当 $a 展开后，会变成变量 a 所包含的值。简单参数也可能用花括号引起来：

${a}

This has no effect on the expansion, but is required if the variable is adjacent to other
text, which may confuse the shell. In this example, we attempt to create a filename by ap-
pending the string “_file” to the contents of the variable a.

虽然这对展开没有影响，但若该变量 a 与其它的文本相邻，可能会把 shell 搞糊涂了。在这个例子中，我们试图
创建一个文件名，通过把字符串 “_file” 附加到变量 a 的值的后面。

    [me@linuxbox ~]$ a="foo"
    [me@linuxbox ~]$ echo "$a_file"

If we perform this sequence, the result will be nothing, because the shell will try to ex-
pand a variable named a_file rather than a. This problem can be solved by adding
braces:

如果我们执行这个序列，没有任何输出结果，因为 shell 会试着展开一个称为 a_file 的变量，而不是 a。通过
添加花括号可以解决这个问题：

    [me@linuxbox ~]$ echo "${a}_file"
    foo_file

We have also seen that positional parameters greater than 9 can be accessed by surround-
ing the number in braces. For example, to access the eleventh positional parameter, we
can do this:

我们已经知道通过把数字包裹在花括号中，可以访问大于9的位置参数。例如，访问第十一个位置参数，我们可以这样做：

${11}

#### Expansions To Manage Empty Variables

#### 管理空变量的展开

Several parameter expansions deal with nonexistent and empty variables. These expan-
sions are handy for handling missing positional parameters and assigning default values
to parameters.

几种用来处理不存在和空变量的参数展开形式。这些展开形式对于解决丢失的位置参数和给参数指定默认值的情况很方便。

${parameter:-word}

If parameter is unset (i.e., does not exist) or is empty, this expansion results in the value
of word. If parameter is not empty, the expansion results in the value of parameter.

若 parameter 没有设置（例如，不存在）或者为空，展开结果是 word 的值。若 parameter 不为空，则展开结果是 parameter 的值。

    [me@linuxbox ~]$ foo=
    [me@linuxbox ~]$ echo ${foo:-"substitute value if unset"}
    if unset
    substitute value
    [me@linuxbox ~]$ echo $foo
    [me@linuxbox ~]$ foo=bar
    [me@linuxbox ~]$ echo ${foo:-"substitute value if unset"}
    bar
    [me@linuxbox ~]$ echo $foo
    bar

${parameter:=word}

If parameter is unset or empty, this expansion results in the value of word. In addition,
the value of word is assigned to parameter. If parameter is not empty, the expansion re-
sults in the value of parameter.

若 parameter 没有设置或为空，展开结果是 word 的值。另外，word 的值会赋值给 parameter。
若 parameter 不为空，展开结果是 parameter 的值。

    [me@linuxbox ~]$ foo=
    [me@linuxbox ~]$ echo ${foo:="default value if unset"}
    default value if unset
    [me@linuxbox ~]$ echo $foo
    default value if unset
    [me@linuxbox ~]$ foo=bar
    [me@linuxbox ~]$ echo ${foo:="default value if unset"}
    bar
    [me@linuxbox ~]$ echo $foo
    bar

---

Note: Positional and other special parameters cannot be assigned this way.

注意： 位置参数或其它的特殊参数不能以这种方式赋值。

---

${parameter:?word}

If parameter is unset or empty, this expansion causes the script to exit with an error, and
the contents of word are sent to standard error. If parameter is not empty, the expansion
results in the value of parameter.

若 parameter 没有设置或为空，这种展开导致脚本带有错误退出，并且 word 的内容会发送到标准错误。若 parameter 不为空，
展开结果是 parameter 的值。

    [me@linuxbox ~]$ foo=
    [me@linuxbox ~]$ echo ${foo:?"parameter is empty"}
    bash: foo: parameter is empty
    [me@linuxbox ~]$ echo $?
    1
    [me@linuxbox ~]$ foo=bar
    [me@linuxbox ~]$ echo ${foo:?"parameter is empty"}
    bar
    [me@linuxbox ~]$ echo $?
    0

${parameter:+word}

If parameter is unset or empty, the expansion results in nothing. If parameter is not
empty, the value of word is substituted for parameter; however, the value of parameter is
not changed.

若 parameter 没有设置或为空，展开结果为空。若 parameter 不为空，
展开结果是 word 的值会替换掉 parameter 的值；然而，parameter 的值不会改变。

    [me@linuxbox ~]$ foo=
    [me@linuxbox ~]$ echo ${foo:+"substitute value if set"}

    [me@linuxbox ~]$ foo=bar
    [me@linuxbox ~]$ echo ${foo:+"substitute value if set"}
    substitute value if set

### Expansions That Return Variable Names

### 返回变量名的参数展开

The shell has the ability to return the names of variables. This is
used in some rather exotic situations.

shell 具有返回变量名的能力。这会用在一些相当独特的情况下。

${!prefix*}

${!prefix@}

This expansion returns the names of existing variables with names beginning with prefix.
According to the bash documentation, both forms of the expansion perform identically.
Here, we list all the variables in the environment with names that begin with BASH:

这种展开会返回以 prefix 开头的已有变量名。根据 bash 文档，这两种展开形式的执行结果相同。
这里，我们列出了所有以 BASH 开头的环境变量名：

[me@linuxbox ~]$ echo ${!BASH*}
BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_COMPLETION
BASH_COMPLETION_DIR BASH_LINENO BASH_SOURCE BASH_SUBSHELL
BASH_VERSINFO BASH_VERSION

#### String Operations

#### 字符串展开

There is a large set of expansions that can be used to operate on strings. Many of these
expansions are particularly well suited for operations on pathnames.

有大量的展开形式可用于操作字符串。其中许多展开形式尤其适用于路径名的展开。

${#parameter}

expands into the length of the string contained by parameter. Normally, parameter is a
string; however, if parameter is either @ or *, then the expansion results in the number of
positional parameters.

展开成由 parameter 所包含的字符串的长度。通常，parameter 是一个字符串；然而，如果 parameter 是 @ 或者是 * 的话，
则展开结果是位置参数的个数。

[me@linuxbox ~]$ foo="This string is long."
[me@linuxbox ~]$ echo "'$foo' is ${#foo} characters long."
'This string is long.' is 20 characters long.

${parameter:offset}

${parameter:offset:length}

These expansions are used to extract a portion of the string contained in parameter. The
extraction begins at offset characters from the beginning of the string and continues until
the end of the string, unless the length is specified.

这些展开用来从 parameter 所包含的字符串中提取一部分字符。提取的字符始于
第 offset 个字符（从字符串开头算起）直到字符串的末尾，除非指定提取的长度。

[me@linuxbox ~]$ foo="This string is long."
[me@linuxbox ~]$ echo ${foo:5}
string is long.
[me@linuxbox ~]$ echo ${foo:5:6}
string

If the value of offset is negative, it is taken to mean it starts from the end of the string
rather than the beginning. Note that negative values must be preceded by a space to pre-
vent confusion with the ${parameter:-word} expansion. length, if present, must not
be less than zero.

若 offset 的值为负数，则认为 offset 值是从字符串的末尾开始算起，而不是从开头。注意负数前面必须有一个空格，
为防止与 ${parameter:-word} 展开形式混淆。length，若出现，则必须不能小于零。

If parameter is @, the result of the expansion is length positional parameters,
starting at offset.

如果 parameter 是 @，展开结果是 length 个位置参数，从第 offset 个位置参数开始。

[me@linuxbox ~]$ foo="This string is long."
[me@linuxbox ~]$ echo ${foo: -5}
long.
[me@linuxbox ~]$ echo ${foo: -5:2}
lo

${parameter#pattern}

${parameter##pattern}

These expansions remove a leading portion of the string contained in parameter defined
by pattern. pattern is a wildcard pattern like those used in pathname expansion. The dif-
ference in the two forms is that the # form removes the shortest match, while the ## form
removes the longest match.

这些展开会从 paramter 所包含的字符串中清除开头一部分文本，这些字符要匹配定义的 patten。pattern 是
通配符模式，就如那些用在路径名展开中的模式。这两种形式的差异之处是该 # 形式清除最短的匹配结果，
而该 ## 模式清除最长的匹配结果。

[me@linuxbox ~]$ foo=file.txt.zip
[me@linuxbox ~]$ echo ${foo#*.}
txt.zip
[me@linuxbox ~]$ echo ${foo##*.}
zip

${parameter%pattern}

${parameter%%pattern}

These expansions are the same as the # and ## expansions above, except they remove
text from the end of the string contained in parameter rather than from the beginning.

这些展开和上面的 # 和 ## 展开一样，除了它们清除的文本从 parameter 所包含字符串的末尾开始，而不是开头。

[me@linuxbox ~]$ foo=file.txt.zip
[me@linuxbox ~]$ echo ${foo%.*}
file.txt
[me@linuxbox ~]$ echo ${foo%%.*}
file

${parameter/pattern/string}

${parameter//pattern/string}

${parameter/#pattern/string}

${parameter/%pattern/string}

This expansion performs a search-and-replace upon the contents of parameter. If text is
found matching wildcard pattern, it is replaced with the contents of string. In the normal
form, only the first occurrence of pattern is replaced. In the // form, all occurrences are
replaced. The /# form requires that the match occur at the beginning of the string, and
the /% form requires the match to occur at the end of the string. /string may be omitted,
which causes the text matched by pattern to be deleted.

这种形式的展开对 parameter 的内容执行查找和替换操作。如果找到了匹配通配符 pattern 的文本，
则用 string 的内容替换它。在正常形式下，只有第一个匹配项会被替换掉。在该 // 形式下，所有的匹配项都会被替换掉。
该 /# 要求匹配项出现在字符串的开头，而 /% 要求匹配项出现在字符串的末尾。/string 可能会省略掉，这样会
导致删除匹配的文本。

    [me@linuxbox~]$ foo=JPG.JPG
    [me@linuxbox ~]$ echo ${foo/JPG/jpg}
    jpg.JPG
    [me@linuxbox~]$ echo ${foo//JPG/jpg}
    jpg.jpg
    [me@linuxbox~]$ echo ${foo/#JPG/jpg}
    jpg.JPG
    [me@linuxbox~]$ echo ${foo/%JPG/jpg}
    JPG.jpg

Parameter expansion is a good thing to know. The string manipulation expansions can be
used as substitutes for other common commands such as sed and cut.
Expansions improve the efficiency of scripts by eliminating the use of external programs.
As an example, we will modify the longest-word program discussed in the previous chapter to
use the parameter expansion ${#j} in place of the command
substitution _$(echo $j | wc -c)_ and its resulting subshell, like so:

知道参数展开是件很好的事情。字符串操作展开可以用来替换其它常见命令比方说 sed 和 cut。
通过减少使用外部程序，展开提高了脚本的效率。举例说明，我们将修改在之前章节中讨论的 longest-word 程序，
用参数展开 ${#j} 取代命令 $(echo $j | wc -c) 及其 subshell ，像这样：

    #!/bin/bash
    # longest-word3 : find longest string in a file
    for i; do
        if [[ -r $i ]]; then
            max_word=
            max_len=
            for j in $(strings $i); do
                len=${#j}
                if (( len > max_len )); then
                    max_len=$len
                    max_word=$j
                fi
            done
            echo "$i: '$max_word' ($max_len characters)"
        fi
        shift
    done

Next, we will compare the efficiency of the two versions by using the time command:

下一步，我们将使用 time 命令来比较这两个脚本版本的效率：

    [me@linuxbox ~]$ time longest-word2 dirlist-usr-bin.txt
    dirlist-usr-bin.txt: 'scrollkeeper-get-extended-content-list' (38
    characters)
    real 0m3.618s
    user 0m1.544s
    sys 0m1.768s
    [me@linuxbox ~]$ time longest-word3 dirlist-usr-bin.txt
    dirlist-usr-bin.txt: 'scrollkeeper-get-extended-content-list' (38
    characters)
    real 0m0.060s
    user 0m0.056s
    sys 0m0.008s

The original version of the script takes 3.618 seconds to scan the text file, while the new
version, using parameter expansion, takes only 0.06 seconds — a very significant improvement.

原来的脚本扫描整个文本文件需耗时3.168秒，而该新版本，使用参数展开，仅仅花费了0.06秒 —— 一个非常巨大的提高。

#### Case Conversion

#### 大小写转换

Recent versions of bash have support for upper/lowercase conversion of strings. bash
has four parameter expansions and two options to the declare command to support it.

最新的 bash 版本已经支持字符串的大小写转换了。bash 有四个参数展开和 delare 命令的两个选项来支持大小写转换。

So what is case conversion good for? Aside from the obvious aesthetic value, it has an
important role in programming. Let's consider the case of a database look-up. Imagine
that a user has entered a string into a data input field that we want to look up in a database.
It's possible the user will enter the value in all uppercase letters or lowercase letters
or a combination of both. We certainly don't want to populate our database with every
possible permutation of upper and lower case spellings. What to do?

那么大小写转换有什么好处呢？ 除了明显的审美价值，它在编程领域还有一个重要的角色。
让我们考虑一个数据库查询的案例。假设一个用户已经敲写了一个字符串到数据输入框中，
而我们想要在一个数据库中查找这个字符串。该用户输入的字符串有可能全是大写字母或全是小写或是两者的结合。
我们当然不希望把每个可能的大小写拼写排列填充到我们的数据库中。那怎么办？

A common approach to this problem is to normalize the user's input. That is, convert it
into a standardized form before we attempt the database look-up. We can do this
by converting all of the characters in the user's input to either lower or
uppercase and ensure that the database entries are normalized the same way.

解决这个问题的常见方法是规范化用户输入。也就是，在我们试图查询数据库之前，把用户的输入转换成标准化。
我们能做到这一点，通过把用户输入的字符全部转换成小写字母或大写字母，并且确保数据库中的条目
按同样的方式规范化。

The declare command can be used to normalize strings to either upper or lowercase.
Using declare, we can force a variable to always contain the desired
format no matter what is assigned to it:

这个 declare 命令可以用来把字符串规范成大写或小写字符。使用 declare 命令，我们能强制一个
变量总是包含所需的格式，无论如何赋值给它。

    #!/bin/bash
    # ul-declare: demonstrate case conversion via declare
    declare -u upper
    declare -l lower
    if [[ $1 ]]; then
        upper="$1"
        lower="$1"
        echo $upper
        echo $lower
    fi

In the above script, we use declare to create two variables, upper and lower. We
assign the value of the first command line argument (positional parameter 1) to each of
the variables and then display them on the screen:

在上面的脚本中，我们使用 declare 命令来创建两个变量，upper 和 lower。我们把第一个命令行参数的值（位置参数1）赋给
每一个变量，然后把变量值在屏幕上显示出来：

    [me@linuxbox ~]$ ul-declare aBc
    ABC
    abc

As we can see, the command line argument ("aBc") has been normalized.

正如我们所看到的，命令行参数（“aBc”）已经规范化了。

There are four parameter expansions that perform upper/lowercase conversion:

有四个参数展开，可以执行大小写转换操作：

<table class="multi">
    <caption class="cap">Table 34-1: Case Conversion Parameter Expansions</caption>
    <tr>
        <th class="title">Format</th>
        <th class="title">Result</th>
    </tr>
    <tr>
        <td valign="top">${parameter,,} </td>
        <td valign="top">Expand the value of parameter into all lowercase.</td>
    </tr>
    <tr>
        <td valign="top">${parameter,} </td>
        <td valign="top">Expand the value of parameter changing only the first
             character to lowercase.</td>
    </tr>
    <tr>
        <td valign="top">${parameter^^} </td>
        <td valign="top">Expand the value of parameter into all uppercase letters.</td>
    </tr>
    <tr>
        <td valign="top">${parameter^}</td>
        <td valign="top">Expand the value of parameter changing only the first
            character to uppercase (capitalization).</td>
    </tr>
</table>

<table class="multi">
    <caption class="cap">表 34-1: 大小写转换参数展开</caption>
    <tr>
        <th class="title">格式</th>
        <th class="title">结果</th>
    </tr>
    <tr>
        <td valign="top">${parameter,,} </td>
        <td valign="top">把 parameter 的值全部展开成小写字母。</td>
    </tr>
    <tr>
        <td valign="top">${parameter,} </td>
        <td valign="top">仅仅把 parameter 的第一个字符展开成小写字母。</td>
    </tr>
    <tr>
        <td valign="top">${parameter^^} </td>
        <td valign="top">把 parameter 的值全部转换成大写字母。</td>
    </tr>
    <tr>
        <td valign="top">${parameter^}</td>
        <td valign="top">仅仅把 parameter 的第一个字符转换成大写字母（首字母大写）。</td>
    </tr>
</table>

Here is a script that demonstrates these expansions:

这里是一个脚本，演示了这些展开格式：

    #!/bin/bash
    # ul-param - demonstrate case conversion via parameter expansion
    if [[ $1 ]]; then
        echo ${1,,}
        echo ${1,}
        echo ${1^^}
        echo ${1^}
    fi
  
Here is the script in action:

这里是脚本运行后的结果：

    [me@linuxbox ~]$ ul-param aBc
    abc
    aBc
    ABC
    ABc

Again, we process the first command line argument and output the four variations
supported by the parameter expansions. While this script uses the first positional parameter,
parameter my be any string, variable, or string expression.

再次，我们处理了第一个命令行参数，输出了由参数展开支持的四种变体。尽管这个脚本使用了第一个位置参数，
但参数可以是任意字符串，变量，或者字符串表达式。

