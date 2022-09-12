---
title: "チュートリアル：モデル開発の基本"
---
# モデルの開発とは？
dbtではSQLで書かれたデータの抽出を行いテーブルのデータになる処理のまとまりを「モデル」と呼んでいます。モデルに書かれたクエリはdbtの処理によりデータの抽出＆テーブルの作成やアップデートが実行されます。この章では「モデルの開発」と「テーブルへの反映」の手順をチュートリアルをベースにハンズオンできるようにコンテンツを準備してみました。

# 新しいブランチの作成とチェックアウト

**Developに移動**
画面左上のハンバーガーメニューを押し、「Develop」を選択してください

![](https://storage.googleapis.com/zenn-user-upload/2d5b56df5ae6-20211130.png)


developの画面に遷移するとエディタ兼dbtの統合環境が開きます。
※細かいエディタの機能説明はこちらを参考ください。

![](https://storage.googleapis.com/zenn-user-upload/6b69189ec284-20211201.png)

**レポジトリの初期化**
dbtの開発に必要なフォルダや設定用のファイルを作成することが出来ます。
画面左上の「initialize your project」を選択してください。
しばらくすると以下のようにフォルダやファイルが生成されます。


![](https://storage.googleapis.com/zenn-user-upload/172afa449a8a-20211201.png)

**初期設定のコミット**
初期設定の差分をコミットしてしまいましょう。
画面左上の緑のボタンの表記が「commit...」になっているので、ボタンを選択します。

![](https://storage.googleapis.com/zenn-user-upload/cf34c56832c9-20211201.png)

選択後、以下のようなダイアログが出てくるので、今回の修正の内容をコメントしてCommitボタンを押して保存をします。

![](https://storage.googleapis.com/zenn-user-upload/4504450dfeb0-20211201.png)

修正が保存されると左上のボタンが「commit...」から「create new branch」に変わっているなら完了です。

# ブランチの作成
「create new branch」の緑のボタンを押してください。
ダイアログが出てくるので「add-customers-model」と入力し「Submit」を押すとブランチが作成されます。

![](https://storage.googleapis.com/zenn-user-upload/1bd2c2be4e4d-20211201.png)

# テストテーブルの準備
:::message
dbt seedという機能を使い、サンプルデータを元にBigQuery上にサンプルデータが含まれたテーブルを簡易的に作成します
:::

通常の開発では事業に紐付いているDWHにあるデータを扱って行うと思いますが、今回のチュートリアルは以下のようなお店の購入者データと購入データをサンプルにハンズオンしようと思います。

![](https://storage.googleapis.com/zenn-user-upload/5f0fa8bd5d02-20211201.png)

以下の3ファイルをそれぞれ「data」フォルダの中に格納をしていきます。

:::details raw_customers.csv
```csv:raw_customers.csv
id,first_name,last_name
1,Yasuko,Kataoka
2,Chizuru,Ozaki
3,Kouhei,Iwasawa
4,Kae,Utsunomiya
5,Yura,Mizukami
6,Chiyoko,Sasamori
7,Mayu,Izawa
8,Kazuo,Ochiai
9,Hiroshi,Imai
10,Rina,Tobita
11,Shougo,Utsumi
12,Hideka,Tashiro
13,Shuu,Moriya
14,Katsuko,Mori
15,Anju,Iwai
16,Himawari,Nagahama
17,Kinzou,Fukumoto
18,Ayana,Shouji
19,Hajime,Isozaki
20,Shigeru,Inomata
21,Himeno,Tsuruoka
22,Tokiya,Okada
23,Kaori,Watanabe
24,Kanon,Hamaguchi
25,Tsuneo,Minamida
26,Ryuu,Kida
27,Toshiharu,Aida
28,Takeshi,Harada
29,Aika,Sakata
30,Daiki,Sugita
31,Aki,Kosugi
32,Kaori,Kiuchi
33,Yuuichi,Iwai
34,Yuito,Imaizumi
35,Takao,Sakurai
36,Shigeo,Doi
37,Aya,Akaishi
38,Yoshiko,Fujino
39,Nobuko,Ookura
40,Shigeko,Tomita
41,Nobuo,Sakai
42,Souta,Shiraki
43,Rio,Koyanagi
44,Hiroto,Nakaoka
45,Aika,Sakurada
46,Susumu,Kawagoe
47,Eiji,Kawahara
48,Kuon,Teramoto
49,Toshiharu,Kuhabara
50,Saki,Shioda
51,Tomoyo,Tsuruta
52,Isao,Yada
53,Kingo,Kido
54,Sueo,Sumida
55,Nanami,Ishikawa
56,Rina,Kakizaki
57,Katsuji,Asai
58,Eijirou,Sasahara
59,Torao,Miyazaki
60,Takahide,Shiozawa
61,Fujiko,Fukai
62,Koto,Kita
63,Saaya,Kurozaki
64,Eisaku,Nihei
65,Saya,Kouya
66,Hirohito,Ootsu
67,Rena,Kawabata
68,Takehiro,Iida
69,Yuri,Takase
70,Maya,Kakizaki
71,Takayuki,Bandou
72,Otoya,Hatori
73,Akiko,Hanada
74,Runa,Nakayama
75,Yuika,Ookura
76,Takako,Nagasaka
77,Kiichi,Matsuoka
78,Nanami,Nakamura
79,Tetsurou,Oosawa
80,Karina,Furuhashi
81,Momone,Funabashi
82,Yukiko,Nakabayashi
83,Sachiko,Sugino
84,Mina,Kan
85,Yuu,Terada
86,Terumi,Nakada
87,Hiromi,Takizawa
88,Ruka,Kurosawa
89,Sachiko,Motohashi
90,Mutsumi,Kuroki
91,Shin,Nakada
92,Hiroshi,Yanase
93,Shinichi,Taki
94,Chika,Sasamori
95,Setsuko,Asanuma
96,Eizaburou,Momose
97,Kae,Itagaki
98,Rikichi,Handa
99,Mika,Ban
100,Shouichirou,Kishi
```
:::
:::details raw_orders.csv
```csv:raw_orders.csv
id,user_id,order_date,status
1,1,2018-01-01,returned
2,3,2018-01-02,completed
3,94,2018-01-04,completed
4,50,2018-01-05,completed
5,64,2018-01-05,completed
6,54,2018-01-07,completed
7,88,2018-01-09,completed
8,2,2018-01-11,returned
9,53,2018-01-12,completed
10,7,2018-01-14,completed
11,99,2018-01-14,completed
12,59,2018-01-15,completed
13,84,2018-01-17,completed
14,40,2018-01-17,returned
15,25,2018-01-17,completed
16,39,2018-01-18,completed
17,71,2018-01-18,completed
18,64,2018-01-20,returned
19,54,2018-01-22,completed
20,20,2018-01-23,completed
21,71,2018-01-23,completed
22,86,2018-01-24,completed
23,22,2018-01-26,return_pending
24,3,2018-01-27,completed
25,51,2018-01-28,completed
26,32,2018-01-28,completed
27,94,2018-01-29,completed
28,8,2018-01-29,completed
29,57,2018-01-31,completed
30,69,2018-02-02,completed
31,16,2018-02-02,completed
32,28,2018-02-04,completed
33,42,2018-02-04,completed
34,38,2018-02-06,completed
35,80,2018-02-08,completed
36,85,2018-02-10,completed
37,1,2018-02-10,completed
38,51,2018-02-10,completed
39,26,2018-02-11,completed
40,33,2018-02-13,completed
41,99,2018-02-14,completed
42,92,2018-02-16,completed
43,31,2018-02-17,completed
44,66,2018-02-17,completed
45,22,2018-02-17,completed
46,6,2018-02-19,completed
47,50,2018-02-20,completed
48,27,2018-02-21,completed
49,35,2018-02-21,completed
50,51,2018-02-23,completed
51,71,2018-02-24,completed
52,54,2018-02-25,return_pending
53,34,2018-02-26,completed
54,54,2018-02-26,completed
55,18,2018-02-27,completed
56,79,2018-02-28,completed
57,93,2018-03-01,completed
58,22,2018-03-01,completed
59,30,2018-03-02,completed
60,12,2018-03-03,completed
61,63,2018-03-03,completed
62,57,2018-03-05,completed
63,70,2018-03-06,completed
64,13,2018-03-07,completed
65,26,2018-03-08,completed
66,36,2018-03-10,completed
67,79,2018-03-11,completed
68,53,2018-03-11,completed
69,3,2018-03-11,completed
70,8,2018-03-12,completed
71,42,2018-03-12,shipped
72,30,2018-03-14,shipped
73,19,2018-03-16,completed
74,9,2018-03-17,shipped
75,69,2018-03-18,completed
76,25,2018-03-20,completed
77,35,2018-03-21,shipped
78,90,2018-03-23,shipped
79,52,2018-03-23,shipped
80,11,2018-03-23,shipped
81,76,2018-03-23,shipped
82,46,2018-03-24,shipped
83,54,2018-03-24,shipped
84,70,2018-03-26,placed
85,47,2018-03-26,shipped
86,68,2018-03-26,placed
87,46,2018-03-27,placed
88,91,2018-03-27,shipped
89,21,2018-03-28,placed
90,66,2018-03-30,shipped
91,47,2018-03-31,placed
92,84,2018-04-02,placed
93,66,2018-04-03,placed
94,63,2018-04-03,placed
95,27,2018-04-04,placed
96,90,2018-04-06,placed
97,89,2018-04-07,placed
98,41,2018-04-07,placed
99,85,2018-04-09,placed
```
:::
:::details raw_payments.csv
```csv:raw_payments.csv
id,order_id,payment_method,amount
1,1,credit_card,1000
2,2,credit_card,2000
3,3,coupon,100
4,4,coupon,2500
5,5,bank_transfer,1700
6,6,credit_card,600
7,7,credit_card,1600
8,8,credit_card,2300
9,9,gift_card,2300
10,9,bank_transfer,0
11,10,bank_transfer,2600
12,11,credit_card,2700
13,12,credit_card,100
14,13,credit_card,500
15,13,bank_transfer,1400
16,14,bank_transfer,300
17,15,coupon,2200
18,16,credit_card,1000
19,17,bank_transfer,200
20,18,credit_card,500
21,18,credit_card,800
22,19,gift_card,600
23,20,bank_transfer,1500
24,21,credit_card,1200
25,22,bank_transfer,800
26,23,gift_card,2300
27,24,coupon,2600
28,25,bank_transfer,2000
29,25,credit_card,2200
30,25,coupon,1600
31,26,credit_card,3000
32,27,credit_card,2300
33,28,bank_transfer,1900
34,29,bank_transfer,1200
35,30,credit_card,1300
36,31,credit_card,1200
37,32,credit_card,300
38,33,credit_card,2200
39,34,bank_transfer,1500
40,35,credit_card,2900
41,36,bank_transfer,900
42,37,credit_card,2300
43,38,credit_card,1500
44,39,bank_transfer,800
45,40,credit_card,1400
46,41,credit_card,1700
47,42,coupon,1700
48,43,gift_card,1800
49,44,gift_card,1100
50,45,bank_transfer,500
51,46,bank_transfer,800
52,47,credit_card,2200
53,48,bank_transfer,300
54,49,credit_card,600
55,49,credit_card,900
56,50,credit_card,2600
57,51,credit_card,2900
58,51,credit_card,100
59,52,bank_transfer,1500
60,53,credit_card,300
61,54,credit_card,1800
62,54,bank_transfer,1100
63,55,credit_card,2900
64,56,credit_card,400
65,57,bank_transfer,200
66,58,coupon,1800
67,58,gift_card,600
68,59,gift_card,2800
69,60,credit_card,400
70,61,bank_transfer,1600
71,62,gift_card,1400
72,63,credit_card,2900
73,64,bank_transfer,2600
74,65,credit_card,0
75,66,credit_card,2800
76,67,bank_transfer,400
77,67,credit_card,1900
78,68,credit_card,1600
79,69,credit_card,1900
80,70,credit_card,2600
81,71,credit_card,500
82,72,credit_card,2900
83,73,bank_transfer,300
84,74,credit_card,3000
85,75,credit_card,1900
86,76,coupon,200
87,77,credit_card,0
88,77,bank_transfer,1900
89,78,bank_transfer,2600
90,79,credit_card,1800
91,79,credit_card,900
92,80,gift_card,300
93,81,coupon,200
94,82,credit_card,800
95,83,credit_card,100
96,84,bank_transfer,2500
97,85,bank_transfer,1700
98,86,coupon,2300
99,87,gift_card,3000
100,87,credit_card,2600
101,88,credit_card,2900
102,89,bank_transfer,2200
103,90,bank_transfer,200
104,91,credit_card,1900
105,92,bank_transfer,1500
106,92,coupon,200
107,93,gift_card,2600
108,94,coupon,700
109,95,coupon,2400
110,96,gift_card,1700
111,97,bank_transfer,1400
112,98,bank_transfer,1000
113,99,credit_card,2400
```
:::

「data」フォルダ以下にファイルを作成したい場合はまず「data」フォルダにカーソルを合わせると右に3点マークのボタンが出るので、それをクリックし、「New File」を選択してください。

![](https://storage.googleapis.com/zenn-user-upload/be7a92d1bf32-20211201.png)

ファイル名には上でそれぞれのデータで定義した名前をそのまま入力してそれぞれ保存をしてみてください。

![](https://storage.googleapis.com/zenn-user-upload/19194458a555-20211201.png)

3つCSVファイルを/seedsに作成すると以下のようなデータになっていればOKです。

![](https://storage.googleapis.com/zenn-user-upload/d0a47667814c-20211201.png)

**サンプルデータの実テーブル化**
画面下部にある細い入力欄に以下のコマンドを入力して、Enterを押してください
```
dbt seed
```
![](https://storage.googleapis.com/zenn-user-upload/ca982080e75b-20211201.png)

実行をすると実行タスク（ジョブ）の結果が確認できる画面が開きます。
左のdbt seedに続くアイコンが緑色のチェックマークだったらテーブルの生成が完了しています。

![](https://storage.googleapis.com/zenn-user-upload/fd47eac89c71-20211201.png)

この時、実際にBigQueryのConsoleでどこにテーブルが生成されたのか確認したい場合は確認画面の右側のDETAILSにある各項目をクリックして展開すると実際のデプロイのログが確認でき、そこにデータセット名とテーブル名が書かれています。

![](https://storage.googleapis.com/zenn-user-upload/acd635ae611a-20211201.png)

:::message
エディタのコマンド機能でデプロイした場合の展開先のデータセットはアカウント情報に紐付いて自動的に設定されており、変更が可能です
:::

# customersテーブルを作ってみる

**customers.sqlを作成する**
先程作成したテーブルのデータを使い、customersテーブルにordersのデータを使い、初回購入日と最終購入日を通常のSQLでまず書いてみましょう。

/models/customers.sqlというファイルを作り以下のクエリを貼りつけ、保存（Ctrl-S or saveボタン）をしてください。

先程確認したテーブルがそれぞれどこのデータセット名.テーブル名に作成されたか再度確認し、fromの参照先は各々で書き換えてください（dbt_xxxxxxxの部分）

```sql:/models/customers.sql
with customers as (
    select
        id as customer_id,
        first_name,
        last_name
    from dbt_xxxxxxx.raw_customers
),
orders as (
    select
        id as order_id,
        user_id as customer_id,
        order_date,
        status
    from dbt_xxxxxxx.raw_orders
),
customer_orders as (
    select
        customer_id,
        min(order_date) as first_order_date,
        max(order_date) as most_recent_order_date,
        count(order_id) as number_of_orders
    from orders
    group by 1

),
final as (
    select
        customers.customer_id,
        customers.first_name,
        customers.last_name,
        customer_orders.first_order_date,
        customer_orders.most_recent_order_date,
        coalesce(customer_orders.number_of_orders, 0) as number_of_orders
    from customers
    left join customer_orders using (customer_id)
)

select * from final
```

この時のデータリネージは以下のようになっています。

![](https://storage.googleapis.com/zenn-user-upload/df49b5705a27-20211201.png)

画面真ん中にある「Preview」というボタンを押すとクエリが実行され、クエリの実行結果が確認できます。

![](https://storage.googleapis.com/zenn-user-upload/77a70cab9c1b-20211201.png)

**dbt runでモデルをデプロイする**
以下のコマンドを下部の入力欄に入力し、Enterを押して実行してください。
モデルをベースに実態のテーブルを作成します。
```
dbt run
```
緑色のチェックが出たら完了です。

![](https://storage.googleapis.com/zenn-user-upload/e8344ced5145-20211201.png)

# モデル間の構造化をしてみる（別テーブルに切り出す）
同一的なデータの多重管理を避けるためにデータ管理の視点でデータの集約が行われることは多いです。その際に利用される側と利用する側といった流れが生まれます。先程実装したクエリはwith句を使い、同一クエリ内で実装してしまています（CTE）がdbtの機能のref機能を使ってクエリの管理を分離してみましょう。

![](https://storage.googleapis.com/zenn-user-upload/47d4b97b1d90-20211201.png)

**modes/stg_customers.sqlの作成**
```sql:stg_customers.sql
select
    id as customer_id,
    first_name,
    last_name
from dbt_xxxxxxx.raw_customers
```

**modes/stg_orders.sqlの作成**
```sql:stg_orders.sql
select
    id as order_id,
    user_id as customer_id,
    order_date,
    status
from dbt_xxxxxxx.raw_orders
```

**customersテーブルで処理するデータを別のモデルから参照するようにする**
```
with customers as (
    select * from {{ ref('stg_customers') }}
),
orders as (
    select * from {{ ref('stg_orders') }}
),
customer_orders as (
    select
        customer_id,
        min(order_date) as first_order_date,
        max(order_date) as most_recent_order_date,
        count(order_id) as number_of_orders
    from orders
    group by 1
),
final as (
    select
        customers.customer_id,
        customers.first_name,
        customers.last_name,
        customer_orders.first_order_date,
        customer_orders.most_recent_order_date,
        coalesce(customer_orders.number_of_orders, 0) as number_of_orders
    from customers
    left join customer_orders using (customer_id)
)
select * from final
```

上記のファイルの追加と修正が完了したら次のコマンドの入力と実行をします。
```
dbt run
```

![](https://storage.googleapis.com/zenn-user-upload/bd9fcdd2b4a6-20211201.png)

成功したらデータリネージがどの様になっているか確認しましょう。
期待通り、stg_customersとstg_ordersが生成され、それをcustomersが参照するようなデータリネージになりました。

![](https://storage.googleapis.com/zenn-user-upload/b8ac7948d00d-20211201.png)

ref機能で実際のテーブル名ではなく、モデルを指定することにより依存関係の解釈がされます。依存関係はデータリネージの可視化だけでなく、上記の場合、まずstg_customersとstg_ordersがデプロイされ、そのテーブルが実在する状態でcustomersのテーブルが生成される処理が実行される流れになります（DAGを考慮したデプロイ）
