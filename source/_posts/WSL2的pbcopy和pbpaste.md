---
title: WSL2的pbcopy和pbpaste
categories: [工具]
date: 2021-03-07 01:07:53
tags:
- 工具
---

## 背景

自从换了mac以后，爱上了 `pbcopy` 和 `pbpaste` 命令。

Linux下也有实现方案：[《如何在 Linux 上使用 pbcopy 和 pbpaste 命令》](https://linux.cn/article-9917-1.html)

`pc-linux`我已经弃坑多年，在家常用 `wsl2` 环境办公，但是少了这两个命令就一直感觉不得劲儿。然后在网上查了一通资料，最终捣鼓了小半天，算是“曲线救国”。

此方案依赖于项目：https://github.com/Konfekt/xclip-xsel-WSL

<!-- more -->

## 实况

### 实现原理

```bash
[pbcopy]
{wsl2/powershell} <--read-- {win_clipboard}

[pbpaste]
{wsl2/bash} --write--> {file_clipboard} --read--> {wsl2/powershell} --write--> {win_clipboard}
```

### 详细步骤

#### <font color="#3a3">1. **在 `wsl2` 中需要能执行win10程序，配置更改如下：**</font>
```bash
# /etc/wsl.conf
[interop]
enabled = true

# when {appendWindowsPath = true}, you needn't do any more
# when {appendWindowsPath = false}, you need to create soft link of powershelll.exe and cmd.exe for linux

ln -s /mn/c/Windows/System32/WindowsPowerShell/v1.0/powershell.exe /usr/local/bin/powershell.exe
ln -s /mn/c/Windows/System32/cmd.exe /usr/local/bin/cmd.exe
```

#### <font color="#3a3">2. **然后需要保证是在 `$USER/AppData/Local/Temp` 下创建 `wsl_clipboard` 文件有写入权限**</font>

> ==其中，options的umask和fmask值必须要满足 `wsl2` 下往win文件下写入内容。==

![WslConfAutomount](https://klusfq.cn/img/wsl_conf_automount.png)

#### <font color="#3a3">3. **将项目脚本放入环境变量中**</font>
> 关于原项目中的 `cat > [file]` 用法，有一些问题。在我的机器上，它获取 STDIN 失败，所以我进行了一些魔改。

```bash
#!/bin/sh
# ... start some other
  (-i|--input|-in)
    # tee <&0 | clip.exe
    # To remove the appended newline:
    TEMP="$(wslpath "$(cmd.exe /c "<nul set /p=%TEMP%" 2>/dev/null)")"
    cat > "$TEMP/wsl_clipboard"
    $pwsh -command "Get-Content ""\$env:TEMP\wsl_clipboard"" | Set-Clipboard"
```

--------------以上是原项目，以下是魔改后的项目--------------

```bash
#!/bin/bash
# ... start some other
  (-i|--input|-in)
    # tee <&0 | clip.exe
    # To remove the appended newline:
    TIN=""
    while read line ; do
        TIN="${TIN}${line}"$'\n'
    done
    TEMP="$(wslpath "$(cmd.exe /c "<nul set /p=%TEMP%" 2>/dev/null)")"
    echo "$TIN" > "$TEMP/wsl_clipboard"
    # cat > "$TEMP/wsl_clipboard"
    $pwsh -command "Get-Content ""\$env:TEMP\wsl_clipboard"" | Set-Clipboard"
```

#### <font color="#3a3">4. **最后，给这些脚本起一个别名。**</font>
```bash
# ~/.bashrc

alias pbcopy='xclip -o'
alias pbpaste='xclip -i'

```

#### <font color="#3a3">5. **使用case**</font>
```bash
# copy or ctrl+c by selected
echo "Hello World" | pbcopy

# paste or ctrl+v by input
pbpaste

```
