1. 什么是 snippet

snippet[ˈsnɪpɪt]，或者说「code snippet」，也即代码段，指的是能够帮助输入重复代码模式，比如循环或条件语句，的模板。通过 snippet ，我们仅仅输入一小段字符串，就可以在代码段引擎的帮助下，生成预定义的模板代码，接着我们还可以通过在预定义的光标位置之间跳转，来快速补全模板。

 

2. 如何配置 snippet

2.1. 操作流程

进入 snippet 设置文件，这里提供了两种方法：
摁「Alt」键切换菜单栏，通过文件 > 首选项 > 用户代码片段，选择进入目的语言的代码段设置文件；
通过快捷键「Ctrl + Shift + P」打开命令窗口（all command window），输入「snippet」，通过候选栏中的选项进入目的语言的代码段设置文件。
填写 snippets
2.2. VSCode 中 snippet 的文法

2.2.1 引子

设置文件头部的一个块注释给出了设置 snippet 的格式，了解过「json」就不会对此感到奇怪。

// Place your snippets for C here. Each snippet is defined under a snippet name and has a prefix, body and
// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the
// same ids are connected.
// Example:
"Print to console": {
"prefix": "log",,
"body": [
"console.log('$1');",
"$2"
],
"description": "Log output to console"
}

注：上例定义了一个名为「Print to console」的 snippet，其功能为：在输入 log 并确认后，可将原文本替换为console.log('');。效果预览如下：

20170112145950068.png
2.2.2 详细文法

然而以上格式只是提供了较为基础的功能，实际上 VSCode 的代码段引擎所能做的远不止这些。本文将以官方教程为本，对此进行详实地阐述。

snippet 由三部分组成：

prefix：前缀，定义了 snippets 从 IntelliSense 中呼出的关键字;
body： 主体，即模板的主体内容，其中每个字符串表示一行;
description：说明，会在 IntelliSense 候选栏中出现。未定义的情况下直接显示对象名，上例中将会显示 Print to console。
其中 body 部分可以使用特殊结构，来控制光标和要插入的文本，其支持的特性及其文法如下：

 

Tabstops：制表符
用「Tabstops」可以让编辑器的指针在 snippet 内跳转。使用 $1，$2 等指定光标位置。这些数字指定了光标跳转的顺序。特别地，$0表示最终光标位置。相同序号的「Tabstops」被链接在一起，将会同步更新，比如下列用于生成头文件封装的 snippet 被替换到编辑器上时，光标就将同时出现在所有$1位置。

"#ifndef $1"

"#define $1"

"#end // $1"

 

Placeholders：占位符
「Placeholder」是带有默认值的「Tabstops」，如${1：foo}。「placeholder」文本将被插入「Tabstops」位置，并在跳转时被全选，以方便修改。占位符还可以嵌套，例如

${1:another ${2:placeholder}}。

比如，结构体的 snippet 主体可以这样写：

struct ${1:name_t} {\n\t$2\n};

作为「Placeholder」的name_t一方面可以提供默认的结构名称，另一方面可以作为输入的提示。

Choice：可选项
「Choice」是提供可选值的「Placeholder」。其语法为一系列用逗号隔开，并最终被两个竖线圈起来的枚举值，比如 ${1|one,two,three|}。当光标跳转到该位置的时候，用户将会被提供多个值（one 或 two 或 three）以供选择。

Variables：变量
使用$name或${name:default}可以插入变量的值。 当未设置变量时，将插入其缺省值或空字符串。 当varibale未知（即，其名称未定义）时，将插入变量的名称，并将其转换为「placeholder」。 可以使用以下「Variable」：

TM_SELECTED_TEXT：当前选定的文本或空字符串
TM_CURRENT_LINE：当前行的内容
TM_CURRENT_WORD：光标下的单词的内容或空字符串
TM_LINE_INDEX：基于零索引的行号
TM_LINE_NUMBER：基于一索引的行号
TM_FILENAME：当前文档的文件名
TM_FILENAME_BASE 当前文档的文件名（不含后缀名）
TM_DIRECTORY：当前文档的目录
TM_FILEPATH：当前文档的完整文件路径
CURRENT_DAY_NAME ：当天的名称（’星期一’）。
CURRENT_DAY_NAME_SHORT ：当天的短名称（’Mon’）。
CURRENT_MONTH_NAME ：本月的全名（’七月’）。
CURRENT_MONTH_NAME_SHORT} ：月份的简称（’Jul’）。
CURRENT_YEAR：当前年(四位数)
CURRENT_YEAR_SHORT：当前年(两位数)
CURRENT_MONTH：当前月
CURRENT_DATE：当前日
CURRENT_HOUR：当前小时
CURRENT_MINUTE：当前分钟
CURRENT_SECOND：当前秒
CLIPBOARD：当前剪切板中的内容
注意，这些都是变量名，不是宏，在实际使用的时候还是要加上$符的。

variable transformations：变量转换
VSCode 自 v1.17 起，其代码段引擎开始支持「variable transformations」 特性，也即变量的值可以经过格式化处理后，再插入预定的位置。这是一个很强大的特性，举个例子，我们可以用它直接生成特定格式的头文件定义宏。由于截止笔者第一次更新的时候，官方教程2仍未更新相关内容，因此笔者只得根据笔者在 VSCode 项目源码中找到的文档 3，进行推理和验证，从而补全该部分空缺。

废话不说。我们可以通过 ${var_name/regular_expression/format_string/options} 的格式来使用变量转换特性。

显然，「variable transformations」也由 4 部分构成：

