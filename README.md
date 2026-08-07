# PaperVN-Today

PaperVN 的 Today 编辑内容仓库。App 会从 GitHub Raw 获取 `manifest.json`，读取 GitHub 响应头中的联网时间，并按 `Asia/Shanghai` 转换为当天日期，然后加载对应的 `feeds/YYYY-MM-DD.json`。

## 目录结构

```text
manifest.json
feeds/
  2026-08-07.json
  2026-08-08.json
images/
  2026-08-07/
    v65076.jpg
    v4.jpg
  2026-08-08/
    ...
videos/
  2026-08-08/
    v65619.mp4
```

- `manifest.json`：Today 服务入口和离线回退入口。
- `feeds/YYYY-MM-DD.json`：某一天的全部 Today 卡片。
- `images/YYYY-MM-DD/`：该日期卡片使用的图片。
- `videos/YYYY-MM-DD/`：可选，只适合存放测试用的小型视频。

可以提前准备未来日期的内容。不同日期使用不同的 JSON 和图片目录，不需要覆盖旧日期的文件。

## 日期和回退

正常联网时，App 会读取联网日期对应的文件。例如联网日期是 `2026-08-08`，App 会读取：

```text
feeds/2026-08-08.json
```

如果联网正常但当天文件不存在，App 会显示“Today不可用”，不会把其他日期的内容冒充成当天内容。

如果网络完全不可用，App 会使用本地缓存；没有可用缓存时才显示网络错误界面。

## manifest.json

保持以下格式即可：

```json
{
  "schemaVersion": 1,
  "currentFeed": "feeds/2026-08-07.json"
}
```

字段说明：

- `schemaVersion`：当前必须是 `1`。
- `currentFeed`：网络日期无法获取时使用的回退文件。建议指向最近一个已经完整发布的 Today 文件。

联网正常时，`currentFeed` 不决定当天内容；当天内容由联网日期决定。

## 每日 Today 文件

文件名必须是：

```text
feeds/YYYY-MM-DD.json
```

文件中的 `date` 必须与文件名一致：

```json
{
  "schemaVersion": 2,
  "date": "2026-08-07",
  "stories": []
}
```

`schemaVersion` 可以是 `1` 或 `2`。使用介绍页时使用 `2`；只有普通 Today 卡片时仍可以使用 `1`。

`stories` 按页面从上到下排列。每个元素就是一张 Today 卡片。

## 卡片字段

完整示例：

```json
{
  "id": "2026-08-07-v60331",
  "groupID": "shiratamaco-2026-08-07",
  "visualNovelID": "v60331",
  "accessibilityTitle": {
    "default": "とける風花とシロうさぎ",
    "en": "Tokeru Fuuka to Shiro Usagi"
  },
  "eyebrow": {
    "default": "即将发行",
    "zh-Hant": "即將發行",
    "ja": "発売予定",
    "ko": "출시 예정",
    "en": "Coming Soon"
  },
  "description": {
    "default": "白玉的最新作品。",
    "zh-Hant": "白玉的最新作品。",
    "ja": "しらたまこの最新作。",
    "ko": "시라타마코의 최신 작품입니다.",
    "en": "The latest title from Shiratamaco."
  },
  "releaseDate": {
    "default": "2026年8月16日",
    "zh-Hant": "2026年8月16日",
    "ja": "2026年8月16日",
    "ko": "2026년 8월 16일",
    "en": "August 16, 2026"
  },
  "image": {
    "path": "images/2026-08-07/v60331.jpg",
    "width": 1200,
    "height": 849
  },
  "related": []
}
```

字段说明：

- `id`：卡片唯一 ID。同一天内不要重复，通常可以使用 `日期-vndbID`。
- `groupID`：可选。上下相邻且属于同一系列的卡片使用相同值，App 会用一个灰色大外框把它们包在一起。不需要分组时省略此字段。
- `visualNovelID`：VNDB 作品 ID，例如 `v60331`。点击卡片会进入这个作品的详情页，App 也会自动显示“相关作品”。
- `accessibilityTitle`：无障碍标题，也作为作品名称未加载完成时的备用名称。支持多语言对象。
- `eyebrow`：卡片左上角的小标题，例如“最近发行”“你知道吗”“即将发行”。支持多语言对象。
- `description`：卡片底部的主要描述文字。支持多语言对象。
- `releaseDate`：可选。填写后会显示在描述下方，例如 `2026年8月16日`。支持多语言对象。
- `image`：可选。指定编辑封面图片及其实际像素尺寸；省略时 App 会从 VNDB 获取该作品的封面。
- `video`：可选。填写后该卡片会以 16:9 播放静音循环视频，下方仍显示相关作品和相关角色。
- `introduction`：可选。填写后，点击卡片封面或视频会打开近全屏介绍页；介绍页末尾可以放置指定的相关作品和角色。
- `related`：相关作品和相关角色。没有额外关联时必须写空数组 `[]`。

