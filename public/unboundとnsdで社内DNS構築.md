---
title: unboundとNSDではじめる社内DNSサーバーの構築
tags:
  - Ubuntu
  - unbound
  - NSD
  - dns
private: false
organization_url_name: null
slide: false
ignorePublish: false
---

# 前置き(状況)

会社の事務所引越しに伴い個人PCや複合機、サーバーetc.を移転させた際に、社内ネットワークに接続されている端末同士で名前解決ができなくなった。根本的原因としては

- DNSサーバーがない
- 固定IPを割り振っていない

が大きく挙げられる。そのため、固定IPを全個人PCに振って、それをマスターブラウザーに反映させればおそらく解決する話であろうが、全PCに固定IPを割り振るように設定していくには台数が多すぎて現実的ではないため、今回はDNSサーバーを建てて名前解決ができるように設定していくこととした。

# 環境

- 個人のPCはWindows PCのみ(一人だけHyper-V上で遊ぶ用のUbuntuが動作)
- サーバーはWindows ServerとLinux(私が管理しないNASのため詳細不明)
- その他複合機やプリンター、テプラー等印刷機が多数

# unboundの作成

とりあえず、素人のためDNSサーバーを建てるにあたり、unboundのキャッシュデータに作成時段階でのIPアドレスと名前のリストを書き込んで、キャッシュDNSサーバーとして動作させる。