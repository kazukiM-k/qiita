---
title: mktexlsrが探しに行くパスを追加する方法
tags:
  - LaTeX
  - upLaTeX
  - mktexlsr
  - kpathsea
  - kpsewhich
private: false
updated_at: '2024-03-12T10:30:58+09:00'
id: 4bfd6be1ef8b6651a62e
organization_url_name: null
slide: false
ignorePublish: false
---
# 状況

%USERPROFILE%直下にtexmfフォルダーを作成、中にbibtex用のファイル等を保存してmktexlsrを実行しても%USERPOFILE%\texmfのライブラリを更新しない。

### 環境

- Windows 10
- TeX Live 2023

## 理想の動作

ネット上(というかTeX Wiki)では%USERPROFILE%\texmfは環境変数TEXMFHOMEとして定義されていて、mktexlsr等各種コマンドを実行した際にはこれらの環境変数を使用してスタイルファイルの更新等を実行する(らしい)。
環境変数の設定については特に触れられていないので、おそらくインストール時に設定してくれると思われる(私の環境ではされてなかったが。)。

## 結論

mktexlsrが実行されるフォルダーを追加する。
追加はtexmf.cnfをいじる

## やり方

C:\texlive\texmf-local\web2c\texmf.cnfに以下のように記載する(なければ新規作成する)。
```
TEXMF = {$TEXMFAUXTREES$TEXMFCONFIG,$TEXMFVAR,$TEXMFHOME,$TEXMFLOCAL,$TEXMFSYSCONFIG,$TEXMFSYSVAR,$TEXMFDIST}
TEXMFDBS = {$TEXMFLOCAL,$TEXMFSYSCONFIG,$TEXMFSYSVAR,$TEXMFDIST,$TEXMF}
TEXMFMAIN = {$TEXMFDIST,$TEXMF}
SYSTEXMF = {$TEXMFSYSVAR,$TEXMFLOCAL,$TEXMFDIST,$TEXMF}
TEXMF_RESTRICTED_SCRIPTS = {$TEXMFLOCAL,$TEXMFDIST}/scripts/{$progname,$engine,}//
TEXMFCNF = {$SELFAUTOLOC,$SELFAUTOLOC/share/texmf-local/web2c,$SELFAUTOLOC/share/texmf-dist/web2c,$SELFAUTOLOC/share/texmf/web2c,$SELFAUTOLOC/texmf-local/web2c,$SELFAUTOLOC/texmf-dist/web2c,$SELFAUTOLOC/texmf/web2c,$SELFAUTODIR,$SELFAUTODIR/share/texmf-local/web2c,$SELFAUTODIR/share/texmf-dist/web2c,$SELFAUTODIR/share/texmf/web2c,$SELFAUTODIR/texmf-local/web2c,$SELFAUTODIR/texmf-dist/web2c,$SELFAUTODIR/texmf/web2c,$SELFAUTOGRANDPARENT/texmf-local/web2c,$SELFAUTOPARENT,$SELFAUTOPARENT/share/texmf-local/web2c,$SELFAUTOPARENT/share/texmf-dist/web2c,$SELFAUTOPARENT/share/texmf/web2c,$SELFAUTOPARENT/texmf-local/web2c,$SELFAUTOPARENT/texmf-dist/web2c,$SELFAUTOPARENT/texmf/web2c,$TEXMF/web2c}
INDEXDICTIONARY = $TEXMF/makeindex/dict//
BIBINPUTS=$TEXMF/{bibtex,pbibtex,upbibtex}/bib//
```

最後にmktexlsrを実行する。
2段階で実行され、初期設定(?)の2023直下のライブラリ一式が更新された後、上記で追加した各パスのライブラリが更新される。

## 参考文献
- [TeX Wiki](https://texwiki.texjp.org/?TeX%20%E3%81%AE%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E6%A7%8B%E6%88%90)
- [LaTeX::設定 基本事項](http://www.yamamo10.jp/yamamoto/comp/latex/settings/basic/index.php)
- [多重TEXMFツリー](https://tutimura.ath.cx/ptetex/?%C2%BF%BD%C5TEXMF%A5%C4%A5%EA%A1%BC)
