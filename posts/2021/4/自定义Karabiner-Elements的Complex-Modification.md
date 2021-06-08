---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-04-10 16:35:00'
modified: '2021-04-10 16:45:26'
parent: 0
password: ''
slug: 自定义Karabiner-Elements的Complex Modification
status: publish
tags: [Karabiner-Elements, Github]
template: ''
title: 自定义Karabiner-Elements的Complex Modification
type: post
---
想要自定义Karabiner-Elements的Complex Modification，但却发现完全没有办法：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210410163726.png)

只能Import from browser，然而别人写的我必然用的有问题。后来发现，这个repo是开源的：https://github.com/pqrs-org/KE-complex_modifications

我们的目标：
- Cmd + Tab -> Ctrl + Tab
- Alt + Tab -> Cmd + Tab
- Ctrl + Tab -> Alt + Tab

根据repo上所说的，clone下来之后，整个过程分为4步：
1.  Put a `.rb` (or `.erb`) template file into [src/json](https://github.com/pqrs-org/KE-complex_modifications/tree/master/src/json). (Or put a `.json` file into [public/json](https://github.com/pqrs-org/KE-complex_modifications/tree/master/public/json) directly.)
2.  (Optional) Put extra description file into [public/extra_descriptions](https://github.com/pqrs-org/KE-complex_modifications/tree/master/public/extra_descriptions).
3.  Add the following json into [public/groups.json](https://github.com/pqrs-org/KE-complex_modifications/tree/master/public/groups.json) &gt; `files`.

    ```json5
    {
      "path": "json/your_awesome_configuration.json", // required
      "extra_description_path": "extra_descriptions/your_awesome_configuration.html" // optional
    },
    ```

4.  Run `make` command on Terminal. <br/> If you've put `.rb` (or `.erb`) source file into `src/json`, formatted json file will be auto generated in the `public/json/your_awesome_configuration.json`.

    ```shell
    make
    ```

遂这样搞了一把，看着别人的写法，照葫芦画瓢整出了一个json文件：https://github.com/JeffersonQin/KE-complex_modifications/blob/master/public/json/ctrl_option_command_tab_change.json

接下来，跑一下`make`, `make server`看一下效果，发现一切正常。由于Karabiner-Elements不允许http协议，所以更据提示: 

```
cp public/json/your_awesome_configuration.json ~/.config/karabiner/assets/complex_modifications
```

即可。最后提交一下PR来公开。