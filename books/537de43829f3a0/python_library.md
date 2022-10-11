---
title: 'dbt向けPythonライブラリのご紹介'
---
written by: [Ken](https://twitter.com/diggy__k)

昨今、dbt向けの便利なPythonライブラリが数多く開発されていますが、それらを探し当てることはなかなか大変ですよね。そこでBI, DataOps, Transformationの3カテゴリーに分けてご紹介したいと思います。

## BI
| ツール名 | 概要 |
| --- | --- |
| [looker-gen](https://github.com/aaronbannin/looker-gen) | dbtからLookMLを生成する。 |
| [dbt2looker](https://github.com/lightdash/dbt2looker) | dbtモデルからLookerのViewファイルを生成する。 |
| [dbt-superset-lineage](https://github.com/slidoapp/dbt-superset-lineage) | dbtドキュメントとApache Supersetの連携。 |
| [dbt-metabase](https://github.com/gouline/dbt-metabase) | dbtからMetabaseへのモデルの同期。 |
| [dbt-exposures-crawler](https://github.com/voi-oss/dbt-exposures-crawler) | BIツールからdbtのExposuresを自動生成する(今の所サポートしているのはTableauのみ) |
## DataOps
| ツール名 | 概要 |
| --- | --- |
| [dbt-dry-run](https://github.com/autotraderuk/dbt-dry-run) | BigQueryのdry run機能を利用して、実行前にクエリが有効であるかどうかを確認することができる。 |
| [py-dbt-cloud](https://github.com/dwallace0723/py-dbt-cloud) | dbt Cloud v2 API向けのPython SDK。 |
| [pre-commit-dbt](https://github.com/offbi/pre-commit-dbt) | dbtプロジェクトの品質を維持するための[pre-commit](https://pre-commit.com/)のリスト。CIツール利用前提。 |
| [dbtenv](https://github.com/brooklyn-data/dbtenv) | dbtのバージョン管理ツール。Pythonの仮想環境やMacのHomebrew、Linux等において複数バージョンのdbtを自動的にインストールし切り替えてくれる。 |
| [dbt-streamlit](https://github.com/Cazoo-uk/dbt-streamlit) | dbt CloudユーザーにAdmin権限を付与することなく、各ユーザーのJobに関する情報を共有することができる。 |
| [dbt-helper](https://github.com/mikekaminsky/dbt-helper) | dbtプロジェクトの開発およびデータウェアハウス管理を支援するコマンドラインツール。 |
| [dbt-container-skeleton](https://github.com/gnilrets/dbt-container-skeleton) | コンテナ化されたdbtの完全な開発環境をブートストラップする。 |
| [cookiecutter-dbt](https://github.com/datacoves/cookiecutter-dbt) | 本番環境レベルのdbtプロジェクトを素早く立ち上げてくれる（フレームワーク）。 |
| [artifactable](https://github.com/artifactable/cli) | dbtプロジェクトの通知サービス。指定したモデルを追跡し、異常時にアラートを受け取ることができる。 |
| [airflow-dbt-python](https://github.com/tomasfarias/airflow-dbt-python) | dbtを操作するためのAirflow operatorとhookを集めたライブラリ。airflow-dbt-pythonを使用してdbt変換パイプラインをエンドツーエンドで実行できる。 |
| [airflow-dbt](https://github.com/gocardless/airflow-dbt) | dbtとの統合を容易にするためにAirflow operatorを集めたもの。 |
| [fzf-dbt](https://github.com/Infused-Insight/fzf-dbt) | ターミナルから対話的にdbtのモデルを検索することができる。 |
| [Terraform-dbt Cloud Module](https://github.com/GtheSheep/terraform-provider-dbt-cloud) | Terraform経由でdbt CloudにIACを導入。 |
## Doc/Test
| ツール名 | 概要 |
| --- | --- |
| [dbt-osmosis](https://github.com/z3z1ma/dbt-osmosis) | ４つの機能が含まれているツール。①YAML管理自動化（上流モデルの情報を下流モデルのYAMLに自動伝搬）　②dbt-power-user for VS Codeなどのツールと統合し、IDEからインタラクティブなクエリやリアルタイムコンパイルを可能にするdbtサーバー　③streamlitベースのワークベンチ　④モデルに対し動的に差分実行する |
| [dbt-coverage](https://github.com/slidoapp/dbt-coverage) | dbt Docsとテストに不足がないかカバー率を算出してくれるCLIツール。 |
| [tap-dbt-artifacts](https://github.com/prratek/tap-dbt-artifacts) | Meltano SDKでビルドされたdbt Artifacts用のSinger Tap。 |
| [oliver-twist](https://github.com/autotraderuk/oliver-twist) | dbtのDAGを監査し、サマリレポートを生成するDAG監査ツール。 |
| [dbt-unit-test](https://github.com/AgeOfLearning/dbt-unit-test) | dbtモデルで再利用される可能性のあるあらゆるコードのテストケースを簡単に書けるようにするツール。 |
| [dbt-test-coverage](https://github.com/mikaelene/dbt-test-coverage) | dbtプロジェクトにおけるテストカバレッジを表示するためのCLIツール。 |
| [dbt-sugar](https://github.com/bitpicky/dbt-sugar) | カラムやモデルのドキュメント追加、テストカバレッジやタグの強制といったタスクをCLIから直接実行できるようにするツール。 |
| [Demeter](https://github.com/mjirv/demeter) | REST API（GraphQLエンドポイントを含む）を提供し、dbtのMetricsにクエリを投げることができる。 |
| [dbt-invoke](https://github.com/Dashlane/dbt-invoke) | dbtのYAMLファイルを作成、更新、削除してくれるCLI。 |
| [dbt-datamocktool](https://github.com/mjirv/dbt-datamocktool) | モデルが使用するsourcesやrefの代わりとなるモックCSV seedsを生成し、モデルが望ましい出力を生成することをテストできる。 |
| [dbt-yaml-check](https://github.com/k-aranke/dbt-yaml-check) | YAMLで定義されたカラムがSQLにも存在するかどうかをチェックしてくれる。 |
| [data-diff](https://github.com/datafold/data-diff) | 2つの異なるデータベース間で効率的に行を差分取得するためのコマンドラインツールとPythonライブラリ。 |
| [dbtc](https://github.com/dpguthrie/dbtc) | dbt Cloudの様々なAPIエンドポイントへの非公式なPythonインターフェース |
| [pytest-dbt-core](https://github.com/godatadriven/pytest-dbt-core) | dbt プロジェクトをテストするためのpytestプラグイン。 |
| [dbt-erdiagram-generator](https://github.com/intellishore/dbt-erdiagram-generator) | dbtプロジェクトからER図を生成する。 |
## Transformation
| ツール名 | 概要 |
| --- | --- |
| [dbt-generator](https://github.com/tuanchris/dbt-generator) | ベースモデルの生成や一括変換を支援してくれる。 |
| [dbt-coves](https://github.com/datacoves/dbt-coves) | Redshift、Snowflake、BigQueryのスキーマを解析し、必要なファイル（sql、yml）を自動生成してくれる。Airbyteとの連携も。 |
| [dbt-ml-preprocessing](https://github.com/omnata-labs/dbt-ml-preprocessing) | SparkのmllibやPythonのscikit-learnのような外部ライブラリを使わずに、データウェアハウス内にFeature Storeを構築する。 |
| [dbt-feature-store](https://github.com/fal-ai/dbt_feature_store) | dbtプロジェクト内でFeature Storeを構築するためのマクロ。 |

今回ご紹介するものは以上です。気になるものがありましたら、ぜひ試してみてくださいね。
また「ここに載っていないけれど、こんなのもあるよ！」というものがありましたら、ぜひdbt-tokyoの `#local-tokyo` チャンネルで教えてください！
