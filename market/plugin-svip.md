# BinSvip

适用格式：X Studio 工程文件 (*.svip)

版本：1.1.6

最后更新日期：2022.05.30

作者：[YQ之神](https://space.bilibili.com/102844209)

-----

## 简介

由于 OpenSVIP 框架的中介模型目前主要是基于 svip 工程文件的数据格式的，因此本插件可以实现几乎无损的完整数据转换。由于输入或输出平台的不同，部分数据可能无法与源平台或目标平台一一对应。

> **[!WARNING]**
>
> 请注意，此插件依赖 X Studio 的部分组件，但为避免法律纠纷，我们无法将该部分被依赖的组件一同打包。因此，需要在本机安装至少 1.8 版本的 X Studio · 歌手软件才可正常使用此插件；若需要转换**力度**参数，则需要 2.0 版本。同时，还需要将软件设置为 .svip 文件的默认打开方式（通常由安装程序自动完成）。如果仍无法使用，请检查注册表项 HKEY_LOCAL_MACHINE\SOFTWARE\Classes\svipfile\shell\open\command 是否为 X Studio 主程序路径。

## 数据支持清单

### 读取 X Studio 工程文件

| 数据内容 | 支持等级 |                             说明                             |
| :------: | :------: | :----------------------------------------------------------: |
|   轨道   |    ✓     |                                                              |
|   曲谱   |    ✓     |                                                              |
|   歌词   |    ✓     |                                                              |
| 唱法标记 |    ✓     |                                                              |
|   颤音   |    ×     | 由于在 X Studio 中添加的颤音会被立即写入音高参数，因此无需支持读取。 |
| 音高参数 |    ✓     |                                                              |
| 音素参数 |    ✓     |                                                              |
| 音量参数 |    ✓     |                                                              |
| 气声参数 |    ✓     |                                                              |
| 性别参数 |    ✓     |                                                              |
| 力度参数 |    ○     |         需要安装 X Studio 2.0 及以上版本才支持读取。         |

### 写入 X Studio 工程文件

| 数据内容 | 支持等级 |                             说明                             |
| :------: | :------: | :----------------------------------------------------------: |
|   轨道   |    ✓     |                                                              |
|   曲谱   |    ✓     |                                                              |
|   歌词   |    ○     | 原则上仅支持中文歌词导入。源格式中的歌词包含字母（通常为汉语拼音或罗马音）时将以“修改发音”转换。可能因此产生不在 X Studio 发音表内的音节，从而导致无法直接播放。 |
| 唱法标记 |    ✓     |                                                              |
|   颤音   |    ×     | 由于 X Studio 仅根据音高参数进行合成，因此不支持单独写入颤音。 |
| 音高参数 |    ✓     | 源格式中任何对音高曲线产生影响的数据（例如“音高偏差”“颤音”）将被映射至音高参数。 |
| 音素参数 |    ✓     | 源格式中任何对音素长短产生影响的数据（例如“辅音长度”）将被映射至音素参数。 |
| 音量参数 |    ✓     |            源格式中的音量包络将被映射至音量参数。            |
| 气声参数 |    ✓     |     源格式中对气息强度产生影响的数据将被映射至气声参数。     |
| 性别参数 |    ✓     |      源格式中对共振峰产生影响的数据将被映射至性别参数。      |
| 力度参数 |    ○     | 需要安装 X Studio 2.0 及以上版本才支持写入。源格式中任何对演唱力度产生影响的数据（例如“张力”“力度”）将被映射至力度参数。 |

## 高级选项

### 输入选项

此插件暂无输入选项。

### 输出选项

#### 缺省歌手

可以指定输出工程文件中的歌手。必须输入完整无误的歌手名字。若此选项指定的歌手不存在，将使用 X Studio 中设置的默认歌手。如果要指定歌手编号与名称的转换关系，或添加已拥有编号但未公开发行的歌手，请修改插件目录下的 SingerDict.json。

#### 缺省曲速

X Studio 支持的曲速范围为 20 ~ 300。曲速超出范围时，将会启用绝对时间轴以确保音符对齐。请尽量将此值设置为源工程文件中曲速的整数倍或整数分之一；只要在合理数值范围内，此选项的值不会影响对齐效果。

#### 输出文件版本

此选项仅控制输出工程文件的头部版本信息。选择较旧的工程文件版本不会造成数据丢失。

此选项支持的值如下：

- 自动选择：使用系统上安装的 X Studio 所对应的工程文件版本
- SVIP 7.0.0：使用 X Studio 2.0 对应的工程文件版本
- SVIP 6.0.0：使用兼容 X Studio 1.8 的工程文件版本
- 最大只读兼容：使用兼容任意版本 X Studio 的工程文件版本

> **[!NOTE]**
>
> 各版本的 X Studio 均有一个对应的工程文件版本号。例如，X Studio 1.8（最高支持 SVIP6.0.0）无法打开由 X Studio 2.0（对应 SVIP7.0.0）保存的工程文件。将头部信息设置为较低版本可以增强兼容性，例如使 X Studio 1.8 能够打开包含力度参数的工程文件（尽管不会在编辑器中显示）。
>
> **注意：能够打开并不意味着能够无损保存。例如，使用 X Studio 1.8 编辑并保存包含力度参数的工程文件，会导致力度参数永久丢失，即使再次使用 X Studio 2.0 打开也无法恢复。<font color="red">若选择保存为“最大只读兼容”，将导致音量、气声、性别、力度参数无法被 X Studio 识别（数据没有丢失）。为了避免无法挽回的数据丢失，强烈建议不要使用 X Studio 修改和保存使用此选项导出的工程文件。若要重新将工程文件恢复至可安全编辑的状态，请选择保存为 SVIP 6.0.0 及以上版本。</font>**

## 更新日志

#### v1.1.6 (2022.05.30)

- 修复了一个导致所有人都转换失败的愚蠢 bug

在此为没有做好测试工作向所有受影响的用户表示由衷的歉意。

#### v1.1.5 (2022.05.26)

- 针对“最大兼容性”选项可能导致的数据丢失风险，添加警告信息
- “最大兼容性”选项改名为“最大只读兼容”
- 更新后使用本插件重新输出为 SVIP 6.0.0 及以上版本，可以从“最大兼容性”工程文件中恢复无法被 X Studio 识别的参数

#### v1.1.4 (2022.05.23)

- 修复了未安装 X Studio 时未能正确显示错误信息的 bug

#### v1.1.3 (2022.05.21)

- 新增“最大兼容性”选项，支持导出兼容任意版本 X Studio 的工程文件

#### v1.1.2 (2022.05.06)

随转换器公测发布

## 开源声明

本插件使用和参考了以下第三方开源项目：

|    开源项目     |                    许可证                     |            相关链接             |
| :-------------: | :-------------------------------------------: | :-----------------------------: |
| Newtonsoft.Json | [MIT License](https://licenses.nuget.org/MIT) | https://www.newtonsoft.com/json |

