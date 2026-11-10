---
layout:       post
title:        "Ubuntu 文件无法拖入飞书、ChatGPT 和 Chrome：一次 Nautilus 拖放故障排查"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - tools
    - linux
---

从 Ubuntu 的“文件”管理器拖一个文件到飞书，鼠标能带着文件图标移动，但松开后没有反应。换成 ChatGPT 和 Chrome，现象也一样。

最终，通过几组本地对照测试，故障范围收敛到了 **Nautilus 文件管理器与 Chromium 类应用之间的拖放交互**。保存打开的文件夹位置、正常退出并重新启动 Nautilus 后，同一条“文件管理器 → Chrome”路径恢复了正常。

这次没有重装软件，也没有调整输入法配置。下面记录排查过程、恢复方法，以及哪些结论已经得到验证。

## 1. 现象与环境

问题发生在 2026 年 11 月 10 日。文件是从“文件”管理器窗口中拖出来的，不是从桌面图标拖出来的。

具体表现为：

- 文件能被鼠标拖起，有跟随鼠标移动的图标。
- 拖到飞书、ChatGPT 或 Chrome 后，释放鼠标没有预期效果。
- Chrome 新标签页上能看到拖动图标及复制样式的“＋”标记，但没有打开文件。
- 普通点击和文件选择仍可操作，并不是整个鼠标失灵。

当时的软件环境如下：

| 项目 | 本机环境 |
| --- | --- |
| 系统 | Ubuntu 22.04.5 LTS |
| 桌面 | GNOME，X11 会话 |
| GNOME Shell | `42.9-0ubuntu2.4` |
| 文件管理器 | Nautilus `1:42.6-0ubuntu2` |
| GTK 3 软件包 | `3.24.33-1ubuntu2.2` |
| Chrome | `149.0.7827.155` |
| 飞书 | `7.22.9-0` |
| 旧 Nautilus 进程运行时间 | 约 62 天 |

长时间运行是一个值得记录的条件，但它本身不能证明故障由运行时间触发。

## 2. 为什么不能只看鼠标图标

拖动图标出现，说明拖动已经开始；它不能证明目标应用接收到了文件。

为了定位问题，可以把这次交互拆成几个观察点：

```text
文件管理器开始拖动
    ↓
向目标应用提供可用的数据类型
    ↓
目标应用获取文件地址或其他数据
    ↓
用户释放鼠标，完成放置
    ↓
目标输入框或网页处理文件
```

其中任一环节异常，都可能表现为“文件拖过去了，但是没反应”。因此，需要同时观察拖动源、接收端和最终输入结果。

Chrome 新标签页也不是理想的测试目标：它不能直观显示浏览器是否收到拖放事件。换成一个明确接受拖放、能显示事件结果的本地网页，判断会更直接。

## 3. 先检查桌面和进程状态

在当前图形桌面会话的终端中，可以先执行：

```bash
echo "$XDG_SESSION_TYPE"
ps -eo pid,ppid,uid,stat,etimes,comm | \
  rg -i 'Xorg|wayland|gnome-shell|nautilus|feishu|ChatGPT'
```

本机仍然是 X11；Nautilus、飞书和 ChatGPT 都以当前普通用户运行。没有发现“文件管理器以 root 身份启动、目标应用以普通用户运行”这一类明显的运行身份差异。

随后检查近期日志：

```bash
journalctl --user --since '2 hours ago' --no-pager -o short-iso | \
  rg -i 'xdnd|drag|drop|nautilus|mutter|JS ERROR'
```

日志里没有直接解释本次失败的拖放错误。虽然出现过鼠标丢失输入事件的记录，但后面的成功拖放对照表明，不能据此把当前故障直接归因于鼠标硬件。

也检查了窗口的 X11 属性。文件管理器与目标应用窗口都声明了 `XdndAware`，所以没有证据表明某个目标窗口完全未声明拖放支持。不过，声明支持并不等于协议交互一定成功。

## 4. 第一组测试：文件管理器能否发出文件地址

先打开一个独立的 GTK 测试窗口，只接受文件 URI，不上传、不移动文件，也不读取文件内容。

从文件管理器拖入一个普通文件后，窗口显示：

```text
已收到文件地址：1 个
文件管理器到 GTK 的拖放链路可用。
```

对应日志包含：

