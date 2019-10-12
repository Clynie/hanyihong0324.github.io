---
layout: post
title:  "你们写report居然用Word？"
date:   2017-10-23 16:57:00 +0900
excerpt: "TexLive Install&Configure on Ubuntu + Atom LaTeX Support"
---

# 你们写report居然用Word？

****

## Why LaTeX

[LaTeX 相对于 Word 有什么优势？](https://www.zhihu.com/question/20542113)

## Install&Configure on Ubuntu

### Install

```
sudo apt update
sudo apt install texlive-lang-cjk
```

### Configure Font Setting

```
sudo kanji-config-updmap-sys auto
```

## Atom Packages

### Syntax Highlight

- Install package "language-latex"

### Document Tree View

- Install package "latex-tree"

### Auto Compile

1. Install package "latex"
2. Go to settings of "latex"
3. Change "Engine" to "uplatex"(for Japanese UTF-8 support)
4. Check "use DiCy", or you have to install latexmk with apt or whatever.
5. Change "Opener" to "evince" for Ubuntu/Debian, "x-reader" for Linux Mint, "okular" for KDE distros, "pdf-view" for using pdf-view Atom package.
6. Now you can open a .tex file with Atom and press Ctrl+Alt+B to build & open the PDF file.
