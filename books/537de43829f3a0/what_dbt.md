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

:::message
この本はdbtの日本での普及を目的にdbt本家の[チュートリアル](https://docs.getdbt.com/tutorial/setting-up)や[ドキュメント](https://docs.getdbt.com/docs/introduction)を元に翻訳＆分かりやすくするためにコンテンツの追加＆再構成させたものです
:::

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
dbtではSQL内に軽量なテンプレート言語であるJinjaを使うことが出来ます。SQLにJinjaを使うことで制御構文（ifやforなど）を使用することが出来る用になります。またマクロ機能で他のdbtユーザーが作った処理を利用したりすることも可能です。

```
select
order_id,
{% for payment_method in ["bank_transfer", "credit_card", "gift_card"] %}
sum(case when payment_method = '{{payment_method}}' then amount end) as {{payment_method}}_amount,
{% endfor %}
sum(amount) as total_amount
from {{ ref('raw_payments') }}
group by 1
```

「Jinjaを使った高度なモデリング処理（後ほど公開）」で詳しくご紹介します。

## テスト
モデルによって生成された結果にアサーションを行うことで、各モデルのSQLの整合性を向上させる方法を提供します。モデル内の指定された列にのみ、以下のものが含まれているかどうかをテストをYAMLで定義するだけで実行することができます。

- `not null`：nullが含まれていないか
- `unique`：ユニークな値になっているか
- `relashonship`：別のモデルで対応する値を持っているか（Ordersモデルのcustomer_idはCustomersモデルのidとして存在しているか）
- `accepted_values`：指定したリストの中にある値か

```
version: 2

models:
  - name: orders
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values: ['placed', 'shipped', 'completed', 'returned']
      - name: customer_id
        tests:
          - relationships:
              to: ref('customers')
              field: id
```

またテストは、組織に特有のビジネスロジックに合わせて簡単に拡張することができます。セレクトクエリの形式でモデルに対して行うことができるアサーションは、すべてテストに変えることができます。

詳しくは「[テストとドキュメント]()」で詳しくご紹介します。

## ドキュメンテーション
dbtではdbtモデルのドキュメントを書き、バージョン管理し、共有する仕組みを提供しています。それぞれのモデルやフィールドに対して、説明文（テキスト、またはマークダウン）を書くことができます。

これらの説明は、追加の暗黙的な情報（例えば、モデルのリネージ、フィールドのデータタイプや適用されたテストなど）とともに、ウェブサイトとして作成され、チーム内で共有することができます。これにより、dbtモデルを使用する誰もが簡単に参照できるドキュメントをを提供することができます。

詳しくは「[テストとドキュメント]()」で詳しくご紹介します。

## 他にも。。
- パケッケージマネージメント
- データスナップショット

などありますが、より詳しいドキュメントは本家の[ドキュメント](https://docs.getdbt.com/docs/building-a-dbt-project/projects)を参考に頂くか、今後拡張していく予定です。