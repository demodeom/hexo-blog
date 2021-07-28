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
    // 设置编辑区域字体大小
    "font_size": 20,
    // 将 tab 转换为 空格
    "translate_tabs_to_spaces": true,
    // 设置 tab 长度为 四个空格
    "tab_size": 4,
    // 设置行顶部距离
    "line_padding_top": 3,
    // 设置行底部距离
    "line_padding_bottom": 3,
    // 自动保存
    "save_on_focus_lost": true,
    // 显示文件编码
    "show_encoding": true,
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