```text
DRAG_ENTER targets=(
  'x-special/gnome-icon-list',
  'text/uri-list',
  'UTF8_STRING',
  'COMPOUND_TEXT',
  'TEXT',
  'STRING',
  'text/plain;charset=utf-8',
  'text/plain'
)
DATA_RECEIVED target=text/uri-list uri_count=1
```

这组结果说明，文件管理器能够发起拖动，提供标准的 `text/uri-list`，而且 GTK 接收端能取得文件地址。

此时可以缩小范围，但还不能宣称文件管理器完全正常。它与 GTK 应用的交互成功，不代表它与 Chromium 应用的交互也一定成功。

## 5. 第二组测试：Chrome 实际收到了什么

为了排除业务网页本身的影响，在 Chrome 中打开了一个完全本地运行的拖放诊断页。

页面监听 `dragenter`、`dragover` 和 `drop`，并在收到 `drop` 后显示文件数量。核心测试逻辑如下，可保存为 `drop-test.html`，再用 Chrome 打开：

```html
<!doctype html>
<html lang="zh-CN">
<meta charset="utf-8">
<title>本地文件拖放测试</title>
<style>
  body { max-width: 800px; margin: 48px auto; font: 20px sans-serif; }
  #drop { border: 3px dashed #5582bf; padding: 64px; background: #f4f8ff; }
  pre { white-space: pre-wrap; background: #eee; padding: 20px; }
</style>
<h1>本地文件拖放测试</h1>
<p>只显示事件和文件数量，不上传、不读取文件内容。</p>
<div id="drop">把文件拖到这里</div>
<pre id="result">等待拖放事件……</pre>
<script>
  const result = document.querySelector('#result');

  document.addEventListener('dragenter', event => {
    event.preventDefault();
    result.textContent = '收到 dragenter，类型：' +
      [...event.dataTransfer.types].join(', ');
  });

  document.addEventListener('dragover', event => {
    event.preventDefault();
    event.dataTransfer.dropEffect = 'copy';
  });

  document.addEventListener('drop', event => {
    event.preventDefault();
    result.textContent = '收到 drop，文件数量：' +
      event.dataTransfer.files.length;
  });
</script>
</html>
```

从原来的文件管理器拖入文件，页面停留在：

```text
收到 dragenter，类型：text/plain
```

松开鼠标后，没有出现 `drop` 结果。

这说明 Chrome 页面已经感知到拖动进入，但它暴露给网页的数据类型只有 `text/plain`，没有预期的 `Files`。本次交互没有完成正常的文件放置。

这里必须区分两个观察层次：GTK 日志看到的是拖动源提供的类型；网页看到的是浏览器处理后暴露的类型。不能只凭这两个输出就断言具体是哪个组件“把文件转换成了文字”，还需要对照实验。

## 6. 第三组测试：换一个拖动源，Chrome 是否能接收

在 GTK 测试窗口底部增加一个可以拖动的区域。这个区域提供一个自动创建的本地测试文件，并且只提供 `text/uri-list` 类型。

这次拖动路径变成：

```text
独立 GTK 测试窗口 → Chrome 本地测试页
```

测试窗口记录到 Chrome 请求文件地址：

```text
SOURCE_DRAG_BEGIN
SOURCE_DATA_REQUESTED target=text/uri-list
SOURCE_DRAG_END
```

Chrome 页面最终显示：

```text
收到 drop，文件数量：1
```

到这里，三组对照结果已经明确：

| 拖动源 | 接收端 | 结果 |
| --- | --- | --- |
| 原 Nautilus 进程 | GTK 测试窗口 | 成功收到文件 URI |
| 原 Nautilus 进程 | Chrome 本地测试页 | 只有 `dragenter`，类型为 `text/plain`，没有 `drop` |
| 独立 GTK 测试窗口 | 同一个 Chrome 测试页 | 成功收到 `drop`，文件数量为 1 |

因此，Chrome 的文件接收能力仍然可用。异常范围进一步收敛到了原 Nautilus 进程与 Chromium 类接收端之间的交互。

## 7. 恢复操作：保存位置，重启文件管理器

确认上述差异后，选择只重启 Nautilus，并保留当前打开的文件夹位置。

### 7.1 检查是否有文件操作正在进行

重启前检查了 Nautilus 的 `org.gtk.Application.Busy`，当时为 `false`：

```bash
gdbus call --session \
  --dest org.gnome.Nautilus \
  --object-path /org/gnome/Nautilus \
  --method org.freedesktop.DBus.Properties.Get \
  org.gtk.Application Busy
```