### 介绍页

介绍页内容写在卡片的 `introduction` 字段中。当前支持的格式版本是 `1`：

```json
"introduction": {
  "schemaVersion": 1,
  "blocks": [
    {
      "style": "primary",
      "markdown": {
        "default": "这是黑色正文，支持**粗体**、*斜体*和~~删除线~~。",
        "ja": "これは黒色の本文です。**太字**、*斜体*、~~取り消し線~~に対応します。"
      }
    },
    {
      "style": "secondary",
      "markdown": {
        "default": "这是灰色的补充说明。"
      }
    }
  ],
  "related": [
    {
      "type": "visualNovel",
      "id": "v65619",
      "title": {
        "default": "百合爱丽丝",
        "ja": "ユリアリス"
      }
    }
  ]
}
```

- `schemaVersion`：介绍页格式版本。当前填写 `1`。
- `blocks`：从上到下排列的正文段落。`style` 为 `primary` 时使用主文字颜色（浅色模式黑色、深色模式白色），`secondary` 时使用系统灰色。
- `markdown`：支持多语言对象。正文支持 Apple Markdown 的粗体 `**文字**`、斜体 `*文字*`、删除线 `~~文字~~` 等行内语法。
- `related`：介绍页末尾显示的关联框，可以放作品、角色，或者两者。这里不会自动添加当前作品，想显示什么就明确写入什么。

点击封面或视频会打开高度约为屏幕 `0.98` 的 Sheet。顶部封面优先使用卡片的 `image`，没有时使用 VNDB 封面；图片向下逐渐变淡并增强模糊。点击关联框中的“查看”才会进入作品或角色详情页。

如果客户端不支持这个介绍页版本，Sheet 会显示“Today不可用”和“更新到最新版本以查看此页面。”。如果要让完全不认识介绍页字段的旧版客户端也拒绝当天 feed，请把当天文件的顶层 `schemaVersion` 设置为 `2`；旧版会将该 Today 内容视为不可用，新版会继续读取 schema 2。

### 多语言文本

Today 会按照用户当前的 App 界面语言选择文本。支持以下语言键：

- `zh-Hans`：简体中文
- `zh-Hant`：繁体中文
- `ja`：日语
- `ko`：韩语
- `en`：英语

文本既可以写成旧格式的普通字符串，也可以写成多语言对象。今后推荐使用多语言对象：

```json
"eyebrow": {
  "default": "最近发行",
  "zh-Hant": "最近發行",
  "ja": "最近発売",
  "ko": "최근 출시",
  "en": "New Release"
}
```

`default` 是没有对应翻译时的回退文本，通常填写简体中文或原文。App 会优先读取当前界面语言；没有该语言时使用 `default`。旧的纯字符串写法仍然有效。

`accessibilityTitle`、`eyebrow`、`description`、`releaseDate` 和关联内容的 `title` 都支持这种格式。`related.original` 保持原文字符串即可，不需要翻译。

### image 图片

`image` 是可选字段。如果作品的 VNDB 封面适合卡片，可以直接省略 `image`，App 会根据 `visualNovelID` 获取 VNDB 原始封面。

只有在需要专门的宣传图、截图或构图时，才需要把图片放进这个仓库：

图片路径相对于仓库根目录：

```text
images/2026-08-07/v60331.jpg
```

`width` 和 `height` 必须填写图片的实际像素尺寸，不是 App 中显示的尺寸。建议使用 JPG 或 PNG，并确保图片已经提交到对应日期目录。

### video 视频

视频卡片使用以下格式：

```json
"video": {
  "url": "https://papervn-today.jizpaper.com/2026-08-08-v65619.mp4"
}
```

`url` 可以是完整 HTTPS 地址，也可以是相对于仓库根目录的路径：

```json
"video": {
  "url": "videos/2026-08-08/v65619.mp4"
}
```

如果对象名称包含日文或其他非 ASCII 字符，建议在 URL 中使用百分号编码。例如对象名为 `2026-08-08-ユリアリス.mp4` 时：

```json
"video": {
  "url": "https://papervn-today.jizpaper.com/Today/2026-08-08-%E3%83%A6%E3%83%AA%E3%82%A2%E3%83%AA%E3%82%B9.mp4"
}
```

卡片有 `video` 时固定使用 16:9，视频默认静音循环播放。只要用户仍在 Today 页面内，即使向下滚动，视频也会继续播放；离开 Today 页面时会保存播放位置并暂停。相关内容区域使用 `image` 或 VNDB 封面作为背景。

