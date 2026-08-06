# PaperVN-Today

PaperVN的Today编辑内容仓库。App通过GitHub Raw读取`manifest.json`，再加载其指向的每日内容文件。

## 目录

- `manifest.json`：当前已发布内容的入口。
- `feeds/YYYY-MM-DD.json`：按日期归档的Today内容。
- `images/YYYY-MM-DD/`：当日卡片使用的图片。

## 发布

1. 新建每日内容和图片。
2. 校验JSON与图片尺寸。
3. 将`manifest.json`的`currentFeed`改为新文件路径。
4. 提交并推送到`main`分支。

App的固定入口：

```text
https://raw.githubusercontent.com/JiZPaper/PaperVN-Today/main/manifest.json
```
