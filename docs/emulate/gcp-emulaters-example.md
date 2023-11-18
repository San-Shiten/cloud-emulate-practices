# GCP をエミュレートする

GCPのエミュレータを調べてみたが、存在はするが、公式のものでなかったり、サービスごとに細分化されているなどAWSやAzureに比べて使い勝手がよくはない印象を受けた

## 前提

本ドキュメントに記載の手順は[Docker](./install-docker-for-windows.md)の利用を前提とするため、インストールしておくこと

## Google Cloud CLI のインストール/設定

- インストール

  ```bash
  curl https://sdk.cloud.google.com | bash
  ```

- 設定ファイルを再読み込み

  ```bash
  /home/{{ユーザー名}}/.bashrc
  ```

- gcloudコマンドのインストール確認

  ```bash
  gcloud version
  ```

- 設定
  エミュレータを gcloudコマンド で使用するには、認証を無効にしてエンドポイントをオーバーライドする必要がある。

  ```bash
  gcloud config configurations create emulator
  gcloud config set auth/disable_credentials true
  gcloud config set project sample-project
  gcloud config set api_endpoint_overrides/spanner http://localhost:9020/
  gcloud config set api_endpoint_overrides/storage http://localhost:4443/storage/v1/
  ```

## GCP Emulaters の導入

[GCP-Emulaters ディレクトリ](../../emulater/GCP-Emulaters/)内でdocker-composeを実行

```bash
docker-compose up -d
```

## Cloud Spanner(データベース)

- インスタンス作成

  ```bash
  gcloud spanner instances create sample-instance \
  --config=emulator-config --description="Sample Instance" --nodes=1
  ```

- DB 作成

  ```bash
  gcloud spanner databases create sample-db --instance=sample-instance
  ```

- テーブル作成
  - 親テーブル

    ```bash
    gcloud spanner databases ddl update sample-db --instance=sample-instance \
    --ddl='CREATE TABLE Games ( GameId INT64 NOT NULL, Title STRING(1024), GameInfo BYTES(MAX) ) PRIMARY KEY (GameId)'
    ```

  - 子テーブル

    ```bash
    gcloud spanner databases ddl update sample-db --instance=sample-instance \
    --ddl='CREATE TABLE Heroines ( GameId INT64 NOT NULL, HeroineId INT64 NOT NULL, FirstName STRING(1024), LastName STRING(1024) ) PRIMARY KEY (GameId, HeroineId), INTERLEAVE IN PARENT Games ON DELETE CASCADE'
    ```

- INSERT
  - 親テーブル

    ```bash
    gcloud spanner rows insert --database=sample-db --instance=sample-instance \
    --table=Games \
    --data=GameId=1,Title=Kono-Aozora-ni-Yakusoku-wo
    ```

  - 子テーブル

    ```bash
    gcloud spanner rows insert --database=sample-db --instance=sample-instance \
    --table=Heroines \
    --data=GameId=1,HeroineId=3,FirstName=Kirishima,LastName=Saeri
    ```

- SELECT

  ```bash
  gcloud spanner databases execute-sql sample-db --instance=sample-instance \
  --sql='SELECT GameId, HeroineId, FirstName, LastName FROM Heroines'
  ```

- DELETE

  ```bash
  gcloud spanner rows delete --table=Heroines  --database=sample-db --instance=sample-instance --keys=1,3
  ```

- テーブル削除

  ```bash
  gcloud spanner databases ddl update sample-db --instance=sample-instance \
  --ddl='DROP TABLE Heroines'
  ```

- DB 削除

  ```bash
  gcloud spanner databases delete sample-db --instance=sample-instance
  ```

- インスタンス削除

  ```bash
  gcloud spanner instances delete sample-instance
  ```

## Cloud Storage

- バケット作成

  ```bash
  gcloud storage buckets create gs://sample-bucket --default-storage-class=standard --location=asia-northeast1 --uniform-bucket-level-access
  ```

- バケット確認

  ```bash
  gcloud storage ls
  ```

- アップロード

  ```bash
  echo "It's GCS sample." > sample.txt
  gcloud storage cp sample.txt gs://sample-bucket
  ```

- オブジェクト確認

  ```bash
  gcloud storage ls gs://sample-bucket
  ```

- ダウンロード(なぜか上手くいかない、失敗して0Bのtmpファイルの生成になる)

  ```bash
  gcloud storage cp gs://sample-bucket/sample.txt sample2.txt
  ```

- オブジェクト削除(エラーになるが結果として削除はできている)

  ```bash
  gcloud storage rm gs://sample-bucket/sample.txt
  ```
  
- バケット削除(エラーになるが結果として削除はできている)

  ```bash
  gcloud storage rm --recursive gs://sample-bucket
  ```