小型测试 MP4 可以放在仓库的 `videos/`目录，但正式视频建议放在对象存储/CDN，并通过类似 `papervn-today.jizpaper.com` 的自定义域名访问。GitHub Raw 不适合长期承担大视频流量。视频服务需要支持 HTTPS、正确的 `video/mp4` 或 HLS MIME 类型，以及 HTTP Range 请求。

### related 关联内容

关联作品：

```json
{
  "type": "visualNovel",
  "id": "v4",
  "title": {
    "default": "CLANNAD",
    "ja": "CLANNAD",
    "en": "CLANNAD"
  },
  "original": null
}
```

关联角色：

```json
{
  "type": "character",
  "id": "c15850",
  "title": {
    "default": "宫泽有纪宁",
    "zh-Hant": "宮澤有紀寧",
    "ja": "宮沢有紀寧",
    "ko": "미야자와 유키네",
    "en": "Yukine Miyazawa"
  },
  "original": "宮沢 有紀寧"
}
```

- `type` 只能是 `visualNovel` 或 `character`。
- `id` 使用 VNDB 的作品或角色 ID。
- `title` 是备用名称。App 会按照用户的语言偏好重新获取和显示名称。
- `original` 用于填写原文名称，没有时可以写 `null` 或省略。

如果 `related` 中没有当前卡片的 `visualNovelID`，App 会自动添加一行“相关作品”，所以普通作品卡片可以直接使用 `"related": []`。

## 关联卡片示例

下面两张卡片使用相同的 `groupID`，并且在 JSON 中连续出现，因此会显示在同一个灰色大外框中：

```json
{
  "id": "2026-08-07-v60331",
  "groupID": "shiratamaco-2026-08-07",
  "visualNovelID": "v60331",
  "accessibilityTitle": "とける風花とシロうさぎ",
  "eyebrow": "即将发行",
  "description": "白玉的最新作品。",
  "releaseDate": "2026年8月16日",
  "image": {
    "path": "images/2026-08-07/v60331.jpg",
    "width": 1200,
    "height": 849
  },
  "related": []
},
{
  "id": "2026-08-07-v28297",
  "groupID": "shiratamaco-2026-08-07",
  "visualNovelID": "v28297",
  "accessibilityTitle": "星空列车与白的旅行",
  "eyebrow": "更多来自卷心菜社",
  "description": "那可不是……梦。",
  "image": {
    "path": "images/2026-08-07/v28297.jpg",
    "width": 383,
    "height": 512
  },
  "related": []
}
```

## 如何写自己的 Today

1. 复制一个已有的 `feeds/YYYY-MM-DD.json`，改成新的日期。
2. 按顺序增删 `stories`，修改每张卡片的 `eyebrow`、`description`、`releaseDate` 和 `visualNovelID`。
3. 对 `eyebrow`、`description` 等文本填写多语言对象；没有翻译的语言可以省略，App 会回退到 `default`。
4. 使用 VNDB 封面时省略 `image`；需要编辑图片时再放入 `images/YYYY-MM-DD/`。
5. 视频卡片填写 `video.url`，并确保视频地址已经可访问。
6. 需要点击封面打开介绍页时，填写 `introduction`，并把当天文件的顶层 `schemaVersion` 改为 `2`。
7. 如果两张上下相邻的卡片属于同一系列，给它们填写相同的 `groupID`。
8. 确认 JSON、图片路径和视频地址正确。
9. 提交并推送到 GitHub 的 `main` 分支。

本地可以用 `jq` 检查 JSON：

```bash
jq empty manifest.json feeds/2026-08-07.json
```

## 预览未来的 Today

PaperVN 的 Debug 构建会在 Today 标题右侧显示日历按钮：

- 选择“预览明天”后，App 会直接请求明天的 `feeds/YYYY-MM-DD.json`。
- 选择“使用联网日期”后，恢复根据 GitHub 响应头日期选择 Today。

预览只存在于 Debug 构建，不会改变正式版 App 的日期判定。预览前需要先把对应日期的 JSON 和媒体推送到 Today 仓库。

## 推荐游戏

Today 卡片本身就是编辑推荐游戏的方式。要推荐一部作品，创建一张卡片并填写：

- `visualNovelID`：要推荐的 VNDB 作品 ID。
- `image`：该作品封面。
- `eyebrow`：推荐角度，例如“编辑推荐”“最近发行”，建议写成多语言对象。
- `description`：推荐理由，建议为每种 App 界面语言提供翻译。

当前页面下方独立的“为你推荐”区域是 PaperVN 根据用户偏好生成的个性化推荐，不读取这个仓库的 JSON。也就是说，编辑者可以通过 Today 卡片推荐游戏，但不能用当前 JSON 直接控制下面那组个性化列表。

## 固定入口

```text
https://raw.githubusercontent.com/JiZPaper/PaperVN-Today/main/manifest.json
```
