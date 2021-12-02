---
title: "dbtのモデルとは"
---
:::message
新しくdbtを利用しようとしている方は「[チュートリアル：モデル開発の基本](https://zenn.dev/dbt_tokyo/books/537de43829f3a0/viewer/tutorial)」を参考に実際に作ってみることをおすすめします。
:::

# dbtのモデルとは？
dbtで呼ばれるモデルとはselect文のことです。モデルは.sqlファイルで定義され、通常ではmodelsディレクトリに格納されます。

- 各.sqlファイルは1つのモデルとして1つのselect文（with句等は除く）で構成されます
- ファイルの名前がモデル名として使用されます
- モデルはmodelsディレクトリ内のサブディレクトリに入れ子にすることができ、ディレクトリ構造がそのままモデル名や実際のテーブル名に影響することはありません（ただし、dbt_project.yml等でスキーマやエイリアスを変更している場合はその限りではありません）
- `dbt run`コマンドを実行するとdbtはこのモデルを`CREATE VIEW as`また`CREATE TABLE as`のステートメントでラップしてDWHに構築します

例えば次のcustomersモデルを考えてみましょう。

```models/customers.sql
with customer_orders as (
    select
        customer_id,
        min(order_date) as first_order_date,
        max(order_date) as most_recent_order_date,
        count(order_id) as number_of_orders

    from jaffle_shop.orders

    group by 1
)

select
    customers.customer_id,
    customers.first_name,
    customers.last_name,
    customer_orders.first_order_date,
    customer_orders.most_recent_order_date,
    coalesce(customer_orders.number_of_orders, 0) as number_of_orders

from jaffle_shop.customers

left join customer_orders using (customer_id)
```

上記のモデルを`dbt run`すると`customers`というviewテーブルが指定されたスキーマ（データセット、以下の例だと`dbt_alice`）に作成されます。

```
create view dbt_alice.customers as (
    with customer_orders as (
        select
            customer_id,
            min(order_date) as first_order_date,
            max(order_date) as most_recent_order_date,
            count(order_id) as number_of_orders

        from jaffle_shop.orders

        group by 1
    )

    select
        customers.customer_id,
        customers.first_name,
        customers.last_name,
        customer_orders.first_order_date,
        customer_orders.most_recent_order_date,
        coalesce(customer_orders.number_of_orders, 0) as number_of_orders

    from jaffle_shop.customers

    left join customer_orders using (customer_id)
)
```

# モデルの設定
モデルは`dbt_project.yml`や各モデルの`configブロック`で設定することが出来ます。
どのような設定が出来るのか例として以下のようなものがあります。

- モデルが使用する`Materialization`を変更する（マテリアアライゼーションはdbtがウェアハウスにモデルを作成する際に使用するSQLを決定するものになります）
  - ※[BigQueryのマテリアライズドView](https://cloud.google.com/blog/ja/products/data-analytics/bigquery-materialized-views-now-ga)のことではなく、dbt内での表現です
- モデルを別のスキーマに構築する
- モデルにタグを適用する

次にモデルの構成例を紹介します。
```yaml:dbt_project.yml
name: jaffle_shop
config-version: 2
...

models:
  jaffle_shop: # これは `name:`` の設定で指定した値と同じものになる必要があります
    +materialized: view # これは現在のプロジェクト全てのモデルに適用されます
    marts:
      +materialized: table # これは`marts/`以下の全てのモデルに適用されます
      marketing:
        +schema: marketing # これは`marts/marketing/`以下の全てのモデルに適用されます

```

```sql:models/customers.sql
{{ config(
    materialized="view",
    schema="marketing"
) }}

with customer_orders as ...
```

重要なのは、設定は階層的に適用されることです。サブディレクトリに適用された設定は、上位に設定された条件よりも優先されます。

# モデル間の依存関係を定義する
クエリの中でテーブル名の代わりにref関数を使うことで、モデル間の依存関係を構築することが出来ます。refの引数には他のモデルの名前を使用します。

dbtはref関数を以下のように使用します

- DAG（dependent acyclic graph）を作成して、モデルを実行する順番を決定します
![](https://storage.googleapis.com/zenn-user-upload/ee10d4f50389-20211203.png)
- 別々の環境を管理する
  - dbtはref関数で指定されたモデルを、テーブル（またはビュー）のデータベース名に置き換えます。dbt_aliceというターゲットスキーマ（データセット）でdbtを実行している場合、同じスキーマ（データセット）のテーブルから選択します

**モデル自体の定義**
```sql:models/customers.sql
with customers as (

    select * from {{ ref('stg_customers') }}

),

orders as (

    select * from {{ ref('stg_orders') }}

),

...

```
**ターゲットが「dev」で、そこに紐づくターゲットスキーマが「dbt_alice」の場合**
```sql
create view dbt_alice.customers as (
  with customers as (

      select * from dbt_alice.stg_customers

  ),

  orders as (

      select * from dbt_alice.stg_orders

  ),

  ...
)

...


```

**ターゲットが「prodction」で、そこに紐づくターゲットスキーマが「dbt_alice」の場合**
```sql
create view analytics.customers as (
  with customers as (

      select * from analytics.stg_customers

  ),

  orders as (

      select * from analytics.stg_orders

  ),

  ...
)

...
```