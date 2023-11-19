# Azurite で Azure Storage をエミュレートする

## 前提

本ドキュメントに記載の手順は[Docker](./install-docker-for-windows.md)の利用を前提とするため、インストールしておくこと

## Azure CLI をインストール

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

## Azuite の導入

[Azrite ディレクトリ](../../emulater/Azurite/)内でdocker-composeを実行

```bash
docker-compose up -d
```

## 接続設定

```bash
export AZURE_STORAGE_CONNECTION_STRING="DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;"
```

## Storage Container

- 作成

  ```bash
  az storage container create --name sample-container
  ```

- 確認

  ```bash
  az storage container list
  ```

- 削除

  ```bash
  az storage container delete --name sample-container
  ```

## Blob Storage

- アップロード

  ```bash
  echo "It's azurite sample." > sample.txt
  az storage blob upload --container-name sample-container --name sample.blob --file sample.txt
  ```

- 確認

  ```bash
  az storage blob list --container-name sample-container
  ```

- ダウンロード

  ```bash
  az storage blob download --container-name sample-container --name sample.blob --file sample2.txt
  ```

- 削除

  ```bash
  az storage blob delete --container-name sample-container --name sample.blob
  ```

## Table Storage(NoSQL)

- テーブル作成

  ```bash
  az storage table create --name sampletable
  ```

- 確認

  ```bash
  az storage table list
  ```

- データ投入

  ```bash
  az storage entity insert --table-name sampletable --entity {partitionkey="001",rowkey="001",name="sample"}
  ```

- データ参照

  ```bash
  az storage entity query --table-name sampletable
  ```

- データ削除

  ```bash
  az storage entity delete --table-name sampletable --partition-key "001" --row-key "001"
  ```

- テーブル削除

  ```bash
  az storage table delete --name sampletable
  ```
