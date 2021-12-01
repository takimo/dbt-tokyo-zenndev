---
title: "dbtとは？"
---
# dbtとは
![](https://storage.googleapis.com/zenn-user-upload/52b75d3cdcdd-20211128.png)

dbt（正式名称はdata build tool）はデータアナリストやアナリティクスエンジニアが（ほぼ）SQLだけで、データを変換しデータウエアハウス、データマートを構築していくことができるツールです。

dbtはELTデータパイプラインのアプローチにおける「T」層の部分を担います。データの抽出やロードは出来ませんが、既にデータウェアハウスに読み込まれたデータを変換することに優れています。

ELTの概念や隣接する議論のモダンデータスタックに関しては以下の記事が参考になります
- [Modern Data Stack / モダンデータスタックというトレンドについて](https://satoshihirose.hateblo.jp/entry/2021/11/20/160548#ELT-ELT-ELT)
- [ELTとETLの違い BIへの影響と最適なアプローチ](https://www.talend.com/jp/resources/elt-vs-etl/)

# こんな事ありませんか？
**クエリで読み込まなければならない処理が多岐にわたり処理が重い**
👱🏻‍♂️「難しかったけどいろんなデータを加工して集計出来たよ！」
👱🏽‍♀️「BIのレポート開くのに1分ぐらいかかって使いづらいんですが。。」

**コードの見通しを良くするためにViewに切り出したがView同士の関係性の把握が難しく迷子になる**
👱🏻‍♂️「Dテーブルのこのカラム間違えていそうだから修正したいんだけど大丈夫かな？」
👱🏽‍♀️「このクエリはAのviewを参照していて、AのviewはBを参照していて、BのviewはCとDとEを参照していて、CはFとGを参照していて、Dは…」
👱🏻‍♂️「どこに影響あるかな…？」

# dbtの何が良いのか？
- SELECTのSQLを書くだけで良く、SQLの知識があればパイプラインを構築できる
- SQLで定義したモデルはrefと呼ばれるテーブル間の関係性を自動的に把握できるようになる仕組みで記述することで、DAG（有向非巡回グラフ）を考慮したデプロイを実行できるため、モデルを反映させるための細かなワークフローを組む必要がない
```sql
select
  orders.id,
  orders.status,
  sum(case when payments.payment_method = 'bank_transfer' then payments.amount else 0 end) as bank_transfer_amount,
  sum(case when payments.payment_method = 'credit_card' then payments.amount else 0 end) as credit_card_amount,
  sum(case when payments.payment_method = 'gift_card' then payments.amount else 0 end) as gift_card_amount,
  sum(amount) as total_amount

from {{ ref('base_orders') }} as orders
left join {{ ref('base_payments') }} as payments on payments.order_id = orders.id
```
![](https://docs.getdbt.com/img/docs/2ce8dce-Screen_Shot_2018-09-16_at_11.46.12_AM.png)
  - 直近のアップデートでモデルのデプロイとそのデータテストのステップを踏みながらデプロイができるbuild機能も登場

# dbtを使ったデータモデリングの流れ
![](https://storage.googleapis.com/zenn-user-upload/6496a4ecf051-20211128.png)

- dbt Cloud環境のセットアップ
  - dbtを使った開発環境とエディタを統合的に備えたdbt Cloudというサービスがあります
  - dbt自体は[CLI](https://docs.getdbt.com/docs/introduction#developing-locally-with-the-command-line-interface-cli)でも利用することが可能ですが、本ドキュメントはCloudを前提にドキュメント化しています
- モデルの実装
  - .sqlのファイルを作成し、必要なデータの流れを定義していきます
  - ここで記述するSQLはほぼ普段書いているSQLと大差ありません
- デプロイ
  - dbtの機能を使い、定義したモデル（.sql）を実際のテーブルとして作成するための処理です
  - dbt Cloud上では簡易的なCLIインターフェースが備わっており、dbt runとコマンドを打つだけでデプロイが完了します
- ジョブの設定
  - 上記のデプロイ処理のスケジューリング等の実行設定が可能です
  - 例えば朝6時に実装したモデルのデータ等を更新したい場合に使用できます

# dbtで他に何ができるのか

## テンプレートエンジンのJinjaを使った高度なクエリ処理

## テスト

## ドキュメンテーション

## 他にも。。
- パケッケージマネージメント
- データスナップショット
