---
title: "Jinjaを使った高度なモデリング"
---
# Jinjaを使わないSQLの場合
1つの注文が多くの支払いを持つことができるデータモデルを考えてみましょう。各支払いには、bank_transfer、credit_card、gift_cardなどのpayment_methodがあり、各注文には複数のpayment_methodが存在することになります。

```sql:models/order_payment_method_amounts.sql
select
order_id,
sum(case when payment_method = 'bank_transfer' then amount end) as bank_transfer_amount,
sum(case when payment_method = 'credit_card' then amount end) as credit_card_amount,
sum(case when payment_method = 'gift_card' then amount end) as gift_card_amount,
sum(amount) as total_amount
from {{ ref('raw_payments') }}
group by 1
```

各支払方法の金額に対応するSQLは繰り返し使用されており、様々な理由でメンテナンスが困難になります。

ロジックやフィールド名が変更になった場合、コードを3箇所で更新する必要があります。
多くの場合、このコードはコピー＆ペーストで作成されているため、ミスが発生する可能性があります。
コードを確認する他のアナリストは、繰り返されるコードにしか目を通さないのが普通なので、間違いに気づく可能性は低いでしょう。
そこで、Jinjaを使ってコードをきれいにする、つまり "DRY"（"Don't Repeat Yourself"）なコードにすることにします。

# for制御を使ったloop処理を使ってSQLのコピペを防ぐ
ここでは、繰り返されるコードをforループに置き換えることができます。以下は同じクエリにコンパイルされますが、メンテナンスが格段に楽になります。

```sql:/models/order_payment_method_amounts.sql
select
order_id,
{% for payment_method in ["bank_transfer", "credit_card", "gift_card"] %}
sum(case when payment_method = '{{payment_method}}' then amount end) as {{payment_method}}_amount,
{% endfor %}
sum(amount) as total_amount
from {{ ref('raw_payments') }}
group by 1
```

# 変数を使って定義と処理を分けて管理をしやすくする
理解しやすく、必要に応じて複数の場所でリストを参照できるようにするため、変数はモデルの先頭に設定することをお勧めします。これは、他の多くのプログラミング言語でも多く見られる宣言と実際の処理を分けて構造化するアプローチです

```sql:/models/order_payment_method_amounts.sql
{% set payment_methods = ["bank_transfer", "credit_card", "gift_card"] %}

select
order_id,
{% for payment_method in payment_methods %}
sum(case when payment_method = '{{payment_method}}' then amount end) as {{payment_method}}_amount,
{% endfor %}
sum(amount) as total_amount
from {{ ref('raw_payments') }}
group by 1
```