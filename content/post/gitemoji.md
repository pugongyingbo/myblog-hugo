+++
title = "git提交使用emoji"
Tags = ["Development"]
menu = "main"
date = "2018-04-10T22:35:32+08:00"
Categories = ["Development"]
+++
### git commit 使用emoji
* 看到GitHub上有在提交信息里使用emoji的，感觉很好玩所以就搜了一下，发现gitemoji开源库，然后打算以后使用表情包提交信息，更加直白的表明提交情况。
* 使用方式很简单 

```
    git commit -m ":bug: 修复某bug"
```


* ![示例](https://github.com/pugongyingbo/pugongyingbo.github.io/tree/master/images/gitemoji.jpg)
* 常用例子
* 
 emoji | emoji代码 | commit说明
 :-:|:-:|:-:
 :art: (调色板)     | : art : |   改进代码     
 :fire:(火焰)        |   : fire :   |   移除代码或文件   
 :bug:(bug)        |    : bug :    |  修复bug  
 :ambulance:(急救车)| : ambulance :|重要补丁
 :sparkles:(火花)| : sparkles :|引入新功能
 :memo:(备忘录)| : memo :|编写文档
 :tada:(庆祝)| : tada :|初次提交

### 参考
* [官方仓库](https://github.com/carloscuesta/gitmoji/) 
* [中文指南](https://github.com/liuchengxu/git-commit-emoji-cn/)
