---
layout: post
title:  "Windows上にLinuxを動かす"
date:   2018-11-02 13:00:40 +0900
excerpt: "WSL及びUbuntu/Debian上の開発環境配置"
---

# Windows Subsystem for Linux（以下WSL）

## WSLのインストール

WSLに関する紹介やインストールのやり方は[こちら](https://qiita.com/Brutus/items/f26af71d3cc6f50d1640)に説明してあります、一応Windowsを最新バージョンにアップデートしてからこの手順に従ってやればできると思います。

チュートリアルの方は"Ubuntu"というLinuxのディストリビューションを選んだが、"Debian"というやつもあります、Microsoft Storeで"Ubuntu"じゃなくて"Debian"で検索すれば出てくるはずです。UbuntuはDebianに基づいて開発されたものなんでこの２つのOSの使い方やトラブル解決法はほぼ共通しています。"Ubuntu"に比べて"Debian"のほうが動作が軽い傾向があるのでパソコンのスペックに自信ない方は"Debian"を入れたほうがいいかもしれません。

もし"Ubuntu"を入れるとしたら、検索結果の方に"Ubuntu","Ubuntu16.04","Ubuntu18.04"などがありますが、バージョンがついているやつはバージョンご固定されているのでおすすめしません、無印の方でインストールしてください。

## LinuxとWSLの基本操作

WSLで構築したLinux環境はGUI（グラフィカルユーザインタフェース）がついてないので基本はコマンド操作で使います。  
*（GUIは一応インストールできるのですが、WSL上ではろくに使えないので結局頑張ってやっても虚無感と残念感しか残らない）*  
GUIがないのですべての操作がコマンド上で実行しなければいけない、普段皆馴染んでいる「コピー」や「ペースト」、「フォルダに入る」、「ファイルの削除」などなど、全部コマンドで実行します。Googleで「linux 基本 コマンド」で検索したらいっぱい紹介文が出てくると思いますが、忙しいときは「Linux コマンド コピー」のように検索すればすぐ答えが出てくると思います。

WSLは自分のディレクトリがあって、LinuxのファイルシステムをWindows上で実装しているって感じです。Windowsのエクスプローラーでもアクセスできますが、エクスプローラーでファイル操作をやるのは非常に **おすすめしません！** なぜかというと、Linuxは自分なりの権限管理（このファイルはどのユーザーに所有してるかや、実行できるかどうかなどを記録・管理しているシステム）が徹底しているので、Windowsのエクスプローラーやテキストエディタからファイルを作成・移動・編集などを行った場合、権限が狂っちゃって認識してくれない可能性があります。WSLでWindows側のファイルを利用する場合は、ちゃんとcp命令使ってWSLのファイルシステムにコピーすることを推奨します。

例えばWindows側の C:\\Users\\cid\\test.py というファイルを使う場合、まずそのファイルをWSL側のホームディレクトリ（「~」と表示する）にコピーします：
```
cp /mnt/c/Users/cid/test.py ~/test.py
```
するとtest.pyはWSLのホームディレクトリにコピーされました。先の"/mnt/c/"はWindowsのCドライブのことで、同じくDドライブなら"/mnt/d/"って入力すればいいです。

## Linuxのパッケージ更新とPython環境の配置

```
Linuxパッケージ更新の確認（sudo権限がいるのでパスワードは要求されます）：
sudo apt update

Linuxパッケージの更新（同じくsudo）：
sudo apt upgrade
```

これでWSLにインストールされているソフトウェア（Linuxシステム自体含む）は最新になります。

Python環境について、最新のUbuntuやDebianはデフォルトでPython2とPython3が搭載されていると思いますが、少なくともPython2は絶対入っている（使うつもり全然ないけど）、万が一Python3が入っていない場合：

```
Python3のインストール：
sudo apt install python3
これでPython3はインストール完了、"python3 test.py"などの命令が実行できるようになりました。念の為、pip(python3-pip)と基本ライブラリ(python3-dev)も入れておく：
sudo apt install python3-pip python3-dev
```

## mecabとneologd

こちらのサイトで全部紹介してある：[ubuntu 18.04 に mecab をインストール - Qiita](https://qiita.com/ekzemplaro/items/c98c7f6698f130b55d53)
