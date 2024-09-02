## How to start development

````bash
# リポジトリからコードを落としてくる
$ git clone https://github.com/Figurout-Co-Ltd/figurout-firehouse-app.git

# ディレクトリに移動
$ cd figurout-firehouse-app

# ビルドする
$ docker-compose build

# アプリケーションを起動
$ docker-compose up

# デタッチモードで実行
$ docker-compose up -d

# コンソールのログ確認
$ docker-compose logs -f console

# webUI
# http://localhost:3000

# firebase emulator console (エミュレーション環境)
# http://localhost:4000

# Hasura console (GraphQL)
# http://localhost:8081

# MinIO (オブジェクトストレージ)
# http://localhost:9001

# コンソールにデータをロードする（開発メンバーにGCPの参照権限をもらう。）
# 最新のCloud_SQL_Export_yyyy-mm-dd(hh:mm:ss).sqlというファイルをローカルにダウンロード
# tmp/initsql配下にファイルを配置してアプリケーションを起動する
$ mv <path/to/downloaded/file>/Cloud_SQL_Export_yyyy-mm-dd(hh:mm:ss).sql tmp/initsql
$ docker-compose up -d

# create ssh key
$ ssh-keygen -t rsa -b 4096 -f ./ssh/id_rsa -q -N "" -C <user>@<hostname>

# codegen
$ docker-compose run --rm base yarn codegen


例：　クライアントコードの任意の場所に

urql.gqlで囲ったgraphqlのステートメントを記載すると

codegenで対応するクライアントコードが自動生成される。


```ts
import { gql } from 'urql';
const GET_S3_PRE_SIGNED_URL = gql`
  mutation GetS3PreSignedURL($input: S3PreSignedURLInput!) {
    GetS3PreSignedURL(object: $input) {
      s3PreSignedURLList {
        id
        path
      }
    }
  }
`;
````

````

## 画面設計

https://www.figma.com/file/KyUfTFwI3F4wh6wzn7aThV/Untitled?node-id=0%3A1

## 設計

## development

```bash
# how to install node modules
docker-compose run --rm base yarn workspace @figurout/console add -D awesome-modules
docker-compose run --rm base yarn workspace @figurout/functions add -D awesome-modules
```

### test on functions

```bash
docker-compose up -d firestore
docker-compose run --rm base yarn workspace @figurout/functions test
```

### How to USE Feature flag

deploy to edge only

```jsx
export default SomeComponent = () => {
  // TODO: remove when deploy to current/main
  if ($env.FEATURES_ENABLED) return <YourFeatureComponent />;
  return <></>;
};
```

### create jwt private key

```bash
$ openssl genpkey -out tmp/jwtRS256-2.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
$ openssl pkey -in tmp/jwtRS256-2.key -pubout -out tmp/jwtRS256-2.key.pub
```

### add data to local postgres

cloud sql から data を sql insert 形式で output する。

tmp/initsql に Cloud*SQL_Export*{YYYY-MM-DD}.sql を移動する。

docker-compose up -d postgresql

docker-compose logs -f postgresql # dump が完了したことを確認する。

### add S3 data to local S3 bucket

[minio README.md](/packages/minio/README.md) 参照

### create a staff user for console frontend development

1. staffs table に任意の uuid で record を追加

2. staffs table に追加した uuid と任意の email と name を使って staffInvitations table に record を追加

3. staffInvitations table の email と name を使って http://localhost:3000/signup から新規登録
````
