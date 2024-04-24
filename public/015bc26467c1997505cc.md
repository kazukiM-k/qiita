---
title: latexmkで文書クラスとオプションに応じてLaTeX(+dvipdfmx)を切り替える
tags:
  - Perl
  - LaTeX
  - Latexmk
  - .latexmkrc
private: false
updated_at: '2024-02-26T08:08:02+09:00'
id: 015bc26467c1997505cc
organization_url_name: null
slide: false
ignorePublish: false
---
LaTeXは文書クラスとオプションに応じてTeXエンジンを切り替える必要があるのは周知の事実であるかと思う。
が、膨大なネットの海を数時間彷徨っても一つも文書クラスとオプションに応じてTeXエンジンを切り替える方法について出てこない(探し方が悪いのかもしれないが)ので、Perl初心者なりに作った。

## 前提条件

1. Windows 10
1. VSCode+LaTeX Workshop(エディタは今回関係ない)
1. .latexmkrc(%USERPROFILE%に保存、今回いじるのはこれ)
1. TeX Live 2022(latexmkが動けば何でもOK)

## やりたいこと

1. 文書クラスとオプションに応じてTeXエンジンを自動で切り替えたい
1. 文書クラスとオプションの組み合わせが正しくない時(コンパイルできない組み合わせとコンパイルできるが推奨されない組み合わせ)にはLaTeXを走らせる前に処理を止めてエラーメッセージを返したい

### 代わりに諦めること
1. latexmkやlatexmk "ファイル名"では実行できなくすること
1. 全体を通じて日本語を含むパスにしないこと(ファイル名を含む、Perlの仕様だったはず)
1. 1行目に文書クラスとオプションを1行で記載すること

## .latexmkrcの記載

説明が下手なのでとりあえずソースコード

```
#!/usr/bin/env perl
use utf8;
my $texfile;
$texfile = $ARGV[0];
#print "$texfile\n";
open( FILE, "$texfile" ) or die "Failed to open a file.\n";
my $line = <FILE>;
#print "$line\n";
close(FILE);

my $class;
my $option;

#文書クラスの抽出
if ( $line =~ /{/ ) {
    $class = $';
    #print "$class\n";
}
if ( $class =~ /}/ ) {
    $class = $`;
    #print "$class\n";
}
#print "$class\n";

