---
layout: page
title: 研究概要
permalink: /research/
---


ここでは主に現在取り組んでいる研究について記述します．

#### 1. 無線システムにおける認証連携ネットワークの自動構築
災害時などの緊急時に，(安全な認証方式<sub>[1](#1)</sub>
に基づく)無線ネットワークを即座に構築することができれば，情報通信の手段を素早く確保することができます．
しかし，無線システムにおける認証連携ネットワーク<sub>[2](#2)</sub>を構築するには，アクセスポイント(AP)や認証サーバの設定を行う必要があり，また，APにつなぐLANケーブルや電源ケーブルを配線するのに，時間や労力がかかります．私はこれらの問題解決のため，耐災害・耐障害性を有する無線LANの認証プロトコルや無線メッシュネットワーク技術についての研究開発を行っております．
![Imgur](http://i.imgur.com/0G7Cxob.png)

<small><a name="1">1.</a> 安全な認証方式とは，利用者の個人認証，無線区間の暗号化，偽アクセスポイント対策ができるなどの機能を備えた認証方式を指します．業界標準としてIEEE 802.1Xという方式があります．<br>
<a name="2">2.</a> 会社や組織間で無線LANインフラの相互利用ができる(所属機関IDで他機関の無線LANが使える)で仕組みです．学術系無線LANローミングシステムとして[eduroam](http://eduroam.jp)(エデュローム)があります．
</small>

------------
<br>

#### 2．無線LAN認証システムの負荷調査と設計フレームワークの開発
国際無線LANローミングシステムである[eduroam](http://eduroam.jp)では，利用者が所属機関のアカウントを利用し，世界各地のeduroam参加機関で無線LANを利用できます．eduroamは公衆無線LANサービスとも連携し，駅や空港，市街地や会議場などでも利用できるケースが増え，そのサービスはキャンパス内にとどまらず，拡大しています．また，災害時などにおいてもeduroamのインフラを緊急時の通信手段にできるようにする研究・開発が行われており，その利用場面の多様化・端末過密環境化が進んでいます．しかしながら，eduroamのような大規模無線LANローミングシステムにおいて，そのサービス品質の低下が問題となっています．そのような状況下で，APにかかる負荷や認証サーバにかかる負荷を定量的に測定し，その原因を検証する研究を行っています．

<blockquote class="twitter-tweet" lang="ja"><p>EDUROAM WHY ARE YOU TRYING TO RUIN MY LIFE</p>&mdash; John (@johnphaam) <a href="https://twitter.com/johnphaam/status/507531547763671041">2014, 9月 4</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet" lang="ja"><p>imagine a world where eduroam actually worked consistently</p>&mdash; harry ʕ •ᴥ•ʔ (@mamoswines) <a href="https://twitter.com/mamoswines/status/523065851969568768">2014, 10月 17</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
<blockquote class="twitter-tweet" lang="ja"><p>＿人人人人人人人人＿&#10;＞　eduroamに死を　＜&#10;￣Y^Y^Y^Y^Y^Y^Y￣</p>&mdash; のぞ (@_nzp) <a href="https://twitter.com/_nzp/status/471099713979052032">2014, 5月 27</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

#### 2.1 参考文献
1. [Load Performance Analysis on the RADIUS Network](https://www.terena.org/activities/tf-mobility/meetings/33/TF-MNM-33rd_niizuma.pdf)