实际操作前，也应确认窗口中没有正在复制、移动或解压的任务。`Busy` 是辅助状态，不应作为所有文件操作都已结束的唯一保证。

### 7.2 记录当前打开的文件夹

本机 Nautilus 提供了 `OpenWindowsWithLocations` 属性，可以查询窗口和文件夹 URI 的对应关系：

```bash
gdbus call --session \
  --dest org.freedesktop.FileManager1 \
  --object-path /org/freedesktop/FileManager1 \
  --method org.freedesktop.DBus.Properties.Get \
  org.freedesktop.FileManager1 OpenWindowsWithLocations
```

本次共保存了 4 个窗口对应的位置。这能恢复文件夹位置，但不应理解为完整保存了窗口布局、选中文件和所有界面状态。

### 7.3 正常退出并重新打开

对于手工操作，确认没有文件操作进行后，可以在桌面终端执行：

```bash
nautilus --quit
```

然后重新从应用菜单打开“文件”，或打开一个指定目录：

```bash
nautilus --new-window "$HOME/Downloads"
```

目录应替换成实际存在的位置。中文桌面环境的下载目录可能不是 `Downloads`。

这次通过诊断会话执行 `nautilus --quit` 时，命令返回了非零状态，但后续检查发现旧进程已经退出。因此，没有仅凭退出码判定重启失败，也没有继续强制结束进程，而是检查实际 PID 和桌面会话接口。

之后通过 `org.freedesktop.FileManager1.ShowFolders` 重新打开保存的位置，并确认 4 个文件夹位置已经恢复。由于窗口打开是异步过程，第一次立即查询曾显示 0 个位置，稍后再次查询才得到 4 个；检查实际状态时也需要考虑这一点。

### 7.4 复测原始路径

完成重启后，再从重新打开的文件管理器拖文件到同一个 Chrome 本地测试页，结果变成：

```text
收到 drop，文件数量：1
```

这组复测与之前的失败使用相同的目标页面，主要改变的是 Nautilus 进程。它支持“原 Nautilus 进程的运行状态与故障有关”这一判断。

## 8. 已确认的原因与尚未确认的部分

本次可以确认到的故障层次是：**旧 Nautilus 进程与 Chromium 类应用之间的文件拖放交互异常，更换 Nautilus 进程后，Chrome 的原始拖放路径恢复。**

已经验证的事实包括：

- 鼠标可以发起拖动，GTK 接收端也可以收到文件 URI。
- 同一个 Chrome 测试页可以接收独立 GTK 拖动源提供的文件。
- 旧 Nautilus 拖入 Chrome 时，只观察到 `text/plain` 类型的 `dragenter`，没有完成 `drop`。
- 重启 Nautilus 后，Chrome 收到了文件类型的放置结果。

尚未查明的是：

- Nautilus 的哪一项内部状态导致了异常。
- 是否与数据类型选择、拖放动作协商或其他 X11 交互细节有关。
- 是否存在特定的触发操作，或者能稳定复现的上游缺陷。
- 连续运行约 62 天是否与故障存在因果关系。

“重启后恢复”是恢复手段和定位证据，还不是代码级根因证明。本次没有记录完整的 X11 拖放协议交互，也没有对 Nautilus 或 Chromium 做调用栈追踪，因此不宜把某种内部机制写成确定原因。

此前这台机器还处理过搜狗输入法切换问题。本次恢复过程中没有修改输入法设置，没有证据表明两次故障有关。

另外，**本次闭环验证的是 Chrome**。飞书和 ChatGPT 最初有同类现象，但在本次记录结束时，尚未获得它们各自恢复的实际测试结果，不能将三者一概写为全部修复。

## 9. 再次遇到时的排查顺序

1. 确认文件是从桌面、文件管理器、压缩包还是远程目录拖出。
2. 区分“拖不起来”和“拖起来但目标没反应”。
3. 用明确支持文件拖放的本地页面测试，记录事件类型和文件数量。
4. 用独立接收端验证源应用能否提供文件 URI。
5. 换一个拖动源，检查目标应用的文件接收功能是否可用。
6. 如果故障收敛到文件管理器，先记录窗口位置并确认文件操作结束，再正常重启文件管理器。
7. 重启后复测最初失败的路径，并对每个受影响应用分别确认。

对于这次问题，最有价值的证据不是某条报错日志，而是三个对照测试加上重启后的复测。它们把“整个系统不能拖文件”缩小到了一个具体的进程交互问题，也让恢复操作能够只涉及文件管理器。
