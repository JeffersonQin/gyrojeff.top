---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [LaTeX, 技术]
created: '2020-12-19 19:16:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/17.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:56:52'
parent: 0
password: ''
slug: '103'
status: publish
tags: [LaTeX]
template: ''
title: LaTeX的算法 | 伪代码排版
type: post
---
# Preface

写论文时要陈述自己提出来的算法，遂学。

# 方法1（deprecated）

引入宏包：

```latex
\usepackage{algorithm}
\usepackage{algorithmic}
```

重新定义样式：

```latex
\renewcommand{\algorithmicrequire}{\textbf{Input:}} 
\renewcommand{\algorithmicensure}{\textbf{Output:}}
```

下面是一些简单的使用：

```latex
\begin{algorithm}[H]
    \caption{algorithm 1}
    \begin{algorithmic}[1] % 此处的[1]控制一下算法中的每句前面都有标号 
        \REQUIRE input
        \ENSURE output 
        \STATE {Step A}
        \STATE {Step B}
        \FOR {$ i = 1 $; $ i < n $; $ i ++ $ }
            \STATE {Step C}
        \ENDFOR
        \WHILE {$ |E_n| \leq L_1 $}
            \STATE {Step D}
        \ENDWHILE
        \FORALL {...}
            \STATE {Step E}
        \ENDFOR
        \IF {condition}
            \STATE {Step F}
        \ENDIF
    \end{algorithmic} 
\end{algorithm}
```

效果图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608375638.png)

# 方法二：`algorithm2e`

引入宏包：

```latex
\usepackage[ruled]{algorithm2e}
```

常见用法：

```latex
\begin{algorithm}[t]
	\caption{Algorithm Test}
	\label{algorithm:test}
	\LinesNumbered
	\KwIn {input}
	\KwOut {output}
	\For{i = 1 to n}{	
		\eIf{condition} {
            Step A \tcp*{comment inline}
            \tcp{new line comment}
	    } {
            i ++;
        }
    }
    \While{condition}{
        do something
    }
    Step B \;
    Step C \;
    Step D ;
\end{algorithm}
```

效果图：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608376405.png)

如果引用宏包时附带其他参数，如：

```latex
\usepackage[ruled,vlined]{algorithm2e}
```

效果：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/2020/12/1608376522.png)

更详细的用法详见文档：

- https://ctan.org/pkg/algorithm2e
- https://mirrors.hit.edu.cn/CTAN/macros/latex/contrib/algorithm2e/doc/algorithm2e.pdf

