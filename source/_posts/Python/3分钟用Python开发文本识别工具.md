---
title: 3分钟用Python开发文本识别工具
date: 2021-08-12 07:05:25
tags:
    - OCR
categories:
    - Python
---

3分钟用Python开发文本识别工具

<!-- more -->

## 原理

- 获取剪切板图片
- 识别图片中的文字
- 将文字复制到剪切板

## 依赖

```bash
pip install pillow  pyclipper shapely paddlepaddle paddlehub opencv-python
```


## 功能实现

### 文件名生成

生成随机图片文件名

```py
def generate_file_name():
    return str(time.time()).replace('.', '') + ".png"
```


### 读取剪切板图片

`grabclipboard()`

功能:

- 对剪贴板上的图像进行快照。目前只支持macOS和Windows。

返回值:

- 在Windows下，是一个图像，一个文件名的列表，如果剪贴板不包含图像数据或文件名，则为 None。注意，如果返回一个列表，文件名可能不代表图像文件。
- 在Mac上，一个图像，或者如果剪贴板不包含图像数据，则为None。


```python
from PIL import ImageGrab

def get_image(self):
    img = ImageGrab.grabclipboard()
    if img not None:
        img_path = os.environ['HOMEPATH'] + '/tmp_image/1.png'
        # 保存图片
        img1.save(img_path)
        return img_path
    return None
```

### 图片文字识别

```py
hub = paddlehub.Module(name="chinese_ocr_db_crnn_mobile")
results = hub.recognize_text(images=[cv2.imread(image_path)])
```

识别结果处理

```bash
for r in results:
    data = r.get('data')
    if data:
        for d in data:
            result_str = result_str + d.get('text') + "\n"
return result_str
```