#オプションの抽出
if ( $line =~ /{/ ) {
    $option = $`;
    #print "$option\n";
}
if ( $option =~ /ss/ ) {
    $option = $';
    #print "$option\n";
}
$option = substr("$option",1,-1);
#print "$option\n";
#ここまでで[]を外したオプションの文字列の抽出

if($class eq "jsarticle" || $class eq "jsbook"  || $class eq "jsreport" || $class eq "jarticle" || $class eq "jbook" || $class eq "jreport" || $class eq "tarticle" || $class eq "tbook" || $class eq "treport"  || $class eq "utarticle" || $class eq "utbook" || $class eq "utreport" || $class eq "ujarticle" || $class eq "ubook" || $class eq "ureport"){
    if($option =~ /uplatex/){#文書クラスによるコンパイラの分岐後にオプションの検索をかける。(おそらく&&と||の優先度が関係してる)
        if($class eq "tarticle" || $class eq "tbook" || $class eq "treport"){
            #uplatexでコンパイルできないやつをはじく
            my $error = sprintf("Failed by inconsistent options and document classes. \nCan't use <%s> in upLaTeX.\n",$class);#エラー表示文章に文書クラスの挿入
            die ("$error");
        }

        #print "upLaTeX\n";
        $latex = 'uplatex -synctex=1 -halt-on-error -file-line-error %O %S';
        $dvipdf = 'dvipdfmx %O -o %D %S';
        $pdf_mode = 3;

    }elsif($option =~ /pdflatex/){
        my $error = sprintf("Failed by inconsistent options and document classes. \nCan't use <%s> in pdfLaTeX.\n",$class);#エラー表示文章に文書クラスの挿入
        die ("$error");
    }else{
        #print "pLaTeX\n";
        $latex = 'platex -synctex=1 -halt-on-error -file-line-error %O %S';
        $dvipdf = 'dvipdfmx %O -o %D %S';
        $pdf_mode = 3;

    }

}elsif($class eq "article" || $class eq "book" || $class eq "report"){
    if($option =~ /uplatex/){#文書クラスによるコンパイラの分岐後にオプションの検索をかける。(おそらく&&と||の優先度が関係してる)
    #print "upLaTeX\n";
    $latex = 'uplatex -synctex=1 -halt-on-error -file-line-error %O %S';
    $dvipdf = 'dvipdfmx %O -o %D %S';
    $pdf_mode = 3;

    }elsif($option =~/pdflatex/){
        #print "pdfLaTeX\n";
        $latex = 'pdflatex -synctex=1 -halt-on-error -file-line-error %O %S';

    }else{
    #print "LaTeX\n";
    $latex = 'latex -synctex=1 -halt-on-error -file-line-error %O %S';
    $dvipdf = 'dvipdfmx %O -o %D %S';
    $pdf_mode = 3;

    }
}elsif($class eq "ltjsarticle" || $class eq "ltjsbook" || $class eq "ltjsreport" || $class eq "ltjspf" || $class eq "ltjskiyou"){
    if($option =~ /uplatex/){#文書クラスによるコンパイラの分岐後にオプションの検索をかける。(おそらく&&と||の優先度が関係してる)
    my $error = sprintf("Warning stop by use document class <%s> with upLaTeX.\n",$class);
    die ("$error");
    }
    #print "LuaLaTeX\n";
    $latex = 'lualatex -synctex=1 -halt-on-error -file-line-error %O %S';

}elsif($class eq "beamer"){
    if($option =~ /uplatex/){#文書クラスによるコンパイラの分岐後にオプションの検索をかける。(おそらく&&と||の優先度が関係してる)
    my $error = sprintf("Warning stop by use document class <%s> with uplatex.\n",$class);
    die ("$error");
    }
    #print "XeLaTeX\n";
    $latex = 'xelatex -synctex=1 -halt-on-error -file-line-error %O %S';

}elsif($class eq "bxjsarticle" || $class eq "bxjsbook" || $class eq "bxjsreport" || $class eq "bxjsslide"){
    if($option =~ /uplatex/){
        #print "upLaTeX\n";
        $latex = 'uplatex -synctex=1 -halt-on-error -file-line-error %O %S';
        $dvipdf = 'dvipdfmx %O -o %D %S';
        $pdf_mode = 3;

    }elsif($option =~/platex/){
        #print "pLaTeX\n";
        $latex = 'platex -synctex=1 -halt-on-error -file-line-error %O %S';
        $dvipdf = 'dvipdfmx %O -o %D %S';
        $pdf_mode = 3;

    }elsif($option =~ /pdflatex/){
        #print "pdfLaTeX\n";
        $latex = 'pdflatex -synctex=1 -halt-on-error -file-line-error %O %S';

    }elsif($option =~/xelatex/){
        #print "XeLaTeX\n";
        $latex = 'xelatex -synctex=1 -halt-on-error -file-line-error %O %S';

    }elsif($option =~ /lualatex/){
        #print "LuaLaTeX\n";
        $latex = 'lualatex -synctex=1 -halt-on-error -file-line-error %O %S';

    }else{
        die "Not found LaTeX engine.\n";
        #基本的にそれ以外使わないはずなのでとりあえずエラーとして返す

    }
}else{
    die "Failed by find a compiler combination for the document class and options entered.\n";
    #文書クラスとオプションの組み合わせが不適切か単なる綴りミス
    #latexが走る前に止めることでエラー停止までの時間短縮
}

$max_repeat = 5;

# BibTeX
$bibtex = 'upbibtex %O %S';
$biber = 'biber --bblencoding=utf8 -u -U --output_safechars %O %S';

# index
$makeindex = 'upmendex -s dot -r -c';

# preview
$pvc_view_file_via_temporary = 0;
if ($^O eq 'linux') {
    $dvi_previewer = "xdg-open %S";
    $pdf_previewer = "xdg-open %S";
} elsif ($^O eq 'darwin') {
    $dvi_previewer = "open %S";
    $pdf_previewer = "open %S";
} else {
    $dvi_previewer = "start %S";
    $pdf_previewer = "start %S";
}

# clean up
$clean_full_ext = "%R.synctex.gz";

#output
$out_dir = "./out/";
```

やっていることはいたってシンプルで、

1. latexmk "ファイル名".texからファイルのフルパスを取得
1. 1行目を取得(文書クラスとオプションを取得)
1. 必要な部分(文書クラスとオプション)をそれぞれ取得
1. 含まれる文字列を総当たりで検索してTeXエンジンを指定、不適切な場合と総当たりから漏れた場合をエラーとして返す
1. それ以降は他の方もやっているようにビューワーや最大繰り返しコンパイル回数の指定、クリーンアップや出力先の指定

文書クラスとオプションの組み合わせで総当たりしているため、新しい文書クラスができた場合や自作の文書クラスを使用する場合は適切な場所に追加していく必要がある。

## その他
VSCodeから動かすとlatexmkrc内のエラー(オプションと文書クラスの組み合わせが不適切な場合等)で強制終了した場合、エラーメッセージが表示されずLaTeXエラーとしてひとまとめにされて返ってくるのが玉に瑕。
