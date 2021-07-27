---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-26 23:15:23'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-27 12:40:08'
parent: 0
password: ''
slug: WSL1-GUI-XServer-方案-VcXsrv
status: publish
tags: [GUI, Linux, WSL]
template: ''
title: WSL1 GUI (XServer) 方案 - VcXsrv
type: post
---
# 引言

由于朋友要跑个GUI脚本，里面还有`pyaudio`这样难搞定的包，以及还是在Windows下，我遂准备搞`WSL1 + XServer`的GUI。（不用WSL2 / WSLg的原因是他还要用安卓模拟器，需要禁用Hyper-V）

# 方案

1. 安装VcXsrv: https://sourceforge.net/projects/vcxsrv/
2. 配置`DISPLAY`环境变量：
	```bash
	echo -e "\n##DISPLAY Configuration" >> ~/.bashrc #配置声明
	echo "export DISPLAY=127.0.0.1:0.0" >> ~/.bashrc #添加配置
	source ~/.bashrc #配置生效
	```

至于桌面环境类似于`Gnome`这种需不需要安装看大家的喜好。

3. 打开安装好的XLaunch: <br/>
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210727123237.png)
4. 模式选择任意（个人偏好Multiple Windows）<br/>
   ![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210727123255.png)
5. 随后一路点Next

# 测试

我们拿一段`Matplotlib`的代码做一下测试好了：

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(0, 2, 100)

plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
plt.plot(x, x**2, label='quadratic')  # etc.
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend()
plt.show()
```

```
jefferson@HQWORKSTATION:/mnt/c/Users/JeffersonQin$ python3
Python 3.6.9 (default, Jan 26 2021, 15:33:00)
[GCC 8.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import numpy as np
>>> import matplotlib.pyplot as plt
>>> x = np.linspace(0, 2, 100)
>>>
>>> plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
[<matplotlib.lines.Line2D object at 0x7f1400564f28>]
>>> plt.plot(x, x**2, label='quadratic')  # etc.
[<matplotlib.lines.Line2D object at 0x7f1400564f60>]
>>> plt.plot(x, x**3, label='cubic')
[<matplotlib.lines.Line2D object at 0x7f1400577438>]
>>> plt.xlabel('x label')
Text(0.5, 0, 'x label')
>>> plt.ylabel('y label')
Text(0, 0.5, 'y label')
>>> plt.title("Simple Plot")
Text(0.5, 1.0, 'Simple Plot')
>>> plt.legend()
<matplotlib.legend.Legend object at 0x7f1400577780>
>>> plt.show()
```

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210727123149.png)

`OpenCV`测试：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210727123914.png)

(liyuu awsl!)

# Reference

https://www.cnblogs.com/liangxuran/p/14274847.html
