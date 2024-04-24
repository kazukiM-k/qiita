---
title: upLaTeXの初期設定#1
tags:
  - upLaTeX
  - jsarticle
private: false
updated_at: '2024-04-23T08:03:19+09:00'
id: df6843c33e1b5a1a4b4f
organization_url_name: null
slide: false
ignorePublish: false
---
# upLaTeXの初期設定方法その1

LaTeXを始めるにあたり、様々な方が様々なページでインストール方法から設定方法まで記載しているが、大体どれを見ても自環境ではエラーが出て解決策を探すのに数時間～数日費やすのは珍しくない。
そこで、__あくまでも私の環境構築で__ の構築方法並びに発生したエラーについて(思い出せる範囲で)その内容と対処方法について複数回に分けて記載していく(続くとは言ってない)。

## 環境

- windows 10 Pro
- TeX Live 2021(以降であれば基本同じ)
- Visual Studio Code(以下、VScode)

## TeX Liveのインストール

i7-7700で4時間ぐらい、i7-12700で1.5時間ぐらい、i9-13900Kで30分ぐらいかかった。
ただただ待つだけではあるがインストール後Pathが通っているか確認した(upLaTeX、XeLaTeX、latexmk、mktexlsrぐらいは確認した)。インストール後の再起動はおまじない。

## VScodeのインストール

公式サイトからダウンロード、インストールした。日本語化拡張機能とLaTeX Workshopをとりあえずインストールしたが、なんだかんだLaTeXと銘打つ拡張機能を片っ端からインストールしたような記憶もある(VScode内のMarketplaceより)。

## latexmkrcファイルの作成

.texから.dviや.pdfへコンパイルする際にupLaTeX→upBiBLaTeX→upLaTeX→upLaTeX→upLaTeX→dvipdfmxのように特定の順番かつ一定回数TeXエンジンを走らせる必要があるのはわざわざ記す必要がない情報ではあるが、これを毎回順番に走らせるのはかなりめんどくさい。
そこで最初からレシピ(上記コンパイルの順番や最大繰り返し回数などを記載したもの)を用意してそのレシピに基づいてコンパイルするのが一般的であり、このレシピが%\USERPROFILE%直下に保存する.latexmkrc(latexmkrcファイル)である。
本記事ではlatexmkrcファイルの中身には触れないが、ドキュメントクラスとTeXエンジンの不一致(jsarticleをpLaTeXでコンパイルする等)をするといつまでもコンパイルできないだけでなくlatexmkrcの書き換えを伴う場合がある(そうならないようにTeXエンジンの指定も自動でするようにすれば問題ないだけではある)。

## その他の設定

.latexmkrcの場所をLaTeX Workshopに教えてあげるために設定を書き換える。

## エラー事象

### latexmkでコンパイルできない

→先述の通りドキュメントクラスとTeXエンジンの不一致が原因。TeXエンジンの指定をする前に自動で切り替えるように設定した([参考](https://qiita.com/kazukiM-k/items/015bc26467c1997505cc))。

### それでもコンパイルできない

→コマンドの綴りミス等はさておき、ファイルパスの文字数制限かも(perlの仕様)。

### 別端末のVScodeではコンパイルできない

→.latexmkrcの場所指定が正しくない。(ユーザー名が同じ(だと信じて設定したとしても)%USERPROFILE%等の環境変数を使って指定するのが無難)

## まとめ
その2があるかは未定
