# 写在前面

大部分内容来自b站up主：[无限十三年](https://space.bilibili.com/698499843)

# 一、文本编缉

## 1. 基本结构

latex源码都包含两个基本部分：

- 导言区：用于设置文档类，导入宏包等。
  - 文档类：用于指定这个文本是属于哪类的文本。
    - `article`：文章
    - `report`：报告
    - `book`：书
  - 指定文档类使用的命令是`\documentclass{<文档类>}`，一般都是代码的第一行。
- 正文区：在正文区就可以书写正文内容了。

```latex
%导言区
\documentclass{article}  % 编写的文档类型（相当于使用模板）
\usepackage{ctex}  % 导入中文宏包

\begin{document}  % 进入document环境
%正文区
hello world
\end{document}
```

- article的文档类型默认不支持中文编写，所以要额外导入`ctex`宏包
- 以`\begin{...}`开关，`\end{...}`结尾的区域称为**环境**。

## 2. 标题、作者、日期

```latex
%导言区
\documentclass{article}  % 编写的文档类型（相当于使用模板）
\usepackage{ctex}  % 导入中文宏包

% 标题、作者、日期
\title{\LaTeX 从入门到入土}
\author{小明 \and 小刚 \and 小智\thanks{Email:123456}}
\date{2024-10-4}  
% 或者用\date{\today}

\begin{document}  % 进入document环境
%正文区
\maketitle
hello world

\end{document}
```

- `\LaTeX`命令于展示latex的logo，即：$\LaTeX$

- 在`\author`中，用`\and` 添加（连接）作者
  - `\thanks{...}`可以用来添加脚注
- 在写完标题、作者、日期区域后，要在正文区中加入`\maketitle`才有用
  - 在正文的哪里写 `\maketitle`，就在正文的哪里加上 标题、作者、日期 这些信息

## 3. 换行、分段、空格

- 换行：用两个反斜杠`\\`，或者用命令 `\newline`。
- 分段：在两个段落之间间隔一个（或多个）**空行**，则两个段落就被视为两个文字段落，会使两个段落的**首行缩进**两个字符。
  - 注意：是间隔一个空行，而不是两个段落这间直接换行。
- 空格：用`\hspace{1em}` 或 `\vspace{1em}` 来表示空格。
  - `\hspace` 表示 "horizontal space"，是水平空格，就是常用的空格。
  - `\vspace` 表示 "vertical space"，是垂直空格，可以起到类似换行的效果。
  - 花括号的 `1em`表示空格的大小
    - `em`表示空格的单位，`1em` 就是一个中文汉字大小的空格，`2em` 就是两个中文汉字大小的空格。
    - 除了`em`，还有很多别的单位，例如：`pt`（磅数）和 `ex`（一个字母x的大小）。

## 4. 居中、缩进、行距

### 4.1 对齐方式

- **居中：**使用`center`环境，

  - 例如：

    ```latex
    诗曰:
    \begin{center}
    混沌未分天地乱，茫茫渺渺无人见。  \\
    自从盘古破鸿蒙，开辟从兹清浊辨。  \\
    覆载群生仰至仁，发明万物皆成善。  \\
    欲知造化会元功，须看西游释厄传。  \\
    \end{center}
    ```


- **左对齐：**用`flushleft`环境。

- **右对齐：**用`flushright`环境。

### 4.2 缩进

- 取消缩进：`\noindent`

  - 例如：

    ```latex
    \noindent
    盖闻天地之数，有十二万九千六百岁为一元。将一元分为十二会，乃子、丑、寅、卯、辰、巳、午、未、申、酉、成、亥之十二支也。每会该一万八百岁。且就一日而论:子时得阳气，而丑则鸡鸣:实不通光，而卯则日出;辰时食后，而已则挨排;日午天中，而未则西蹉;申时哺而日落西，成黄昏而人定亥。譬于大数，若到戌会之终，则天地昏缯而万物否矣。
    
    再去五千四百岁，交亥会之初，则当黑暗，而两间人物俱无矣，故曰混沌。又五千四百岁，亥会将终，贞下起元，近子之会，而复逐渐开明。邵康节曰:“冬至子之半，天心无改移。一阳初动处，万物未生时。”到此天始有根。
    ```

  - 注意：在一个段落前加`\noindent`，只能取消该段落的首行缩进，而不能取消全文的首先缩进。
    - 也就是如果正文区域出现了`\noindent`，则该命令只对其后紧跟着的一个段落有效。


- 全文取消缩进：`\parindent = 0pt`
  - 或者写为：`\setlength{parindent}{0pt}`
  - **注意：**这个命令需要在导言区使用，不能在正文区域使用。
  - `0pt`就是指：缩进距离为0磅。


### 4.3 行距

#### 4.3.1 行距的定义

- 行距的定义：$行距 = 倍数 \times 基础行距$

  - 例如：$1.5倍行距 = 1.5 \times 基础行距$。

- 基础行距：

  - 在latex中，$基础行距 = 1.2 \times 字号$
  - 在word中，$基础行距 = 1.3 \times 字号$
  - 所以要注意word中的1.3倍行距，在latex中是等于 $1.3 \times 1.3 \div1.2 \approx 1.4083$

- latex中的行距和word中的行距满足如下关系：
  $$
  word \times 1.3 = latex \times 1.2
  $$
  即：
  $$
  latex = word \times \cfrac{1.3}{1.2}
  $$

  - 其中 $latex$ 表示latex中的行距，
  - $word$ 表示word中的行距。

#### 4.3.2 设置行距

- 设置全文行距：`\linespread{2}`

  - 这个命令要用在导言区。
  - 该语句的作用就是设置全文的行距为两倍行距。
  - latex中默认的行距就是1.3倍行距。

- 设置局部段落行距：

  - 用在`{}`把要设置的连续段落括起来，

  - 在这些段落前加 `\linespread{2}\selectfont`，

  - 则这些段落都被设置为两部行距。

  - 例如：

    ```latex
    \begin{document}
    {\linespread{2}\selectfont
    盖闻天地之数，有十二万九千六百岁为一元。将一元分为十二会，乃子、丑、寅、卯、辰、巳、午、未、申、酉、成、亥之十二支也。每会该一万八百岁。且就一日而论:子时得阳气，而丑则鸡鸣:实不通光，而卯则日出;辰时食后，而已则挨排;日午天中，而未则西蹉;申时哺而日落西，成黄昏而人定亥。譬于大数，若到戌会之终，则天地昏缯而万物否矣。
    
    再去五千四百岁，交亥会之初，则当黑暗，而两间人物俱无矣，故曰混沌。又五千四百岁，亥会将终，贞下起元，近子之会，而复逐渐开明。邵康节曰:“冬至子之半，天心无改移。一阳初动处，万物未生时。”到此天始有根。
    
    }
    
    再五千四百岁，正当子会，轻清上腾，有日有月有星有辰。日月星辰，谓之四象。故曰天开于子。又经五千四百岁，子会将终，近丑之会，而逐渐坚实。《易》曰:“大哉乾元!至哉坤元!万物资生，乃顺承天。”至此，地始凝结。
    \end{document}
    ```

    - **注意：**行距只有在一个段落被分段后才生效，如果把`}`直接放在“天始有根"后，则该段会被认为不是一个段落，该段不会被设置为2倍行距。

- `setspace` 宏包

  - 在导言区导入宏包：`\usepackage{setspace}`

  - 设置全文的行距：`\setstrech{2}`

    - 这命令的作用是设置全文的行距为2倍字号。
    - **注意：**`\setstrech` 设置的行距**是字号的倍数**，而不是基础行距的倍数，和`\linespread`不一样。

  - 设置局部的行距：使用space环境

    - 例如：

      ```latex
      \begin{spacing}{1.5}
      再五千四百岁，正当子会，轻清上腾，有日有月有星有辰。日月星辰，谓之四象。故曰天开于子。又经五千四百岁，子会将终，近丑之会，而逐渐坚实。《易》曰:“大哉乾元!至哉坤元!万物资生，乃顺承天。”至此，地始凝结。\par
      再五千四百岁，正当丑会，重浊下凝，有水有火有山有石有土。水火山石土，谓之五形。故曰地辟于丑。又经五千四百岁，丑会终而寅会之初，发生万物。历曰:“天气下降，地气上升;天地交合，群物皆生。”至此，天清地爽，阴阳交合。
      \end{spacing}
      ```



## 5. 字体字号

### 5.1 字体

#### 5.1.1 正文字号

正文字号是全文的正文的默认字号，在latex中只能取为**三种**：10pt、11pt、12pt。

在`\documentclass`的选项中设置正文字号：

- 如：`\documentclass[11pt]`，则正文字号为11pt。

#### 5.1.2 latex字号

在latex中定义了一些关键字（命令）用于更改字号，具体如下表：<img src="D:\!学习\笔记\notes\latex笔记\图片\字体相对尺寸.png" alt="字体相对尺寸"  />

说明：

- 正文字号选择不同，同样的命令会对应不同的字号。
- 例如：
  - 当正文字号是11pt时，`\tiny`命令对应6pt。
  - 当正文字号是12pt时，`\small`命令对应11pt。

#### 5.1.3 中文字号

针对中文字号，导入ctex宏包后，可以使用`\zihao`命令，来指定中文字号。

格式：

- `\zihao{<字号>}`

- 如：`{\zihao{-4} 盖闻天地之数，有十二万九千六百岁为一元。...}`，表示花括号内的文本为小四字号。

不同的参数对应不同的中文字号，具体见：[字号对照表](./图片/字号对照表.png)

#### 5.1.4 自定义字体大小

格式：`\fontsize{<字号大小>}{<行间距>}\selectfont`

例如：

```latex
{\fontsize{<20pt>}{<22pt>}\selectfont  % 字号20pt，行间距22pt 
盖闻天地之数，有十二万九千六百岁为一元。...}
```
#### 5.1.5 修改字号

如果直接在环境中某处写`\large`，效果是把这个命令后的所有文本都设置为`large`字号。

注意：

- 这种效果的作用范围只包含这个命令当前所在的环境。例如：

  ```latex
  \begin{document}
  
  \begin{center}
  \large  %该命令的作用是令当前的center环境中的字号为large，并不会让document环境中的字号为large
  第一回\hspace{2em}灵根育孕源流出\hspace{1em}心性修持大道生  \vspace{1em}\\
  \end{center}
  
  诗曰:
  \begin{center}
  混沌未分天地乱，茫茫渺渺无人见。  \\
  自从盘古破鸿蒙，开辟从兹清浊辨。  \\
  覆载群生仰至仁，发明万物皆成善。  \\
  欲知造化会元功，须看西游释厄传。  \\
  \end{center}
  \end{document]}
  ```

- 如果只想修改环境中局部的字号，可以用下方讲述的方式。

---

有两种方式可以局部修改字号：

- `{<字号> <文本>}`。

  - 即用花括号把要更改字号的文本括起来。
  - 如`{\large 盖闻天地之数，有十二万九千六百岁为一元。...}`

- 创建字号环境，如：

  ```latex
  \begin{large}
  盖闻天地之数，有十二万九千六百岁为一元。...
  \end{large}
  ```

- **注意：**
  - 只有latex字号才可以使用创建字号环境的方式，
  - 中文字号和自定义字号都只能采用花括号的方式。

### 5.2 字体

#### 5.2.1 英文字体

英文中常用的**字体命令**有：

- `\bfseries`：加粗
- `\itshape`：斜体
- `\rmfamily`：衬线字体（罗马字体）
- `\sffamily`：非衬线字体
- `\ttfamily`：等宽字体

注意：

- 衬线字体、非衬线字体等都是一类具体字体的统称，如：字体consola属于非衬线字体。
- 在latex中，`\rmfamily`、`\sffamily`、`\ttfamily`这些字体命令 已经预定义了对应哪些具体的字体。
  - 如：`\sffamily`使用的是`sans serif`字体。

---

latex的字体命令的详细描述可以参考下表：

<img src=".\图片\latex字体命令.png" alt="latex字体命令" style="zoom:80%;" />

#### 5.2.2 中文字体

在导入ctex宏包后，有一些预设的字体命令：

- `\songti`：宋体，默认情况下使用的就是宋体。
- `\heiti`：黑体
- `\yahei`：雅黑
- `\fangsong`：仿宋
- `\kaishu`：楷书
- `\lishu`：隶书
- `\youyuan`：幼圆

#### 5.2.3 查看系统字体

如果要查看电脑系统已经安装了哪些字体，可以运行下面的语句：

- `fc -list -f "%{family}\n"`
- `fc -list -f "%{family}\n":lang=zh`

**注意：**

- 这些命令要在系统控制台运行。
- 这些命令只有在电脑上安装了texlive后才能用。

#### 5.2.4 自定义字体命令

**更改字体命令对应的字体：**

- 对于英文：

  - `setmainfont{<字体名称>}`：设置主字体（即正文字体）
    - 更改掉主字体后，正文的字体都会改变。
    - 默认的主字体是`\rmfamily`字体，即`roman`字体。

  - `setsansfont{<字体名称>}`：设置衬线字体
    - 更改掉衬线字体后，被`\sffamily`控制的文字都被会更改字体。

  - `setmonofont{<字体名称>}`：设置等宽字体
    - 更改掉等宽字体后，被`\ttfamily`控制的文字都被会更改字体。

- 对于中文：

  - `setCJKmainfont{<字体名称>}`：设置中文的主字体（即中文正文字体）

**指定想要的字体：**

- 对于英文：

  - `\fontspec`：如果想要更改局部字体为某一具体字体，则可以用`\fontspec`命令。

  - `\newfontfamily{<新命令的名称>}{<对应的字体>}`：可以定义一个自己的字体命令。

- 对于中文：

  - `\CJKfontspec`：如果想要更改局部的中文字体为某一具体中文字体，则可以用`\CJKfontspec`命令。

  - `\newCJKfontfamily{<新命令的名称>}{<对应的字体>}`：可以定义一个自己的中文字体命令。

- 例如：

  ```latex
  \documentclass{article}
  \usepackage{fontspec}
  \newfontfamily{\co}{Consolas}  % 自定义一个字体命令，叫做\co
  
  \title{I have a dream}
  \author{Martin Luther King}
  \date{\today}
  
  \begin{document}
  	\maketitle
  	
  	I Have a Dream
  	
  	{\co
      I am happy to join with you today in what will go down in history as the greatest demonstration for freedom in the history of our nation.}  % 使用自定义的字体命令
  		
  	{\fontspec{Arial}
      Five score years ago, a great American, in whose symbolic shadow we stand today, signed the Emancipation Proclamation. This momentous decree came as a great beacon light of hope to millions of Negro slaves who had been seared in the flames of withering injustice. It came as a joyous daybreak to end the long night of their captivity.}  % 令这段文字为Arial字体
  \end{document}
  ```

  - 注意：
    - 实现如果要使用`\fontspec`和`\newfontfamily`命令，需要导入**宏包`fontspec`**。

- 再比如：

  ```latex
  \documentclass{article}
  \usepackage{ctex}
  
  \title{闻官军收河南河北}
  \author{杜甫}
  \date{\today}
  
  \setCJKmainfont{华文行楷}  % 令中文的正文字体为华文行楷
  
  \begin{document}
  	\maketitle
  	
  	\begin{center}
  	剑外忽传收蓟北，初闻涕泪满衣裳。  \\
  	却看妻子愁何在，漫卷诗书喜欲狂。  \\
  	白日放歌须纵酒，青春作伴好还乡。  \\
  	即从巴峡穿巫峡，便下襄阳向洛阳。  \\
  	\end{center}
  \end{document}
  ```



## 7. 纸张和页边距

设置页边距需要使用到宏包：`\usepackage{geometry}`

### 7.1 横版

如果在导入`geometry`时，设置选项`[landscape]`可以使全文页面变为横版。

即：`\usepackage[landscape]{geometry}`

### 7.2 纸张大小

导入宏包`geometry`后，用命令`\geometry`可以设置纸张大小

- 格式：`\geometry{<纸张大小>}`
- 例如：
  - `\geometry{a4paper}`：设置纸张大小为A4。

### 7.3 设置边距

用命令`geometry`可以控制页边距

- `margin`：设置总边距（即上、下、左、右页边距）
- `hmargin`：设置水平页边距（即左边距和右边距）
- `vmargin`：设置垂直边距（即上边距和下边距）
- `left`：设置左页边距
- `right`：设置右页边距
- `top`：设置上页边距
- `bottom`：设置下页边距

例如：

```latex
\geometry{a4paper, margin=2cm}  % 设置全部页边距为2cm
\geometry{a4paper, hmargin=2cm, vmargin=2cm}  % 设置水平页边距为2cm，垂直页边距为2cm
\geometry{a4paper, left=2cm, right=1cm, top=2.5cm, bottom=2.5cm}  % 设置左页边距为2cm，右页边距为1cm，上页边距为2.5cm，下页边距为2.5cm
```



## 8. 分页和分栏

**分页命令：**

- `\newpage`：新起一页，如果是文章是分栏的话，则**新起一栏**。
- `\clearpage`：新起一页，不管文章是不是分校，都**新起一页**。

**分栏有两种方式：**

- 在`\documentclass`中设置分栏。
  - 如：`\documentclass[twocolumn]{article}`，设置全文为两栏。
- 用`\twocolumn`命令
  - 在该命令后的内容被**新起一页**，并**分成两栏**。
  - 如果要恢复成一栏，则用`\onecolumn`命令。



## 9. 摘要、摘录、脚注、边注

### 9.1 摘要

编写**摘要**可以用摘要环境`abstract`

即：

```latex
\begin{abstract}  % 摘要环境
...
\end{abstract}
```

注意：

- 这个摘要环境**不适合写论文的摘要**，感觉像是用来写一本书的摘要。

---

### 9.2 摘录

编写摘录可以用摘录环境，摘录环境有多种：

- `quote`：适用于摘录短句
  - quote环境中的语句都是没有缩进的。
- `quotation`：适用于摘录段落
  - quotation环境中的语句带有缩进。
- `verse`：适用于摘录诗歌
  - quotation环境中的语句首先无缩进，除首行外的行有缩进，即**悬挂两个字符**。
- `verbatim`：适用于摘录代码
  - 源码中有缩进，编译后也有缩进。

**注意：**

- 所有的摘录环境相对于正文都是前后都缩进两个字符。
- 而quotation和verse是在相对于正文前后缩进两个字符的基础上，再有相应的缩进。

### 9.3 脚注

脚注可以通过`\footnote`命令来添加

- 格式：`\footnote{<脚注内容>}`
  - 例如：`\footnote{这是一个脚注}`

- 说明：

  - \footnote可以在文中任何位置使用。
    - 可以在正文中，也可以在摘录中。

  - 使用\footnote命令后，当页的最一方会出现脚注的一栏。
  - 脚注默认会按照从小到大的顺序编号，即第一个脚注的编号是1、第二个脚注的编号是2。
    - 如果要更改脚注编号的记数，则用`\setcounter{footnote}{0}`
    - 在该命令后的脚注会重新从1开始编号。

### 9.4 边注

可以使用`\marginpar`命令来进行边注。

- 格式：`\marginpar{<边注内容>}`
  - 例如：`\marginpar{这是一个边注}`

- 说明：
  - 使用边注后会在被边注处的段落右侧显示边注的文字。
  - 默认边注的文字是正文大小，可以用`\footnotesize`命令使其变为脚注字号

### 9.5 示例

```latex
\documentclass{article}
\usepackage{ctex}

\title{杜甫诗三首}
\author{杜甫}
\date{\today}

\begin{document}
\maketitle

\begin{abstract}
杜甫（712年2月12日，770年），字子美，自号少陵野老，祖籍襄阳（今属湖北），自其曾祖时迁居巩县（今河南巩义西南）。唐代著名现实主义诗人。与李白合称“李杜”“大李杜”，也常被称为“老杜”。
\end{abstract}

\newpage
杜甫自幼好学，知识渊博，颇有政治抱负。唐玄宗开元后期，举进士不第，漫游各地。后寓居长安近十年，未能有所施展，生活贫困，逐渐对当时的社会状况有较深的认识。
\marginpar{\footnotesize 这段内容来自百度}  % 边注，脚注文字在大小就是\footnotesize的大小

% quote环境中默认没有缩进，但是相对于正文前后缩进
\begin{quote}
风急天高猿啸哀，渚清沙白鸟飞回。  \\
无边落木萧萧下，不尽长江滚滚来。  \\
万里悲秋常作客，百年多病独登台。  \\
艰难苦恨繁霜鬓，潦倒新停浊酒杯。
\footnote{唐·杜甫 《登高》}
\end{quote}

% quotation环境中首行缩进
\begin{quotation}
杜甫的祖父为唐初诗人杜审言。杜审言很有才华，但恃才傲世。少与李峤、崔融、苏味道合称“文章四友”。唐高宗咸亨元年（670）擢进士第，为隰城尉。后转洛阳丞。武后圣历元年（698），坐事贬吉州司户参军。
\footnote{杜甫身世}
\end{quotation}

% 脚注重新记数
\setcounter{footnote}{0}

% verse环境中首行顶格，其余行缩进
\begin{verse}
杜甫出身于京兆杜氏，乃北方的大士族。其远祖为汉武帝有名的酷吏杜周，祖父杜审言。杜甫与唐代另一大诗人即“小李杜”的杜牧同为晋代大学者、名将杜预之后。不过两支派甚远，杜甫出自杜预次子杜耽，而杜牧出自杜预少子杜尹。
\footnote{杜甫身世2}
\end{verse}


% verbatim会把原文一字不差的显示（不会相对于原文前后缩进）
\begin{verbatim}
    #include<iostream>
    int main{
        cout << "hello world" << endl;
        return 0;
    }
\end{verbatim}

\end{document}
```



## 10. 盒子

### 10.1 盒子类别

盒子的作用类似于在正文中插入一个文本框，可以对文本框做旋转、加边框线等操作。

盒子有多种，对应不同的命令：

- `\fbox`
- `\framebox`
- `\begin{minipage}`
- `\rule`
- `\rotatebox`
- `\rlap`、`\llap`

### 10.2 \fbox

`\fbox`的作用就是用一边框线把文字包裹起来。

- 格式：`\fbox{<文字内容>}`
- 样式设置：
  - `\setlength{\fboxrule}{<粗细>}`：可以设置`\fbox`边框线的粗细。
    - 例如：`\setlength{\fboxrule}{2pt}`，效果为设置边框线为2pt粗。
  - `\setlength{\fboxsep}{<距离>}`：可以设置内边距，即边框线到其内文字的距离。
    - 例如：`\setlength{\fboxsep}{2em}`，效果为设置边框线到其内文字的距离为2个中文字大小。
- 注意：
  - `\fbox`中的内容**不会自动换行**，即：如果文字过多且没有手动换行，这个盒子会超出纸张大小。
  - 使用`\setlength{\fboxrule}{<粗细>} `和 `\setlength{\fboxsep}{<距离>}`时，可能会对其他类型的盒子也造成影响。

### 10.3 \framebox

`\framebox`的作用和`\fbox`的作用差不多，都是用边框线把文字包裹起来，但是多一些功能。

- 格式：`\framebox[<宽度>][<对齐方式>]{<文字内容>}`
  - 宽度：可以指定盒子的宽度
  - 对齐方式：
    - `l`：表示“left”，左对齐。
    - `c`：表示“center”，居中对齐。
    - `r`：表示“right”，右对齐。
    - `s`：两端对齐。

- 注意：
  - 对齐方式指的是盒子内的文本相对于盒子的对齐方式。
    - 例如：`\framebox[10em][r]{哈哈哈}`，
    - 表示这个盒子有10个中文字的宽度，而要显示的文本只有“哈哈哈”三个字，
    - 采用右对齐的方式，则表示文本“哈哈哈”会显示在盒子的右侧。
    - 如果不指定对齐方式，则默认为居中对齐。

### 10.4 minipage环境

minipage是一个环境，他可以把很多内容整合到一个盒子里，默认是**不带边框的盒子**。

- 格式：`\begin{minipage}[<外对齐>][<高度>][<内对齐>]{<宽度>}`
  - 外对齐和内对齐的选项都有以下三种：
    - `t`：表示“top”，顶端对齐。
    - `m`：表示“middle”，居中对齐。
    - `b`：表示“bottom”，底部对齐。
  - 外对齐指的是盒子相对于盒子外（即盒子两侧）的文本的对齐方式。
  - 内对齐指的是盒子内部的文本的对齐方式。

### 10.4 \rule

`\rule`的作用是画一个黑色矩形。

- 格式：`\rule[<相对位置>]{<宽度>}{<高度>}`
  - 相对位置指的是黑色矩形相对两边的文本在垂直方向上的距离。
    - 如果为负数，则黑色矩形下沉。
    - 如果为正数，则黑色矩形上升。
  - 例如：`\rule[-2pt]{3em}{2pt}`，表示一个下沉2pt，宽为3em，高为2pt的黑色矩形。

### 10.5 \rotatebox

`\rotatebox`可以让其内的内容绕盒子上一点旋转。

- 格式：`\rotatebox[origin=<旋转点>]{<角度（角度制）>}{<文字内容>}`
  - 旋转点可以有三种选择：
    - `l`：绕盒子左端点旋转。
    - `c`：绕盒子中心旋转。
    - `r`：绕盒子右端点旋转。
- 注意：在使用`\rotatebox`命令之前，需要先导入宏包：`\usepackage{graphicx}`。

### 10.6 \rlap 和 \llap

`\rlap`和`\llap`的作用是把文字盖在另一文字之上。

- 格式：
  - `\rlap{<文字内容>}`
  - `\llap{<文字内容>}`

### 10.7 示例

```latex
\fbox{风急天高猿啸哀}，  % 盒子
\framebox[10em][l]{渚清沙白鸟飞回}。  \\  % 一个宽度为10em，其内文本左对齐的例盒子

前面的内容
\fbox{  % 因为minipage默认没有边框，为了可视化，用\fbox给minipage加上边框
\begin{minipage}[b][10em][t]{20em}  
% 外对齐方式为底部对齐，minipage的高度为10em，内对齐方式为顶部对齐，minipage的宽度为20em
无边落木萧萧下，不尽长江滚滚来。  \\
万里悲秋常作客，百年多病独登台。  \\
艰难苦恨繁霜鬓，潦倒新停浊酒杯。
\end{minipage}}
后面的内容  \\

填空题\rule[-2pt]{3em}{1pt}  \\  % 画一个黑盒子当横线用

\rotatebox[origin=l]{45}{无边落木萧萧下}，  % 文字内容绕盒子左端点旋转45度
\rlap{今天}不尽长江滚滚来\llap{明天}。
```



## 11. 自定义命令

就像编程语言定义函数和宏一样，latex也可以自定义一些命令。

- latex自定义命令有**两种格式**（类似于无参函数和有参函数）：

  - `\newcommand{<命令名>}{<命令内容>}`
  - `\newcommand{<命令名>}[<参数数目>]{<命令内容>}`
    - 在命令内容中，可以用`#1`，`#2`的方式引用第一个、第二个传入参数

- 例如：

  ```latex
  \newcommand{\hello}{hello world!}  % 创建\hello 命令
  \hello  % 调用\hello命令
  
  \newcommand{\hi}[2]{\textbf{#1} \textit{#2}}  % 创建\hi命令，接收两个参数
  \hi{hello}{ world!}  % 调用\hi命令
  ```

---

修改已经存在的命令：

- `\renewcommand{<已经存在的命令>}{<命令内容>}`



## 12. 图片

### 12.1 插入图片

插入图片需要用到`graphicx`宏包，接着使用宏包中的`\includegraphicx`命令。

- **格式：**`\includegraphicx[<选项>]{<图片文件的路径>}`
  - 图片文件的路径可以是绝对路径，也可以是相对路径。
  - 图片文件名可以带后缀，也可以不带后缀。
  - 文件路径需要用`/`分隔，例如：`C:/Users/zw`，**不能是**`C:\Users\zw`
- **选项：**
  - `width`：指定图片的宽度
    - 例如：`\includegraphics[width=20pt]{img\girl}`，则插入当前目录下img目录中的girl图片，宽度为20pt。
    - 可以设置`width=\linewidth`，使得图片宽度等于正文一行的宽度。
      - 可以在`\linewidth`前加入数字来表示`linewidth`的几倍，
      - 例如：`width=0.5\linewidth`，则表示图片宽度为正文一行的0.5倍。
      - 如果正文是分栏排版，则`\lindewith`表示一栏文字的宽度，而不是一页文字的宽度。
  - `scale`：指定图片的缩放比例
    - 例如：`\includegraphics[width=20pt, scale=0.5]{img\girl}`，则插入的图片缩放为原来的0.5倍。
  - `angle`：指定图片的旋转角度
    - 例如：`\includegraphics[angle=45]{img\girl}`，则插入的图片旋转45度。
  - 注意：
    - 选项width和scale都是控制图片的宽度，不能同时设置，否则只有width会有效。
    - 例如：`\includegraphics[width=20pt, scale=0.5]{img\girl}`，图片并不会先是20pt的宽度，再缩小为10pt的宽度
    - 但是`\includegraphics[width=20pt, angle=45]{img\girl}`，会让图片先是20pt的宽度，两旋转45度。
- **草稿选项：**在导入`graphicx`宏包的时候，可以加上选项`draft`，即 `usepackage[draft]{graphicx}`
  - 这个选项会使得插入的图片都变为表示大小的方框，而不显示实际的图像，
  - 这个选项的作用是防止每次编译时都要加载图片，使得编译过慢，
  - 用draft选项后，编译时并不需要加载实际的图片，会提高编译，这对大型tex文件的编译加速有显著的作用。
  - 在最后确定文本不再修改后，可以把draft选项去掉，就能在文中显示完整的图片了。



### 12. 2 浮动体

浮动体是一个**环境**，其作用也是插入图片，与直接使用`\includegraphics`不同，用浮动体插入的图片在文档中的位置并不是固定的，而是会自动调整位置来达到更好的排版效果（所谓的自动调整，就是编译器帮你做了这件事），具体的例子在介绍完浮动体格式后会讲。

- 格式：

  ```latex
  \begin{figure}[<选项>]  % 浮动体环境
  	\centering  % 表示图片居中
  	\includegraphics[<选项>]{<图片路径>}  % 插入图片
  	\caption{<图片标题>}  % 给这个图片取一个标题，编译后会显示在图片的下方
  	\label{<图片标签>}  % 给这个图片取一个标签，编译后不会显示在正文中，这个标签用于在源码中引用该图片
  \end{figure}
  ```

- 选项：

  - `h`：表示“here”，优先考虑图片放置在原位上。

    - 原位在意思就是浮动环境在源代码中的位置，

    - 例如：

      ```latex
      前面的内容
      \begin{figure}[<选项>]  % 浮动体环境
      	\centering  % 表示图片居中
      	\includegraphics[<选项>]{<图片路径>}  % 插入图片
      	\caption{<图片标题>}  % 给这个图片取一个标题，编译后会显示在图片的下方
      	\label{<图片标签>}  % 给这个图片取一个标签，编译后不会显示在正文中，这个标签用于在源码中引用该图片
      \end{figure}
      后面的内容
      ```

    - 则原位表示图片出现在 文字“前面的内容” 和 文字“后面的内容” 之间，而不是浮动到这些文字上方或下方。

  - `t`：表示“top”，优先考虑图片放置在页面顶部。

  - `b`：表示“bottom”，优先考虑图片放置在页面底部。

  - `p`：表示“page”，优先考虑图片放置在新一页上。

  - `！`：忽略一些严格的限制。

- 注意：

  - 选项中写的参数对于编译器只是建议，并不会严格执行。
  - 例如：`\begin{figure}[h]`，则编译后图片并不是一定就出现在原位上。
  - 一般情况下，都是把好几个选项写在一块：`\begin{figure}[htbp!]`。
  - `\caption`如果写在`\includegraphics`上面，则标题在文字上面。

- 多栏的情况：

  - 在多栏的情况下，如果图片要忽略多栏的限制，依然**当做一栏**，就需要在环境名上加入星号`*`，即：`\begin{figure*}`，
  - 只是此时**选项**中只能填写`t`和`b`，而不能写`h`，如：`\begin{figure*}[tb]`



### 12.3 图片排版

#### 12.3.1 常用形式一

效果：多个图片共用一个标题

<img src=".\图片\图片排版常用形式一.png" alt="图片排版常用形式一" style="zoom: 67%;" />

思路：在`figure`环境下多次使用`\includegraphics`命令，插入多张图片。

代码：

```latex
\begin{figure}
    \centering
    \includegraphics[width=0.24\linewidth]{img/kid.png}  % 第一张图片
    \hspace{0.5em}
    \includegraphics[width=0.24\linewidth]{img/daiyu.jpg}  \\  % 第二图片
    \vspace{0.5em}
    \includegraphics[width=0.5\linewidth]{img/girl.jpeg}  % 第三图片
    \caption{女孩}
    \label{fig:enter-label}
\end{figure}
```

#### 12.3.2 常用形式二

效果：一行中排入两个图片，每个图片都有标题

<img src=".\图片\图片排版常用形式二.png" alt="图片排版常用形式二" style="zoom: 80%;" />

思路：在`figure`环境下创建两个`minipage`，并在`minipage`中指定图片标题。

代码：

```latex
\begin{figure}
    \centering
    \begin{minipage}{0.4\linewidth}
        \includegraphics[width=\linewidth]{img/kid.png}
        \caption{标题一}
        \label{fig:kid}
    \end{minipage}
    \hspace{2em}
    \begin{minipage}{0.4\linewidth}
        \includegraphics[width=\linewidth]{img/daiyu.jpg}
        \caption{标题二}
        \label{fig:girl}
    \end{minipage}
\end{figure}
```

#### 12.3.3 常用形式三

效果：多张图共用一个大标题，每个图片有一个小标题

<img src="D:\!学习\笔记\notes\latex笔记\图片\图片排版常用形式三.png" alt="图片排版常用形式三" style="zoom:67%;" />

思路：使用宏包`subcaption`，然后使用`subfigure`环境，其语法与`minipage`类似

代码：

```latex
\begin{figure}
    \centering
    \begin{subfigure}{0.24\linewidth}
        \includegraphics[width=\linewidth]{img/kid.png}
        \caption{子标题一}
    \end{subfigure}
    \hspace{0.5em}
    \begin{subfigure}{0.24\linewidth}
        \includegraphics[width=\linewidth]{img/cat.png}
        \caption{子标题二}  
    \end{subfigure}  \\
    \begin{subfigure}{0.5\linewidth}
        \centering
        \includegraphics[width=\linewidth]{img/girl.jpeg}
        \caption{子标题三}
    \end{subfigure}
    \caption{大标题}
\end{figure}
```

### 12.4 图片引用

使用`\ref`命令可以引用文中的图片的编号。

- 格式：`\ref{<图片标签>}`
  - 图片标签就是在figure环境中，用`\label`命令定义的。
  - 图片标签我们可以任意命名，但是为了方便管理和记忆，一般我们把图片标签起成这样的形式：`\label{fig:xxx}`
- 例如：`\ref{fig:girl}`

- 注意：
  - 编译后，`\ref`命令会被编译成引用图片的数字编号，而不带图这个字。
    - 如果需要，只能自己在文中手动加上。
    - 或者用下文讲的使用`hyperref`宏包。
  - 例如：`如图\ref{fig:girl}所示，这是一张照片。`

---

可以借用宏包`hyperref`，使用`\autoref`命令来实现一些更方便美观的引用，会在引用时不仅引用编号，还会在前面带上"图"这个字。

- 插入宏包：`\usepackage[hide links]{hyperref}`
  - 之所以加上`hide links`这个选项是因为默认`\autoref`编译后会在引用文字上加上边框，`hide links`是为了取消掉这个边框。
  - 默认使用`\aotoref`命令，编译后会在图片编号前加上figure这个前缀。
    - 如果要更改这个前缀，则用这条语句：`\renewcommand{\figureautorefname}{图}`

- 格式：`\autoref{<图片标签>}`

---

使用`\pageref`命令可以引用文中图片所在的页码。

- 格式：`\pageref{<图片标签>}`
- 注意：
  - 与`\ref`类似，`\pageref`命令编译后，只会显示为数字，而不带“页”这个字。

---

示例

```latex
如第\pageref{fig:daiyu}页 图\ref{fig:daiyu}所示，这是一张林黛玉的照片。  \\
% 编译结果
% 如第6页图4所示，这是一张林黛玉的照片。

如第\pageref{fig:daiyu}页 \autoref{fig:girl}所示，这是一张林黛玉的照片。
% 编译结果
% 如第5页 图 1所示，这是一张林黛玉的照片。
```



## 13. 列表

### 13.1 无序列表

无序列表可以用`itemize`环境实现，用`\item`命令一项一项列出内容。

- 格式：

  ```latex
  \begin{itemize}
  	\item[<列表符号>] <第一项内容>
  	\item[<列表符号>] <第二项内容>
  	
  	% 无序列表可以嵌套(最多可以嵌套四层，加上去)
  	\begin{itemize}
  		\item[<列表符号>] <第二项的第一项子内容>
  	\end{itemize}
  	...
  \end{itemize}
  ```

- 注意：

  - 无序列表最多可以嵌套四层，每层默认的列表符号为：`•`、`–`、`∗`、`·`。

  

### 13.2 有序列表

有序列表可以用`enumerate`环境来实现，用`\item`列出每一项内容，每一项内容前会加上有序的编号，其格式与无序列表类似。

- 格式：

  - ```latex
    \begin{enumerate}
    	\item[<列表符号>] <第一项内容>
    	\item[<列表符号>] <第二项内容>
    	
    	% 有序列表可以嵌套(最多可以嵌套四层，加上去)
    	\begin{enumerate}
    		\item[<列表符号>] <第二项的第一项子内容>
    	\end{enumerate}
    	...
    \end{enumerate}
    ```

- 例如：

  ```latex
  % 无序列表
  我来鹅城只办三件事：
  \begin{itemize}
      \item 公平！
      \begin{itemize}
          \item 你无敌了
              \begin{itemize}
                  \item 我无敌了吗
                  \item 是我之前对你太温柔了吗
                  \begin{itemize}
                      \item 你嘴上说着一套一套
                      \item 实际又做不出来
                  \end{itemize}
              \end{itemize}
          \item 你又无敌了
          \item 你太无敌了
      \end{itemize}
      \item 公平！
      \item[*] 还是他妈的公平！
  \end{itemize}
  
  % 有序列表
  我来鹅城只办三件事：
  \begin{enumerate}
      \item 公平！
      \begin{enumerate}
          \item 你无敌了
              \begin{enumerate}
                  \item 我无敌了吗
                  \item 是我之前对你太温柔了吗
                  \begin{enumerate}
                      \item 你嘴上说着一套一套
                      \item 实际又做不出来
                  \end{enumerate}
              \end{enumerate}
          \item 你又无敌了
          \item 你太无敌了
      \end{enumerate}
      \item 公平！
      \item[*] 还是他妈的公平！
  \end{enumerate}
  ```

- 效果：

  <img src=".\图片\列表效果.png" alt="无序列表效果" style="zoom: 50%;" />

### 13.3 描述环境

使用环境`description`可以用latex中的描述格式。

- 格式：

  ```latex
  \begin{description}
  	\item[<描述对象>] <描述内容>
  \end{description}
  ```

- 例如：

  ```latex
  \begin{description}
      \item[杜甫] 唐代著名现实主义诗人
      \item[李白] 唐朝伟大的浪漫主义诗人
  \end{description}
  ```

- 效果：<img src="D:\!学习\笔记\notes\latex笔记\图片\描述环境效果.png" alt="描述环境效果" style="zoom:80%;" />



## 14. 一些空格

- `\hspace`、`\vspace`：增加水平空格和垂直空格





# 二、表格

## 1. 基本表格格式

在latex中绘制表格可以用`tabular`环境

- **格式：**

  ```latex
  \begin{tabular}[<外对齐>]{<列格式>}
  <内容>	& <内容>	  \\
  <内容>	& <内容>	  \\
  \end{tabular}
  ```

  - 表格中同一行的相邻单元格用`&`来表示区分。
  - 表格加一行用`\\`。

- **外对齐：**和minipage环境类似 ，有三种外对齐方式

  - `t`：顶部对齐。
  - `m`：居中对齐。
  - `b`：底部对齐。

- **列格式：**

  - 每个单元格有三种对齐方式：
    - `l`：左对齐。
    - `c`：居中对齐。
    - `r`：右对齐。
    - `p{<单元格宽度>}`：可以限制这一列单元格的宽度。
  - 需要表格有多少列，就写多少个对齐方式。
    - 例如：`\begin{tabular}{ccl}`，则表示表格有三列，第一列的单元格都是居中对齐，第二列也是居中对齐，第三列左对齐。
  - 如果有很多列，可以有简写形式：`*{<重复次数>}{<重复内容>}`。
    - 例如：`\begin{tabular}{*{5}{|c}|}`等价于`\begin{tabular}{|c|c|c|c|c|}`

- **加边框线：**默认表格每个单元之间没有边框线，需要额外的命令来加上边框线

  - `\hline`：从左到右在表格上画一条边框线。
  - `\cline{<第m列单元格>-<第n列单元格>}`：只从第m列单元格 画到 第n列单元格 的横线。
    - 例如：`\cline{2-4}`，则只从第2列画到第4列的横线。
  - 在列格式中添加`|`，可以从上到下在表格上画一条边框线。
    - 例如：`\beign{tabular}{|c|c|c|}`，则表示表格的每一列都被隔开了。

- 示例：

  ```latex
  前面的内容
  % 画一个5*5的表格
  \begin{tabular}[t]{|p{3em}*{4}{|c}|}  % 五列的表格，等价于\begin{tabular}{|p{3em}|c|c|c|c|}
      \hline  % 水平分割线
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      \hline
      冯宝宝宝宝宝宝宝宝宝宝    &    85   &      90     &    85      & 合格      \\
      \hline
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      \hline
      张灵玉    &    90   &      82     &    75      & 合格    \\
      \hline
      诸葛青    &    75   &      90     &    58      & 不合格    \\
      \hline
  \end{tabular}
  后面的内容
  ```

- 效果：

​			<img src="D:\!学习\笔记\notes\latex笔记\图片\基本表格效果.png" alt="基本表格效果" style="zoom:80%;" />



## 2. 浮动表格和表格引用

浮动表格和浮动图片类似，可以由编译自动调整表格位置，自动优化排版。

浮动表格可以由`table`环境创建。

- 格式：

  ```latex
  \beign{table}[<选项>]
  	\centering
  	\caption{<表格标题>}  % 一般要求表格标题放在表格上方
  	\begin{tabular}
  		<表格内容>
  	\end{tabular}
  	\label{<表格标签>}  % 和图片标签类似，是该表格的唯一标识符，用于表格引用
  \end{table}
  ```

- 选项：浮动表格的选项和浮动图片类似，可以有以下几个

  - `h`：表示“here”，优先考虑图片放置在原位上。
  - `t`：表示“top”，优先考虑图片放置在页面顶部。
  - `b`：表示“bottom”，优先考虑图片放置在页面底部。
  - `p`：表示“page”，优先考虑图片放置在新一页上。
  - `！`：忽略一些严格的限制。

---

表格引用类似于图片引用，可以用`\ref`命令，也可以导入宏包`hyperref`，用`\autoref`命令。

- 格式：`\ref{<表格标签>}`
  - 或：`\usepackage[hide links]{hyperref}` + `\renewcommand{\tableautorefname}{表}` + `\autoref{<表格标签>}`



## 3. 表格常用形式

### 3.1 表格常用形式一

效果：

<img src=".\图片\表格常用形式一.png" alt="表格常用形式一" style="zoom:80%;" />

思路：导入宏包`diagbox`，使用`\diagbox`命令来完成左上角的形式。

代码：

```latex
\begin{tabular}[t]{*{5}{|c}|}  % 五列的表格，等价于\begin{tabular}{|p{3em}|c|c|c|c|}
    \hline  % 水平分割线
    \diagbox{内容}{分数}{姓名}      &   \textbf{面试}  &   \heiti 理论知识   &   \heiti 上机考试  &  \heiti 考评结果  \\
    \hline
    冯宝宝    &    85   &      90     &    85      & 合格      \\
    \hline
    张楚岚    &    90   &      55     &    60      & 不合格    \\
    \hline
    张灵玉    &    90   &      82     &    75      & 合格    \\
    \hline
    诸葛青    &    75   &      90     &    58      & 不合格    \\
    \hline
\end{tabular}
```

### 3.2 表格常用形式二

效果：

<img src=".\图片\表格常用形式二.png" alt="表格常用形式二" style="zoom:80%;" />

思路：使用`\multirow`和`\multicolumn`来合并单元格

- 在使用`\multirow`命令前需要导入宏包`multirow`，使用`\multicolumn`不需要导入宏包

- 格式：`\multirow{<向下合并的数目>}{<合并后的宽度>}{<合并单元格后的内容>}`
  - `<合并后的宽度>`可以用`*`来表示自动根据内容来设置宽度。
  - 例如：`\multirow{2}{*}{姓名}`，表示向下合并一个单元格，合并后的内容为“姓名”，宽度自动调整。
- 格式：`\multicolumn{<向右合并的数目>}{<合并后的列格式>}{<合并单元格后的内容>}`

代码：

```latex
\begin{table}[htbp!]
    \centering
    \caption{表格常用形式二}
    \begin{tabular}{*{5}{|c}|} 
        \hline  % 水平分割线
        \multirow{2}{*}{姓名} &  \multicolumn{3}{c|}{考试内容}  & \multirow{2}{*}{考评结果}  \\
        \cline{2-4}
                 &   面试  &    理论知识   &  上机考试  &           \\
        \hline
        冯宝宝    &    85   &      90     &    85      & 合格      \\
    
        张楚岚    &    90   &      55     &    60      & 不合格    \\
    
        张灵玉    &    90   &      82     &    75      & 合格      \\
    
        诸葛青    &    75   &      90     &    58      & 不合格    \\
        \hline
    \end{tabular}
\end{table}
```



## 4. 三线表

要绘制三线表，可以借助宏包`booktabs`

- 三线表涉及的命令主要有：

  - `\toprule`：画顶部粗线。

  - `\midrule`：画表头线（中间细线）。

  - `\bottomrule`：画底部粗线。

  - `\cmidrule`：类似于\cline，画指定两列间的横线。

  - `\specialrule`：用于分隔表格（不知道有什么用）。

- 格式：`\toprule[<线条粗细>]`

  - `\midrule`、`\bottomrule`的语法格式和`\toprule`一样

- 格式：`\cmidrule{<第m列>-<第n列>}`
  - 如果要多行，则使用`\morecmidrules`命令。
  - 例如：`\cmidrule{2-4} \morecmidrules \cmidrule{2-4}`
- 格式：`specialrule{<粗细>}{<上空白距离>}{<下空白距离>}`
  - 例如：`\specialrule{1pt}{1em}{1em}`，表示线条粗细为1pt，线条上方空白1em的大小，线条下方也空白1em的大小。

示例：

```latex
\begin{table}[htbp!]
    \centering
    \caption{表格常用形式二}
    \begin{tabular}{*{5}{|c}|} 
        \hline  % 水平分割线
        \multirow{2}{*}{姓名}     &   \multicolumn{3}{c|}{考试内容}  & \multirow{2}{*}{考评结果}  \\
        \cline{2-4}
                 &   面试  &    理论知识   &  上机考试  &           \\
        \hline
        冯宝宝    &    85   &      90     &    85      & 合格      \\
    
        张楚岚    &    90   &      55     &    60      & 不合格    \\
    
        张灵玉    &    90   &      82     &    75      & 合格      \\
    
        诸葛青    &    75   &      90     &    58      & 不合格    \\
        \hline
    \end{tabular}
\end{table}
```

效果：

<img src=".\图片\三线表.png" alt="三线表" style="zoom:80%;" />



## 5.  可变宽表格

可变宽表格：给定表格的总宽度，表格可以自动调整每个单元格的宽度来满足给定的总宽度。

使用可变宽表格需要导入宏包`tabularx`

- 格式：`\begin{tabularx}{<表格总宽度>}{<列格式>}`
- 列格式：
  - `c`、`l`、`r`：分别表示居中对齐、左对齐、右对齐。
  - `X`：表示可变宽列。
    - 例如：`\begin{tabularx}{\linewidth}{|X|c|c|c|}`，则第一列会自动调整宽度，来使得表格的总宽度为指定的`\linewidth`，而列格式为`c`的列则为默认列宽，不会自动变宽或变窄。
    - 注意：这个`X`是**大写**，如果写成小写会报错。
- 可变宽列的对齐方式：默认情况下可变宽列中的文字内容都是左对齐的。
  - `>{\raggedright}`：可变宽列左对齐。
  - `>{\raggedleft}`：可变宽列左对齐
  - `>{\centering}`：可变宽列左对齐
  - 例如：`\begin{tabularx}{\linewidth}{|>{\reggedright}X|>{\raggedleft}X|>{\centering}X|}`。
    - 则第一列是左对齐，第二列是右对齐，第三列是居中对齐。

  - 注意：
    - `\raggedright`对应的是**左对齐**，而不是右对齐，单词ragged是不齐的意思，raggedright就是右边不齐的意思，那也就是左边齐。
    - 有时候改变可变宽列的对齐方式时（尤其是对于最后一列），表格会错乱，这时需要在列格式后添加语句`\arraybackslash`。
    - 例如：`>{\raggedleft}X\arraybackslash`。
    - 如果觉得命令太长，可以使用`\newcommand`命令，如：`\newcommand{\RR}{>{\raggedleft}X\arraybackslash}`


示例：

```latex
    \newcommnad{\RC}{>{\centering}X\bcakslash}
    \begin{tabularx}{\linewidth}{
    |X|  % 可变宽列，默认为左对齐
    >{\raggedright\arraybackslash}X|  % 第二列左对齐，可变宽
    >{\raggedleft\arraybackslash}X|  % 第三列右对齐，可变宽
    c|
    \RC}  % 第五列居中对齐，可变宽
            \hline  % 水平分割线
            姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
            \hline
            冯宝宝    &    85   &      90     &    85      & 合格      \\
            \hline
            张楚岚    &    90   &      55     &    60      & 不合格    \\
            \hline
            张灵玉    &    90   &      82     &    75      & 合格    \\
            \hline
            诸葛青    &    75   &      90     &    58      & 不合格    \\
            \hline
    \end{tabularx}
```

效果：

<img src=".\图片\可变宽表格.png" alt="可变宽表格" style="zoom:80%;" />





# 三. 新式表格

环境`tabular`是很早的时候开发出来的，已经很古老，有些时候已经不能满足现在的要求。所以为了适应现在人们对表格的排版需求，有人研发了新式表格，来提供更方便和强大的功能。

## 1. 基本用法

使用新式表格需要导入宏包`taluarray`，然后使用环境`tblr`来创建新式表格。

- 格式：`\begin{tblr}`
  - 新式表格默认每个单元格内的内容距离单元格边框间隔一段距离，**看起来比旧表格宽松**。
  - 如果要更改这个间隔，可以用命令`\SetTblrInner{rowsep=0pt}`

- 例如：

  ```latex
  % \SetTblrInner{rowsep=0pt}  % 取消行间距
  \begin{tblr}{|c|c|c|c|c|}  % 使用环境tblr创建新式表格
          \hline  % 水平分割线
          姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
          \hline
          冯宝宝    &    85   &      90     &    85      & 合格      \\
          \hline
          张楚岚    &    90   &      55     &    60      & 不合格    \\
          \hline
          张灵玉    &    90   &      82     &    75      & 合格    \\
          \hline
          诸葛青    &    75   &      90     &    58      & 不合格    \\
          \hline
      \end{tblr}
  ```

效果：

<img src=".\图片\新式表格基本用法.png" alt="新式表格基本用法" style="zoom:80%;" />



## 2. 换行

在新式表格中，每个单元格中的内容允许包含换行，即可以把多行内容放到一个单元格里，这在旧表格中是很难实现的（最多通过限制单元格宽度来令他自动换行）。

- 格式：用花括号把一个单元格包裹起来，然后在其内换行。

  - 例如：`{哈哈 \\ 啦啦}`

- 例如：

  ```latex
  \begin{tblr}{|c|c|c|c|c|}  % 使用环境tblr创建新式表格
  \hline  % 水平分割线
  姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
  \hline
  {冯宝宝  \\ 冯宝宝  \\ 冯宝宝}   &    85   &      90     &    85      & 合格      \\
  \hline
  {张楚岚  \\ 张楚岚}   &    90   &      55     &    60      & 不合格    \\
  \hline
  张灵玉    &    90   &      82     &    75      & 合格    \\
  \hline
  诸葛青    &    75   &      90     &    58      & 不合格    \\
  \hline
  \end{tblr}
  ```

  <img src="D:\!学习\笔记\notes\latex笔记\图片\新式表格换行.png" alt="新式表格换行" style="zoom:80%;" />



## 3. Q格式

Q格式是一种列格式，不仅可以设置列单元水平方向上的对齐方式，还可以设置垂直方向上的对齐方式，以及列单元的宽度。

- 格式：`Q[<水平对齐方式>, <垂直对齐方式>, <宽度>]`

- 水平对齐方式：

  - `c`、`r`、`l`：分别表示水平方向上居中对齐，右对齐，左对齐。

- 垂直对齐方式：

  - `h`：表示“head”，顶端对齐。
  - `f`：表示“foot”，底部对齐。
  - `m`：表示“middle”，垂直方向上居中对齐。

- 注意：

  - 在第一列指定的列格式的垂直对齐方式，会默认也赋给其他列。
  - Q格式中的三个选项可以任意交换顺序，也可以不指定全参数。
  - 例如：
    - `Q[r, h, 2em]`：指定列格式为右对齐，顶端对齐，宽度为2em。
    - `Q[f, l]`：指定列格式为底部对齐，左对齐。
    - `Q[2em]`：指定列格式为2em宽度。

- 例如：

  ```latex
  \begin{tblr}{|Q[c, m]|Q[l, h]|Q[f, r]|Q[c, 4em]|c|}
   % 第一列指定为水平、垂直方向居中
   % 第二列指定为左对齐，顶端对齐，即左上角对齐。
   % 第三列指定为底部对齐，右对齐。
   % 第四列指定为水平居中对齐，垂直居中对齐（受第一列指定），宽度为4em
   % 跟第一列格式一致。
      \hline  % 水平分割线
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      \hline
      {冯宝宝  \\ 冯宝宝  \\ 冯宝宝}   &    85   &      90     &    85      & 合格      \\
      \hline
      {张楚岚  \\ 张楚岚}   &    90   &      55     &    60      & 不合格    \\
      \hline
      张灵玉    &    90   &      82     &    75      & 合格    \\
      \hline
      诸葛青    &    75   &      90     &    58      & 不合格    \\
      \hline
  \end{tblr}
  ```

<img src=".\图片\新式表格Q格式.png" alt="新式表格Q格式" style="zoom:80%;" />



## 4. 宽度调整

如果需要功能更强大的列单元格宽度调整，可以使用`X格式`。

与可变宽表格类似，新式表格的列格式`X`也具有可变宽的功能，但也跟`Q`格式类似，可以在其后追加丰富的选项。

- 格式：`X[<选项>]`

  - 默认不加选项，则`X格式`表示该列会**自动调整宽度**，来使得表格的总体宽度达到指定值（默认为页面宽度）。

- 选项：

  - 水平对齐：
    - `c`、`r`、`l`：分别表示水平方向上居中对齐、右对齐、左对齐。
  - 垂直对齐：
    - `h`、`m`、`f`：分别表示垂直方向上顶端对齐、居中对齐、底部对齐。
  - 比例系数：当存在多个可变宽列时，这些列间会按照这个比例系数来分配列宽。
    - 比例系数可以是负数，表示该列宽度为满足单元格内容的最小宽度。
    - 只要是负数，无论多负效果都一样，即：-1和-100的效果是一样的。
  - 列宽度：可以直接指定列宽度为多少，此时自动宽度调整就不再发挥作用。
  - 例如：`\begin{tblr}|{X[c, m, 3]|X[4]|c|}`
    - 则第一列和第二列为可变宽列，
    - 他们将会按照3:4的比例自动调整宽度，使得表格的总宽度为页面宽度，
    - 且每一列的格式为水平和垂直居中对齐。

- 如果要指定表格的**整体宽度**，可以在环境选项中指定：`\begin{tblr}{colspec={<列格式>}, width={<表格宽度>}}`

- 例如：

  ```latex
  \begin{tblr}{
      colspec = {|X[c, m, 2]|X[c, m, -1]|c|c|X[c, 3]|},
      width = 0.6\linewidth}
      % 第一列、第二列和最后一列是可变宽列
      % 第一列和最后一列按照2：3的比例调整宽度
      % 第二列的宽度是满足文字内容的最小宽度
      % 表格的整体宽度被设置为0.6倍的页面宽度
      \hline  % 水平分割线
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      \hline
      {冯宝宝  \\ 冯宝宝  \\ 冯宝宝}   &    85   &      90     &    85      & 合格      \\
      \hline
      {张楚岚  \\ 张楚岚}   &    90   &      55     &    60      & 不合格    \\
      \hline
      张灵玉    &    90   &      82     &    75      & 合格    \\
      \hline
      诸葛青    &    75   &      90     &    58      & 不合格    \\
      \hline
  \end{tblr}
  ```

<img src=".\图片\新式表格宽度调整.png" alt="新式表格宽度调整" style="zoom:67%;" />



## 5. 行列格式分离

​		在上方的旧表格和新式表格中，表格的各单元格的样式都是通过列格式来指定的，这有些不符合直觉，因为列格式不仅负责指定水平方向上的对齐方式，还负责垂直方向的对齐方式，应当是有一个**行格式**来专门负责垂直方向的对齐方式和行距等的管理。

**所以新式表格支持以行格式、列格式来独立管理表格。**

- 格式：`\begin{tblr}{colspec={<列格式>}, rowspec={<行格式>}`
- 行格式：
  - 行格式和列格式的写法基本相同，只是在行列格式分离的思想下，垂直方向的对齐方式应该交给行格式管理。
  - 行间的边框线可以通过在 行格式 上加`|`来添加，就像列格式通过`|`来添加列间的边框线。
    - 通过这种方式加行边框线，就可以不用在数据间插入`\hline`命令来添加行边框线。
    - 此时数据区域就只包含数据，而表格的各种样式设置都由环境选项`colspec`和`rowspec`等指定。

- 例如：

  ```latex
  \begin{tblr}{
      colspec = {|c|c|c|c|c|},
      rowspec = {|Q[m]|Q[m]Q[m]Q[m]Q[m]|}}
      % 第一列、第二列和最后一列是可变宽列
      % 第一列和最后一列按照2：3的比例调整宽度
      % 第二列的宽度是满足文字内容的最小宽度
      % 表格的整体宽度被设置为0.6倍的页面宽度
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      {冯宝宝  \\ 冯宝宝}   &    85   &      90     &    85      & 合格      \\
      {张楚岚  \\ 张楚岚}   &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

  <img src=".\图片\新式表格_行列格式分离.png" alt="新式表格_行列格式分离" style="zoom: 80%;" />



## 6. 边框线

### 6.1 边框样式、颜色

在行间和列间添加边框线可以通过在行格式和列格式间加入`|`，但是tblr环境还提供了别的方式：`hlines`和`vlines`，这两个选项不仅可以添加行列边框线，还可以设置他们的样式：粗细、线形和颜色。

- 格式：`\begin{tblr}{hlines={<样式>}, vlines={<样式>}}`

  - 如果不指定样式，则只会加上普通的边框线。
    - 每个行间和列间都会加上边框线。
  - 例如：`\begin{tblr}{hlines, vlines}`
    - 则表格只加上普通的边框线。
    - 如果只写`\begin{tblr}{}`，则默认没有边框线，只有一堆罗列好的数据。

- 样式：

  - 粗细：可以直接数字加单位指定线条粗细。

    - 例如：`hlines = {2pt}`

  - 线型：

    - `solid`：实线，默认就是这种线型。
    - `dashed`：虚线。
    - `dotted`：点线。
    - 例如：`vlines = {dashed}`，则列间边框线变为虚线

  - 颜色：

    - 要使用颜色要导入宏包`xcolor`
    - 一般直接用颜色的英文名称指定颜色就行。

    - 例如：`hlines = {red}`
    - 在颜色名后追加数字可以改变其系列，数字可以从1-9，数字越大颜色浅。
    - 例如：`vlines = {red9}`，则对应的颜色基本上变成灰色了。
    - 也可以颜色名称后加`!`和数字，来指定颜色的不透明度。
    - 例如：`vlines = {blue!50}`，则表示50%透明的蓝色。

- 几类样式可以任意搭配，例如：

  - `hlines = {2pt, blue!40, dashed}`：框线为2pt粗的蓝色虚线。
  - `hlines={dotted, 1pt}`：框线为1pt粗的点线。

- 例如：

  ```latex
  \begin{tblr}{
  colspec = {ccccc},
  hlines = {1.5pt, red!20},  % 1.5pt粗，红色实绩
  vlines = {1.5pt, dashed, cyan5}}  % 1.5pt粗，青色虚线
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85   &      90     &    85      & 合格      \\
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

<img src=".\图片\新式表格_框线样式、颜色.png" alt="新式表格_框线样式、颜色" style="zoom: 80%;" />

### 6.2 按范围绘制边框线

可以按照需要指定行列边框线的绘制范围。

- 格式：`hlines = {<绘制范围>}{<线条样式>}`（`\vlines`格式一样）

- 绘制范围：

  - 单个列：写上对应列的序号。
    - 例如：`hlines = {2}{}`，第二列画上行边框线。
  - 多个列：写上多个列对应的序号，用逗号隔开。
    - 例如：`hlines = {1, 3, 4}{}`，第1列、第3列和第4列都画上行边框线。
  - 连续列：用`-`来连接首列和尾列的序号。
    - 例如：`hlines = {2-4}{}`，第2列到第4都画上行边框线。
  - 所有列：`hlines = {-}{}`。
    - `hlines{-}{}`跟`\hlines{}`的效果是一样的。
    - 这所以要用到`-`，是因为后面还会讲到`\lines{}{}{}`三个花括号的形式，这时需要用到`-`来占位。
  - 奇数列：`hlines = {odd}{}`
  - 偶数列：`hlines = {even}{}`

- 注意：

  - hlines 和 vlines 可以指定多次，即可以写很多个`hlines = {}{}`。
  - 例如：`\begin{tblr}{hlines = {1, 4}{red}, hlines = {2, 3}{blue}}`。
    - 指定第1列和第4列的行边框线为红色，第2列和第3列的行边框线为蓝色。

- 例如：

  ```latex
  \begin{tblr}{
      colspec = {ccccc},
      hlines = {1, 5}{1.5pt},  % 第1列和第5列的行边框线为1.5pt粗
      hlines = {2 - 4}{1.5pt, blue},  % 第2-4列的行边框线为1.5pt粗、蓝色
      vlines = {odd}{red!50, 2pt},  % 奇数行的列边框线为红色、2pt粗
      vlines = {even}{orange!50, 2pt}}  % 偶数列的列边框线为橙色、2pt粗
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85   &      90     &    85      & 合格      \\
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

<img src=".\图片\新式表格_按行按列绘制边框线.png" alt="新式表格_按行按列绘制边框线" style="zoom: 80%;" />

### 6.3 多边框线

如果需要多条边框线共同分隔两行或两列单元格，则可以用多边框线。

- 格式：`hlines = {<序号>}{<绘制范围>}{<线条样式>}`

- 序号：当序号为2时，则有2条线来分隔，为3时，则有3条线来分隔。

- 例如：

  ```latex
  \begin{tblr}{
      colspec = {ccccc},
      hlines = {1}{-}{},
      hlines = {2}{1, 5}{},  % 第1, 5列有第二要行边框线
      vlines = {odd}{red!50, 2pt},  % 奇数行的列边框线为红色、2pt粗
      vlines = {even}{orange!50, 2pt}}  % 偶数列的列边框线为橙色、2pt粗
  姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
  冯宝宝    &    85   &      90     &    85      & 合格      \\
  张楚岚    &    90   &      55     &    60      & 不合格    \\
  张灵玉    &    90   &      82     &    75      & 合格    \\
  诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

<img src=".\图片\新式表格_多边框线.png" alt="新式表格_多边框线" style="zoom:80%;" />

### 6.4 自由绘制边框线

`hlines`选项和`vlines`选项都是画所有行的边框线和所有列的边框线，如果想**只画指定行或指定列**的边框线，可以用`hline`或`vline`选项，此时绘制边框线的自由度非常高，几乎可以画出所有形式的边框线。

- 格式：`hline{<行范围>} = {<序号>}{<绘制范围>}{<线条样式>}`（`vline`格式一样）

- 行范围：和绘制范围格式类似，通过行序号，选择画指定行的行边框线，而不像`hlines`一样绘制所有行的行边框线。

- 例如：

  ```latex
  \begin{tblr}{
      colspec = {ccccc},
      % 用红线圈出一组数据
      hline{2, 4} = {3, 4}{red},
      vline{3, 5} = {2, 3}{red}}
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85   &      90     &    85      & 合格      \\
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

  ![新式表格_自由绘制边框线](.\图片\新式表格_自由绘制边框线.png)



## 7. 格式设置

### 7.1 设置行列格式

还是运用行列格式分离类似的思想，行列格式可以不用在`rowspec`和`colspec`里设置，而是在`rows`和`columns`里设置，而且这两个选项还提供了更丰富的功能。

- 格式：`rows = {<单元格格式>}`（`columns`格式一样）
- 单元格格式：
  - 对齐方式：
    - 如果是`rows`选项，那对齐方式就有`t`、`m`、`f`。
    - 如果是`columns`选项，那对齐方式就有`l`、`c`、`r`。
  - `bg`：代表"backgrouond"，单元格背景色
  - `fg`：代表“front”，单元格前景色，即字体颜色。
  - `font`：设置单元格字体。
  - 例如：`rows = {m, bg = blue, fg = white, font = \kaisu\large}`

---

`rows`和`columns`都是设置所有行和所有列的格式，不太常用，更常用的是用`row`和`column`设置指定和指定的格式。

- 格式：`row{<行号>} = {<单元格格式>}`
- 单元格格式：和`rows`的选项一样。
- 行号：和前面选择边框线绘制范围一样，通过`,`或`-`来指定多行。

- 例如：

  ```latex
  \begin{tblr}{
  	% 设置第一行为青色背景，白色文字，楷书大号字体
      row{1} = {m, bg = cyan, fg = white, font = \kaishu\Large}, 
      hlines, vlines}
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85   &      90     &    85      & 合格      \\
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

![新式表格_设置行列格式](.\图片\新式表格_设置行列格式.png)

### 7.2 设置单元格式

设置单元格的格式可以用`cells`和`cell`选项。

- 格式：`cells = {<单元格样式>}`
  - 这会让所有单元格设置为统一的样式，不太常用。

---

- 格式：`cell{<行号m>}{<列号n>} = {<单元格样式>}`

  - 可以设置第m行，第n列的单元格的格式。

- 例如：

  ```latex
  \begin{tblr}{
      row{1} = {fg = white, font = \kaishu\large},  % 第一行字体设置的白色，字体为楷书大号
      cell{1}{odd} = {bg = cyan},  % 第一行奇数单元格背景色为白色
      cell{1}{even} = {bg = blue7},  % 第一行偶数单元格背景色为蓝色
      hlines, vlines}
      姓名      &   面试  &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85   &      90     &    85      & 合格      \\
      张楚岚    &    90   &      55     &    60      & 不合格    \\
      张灵玉    &    90   &      82     &    75      & 合格    \\
      诸葛青    &    75   &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

<img src=".\图片\新式表格_设置单元格格式.png" alt="新式表格_设置单元格格式" style="zoom:80%;" />

### 7.3 合并单元格

在旧表格中，`\multirow`和`\multicolumn`来合并单元格，合并单元格的命令要写在数据中，会破坏数据在源码中的排布，让数据区看起来很混乱。

在新式表格中，可以使用`cell`选项合并单元格。

- 格式：`cell{<行号>}{<列号>} = {<合并数>}{<合并后的单元格格式>}`

- 合并数：

  - `r = <向下合并数>`
  - `c = <向右合并数>`
  - 例如：`cell{1}{1} = {r=2}{}`，则表格左上角的单元向下合并一个单元格。

- 例如：

  ```latex
  \begin{tblr}{
      columns = {c},
      hlines, vlines,
      cell{1}{1} = {r=2}{},  % 向下合并
      cell{1}{5} = {r=2}{},  % 向下合并
      cell{1}{2} = {c=3}{},  % 向右合并
      cell{3}{2} = {r=4, c=3}{}  % 向右合并2列，向下合并3行
      }  
      姓名      &   考试内容 &   理论知识   &   上机考试  &  考评结果  \\
      姓名      &   面试    &   理论知识   &   上机考试  &  考评结果  \\
      冯宝宝    &    85     &      90     &    85      & 合格      \\
      张楚岚    &    90     &      55     &    60      & 不合格    \\
      张灵玉    &    90     &      82     &    75      & 合格    \\
      诸葛青    &    75     &      90     &    58      & 不合格    \\
  \end{tblr}
  ```

<img src=".\图片\新式表格_合并单元格.png" alt="新式表格_合并单元格" style="zoom:80%;" />



## 8. 特殊表格

### 8.1 三线表

但是在旧表格中，需要导入宏包`booktabs`，接着使用`\toprule`、`\midrule`、`\bottomrule`来绘制三线表。

在新表格中，依然用`\toprule`、`\midrule`、`\bottomrule`绘制三线表，但是导入宏包的方式有些不同。

- 导入bootabs：`\UseTblrLibrary{booktabs}`

- 示例：

  ```latex
  \begin{tblr}{
      columns = {c},
      rows = {m}
      }  
      \toprule
      姓名      &   面试    &   理论知识   &   上机考试  &  考评结果  \\
      \midrule
      冯宝宝    &    85     &      90     &    85      & 合格      \\
      张楚岚    &    90     &      55     &    60      & 不合格    \\
      张灵玉    &    90     &      82     &    75      & 合格    \\
      诸葛青    &    75     &      90     &    58      & 不合格    \\
      \bottomrule
  \end{tblr}
  ```

<img src=".\图片\新式表格_三线表.png" alt="新式表格_三线表" style="zoom:80%;" />

### 8.2 长表格

### 8.3 横向表格





# 四、数学公式

## 1. 数学模式



# 五、文章结构

## 1. 章节标题与目录

### 1.1 章节划分

在 $\LaTeX$ 中，已经预定义好了各类章节标题的样式（就像word中预定义的样式一样），可以使用相应的命令，来赋予标题文字相应的样式。

对于一般的文章，相应的章节划分有（层级从大到小，即`\part`层级最高，`\subparagraph`层级最低）：

- `\part`：部分
- `\chapter`：章
  - 章这一层级只有在`book`和`report`类中有。
  - 在`article`类中没有章，即在article中使用不了`\chapter`命令。
    - 因为一个`article`就相当于一章。
- `\section`：节
- `\subsection`：小节
- `\subsubsection`：小小节
- `\paragraph`：段落
- `\subparagraph`：小段落

**注意：**

- 对于**不同的文档类**，可以使用的**章节层级种类和效果**是不同的：
  - `article`：每个层级都**不会新起一页**。
  - `book`：`\maketile`、`\part`、`\chapter`都会**新起一页**。

### 1.2 自动编号

不同层级的章节会被自动编上号。

对于**不同的文档类**，会**自动编号的层级范围**也不一样：

- 对于`article`，带编号的层级为：
  - `\section`、`\subsection`、`\subsubsection`。
  - 在`\subsubsection`以下的层级都不带编号。
- 对于`report`和`book`，带编号的层级为：
  - `\chapter`、`\section`、`\subsection`。
  - 在`\subsection`以下的层级都不带编号，包括`\subsubsection`。
- 不论什么文档类，`\part`都会自动编号。

**注意：**

- 在章被自动编号后，在章中插入的图片和表格也会加上章的编号。
  - 例如：
    - 第一章的第2张图片，会被编号为：图1.2。
    - 第三章的第1张表格，会被编号为：表3.1。

### 1.3 文章结构

在**`book`**类中，还可以再根据内容再划分为以下几个部分：

- `\frontmatter`：前言部分
  - 页码用小写的罗马数字；
  - 其后的`\chapter`不用自动编号

- `\mainmatter`：正文部分
  - 页码用阿拉伯数字，从1开始计数；
  - 其后的章节会自动编号。

- `\backmatter`：后记部分
  - 页码格式不变，继续开始计数，即接着正文部分的页码继续计数；
  - 其后的`\chapter`不自动编号。


**注意：**

- `\frontmatter`、`\mainmatter`、`\backmatter`在book类中是最在的层级。
- 在这些部分下，再划分`\part` 、`\chapter`等层级。

---

对于**所有的文档类**，$\LaTeX$ 还提供了命令：

- `\appendix`：用于表示附录，层级与`\chapter`平等。
  - `\appendix`会用大写英文字母编号（所以附录最多不超过26个），例如：`附录A latex保留符号`、`附录B 常用宏包`。
  - 其后的`\section`的自动编号，形如：`A.1`、`A.2`。

### 1.3 目录

按照内容不同，可以用不同的命令生成相应的列表：

- `\tableofcontents`：按照章节内容列出目录列表
- `\listoftables`：图片列表
- `\listoffigures`：表格列表

---

默认情况下目录中是不列出目录本身的，也不列出图片列表和表格列表，如果要在目录中添加这些，可以使用命令`\includecontents`

- 格式：`\includecontents{<层级>}{<标题名>}`
- 层级：即在该列表在目录列表中显示的章节层级。
- 这个命令要跟在对应列表命令的后面。
  - 例如：
  - `\tableofcontents \includecontents{\chapter}{目录}`，则目录这一页也被列进目录列表中去。
  - `\listoffigures \includecontents{\chapter}{图片列表}`，则图片列表这一页也被列进目录列表中去。

### 1.4 文档拆分

文档拆分的原因和作用：

- 当文章内容很多，章节层级很多的时候，如果把所有的东西都写在一个文件里，会使得这个文件的内容有点杂乱。
- 这时可以根据需要把不同的内容拆分为多个tex文件，然后在主文件中用`\include`命令或`\input`命令来来导入其他tex文件，就可以更高效地管理文章。
- 通常来说，一个章对应一个tex文件，即每个章由一个tex文件管理。

例如：

```latex
\documentclass{ctexbook}  % 中文书籍类
\input{setup}  % 导入各种宏包

\title{\LaTeX 文章结构练习}
\author{郑 威}
\date{\today}

\begin{document}
% 书籍封面
\maketitle  

% 前言部分
\frontmatter
\include{thanks_zh}  % 中文致谢
\include{thanks_eng}  % 英文致谢
\include{menu}  % 生成目录


% 正文部分
\mainmatter  

% 第一部分
\part{\LaTeX 基础}
\include{chapter_01}  % 第一章
\include{chapter_02}  % 第二间


% 附录
\appendix
\include{appendix_A}  % 附录A
\include{appendix_B}  % 附录B


% 后记部分
\backmatter
\include{reference}  % 参考文献
\include{index}  % 索引

\end{document}
```



# 六、latex作图

## 0. tikz绘图环境

- 使用tikz宏包来进行绘图

  - 导入宏包：`usepackage{tikz}`

  - 进入tikz绘图环境：`\begin{tizkpicture} ... \end{tikzpicture}`

- **注意：**
  - 在`tikzpicture`环境中，每条语句结尾都要**加封号**`;`，就像c一样，否则会报错。

## 1. 直线

可以通过给定两个点的坐标，使用`\draw`命令画两点的连线。

- 格式：`\draw (<起点坐标>) -- (<终点坐标>)` 或 `\draw (<起点坐标>) --+ (<增量坐标>)`
  - 可以是多段直线，例如：`\draw (0, 0) -- (0, 1) -- (1, 2);`
  - 多段直线使用cycle转成封闭区域，例如：`\draw (0, 0) -- (0, 1) -- (1, 2) -- cycle;`

- 坐标表示：

  - 直角坐标系：`(x, y)`
    - 长度默认单位：cm。
    - 可以在数值后显式地加单位，例如：(0, 2pt)。
  - 极坐标系：`(a:r)`
    - `a`为角度值，单位为角度，例如：（60, 2）表示角度为 $60^\circ$，长度为2cm的一个坐标。

- 示例：

  ```latex
  \documentclass{article}
  \usepackage{ctex}
  \usepackage{tikz}
  
  \begin{document}
  
  \section{直线绘制与坐标表示}
  
  % 进入绘图环境
  \begin{tikzpicture}  
  \draw (-1, 0) -- (3, 0);  % 画一条从坐标（-1， 0）到坐标（3，0）的直线，单位长度是一厘米
  \draw (0, -1) -- (0, 3);  % 画一条从坐标（0， -1）到坐标（0，3）的直线，单位长度是一厘米
  \draw (0, 0) -- (30:2);   % 画一条从直角坐标（0，0）到极坐标（30：2）的直线，角度单位是角度值（不是弧度值）
  
  \draw (30:2) --+ (120:2);  % 直线的起点坐标为（30：2），终点坐标为以（30：2）为起点的（120：2）
  \draw (1, 2) --+ (-2, 1);  % 直线的起点坐标为（1，2），终点坐标为以（1，2）为起点的（-2，1）
  
  \draw (0, 0) -- (0, 1) -- (1, 2) -- cycle;  % 多段直线围成一个三角形区域
  
  \end{tikzpicture}
  
  \end{document}
  ```

  - `\draw`命令中不同点坐标系可以混用。



## 2. 定义坐标

可以使用`\coordinate`为一个坐标点定义名称

- 格式：`\coordinate (<名称>) at (<点坐标>)`

- 例如：

  ```latex
  \coordinate (A) at (2, 1);  % 定义点A为（2，1）
  \coordinate (B) at (60:2);  % 定义点B为（60：2）
  \draw (A) -- (B);  % 画点A到点B的连线
  ```

- 注意：

  - 定义时点名称要用**括号括起来**，不能是：`\coordinate A at (2, 1)`。
  - 引用点时也用要括号括起来，不能是：`\draw A -- B`。



## 3. 基本图形绘制

### 3.1 箭头

可以在`\draw`命令中指定箭头类型来在直线末端添加箭头。

- 箭头类型：`>`、`|`、`latex`、`stealth`。

- 格式：`\draw[<箭头类型>-<箭头类型>] (<起点坐标>) -- (<终点坐标>)`

- 例如：

  ```latex
  \begin{tikzpicture}
  \draw[->] (1, 1) -- (5, 1);  % 在直线终点上添加一个向右的箭头
  \draw[<-] (1, 1.2) -- (5, 1.2);  % 在直线起点上添加一个向左的箭头
  \draw[<->] (1, 1.4) -- (5, 1.4);  % 在直线起点和终点上依次添加向左和向右的箭头
  \draw[>->] (1, 1.6) -- (5, 1.6);  % 在直线起点和终点上都添加向右的箭头
  \draw[|->|] (1, 1.8) -- (5, 1.8); % 在直线起点上添加一竖线，终点上添加一向右箭头，再串上一个竖线
  \draw[|->>] (1, 2) -- (5, 2);  % 在直线起点上添加一竖线，终点上添加一个向右箭头，再串上一个向右箭头
  
  \draw[-stealth] (1, 3) -- (5, 3);  % 在直线终点上添加一个stealth风格的箭头
  \draw[-latex] (1, 3.2) -- (5, 3.2);  % 在直线终点上添加一个latex风格的箭头
  \draw[latex-latex] (1, 3.4) -- (5, 3.4);  % 在直线起点和终点上分别添加上latex风格的箭头，箭头方向朝外
  
  % 画一直角坐标系
  \draw[-latex] (-1, 0) -- (6, 0);
  \draw[-latex] (0, -1) -- (0, 6);
  \end{tikzpicture}
  ```

- 指定环境箭头：

  ```latex
  \begin{tikzpicture}[>=latex]  % 指定在该环境中，默认的>箭头都是latex箭头
  ... 
  \end{tikzpicture}
  ```

### 3.2 直角

通过指定两点，可以用一条水平和一条竖直线连接两点，从而画出直角线。

- 格式：`draw -| (<起点坐标>) -- (<终点坐标>)` 或 `draw |- (<起点坐标>) -- (<终点坐标>)`

- 例如：

  ```latex
  \begin{tikzpicture}
  \draw (0, 2) |- (3, 0);  % 从点（0， 2）出发，先竖直线，后水平线，到点（3，0）
  \draw (0, 2) -| (3, 0);  % 从点（0， 2）出发，先水平线，后竖直线，到点（3，0）
  \end{tikzpicture}
  ```

### 3.3 矩形

通过指定矩形的两个对角点，画出一个矩形

- 格式：`\draw (<起点>) rectangle (<对角点>)` 或 `\draw (<起点>) rectangle+ (<宽，高>)` 

- 例如：

  ```latex
  \begin{tikzpicture}
  \draw (0, 0) rectangle (2, 3);  % 以（0，0）为矩形的左下角点，（2，3）为矩形的右上角点，画一矩形
  \draw (3, 0) rectangle+ (2, 3);  % 以（3，0）为矩形的左下角点，（3+2，0+3）为矩形的右上角点，画一矩形
  \end{tikzpicture}
  ```

### 3.4 网格

通过指定网格的两个对角点（网格与矩形类似，只是在矩形的基础上在其内部填充了网格线），画出一个网格

- 格式：`\draw[step=1] (<起点>) grid (<网格区域对角坐标>)`  或  `\draw (<起点>) grid+ (<网格区域宽，高>)` 

  - step 指定步长，即网格线的间距。默认的距离单位都是厘米，可以显示指定单位。

- 例如：

  ```latex
  \subsection{网格}
  \begin{tikzpicture}
  \draw (0, 0) grid (2, 3);
  \draw (3, 0) grid+ (2, 3);
  \draw[step=0.5] (6, 0) grid+ (2, 3);  % 指定步长为0.5厘米
  \draw[step=8pt] (9, 0) grid+ (2, 3);  % 指定步长为8磅
  \end{tikzpicture}
  ```

### 3.5 圆和椭圆

通过指定圆心和半径，画一个圆

- 格式：`\draw (<圆心>) circle (<半径>)` 或者 `\draw (<圆心>) circle [radius=<半径>]`

---

通过指定椭圆心、x轴半径和y轴半径，画一个圆。

- 格式：`\draw (<椭圆心>) ellipse [x radius = r1, y radius = r2]`

- 例如：

  ```latex
  \subsection{圆}
  \begin{tikzpicture}
  \draw (0, 0) circle (2);  % 画一个圆心为（0，0），半径为2的圆
  \draw (0, 0) circle [radius = 1];  % 画一个圆心为（0，0），半径为1的圆
  \draw (0, 0) ellipse [x radius = 2, y radius = 1];  % 画一个椭圆心为（0，0），x轴半径为2，y轴半径为1的椭圆。
  \end{tikzpicture} 
  ```

### 3.6 圆弧和椭圆弧

通过指定起点、起始角度、终止角度和半径来画一个圆弧。

- 格式：`\draw (<起点>) arc (<起始角度>：<终止角度>：<半径>)`

  - 或者：`\draw (<起点>) arc [start angle = <起始角度>, end angle = <终止角度>, radius = <半径>]`

- 例如：

  ```latex
  
  ```

---

通过指定起点、起始角度、终止角度、x轴径和y轴径，画一个椭圆弧。

- 格式：`\draw (<起点>) arc (<起始角度>：<终止角度>：<x轴径> and <y轴径>)`

  - 或者：`\draw (<起点>) arc [start angle = <起始角度>, end angle = <终止角度>, x radius = <x轴径>] y radis = <y轴径>`

- 例如：

  ```latex
  \begin{tikzpicture}
  % 画坐标系
  \draw[-latex] (-1, 0) -- (3, 0);
  \draw[-latex] (0, -1) -- (0, 3);
  
  % 画圆弧 
  \draw (0, 0) arc (30:150:2);
  \draw (0, 0) arc [start angle = 15, end angle = 90, radius = 1];
  
  % 画椭圆弧
  \draw (0, 1) arc (30:150:2 and 1);
  \draw (0, 1) arc [start angle = 15, end angle = 90, x radius = 1, y radius = 2];
  ```

## 4. 线条样式

### 4.1 粗细

- 粗细类别：

  - `ultra thin`：0.1 pt
  - `very thin`：0.2 pt
  - `thin`：0.4 pt
  - `semithick`：0.6 pt
  - `thick`：0.8 pt
  - `very thick：1.2 pt
  - `ultra thick`：1.6 pt
  - `line width = ` ：自定义线宽

- 环境设置线条粗细：

  - 格式：

    ```latex
    \begin{tikzpicture}[ultra thin]  % 设置该环境中默认线宽为ultra thin，即0.1pt
    ...
    \end{tikzpicture}
    ```

- 设置单条线条粗细：

  - 格式：`\draw[<线宽>] (<起点>) -- (<终点>)`

    - 或者：`\draw[line width = <线宽>] (<起点>) -- (<终点>)`

  - 例如：

    ```latex
    \begin{tikzpicture}[very thin]   % 设置该环境中默认线宽为very thin
    
    \draw[line width=0.8pt] (0, 0.6) -- (4, 0.6);  % 设置该线的线宽为0.8pt
    \draw[semithick, -latex] (0, 0.4) -- (4, 0.4);  % 设置该线的线宽为semithick，同时线段终点有一latex样式的箭头
    \draw[thin] (0, 0.2) -- (4, 0.2);  % 设置该线的线宽为thin
    \draw (0, 0) -- (4, 0);  % 该线的线宽为上面指定的环境线宽，即very thin
    
    \end{tikzpicture}
    ```

### 4.2 线型

- 线型类别：

  - `help lines`：辅助线
  - `solid`：实线
  - `dashed`：虚线
  - `dotted`：点线
  - `dash dot`：点划线
  - `dash dot dot`：双点划线

- 线型稀疏：

  - densely：线型密集
  - loosely：线型稀疏

- 格式：`[<线型稀疏程度> 线型类别]`

- 例如：

  ```latex
  \begin{tikzpicture}[dashed]  % 设置环境默认线型为虚线
  
  \draw[loosely dash dot dot] (0, 1.8) -- (4, 1.8);  % 稀疏点划线 
  \draw[dash dot] (0, 1.4) -- (4, 1.4);  %普通点划线
  \draw[densely dash dot] (0, 1.6) -- (4, 1.6);  % 密集点划线
  
  \draw[dash dot dot] (0, 0.8) -- (4, 0.8);  % 双点划线
  \draw[dash dot] (0, 0.6) -- (4, 0.6);  % 点划线
  \draw[dotted] (0, 0.4) -- (4, 0.4);  % 点线
  \draw[solid] (0, 0.2) -- (4, 0.2);  % 实线
  \draw (0, 0) -- (4, 0);  % 为环境默认线型，即虚线
  
  \end{tikzpicture}
  ```

### 4.3 颜色

- 格式：在方括号里填写相应颜色名称。

- 自定义颜色：`\definecolor{<新定义的颜色名>}{<颜色模式>}{<颜色参数>}`

- 设置颜色饱和度：[<颜色名称>!<饱和度百分比>]

  - 例如：

    ```latex
    \definecolor{my_orange}{rgb}{1 0.5 0}  % 定义橘色
    \begin{tikzpicture}[red]
    \draw[red!20] (0, 1.6) -- (4, 1.6);  % 20%的红色
    \draw[my_orange] (0, 1.4) -- (4, 1.4);  % 自定义的橘色
    \draw[violet] (0, 1.2) -- (4, 1.2);	% 紫
    \draw[blue] (0, 1) -- (4, 1);		% 蓝
    \draw[cyan] (0, 0.8) -- (4, 0.8);	% 青
    \draw[green] (0, 0.6) -- (4, 0.6);	% 绿
    \draw[yellow] (0, 0.4) -- (4, 0.4); % 黄
    \draw[orange] (0, 0.2) -- (4, 0.2); % 橙
    \draw (0, 0) -- (4, 0);  % 红
    \end{tikzpicture}
    ```

  - 注意：自定义的颜色要在`tikzpicture`**环境外定义**。

### 4.4 样式复合

可以为直线指定多种线条样式，多个样式之间用逗号`,`隔开。

例如：

```latex
\begin{tikzpicture}[thin]
\draw[violet] (0, 1.2) -- (4, 1.2);  % 紫色细线
\draw[blue, dashed, line width=0.5pt, <-latex] (0, 1) -- (4, 1);  % 蓝色0.5磅虚线，两种箭头样式
\draw[cyan, semithick, dash dot, stealth-] (0, 0.8) -- (4, 0.8);  % 青色半粗点划线带左箭头
\draw[green, thick, -latex] (0, 0.6) -- (4, 0.6);  % 绿色粗线带右箭头
\draw[yellow, ->] (0, 0.4) -- (4, 0.4);  % 黄色线带右箭头
\draw[orange, dashed] (0, 0.2) -- (4, 0.2);  % 橙色虚线
\draw (0, 0) -- (4, 0);  % 细线
\end{tikzpicture}
```

### 4.5 圆角

对于一个多段线或封闭图形，可以用`[rounded corners]`来使直线连接处产生圆角

例如：

```latex
\begin{tikzpicture}[rounded corners]  % 设置环境中的线转折点都采用圆角过渡
\draw (0, 0) rectangle (2, 3);  % 圆角矩形
\draw (3, 0) -- (4, 0)[rounded corners=10pt] -- (4, 2)[sharp corners] --cycle;  % 默认为圆角过渡，sharp corners的作用是该点处取消圆角过渡，还可以用rounded corners指定圆角的大小
\end{tikzpicture}
```



### 4.6 scope环境和样式复用

- scope环境：如果有多条直线共用同一样式，可以把他们都放入scope环境中。

- 自定义样式：`[<样式名>/.style = {<latex内置的样式>}]`

- 例如：

  ```latex
  [my_style/.style = {red, ->|, ultra thick}]  % 自定义样式
  \begin{tikzpicture}
  \begin{scope}[thick, cyan, densely dashed, rounded corners]  % 在这个环境下，线条样式统一为 青色密集粗虚线带圆角
  \draw (0, 0) rectangle (2, 2);
  \draw (1, 3) circle (1);
  \end{scope}
  
  \draw[my_style] (3, 0) -- (5, 1) -- (8, 0.5) -- (10, 1);  % 有自定义的样式画线
  \end{tikzpicture}
  ```



## 5. 形变

形变类别：

- 位移：`xshift`、`yshift`、`xslant`、`yslant`。
- 缩放：`scale`。
- 旋转：`rotate`。

示例：

```latex
\begin{tikzpicture}[scale = 2, rounded corners]  % 环境中的所有图像放大一倍，并倒圆角
\draw[dashed, thick] (0, 0) rectangle (1, 1.2);  % 画一虚线矩形，表示没有形变前矩形的样子
\draw[red, xshift=2cm] (0, 0) rectangle (1, 1.2);  % 矩形沿x轴（向右）移动2cm
\draw[red, yshift=2cm] (0, 0) rectangle (1, 1.2);  % 矩形沿y轴（向上）移动2cm
\draw[orange, xslant=0.5] (0, 0) rectangle (1, 1.2);  % 矩形上端沿x轴 平移0.5cm
\draw[orange, yslant=1] (0, 0) rectangle (1, 1.2);  % 矩形右端沿y轴移动1cm

\draw[green, scale=0.5] (0, 0) rectangle (1, 1.2);  % 矩形缩小到原来的0.5倍
\draw[green, scale=1.5] (0, 0) rectangle (1, 1.2);  % 矩形放大到原来的1.5倍

\draw[cyan, rotate=60] (0, 0) rectangle (1, 1.2);  % 矩形绕原点旋转60度
\draw[cyan, rotate=-65] (0, 0) rectangle (1, 1.2);  % 矩形绕原点旋转-65度

\draw[blue, rotate=30, xshift=2cm, scale=0.5] (0, 0) rectangle (1, 1.2);  %
\draw[violet, xshift=2cm, rotate=30, scale=0.5] (0, 0) rectangle (1, 1.2);

\end{tikzpicture}
```

- 注意：
  - 所有形变操作，都是围绕原点进行的，即原点永远不变。
    - 如果使用`rotate`，则是图形绕原点旋转，而不是绕自身上某个点旋转。
    - 如果使用`xslant`或`yslant`，则是图形以原点为定点开始剪切，而不是以自身上某个点开始剪切。
  - 多个形变操作可以复合在一起，一起写在方括号`[]`内，用逗号`,`隔开。
    - 操作复合时要**注意顺序**，先平移再旋转 和 先旋转再平移 是不一样的。
  - 和大多数操作一样，形变操作可以作用在整个环境上。



## 6. 绘制与填充

绘制一个封闭图形并填充颜色有多种方法，包括：

- `\draw[fill=<颜色>]`
- `\fill[draw=<颜色>]`
- `\filldraw[fill=<颜色>, draw=<颜色>]`

例如：

```latex
\begin{tikzpicture}
\draw[fill=blue!20] (0, 0) rectangle (2, 2);  % 画一个矩形，内部填充颜色是20%饱和度的蓝色
\draw[fill=blue] (1, 1) -- (1.5, 1) -- (1.5, 2);  % 如果不是一个封闭图形，在使用fill选项后，会自动补全为封闭图形
\draw[green, fill=blue!20] (0, 3) rectangle+ (2, 2);  % 画一个矩形，线条颜色是绿色，内部填充颜色是20%饱和度的蓝色
\fill[blue!20, draw=green] (3, 0) rectangle+ (2, 2);  % 画一个蓝色的矩形（默认没有边框），通过draw=green 指定边框为绿色
\filldraw[blue!20, draw=green] (6, 0) rectangle+ (2, 2);  % 用法和\draw和\fill差不多，没仔细研究过
\end{tikzpicture}
```

- 注意：
  - 使用写到类似 `\draw[green, fill=blue!20]`的语句时
    - green表示指定线条的颜色（即边框的颜色），`fill`指定填充的颜色。
    - 不能写成：`\draw[fill=blue!20, green]`，这样编译器会认为`fill=green`，而不是边框颜色为green，即**注意顺序**。
    - 如果一定要把`fill`写前头，可以用`draw`指定边框颜色，即：`\draw[fill=blue!20, draw=green]`
    - 但是类似这种语句 `\draw[fill=blue!20, thick]` 就不影响。
  - 不论是`\draw`、`\fill`还是`\filldraw`，在方括号选项中，都是`fill`来指定填充颜色，`draw`来指定边框颜色。
  - 如果不是一个封闭图形，在使用fill选项后，会自动补全为封闭图形。



## 7. 文字结点

文字结点可以在图像上一个地方插入文本，即**插入一个文本框**。

文字结点有两语法格式：

- `node[<选项>] <文字结点名称> at (<结点坐标>) {文本内容}`
  - 如果不想指定文本结点的名称，可以省略掉这一项，例如：`node at (0, 0) {原点}`
  - 也就是在`(0, 0)`点创建了一个文字结点，内容为“原点”两个字。
- `<对象>node[<选项>] {文本内容}`
  - 这里的对象通常是一个点，例如：`\draw (0, 0)node{原点} -- (1, 1)`。
  - 这里`(0, 0)`就是那个对象，`node`是依附于这个对象的，同时这个文本框里的内容是“原点”两个字。

选项：

- `fill`：背景颜色。

- `draw`：边框颜色。

- `text`：文本颜色。

- `node font`：文本字体。

- 形状：

  - 如`circle`，则文本框为圆形。

- 位置：

  - `above`：文本在结点坐标 正上方。
  - `below`：文本在结点坐标 正下方。
  - `left`：文本在结点坐标 正左方。
  - `right`：文本在结点坐标 正右方。
  - `centered`：文本就落在结点坐标上。
  - `above left`：文本在结点坐标 左上方。
  - `above right`：文本在结点坐标 右上方。
  - `below left`：文本在结点坐标 左下方。
  - `below right`：文本在结点坐标 右下方。
  - 可以为这些选项指定数值，表示偏离的程度。
    - 如：`\draw (0, 0) -- (3, 0)node[above=0.5cm] {哈哈}`
    - 则文本结点将在`(3, 0)`点上方0.5cm处创建。

- 设置环境样式：

  ```latex
  \begin{tikzpicture}[every node/.style = {fill=blue!20, circle}]
  ...
  \end{tikzpicture}
  ```

- 例如：

  ```latex
  \begin{tikzpicture}[every node/.style = {fill=red!20, circle}]  % 设置环境默认的文本结点的样式为红色填充的圆形
  
  \node[fill=blue!20, draw=green!50, text=white, node font={\kaishu}, above right] at (1, 1) {哈哈};
  \draw[fill=red] (1, 1) circle (2pt);
  
  \draw (0, -1) -- (0, 3)node[left] {y};  % 在坐标（0，3）在左侧，创建一个矩形的文字结点，内容是“y”
  \draw (-1, 0) --node[below]{4cm} (3, 0)node[right] {x};  % 在两点连线的中心的下侧，创建了矩形的一个文字结点，内容是“4cm”，同时在坐标（3，0）在右侧，创建一个圆形（指定的默认环境形状）的文字结点，内容是“x”
  
  %创建A、B节点
  \node[rectangle] (A) at (3, 2) {A节点};
  \node[rectangle] (B) at (5, -1) {B节点};
  
  \draw[cyan] (A) -- (B);  % 可以使用文字结点来画线
  \draw[yellow] (A.east) -- (B.west);  % 还可以通过east、west、north和south来指定线段端点在文字结点上位置
  
  \end{tikzpicture}
  ```

  - 注意：
    - 指定`node font`时，不要忘了用`{}`把字体名称括起来，不能是`node font = \kaishu`

## 8. 循环绘制

- 格式：`foreach \\<变量名> in {<列表>}{<绘图命令>}`

- 例如：

  ```latex
  \begin{tikzpicture}
  \foreach \y in {0, 0.2, ..., 1}{
      \draw (0, \y) -- (4, \y);
  }
  \foreach \x in {5.5, 6.6, 7.7, 8.8, 9.9}{
      \draw (\x, 0) rectangle +(1, 1);
  }
  \end{tikzpicture}
  ```



## 9. 画坐标轴

使用上面学过的知识，画一个坐标轴：

```latex
\begin{tikzpicture}[-stealth]
% 画网格
\draw[dashed, help lines, blue!40, very thin] (-1.9, -1.9) grid (3.9, 3.9);

% 画坐标轴
\draw[thick] (0, -2) -- (0, 4)node[left] {$y$};
\draw[thick] (-2, 0) -- (4, 0)node[below] {$x$};
\node[below left] at (0, 0) {$O$};  % 原点

% 画刻度
\foreach \x in {-1, 1, 2, 3}{
    \draw[-] (\x, 0)node[below] {\x} -- (\x, 0.2);  % 画x轴刻度
    \draw[-] (0, \x)node[left] {\x} -- (0.2, \x);  % 画y轴刻度
}

\end{tikzpicture}
```



## 10. 函数图像

- 格式：`\draw[选项] plot (\x, {<函数表达式>})`
  - 选项：
    - `domain`：`[domain=<定义域左边界>:<定义域右边界>]`
    - `samples`：函数图像的取样点数，默认为25点，有时会让曲线不光滑。

- 例如：

  ```latex
  \begin{tikzpicture}[samples=100]  % 令整个绘图环境中的取样点数为100
  % 画网格
  \draw[dashed, help lines, blue!40, very thin] (-4.5, -0.5) grid (4.5, 8.5);
  % 画坐标轴
  \draw[thick, -latex] (0, -1) -- (0, 9)node[left] {$y$};
  \draw[thick, -latex] (-5, 0) -- (5, 0)node[below] {$x$};
  \node[below left] at (0, 0) {$O$};  % 原点
  % 画刻度
  \foreach \x in {-4, -3, ..., -1, 1, 2, ..., 4}{
      \draw[-] (\x, 0)node[below] {\x} -- (\x, 0.2);  % 画x轴刻度
  }
  \foreach \x in {-1, 1, 2, ..., 8}{
      \draw[-] (0, \x)node[left] {\x} -- (0.2, \x);  % 画x轴刻度
  }
  
  % 画函数
  \draw[domain=-1:5] plot (\x, {\x})node[right]{$y=x$};  % y = x
  \draw[domain=-4:4, red] plot (\x, {\x*\x/2})node[left]{$y=\frac{1}{2}x^2$};  % y = 0.5*x^2
  \draw[domain=-4:5, blue] plot (\x, {0.25*2^\x})node[right]{$y=0.25 x^2$};  % y = 0.25*2^x
  \draw[domain=0:5, cyan] plot (\x, {\x^0.5})node[right]{$y=\sqrt{x}$};  % y = x^0.5
  \end{tikzpicture}
  ```

- 再比如：

  ```latex
  \begin{tikzpicture}[samples=100, domain=-1.5*pi:1.5*pi]  % 函数图像取样点数为100，设置整个图像中的定义域从-1.5pi到1.5pi
  % 画网格
  \draw[dashed, help lines, blue!40, very thin] (-4.5, -1.5) grid (4.5, 1.5);
  % 画坐标轴
  \draw[thick, -latex] (0, -1.5) -- (0, 1.5)node[left] {$y$};
  \draw[thick, -latex] (-5, 0) -- (5, 0)node[below] {$x$};
  \node[below left] at (0, 0) {$O$};  % 原点
  % 画刻度
  \foreach \x in {-4, -3, ..., -1, 1, 2, ..., 4}{
      \draw[-] (\x, 0)node[below] {\x} -- (\x, 0.2);  % 画x轴刻度
  }
  \foreach \y in {-1, 1}{
      \draw[-] (0, \y)node[left] {\y} -- (0.2, \y);  % 画y轴刻度
  }
  
  % 画函数
  \draw[domain=-5:5] plot (\x, {sin(\x r)})node[below]{$y=\sin{x}$};  % y = sin(x)
  \draw[domain=-5:5, red] plot (\x, {sin(\x r) + 1})node[above=0.5cm]{$y=\sin{x}+1$};  % y = sin(x) + 1
  \draw[domain=-5:5, blue] plot (\x, {sin(\x r + 1 r)})node[right]{$y=\sin{x+1}$};  % y = sin(x+1)
  \end{tikzpicture}
  
  \begin{tikzpicture}[samples=100, domain=-1.5*pi:1.5*pi]
  % 画网格
  \draw[dashed, help lines, blue!40, very thin] (-4.5, -1.5) grid (4.5, 2.5);
  % 画坐标轴
  \draw[thick, -latex] (0, -1.5) -- (0, 3)node[left] {$y$};
  \draw[thick, -latex] (-5, 0) -- (5, 0)node[below] {$x$};
  \node[below left] at (0, 0) {$O$};  % 原点
  % 画刻度
  \foreach \x in {-4, -3, ..., -1, 1, 2, ..., 4}{
      \draw[-] (\x, 0)node[below] {\x} -- (\x, 0.2);  % 画x轴刻度
  }
  \foreach \y in {-1, 1}{
      \draw[-] (0, \y)node[left] {\y} -- (0.2, \y);  % 画y轴刻度
  }
  
  % 画函数
  \draw plot (\x, {sin(\x r)})node[below]{$y=\sin{x}$};  % y = sin(x)
  \draw[red] plot (\x, {sin(\x r) + 1})node[above=0.5cm]{$y=\sin{x}+1$};  % y = sin(x) + 1
  \draw[blue] plot (\x, {sin(\x r + 1 r)})node[right]{$y=\sin{(x+1)}$};  % y = sin(x+1)
  \end{tikzpicture}
  ```

  - 注意：
    - 在latex中，角度的默认单位都是角度值，而不是弧度值，
    - 因此对于sin函数，默认的自变量单位也是角度值，
    - 如果要指定自变量单位是弧度值，需要在输入的数值后加上r，如：`sin(\x r)`





# 附录

## 1. 保留符号

| 保留符号         | 写法           |
| ---------------- | -------------- |
| $\textbackslash$ | \textbackslack |
| $\{ \}$          | \\{ \\}        |
| $\#$             | \\#            |
| $\%$             | \%             |



## 2. 常用宏包

- `ctex`：中文宏包
- `setspace`：设置行距
- `fontspec`：设置字体
- `geometry`：设置页边距
- `graphicx`：图像
- `subcaption`：图像小标题
- `hyperref`：引用
- `diagbox`：对角线表格
- `multirow`：合并多选单元格
- `booktabs`：绘制三线表
- `tabularx`：可变宽表格
- `tabularry`：新式表格
- `xcolor`：颜色
- `tikz`：画图



## 3. 几个重要的环境

- `document`：编写文档
  - `figure`：浮动图片
  - `itemize`：无序列表
  - `enumerate`：有序列表
- `tabular`：绘制表格
  - `table`：浮动表格
  - `tabularx`：可变表格
- `tblr`：新式表格
- `equation`：行间数学公式
- `tikzpicture`：绘制图像



