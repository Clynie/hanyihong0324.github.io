---
layout: post-for-project
title:  "修士研究初号機（仮）"
date:   2018-10-16
excerpt: "2018年後半の試行研究。漫画のコマを分割した上、読む順番に並び替える、加えて漫画の読みやすさを数値化して評価するのは仮の目標になっている"
project: true
---

# コマ分割

## 手法

### 【4コマ向】画素値のピークから算出する手法

Source: [ゆゆ式を無限に楽しみたかった話](http://non117.hatenablog.com/entry/2014/12/20/175053)

横一行・縦一列で画素値を合計（つまりヒストグラム）、コマの境界である余白のほうはヒストグラムが0のはず、走査により余白の幅も検出可能

4コマならたぶんこれで解決だと思う。コマはちゃんと矩形に作られてる場合も調整せれば応用できる。

![](http://cdn-ak.f.st-hatena.com/images/fotolife/n/non_117/20141220/20141220173536.png)

### 直線検出の後コの字・Lの字検索でコマ探索

Source: [漫画コマ切り出しツール「ミズハノメ」ツールのご紹介](https://whomor.com/manga/?p=511)

直線抽出で抽出した線でコの字または、L字型になっている箇所を探します。そこで切り取り、コマとする

![](https://whomor.com/manga/wp-content/uploads/2015/10/5.png)

### 木構造によるコマ探索

Source: [【PDF】コマ割りされた画像における余白に着目したコマの自動分割手法](https://shingi.jst.go.jp/past_abst/abst/p/12/1263/akita03.pdf)

### 領域着色(?)によるコマ分割

Source: [nyaa式４コマ切り出しプログラムを作った](http://nyaa.hatenadiary.jp/entry/2016/12/18/000738)

領域の面積から余白を判断、余白じゃない部分をコマとして推定。（ラベリング（着色）のやり方は言及していない）

![](https://cdn-ak.f.st-hatena.com/images/fotolife/n/nyaaj/20161217/20161217222853.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/n/nyaaj/20161217/20161217223523.png)

### 前景背景判定(?)によるコマ分割

Source: [GT-Scan by FujiFilm](https://www.fujifilm.co.jp/rd/report/rd057/pack/pdf/ff_rd057_010.pdf)

二値化→前景背景判定→四角形検出→線検出→不要線除去→コマ境界線統合→コマ領域候補抽出→コマ確定

効果はよさそうけど、前景背景判定の部分はまだ謎。

## アイディア

コマ割り（漫画の作る段階）から逆に考える  
　→ソフトウェアで生成したコマ割りに何か特徴があるかも、逆に利用すれば…

## 製品

- GT-Scan by FujiFilm
- ミズハノメ by フーモア
- [GT-MangaAuthor?](http://www.chem-t.com/fax/images/tmp_file1_1341288222.pdf)
