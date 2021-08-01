---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2020-09-02 09:40:38'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2020-09-02 09:40:38'
parent: 0
password: ''
slug: VBA实现日期自动标记以及表格数据更新
status: publish
tags: [Excel, VBA]
template: ''
title: VBA实现日期自动标记以及表格数据更新
type: post
---
# 需求

- 自动标记当前日期所在的列，高亮
- 自动更新图标数据源

# 实现

使用ActiveX控件实现

```VB
'Get English Name of Column
Public Function Fun_GetEngName(ByVal argColumn As Integer) As String
    Dim strEngName As String
    Dim iNum, iMod As Integer

    iNum = argColumn \ 26
    iMod = argColumn Mod 26
    If (iMod = 0) Then
        If (iNum = 1) Then
            strEngName = Chr(90)
        Else
            strEngName = Chr(65 + iNum - 2) + Chr(90)
        End If
    Else
        If (iNum = 0) Then
            strEngName = Chr(65 + iMod - 1)
        Else
            strEngName = Chr(65 + iNum - 1) + Chr(65 + iMod - 1)
        End If
    End If
    Fun_GetEngName = strEngName
End Function

Private Sub CommandButton1_Click()
    'Clear Background Color
    Rows("1:1").Interior.ColorIndex = 2
    Rows("2:2").Interior.ColorIndex = 2
    'Get Current Date
    Dim idate As Date
    idate = Format(Now, "yyyy/m/d")
    MsgBox "Today is " & idate & ", have a nice day!", , "Auto Dater, by H.Q."
    'Set the bgcolor of the cell of current date
    Worksheets(1).Cells(171, 3).Value = idate
    Dim i
    i = Worksheets(1).Cells(171, 5).Value
    Worksheets(1).Cells(2, i).Interior.ColorIndex = 8
    Worksheets(1).Cells(1, i).Interior.ColorIndex = 8
    'Set data source of charts (till yesterday)
    Dim s As String
    s = Fun_GetEngName(i - 1)
    ActiveSheet.ChartObjects("TimingStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$107:$" & s & "$111")
    ActiveSheet.ChartObjects("SleepingStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$107:$" & s & "$107")
    ActiveSheet.ChartObjects("EntertainmentStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$110:$" & s & "$110")
    ActiveSheet.ChartObjects("StudyStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$108:$" & s & "$108")
    ActiveSheet.ChartObjects("NormalStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$109:$" & s & "$109")
    ActiveSheet.ChartObjects("TechStatistics").Activate
    ActiveChart.SetSourceData Source:=Range("Main!$A$2:$" & s & "$2,Main!$A$111:$" & s & "$111")
End Sub
```

# 注意事项

- 图表名称需要命名，具体方法：https://www.xuexila.com/excel/biaoge/1647271.html
- 日期的减法时使用Excel内置函数的`-`实现的

# 效果

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210801104744.png)