var_name：变量名
regular_expression：正则表达式
format_string：格式串
options：正则表达式匹配选项
其中正则表达式的写法和匹配选项部分不在本篇博文的讲解范围之内，具体内容请分别参考 javascript 有关 RegExp(pattern [, flags]) 构造函数中的 pattern 及 flags 参数项的说明4。

下面我们将着重介绍 format_string 这个特色部分。

根据其 EBNF 范式，我们可以知道 format_string 其实是 format 或 text 的线性组合。那么 format 和 text 分别是什么呢？

text：也即没有任何作用的普通文本，你甚至可以在其中使用汉字。
format：格式串，分为 7 种：
$sn：表示插入匹配项
${sn}：同 $sn
${sn:/upcase} 或 ${sn:/downcase} 或 ${sn:/capitalize}：表示将匹配项变更为「所有字母均大写/所有字母均小写/首字母大写其余小写」后，插入
${sn:+if}：表示当匹配成功时，并且捕捉括号捕捉特定序号的捕捉项成功时，在捕捉项位置插入「if」所述语句
${sn:?if:else}：表示当匹配成功，并且捕捉括号捕捉特定序号的捕捉项成功时，在捕捉项位置插入「if」所述语句；否则当匹配成功，但当捕捉括号捕捉特定序号的捕捉项失败时，在捕捉项位置插入「else」所述语句
${sn:-else}：表示当匹配成功，但当捕捉括号捕捉特定序号的捕捉项失败时，在捕捉项位置插入「else」所述语句
${sn:else}：同 ${sn:-else}
其中 format 的后三条理解起来可能比较困难。这里我们以倒数第三条为例讲解。假设我们有一个「make.c」文件，我们有这么一条 snippet: "body": "${TM_FILENAME/make.c(pp|\+\+)?/${1:?c++:clang}/}"。整个模式串匹配成功，但是捕捉括号捕捉后缀名中的 pp 或 ++ 失败，因此判断条件在捕捉括号的位置插入捕捉失败时应插入的字符串，也即「clang」。

注意:
其中 sn 表示捕捉项的序号

其中 if 表示捕捉项捕捉成功时替换的文本

其中 else 表示捕捉项捕失败时替换的文本

2.3. 有用的建议

默认情况下 snippet 在 IntelliSense 中的显示优先级并不高，而且在 IntelliSense 中选择相应 snippet 需要按「enter」键，这对于手指短的人来说并不是什么很好的体验。所幸，VSCode 意识到了这一点，并为我们提供了改进的方式。

在 VSCode 的用户设置（「Ctrl+P」在输入框中写「user settings」后点选）中，检索代码段，然后根据提示修改，设置建议优先显示，并且可以通过「TAB」补全 snippet。

20170112155152376.png
 

最后放出的我的配置文件:

{
“文件头部注释”:
{
“prefix”: “#S”,
“body”:
“/******************** (C) COPYRIGHT $CURRENT_YEAR 陈苏阳 **********************************\r\n* File Name\t\t\t:  $TM_FILENAME\r\n* Author\t\t\t:  陈苏阳\r\n* CPU Type\t\t\t:  ${2|NRF52810,NRF52832,STM32F103,STM32F4,ESP32|} \r\n* IDE\t\t\t\t:  ${3|IAR 8.22.1,Keil4,Keil5,PlatformIO IDE|} \r\n* Version\t\t\t:  V1.0\r\n* Date\t\t\t\t:  $CURRENT_DATE/$CURRENT_MONTH/$CURRENT_YEAR\r\n* Description\t\t:  $7\r\n*******************************************************************************/”,
“description”: “在当前位置生成一段代码文件的头部注释”
},

“文件尾部注释”:
{
“prefix”: “#E”,
“body”:
“/******************* (C) COPYRIGHT $CURRENT_YEAR 陈苏阳 *****END OF FILE******************/”,
“description”: “在当前位置生成一段代码文件的尾部注释”
},

“.C文件标准格式”:
{
“prefix”: “#C”,
“body”:
“/* Includes ——————————————————————*/\r\n\r\n\r\n/* Private variables ———————————————————*/\r\n\r\n\r\n/* Private function prototypes ———————————————–*/\r\n\r\n\r\n/* Private functions ———————————————————*/\r\n\r\n\r\n”,
“description”: “在当前位置填充.C文件标准格式”
},

“.H文件标准格式”:
{
“prefix”: “#H”,
“body”:
“/* Define to prevent recursive inclusion ————————————-*/\n#ifndef __${1:${TM_FILENAME/(.*)\\.C$/${1:/upcase}_H/i}}\n#define __${1:${TM_FILENAME/(.*)\\.C$/${1:/upcase}_H/i}}\n\n\n/* Includes ——————————————————————*/\n\n\n/* Private define ————————————————————*/\n\n\n/* Private typedef ———————————————————–*/\n\n\n/* Private variables ———————————————————*/\n\n\n/* Private function prototypes ———————————————–*/\n\n\n#endif /* ${1:${TM_FILENAME/(.*)\\.C$/${1:/upcase}_H/i}} */”,
“description”: “在当前位置填充.H文件标准格式”
},

“函数头部注释”:
{
“prefix”: “#FS”,
“body”:
“/*******************************************************************************\n*\t\t\t\t\t\t$1@$CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE\n* Function Name\t\t:  \n* Description\t\t:  $2\n* Input\t\t\t\t:  None\n* Output\t\t\t:  None\n* Return\t\t\t:  None\n*******************************************************************************/”,
“description”: “在当前光标处添加一个函数头部注释”
},

“函数尾部注释”:
{
“prefix”: “#FE”,
“body”:
“// End of “,
“description”: “在当前光标处添加一个函数尾部注释”
},
}