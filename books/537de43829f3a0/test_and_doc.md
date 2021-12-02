---
title: "テストとドキュメント"
---
https://docs.getdbt.com/tutorial/test-and-document-your-project

# モデルへテストとドキュメントの追加をしよう
プロジェクトにYAMLファイルで定義を追加することでモデルが期待通りに動作しているか検証するテストを行ったり、モデルのドキュメンテーションを残すことが出来るようになります。

1. `models/schama.yml`というYAMLファイルを作成します。
2. そこに以下のコンテンツを追加してください。

```sql:models/schema.yml
version: 2

models:
  - name: customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null

  - name: stg_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null

  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values: ['placed', 'shipped', 'completed', 'return_pending', 'returned']
      - name: customer_id
        tests:
          - not_null
          - relationships:
              to: ref('stg_customers')
              field: customer_id
```
3. 下部の入力欄に`dbt test`と入力しEnterを押すとテストが実行されます

![](https://storage.googleapis.com/zenn-user-upload/0b989483a7a5-20211201.png)

# どのようなスキーマテストが出来るのか
標準では以下の4つのスキーマテストが利用できます。
このスキーマテストは自分で拡張することも可能で、[dbt-utils package](https://github.com/dbt-labs/dbt-utils/tree/0.2.4/#schema-tests)といった外部のパッケージ化されたものを使用することも可能です。
詳しくは「テストのドキュメント（後日公開）」を参照してください

```yaml:schema.yml
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

- `unique`: `order_id`のカラムがユニークな値になっているか
- `not_null`: `order_id`のカラムが`null`を含んでいないか
- `accepted_values`: `status`のカラムが決められた4つの値（`placed`,`shipped`,`completed`,`returned`）になっているか
- `relationships`: `orders`にある`customer_id`が`customers`のモデルから生成されるテーブルの`id`というカラムの値と結合できるか

# モデルのドキュメントを作る
モデルのドキュメントを定義することでチームで情報を共有することができます。

1. 先程テストで作成したYAMLファイルを以下のように更新します
```yaml:models/schema.yml
version: 2

models:
  - name: customers
    description: One record per customer
    columns:
      - name: customer_id
        description: Primary key
        tests:
          - unique
          - not_null
      - name: first_order_date
        description: NULL when a customer has not yet placed an order.

  - name: stg_customers
    description: This model cleans up customer data
    columns:
      - name: customer_id
        description: Primary key
        tests:
          - unique
          - not_null

  - name: stg_orders
    description: This model cleans up order data
    columns:
      - name: order_id
        description: Primary key
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values: ['placed', 'shipped', 'completed', 'return_pending', 'returned']
```
2. 下部の入力欄に`dbt docs generate`と入力しEnterを押すとドキュメントの生成タスクが実行され、完了すると画面左上に「view docs」というリンクが有効になり、そこからドキュメントが見れるようになります

![](https://storage.googleapis.com/zenn-user-upload/9c94b7c4003e-20211201.png)
