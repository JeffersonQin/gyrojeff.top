---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-07-27 20:51:06'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-07-27 22:36:35'
parent: 0
password: ''
slug: WSL访问Windows剪切板图片
status: publish
tags: [Linux, Powershell, Shell, WSL, Windows, 剪切板]
template: ''
title: WSL访问Windows剪切板图片
type: post
---
# 引言

今天给`PicGo`的项目想要增加在WSL下的剪切板图片读取，于是乎构思出如下想法：

```
WSL -> powershell.exe -> 获取Windows剪切板图片
```

# 方法

在`PicGo-Core`的框架下，后半部分已经是现成的了：

```powershell
# Adapted from https://github.com/octan3/img-clipboard-dump/blob/master/dump-clipboard-png.ps1
param($imagePath)

# https://github.com/PowerShell/PowerShell/issues/7233
# fix the output encoding bug
[console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding

Add-Type -Assembly PresentationCore
function main {
    $img = [Windows.Clipboard]::GetImage()

    if ($img -eq $null) {
        "no image"
        Exit 1
    }

    if (-not $imagePath) {
        "no image"
        Exit 1
    }

    $fcb = new-object Windows.Media.Imaging.FormatConvertedBitmap($img, [Windows.Media.PixelFormats]::Rgb24, $null, 0)
    $stream = [IO.File]::Open($imagePath, "OpenOrCreate")
    $encoder = New-Object Windows.Media.Imaging.PngBitmapEncoder
    $encoder.Frames.Add([Windows.Media.Imaging.BitmapFrame]::Create($fcb)) | out-null
    $encoder.Save($stream) | out-null
    $stream.Dispose() | out-null

    $imagePath
    # fix windows 10 native cmd crash bug when "picgo upload"
    # https://github.com/PicGo/PicGo-Core/issues/32
    Exit 1
}

try {
    # For WIN10
    $file = Get-Clipboard -Format FileDropList
    if ($file -ne $null) {
        Convert-Path $file
        Exit 1
    }
} catch {
    # For WIN7 WIN8 WIN10
    main
}

main
```

(脚本名称为`windows10.ps1`)

使用方法是传入要保存到的路径。举例：

```
powershell.exe -noprofile -noninteractive -nologo -sta -executionpolicy unrestricted -file windows10.ps1
```

对于`WSL`下的脚本，我们可以用同样的参数传入，只不过需要对路径动一下手脚。其中用到的关键方法：
- `awk`对`string`进行截取 (注：它的`index`从1开始，不是0)
- `wslpath -w`将`WSL`路径转换为`Windows`路径

```bash
#!/bin/sh
# grab the paths
scriptPath=$(echo $0 | awk '{ print substr( $0, 1, length($0)-6 ) }')"windows10.ps1"
imagePath=$(echo $1 | awk '{ print substr( $0, 1, length($0)-18 ) }')
imageName=$(echo $1 | awk '{ print substr( $0, length($0)-17, length($0) ) }')

# run the powershell script
res=$(powershell.exe -noprofile -noninteractive -nologo -sta -executionpolicy unrestricted -file $(wslpath -w $scriptPath) $(wslpath -w $imagePath)"\\"$imageName)

# note that there is a return symbol in powershell result
noImage=$(echo "no image\r")

# check whether image exists
if [ "$res" = "$noImage" ] ;then
    echo "no image"
else
    echo $1
fi
```

使用方法：

```
sh <path-to-script> <path-to-image>
