---
title: Sublime Text 4 配置
date: 2021-07-08 18:50:45
tags:
---

Sublime Text 4 配置

<!-- more -->

## 基础配置

```json
{

    "font_size": 20,  // 设置编辑区域字体大小

    "translate_tabs_to_spaces": true,  // 将 tab 转换为 空格

    "tab_size": 4,  // 设置 tab 长度为 四个空格

    "line_padding_top": 3,  // 设置行顶部距离

    "line_padding_bottom": 3,  // 设置行底部距离

    "save_on_focus_lost": true,  // 自动保存

    "show_encoding": true,  // 显示文件编码

    "preview_on_click": "only_left",

    "trim_trailing_white_space_on_save": "not_on_caret",  // 保存时，删除多余空白
}
```


## PHP

### 运行 PHP 文件

添加新的编译系统

`Tools` -> `Build System` ->` New Build System` 

内容如下

```json
{
     "cmd": ["php", "$file"],    
     "file_regex": "php$",     
     "selector": "source.php" 
}
```

保存为 `PHP.sublime-build`

重启 Sublime 生效


