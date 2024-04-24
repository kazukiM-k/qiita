---
title: jsarticleクラスの表紙書式の変更方法
tags:
  - upLaTeX
  - jsarticle
private: false
updated_at: '2024-02-27T07:53:56+09:00'
id: 76205d1e99cb15675dca
organization_url_name: null
slide: false
ignorePublish: false
---
## 結論

maketitleをdefで再定義する。

## デフォルトの書式

まずはデフォルトの書式を確認する
```
\documentclass[12pt,a4j,uplatex]{jsarticle}
%upLaTeX+jsarticleを指定
%フォントサイズは12pt、用紙サイズはA4
%用紙サイズにjを付けると日本のサイズになるらしいけどISOとJISのサイズは確か一緒
%(upLaTeX的には違うものだったような気もするが要確認。)

%デフォルトを確認したいのでプリアンブルでは表示させたい内容だけを記載

\title{up\LaTeX +jsarticleの表紙書式}
\author{kazuki}
\date{2024/02/25}
%\todayを使えばコンパイルの日時になるが、YYYY年MM月DD日とかにしようと思うとプリアンブルで定義する

\begin{document}
\maketitle
%表紙生成

up\LaTeX の場合dviで出てくるのでpdfに変換するためにdvipdfmx等を使う必要がある。
%表紙だけでは味気ないので適当な文章を追加。

\end{document}
```

コンパイル結果は以下の通りである。![default.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3722880/d246de8d-86c2-ee02-9ef5-c65ee9ee9479.jpeg)

デフォルト状態でもタイトル、筆者、日付が表示された後に本文が表示される。
論文の場合タイトルページに主要項目と合わせて概要を同じページに書くことが基本であるためこれでも十分である。しかし、概要は次ページに書く場合はこの出力では問題がある。

## デフォルトを弄らない程度の変更

上記状況(表紙は表紙として独立した体裁)の場合、以下のような要件を満たす必要がある~~ということにする~~。

1.  表紙にはページ番号を表示しない
1.  タイトル、筆者、日付の順番で用紙中央に配置すること
1.  日付はYYYY年MM月DD日形式で作成日を挿入すること
1.  概要等表紙以外の内容は次ページ以降に挿入すること

という場合、
```
\maketiltle
\thispagestyle{empty}
\newpage
```
と記載してページ番号を消してページを切り離せば上記要求を満たすことはできる。
しかし例えば画像(校章等の画像)や筆者、査読者等を表にして記載すること(~~罫線大好きな人が多いのは別の問題~~)、などという意味不明な(~~過去に固執する~~)人の言い分を聞かなければいけない場合には上記のように数行書き足すだけでは対応しきれない。

## デフォルトを弄る程の変更

さて本題である。デフォルト体裁では物足りない場合には
```
\makeatletter
```
と
```
\makeatother
```
の間で\maketitleの書式を再定義する必要がある。
(\makeatletterと\makeatotherについては[こちらの記事](https://qiita.com/alphalia/items/22f63b42483b541f66f4)から)

```
\documentclass[12pt,a4j,uplatex]{jsarticle}

\newcommand{\reviewer}[1]{#1}
%査読者の追加
%[1]{#1}でこの行より下の任意個所で引数を設定し代入可能

\makeatletter
%この中でmaketitleを再定義することで書式(体裁)を変更

\def\@maketitle{

%変更後の書式を通常のTeXの書式で記載
%makeatletter以前でusepackageを使用していればそれらのパッケージも使用可能
%画像、表、等の挿入や右寄せ、左寄せ、中央配置等ほぼ全てが使用可能

%以下は例

\begin{center}
{\Large \@title}
\end{center}
%タイトルを中央に配置

\begin{flushright}
\@date
\end{flushright}
%日付を右寄せで配置

\begin{center}
\@author \\
\@reviewer
\end{center}
%筆者と査読者を中央に配置

\thispagestyle{empty}
%ページ番号削除

\newpage
%改ページ

}%defの閉じ括弧

\makeatother

%以下は変更前と同様
\title{up\LaTeX +jsarticleの表紙書式}
\author{kazuki}
\reviewer{名無しの権兵衛}%3行目で定義したコマンドに引数を設定
\date{\today}

\begin{document}
\maketitle
%表紙生成

up\LaTeX の場合dviで出てくるのでpdfに変換するためにdvipdfmx等を使う必要がある。
%表紙だけでは味気ないので適当な文章を追加。

\end{document}
```

出力結果は以下のとおりである。
![change-0.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3722880/14a6cf0b-36dc-d9b8-689b-af7d9b0c251a.jpeg)
![change-1.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3722880/1d7d01ce-97e5-33ec-02e5-e686f6cb4c13.jpeg)


2ページ目でページ番号が"1"ではなく"2"になっているが、これは1ページ目をカウントしているためである。
これの改善方法としては目次の次のページで、\setcounterでページ番号のカウンターを-1をするか、目次のページ番号をアラビア数字ではなくローマ数字に変更して本文のページをアラビア文字に変更する。
一般的な書籍でも目次と本文ではページの数字をアラビア数字とローマ数字等使い分けており、それに倣う形である。LaTeXの場合ページ数カウンターは数字の書式を変更するたびにリセットされるため、切り替え個所毎に1から始まる。

## まとめ

表紙書式の変更はmaketitleをdefで再定義する。ページ番号の整理等表紙だけで完結しない体裁の設定については別途設定する必要がある(目次の設定なども書くかもしれないが現段階では未定)。
