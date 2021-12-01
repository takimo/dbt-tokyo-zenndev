---
title: "dbt Cloudのセットアップ"
---
# BigQueryの準備
このコンテンツでは既にBigQueryが使用できるようになっており、データも入っている状態の方を対象としています。BigQueryのプロジェクト作成やBigQueryに対してのデータロード等は以下の記事を参考にしてください。

:::message
次の章で取り扱うサンプルのテーブルデータは簡易的なものBigQueryに取り込んで扱えるような用意をしてあります
:::

- [第1回：マーケティング担当のためのBigQuery 入門　～BigQuery導入メリット～](https://azkk.co.jp/bigquery_for_marketer_01_merit)
- [trocco（日本企業が提供するのデータ転送サービス）](https://trocco.io/lp/index.html)
- [CLoud Data Fusion（Google製のフルマネージドデータ転送サービス）](https://cloud.google.com/data-fusion/?hl=ja)
- [Fivetran（グローバルでよく使われているデータ転送サービス）](https://classmethod.jp/partner/fivetran/)

# BigQueryにアクセスできるサービスアカントの発行

**コンソール画面へのアクセスし、サービスアカウント画面へ遷移する**
まずBigQueryの[コンソール画面](https://console.cloud.google.com/)にアクセスし、左上のハンバーガーメニューを選択し、「IAMと管理」＞「サービスアカウント」に遷移してください。

![](https://storage.googleapis.com/zenn-user-upload/c306129bcb4b-20211129.png =400x)

**サービスアカウントの発行**
上部中央にある「サービスアカウント」を選択します
![](https://storage.googleapis.com/zenn-user-upload/54973023afec-20211129.png =400x)

サービスアカウント、説明を以下のように入力し、「作成して続行」を選択します
![](https://storage.googleapis.com/zenn-user-upload/de05ccfcc01f-20211129.png =400x)

BigQueryへのアクセスをすべてできるように「BigQuery管理者」の権限を付与します

![](https://storage.googleapis.com/zenn-user-upload/223d09e5b32d-20211129.png =400x)

ロールの部分に「BigQuery管理者」というのが選択されている事を確認し、「完了」を押します。

**キーの発行**
次の工程のdbt Cloudの設定時に上記で作成したアカウント情報をアップロードする必要があり、「キー」を発行しておきます。

サービスアカウント一覧に作成したアカウントが表示されていると思いますが、右端の操作の3点マークのボタンを選択し、「鍵を管理」を選択

![](https://storage.googleapis.com/zenn-user-upload/0b35868bd237-20211129.png)

「鍵を追加」＞「新しい鍵を作成」を選択
![](https://storage.googleapis.com/zenn-user-upload/f5d22e845401-20211129.png)

キーのタイプはJSONのまま、「作成」ボタンを押してしばらくすると、キー情報が含まれたJSONファイルがダウンロードされます
![](https://storage.googleapis.com/zenn-user-upload/a0ccefcff155-20211129.png =400x)

:::message
ここで発行するサービスアカウントは取り扱いに関して非常に注意しなければならないものです。不安がある方は社内のGCP担当者等に相談してみてください。
:::

# dbt Cloucdのアカウント登録
BigQueryの準備が完了したところで、dbtを使った作業をブラウザ上で実行できる環境の「dbt Cloud」のアカウント登録をします。

**アカウント作成**

以下の登録画面に進み、必要な情報を入力して完了ボタン（Create my account）を選択してください。

![](https://storage.googleapis.com/zenn-user-upload/e9c756f989a8-20211129.png)

**メールに届いている認証ボタンをクリック**
アカウント作成後メールアドレスにメールアドレス認証のためのボタンがメール本文に表示されているので、それをクリック。

![](https://storage.googleapis.com/zenn-user-upload/341bebbd88c4-20211129.png =400x)

画面に「Set Up "Analytics"」と表示されればアカウント作成完了

# プロジェクトの作成
**プロジェクトのセットアップを進める**
Continueを押して下さい。
![](https://storage.googleapis.com/zenn-user-upload/cf66597d53a0-20211129.png =400x)

**接続先のデータ環境を設定**
BigQueryを選択します
![](https://storage.googleapis.com/zenn-user-upload/02ccbb79e565-20211129.png =400x)

**接続先情報の入力**
先程作成したサービスアカウントに紐づくキー情報をアップロードします。
「Upload a Service Account JSON file」というボタンをクリックし、先程ダウンロードしたキー情報が入ったJSONファイルをダイアログで選択します。

![](https://storage.googleapis.com/zenn-user-upload/f3a9a47b4078-20211129.png =400x)

認識が終わると上部に「Uploaded file successfully!」と表示され、「Test」というボタンが押せるようになっているので、ボタンを押して接続ができるかテストを行います。

![](https://storage.googleapis.com/zenn-user-upload/8b332d10aba7-20211129.png =400x)

接続にも問題がなければ、以下のような表示になるので「Continue」を選択してください

![](https://storage.googleapis.com/zenn-user-upload/9457d54fbaad-20211129.png =400x)

# GITHUBのアカウントとレポジトリの準備
dbt Cloudでは作成したクエリ等を保管するための環境を提供してくれるオプションも有りつつ、CI/CDの開発手法を取り入れられる点、Managedで作成した場合他に切り替えようとすると大変という理由で個人的にはGithubをおすすめしています。

![](https://storage.googleapis.com/zenn-user-upload/bd0e200ab16d-20211130.png)

**Githubの始め方とレポジトリ作成（5分）**
[こちらの記事](https://tech-blog.rakus.co.jp/entry/20200529/git#1-3-GitHub%E3%81%AE%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E4%BD%9C%E6%88%90)を参考に「1-4. リモートリポジトリを作成する」まで進めてみてください。
- レポジトリ名を迷う人は「dbt-tutorial」等で問題ありません
- 作成するレポジトリは「Private」を選択し、非公開にしておきましょう。

![](https://storage.googleapis.com/zenn-user-upload/050457d6ec5c-20211130.png =400x)

# レポジトリの設定
![](https://storage.googleapis.com/zenn-user-upload/a95e26f608d3-20211130.png =400x)

- 前のステップでGithubのアカウントを作った人＆持っている人はGithubを選択することをおすすめします
- それ以外の方はManagedもしくは状況に合わせGitやGitLabを選択してみてください

**Githubを選択**
Githubを選択するとGithubとの認証ページに移動します。
規約を確認し、「Authorize dbt Cloud」を選択してください。

![](https://storage.googleapis.com/zenn-user-upload/67263e8d99f4-20211130.png =400x)

**認可が終わったら**
プロジェクトに紐付けるレポジトリを選択します。

![](https://storage.googleapis.com/zenn-user-upload/c1a68b772360-20211201.png)

画面に戻るので、「Continue」を押しましょう。

**プロジェクトへの招待**
他の作業者がいる場合招待することができます。
dbt Cloudでは作業者が1人までの場合Developerライセンスと呼ばれる形態で無料で使用することができます（2021年11月29日時点）

必要に合わせて選択し、Sikp&Continueに進みましょう。
![](https://storage.googleapis.com/zenn-user-upload/c7cbab4682c2-20211130.png =400x)

**セットアップの完了**
お疲れさまでした、これで開発に移れます。

![](https://storage.googleapis.com/zenn-user-upload/b6b34293eb85-20211130.png =400x)
