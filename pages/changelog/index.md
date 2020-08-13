---
alias_paths:
  - /docs/api-changelog
centered: true
rank: 0
category_id: changelog
subcategory_id: null
is_index: true
id: changelog
type: page
total_steps: 2
sibling_id: pages
parent_id: pages
next_page_id: changelog/2018
previous_page_id: ''
source_url: >-
  https://github.com/box/developer.box.com/blob/default/content/pages/changelog/index.md
---
<!-- alex disable postman-postwoman -->

# 変更ログ

過去の変更ログについては、[2019](page://changelog/2019)および[2018](page://changelog/2018)のリリースノートを参照してください。

## 2020年7月22日/新しい圧縮ダウンロードAPI

APIの新しいコレクションが利用可能になりました。開発者は、これを使用して、指定したリストのファイルやフォルダを含む圧縮ファイルを作成できます。Boxは、この[圧縮ダウンロードAPI][zip_downloads_api]に関するリファレンスドキュメントを更新しました。これらの新しいエンドポイントに対するSDKのサポートは、現在開発中であり、今後3か月以内にリリースする予定です。

[zip_downloads_api]: e://post_zip_downloads

## 2020年7月16日/コンテンツアクセスに関するエンタープライズイベントの変更

本日以降、[エンタープライズイベントストリーム](g://events/for-enterprise/)は、新しいコンテンツアクセスイベントの生成を開始します。

### 新しいイベント

* 新しい`CONTENT_ACCESS`イベントは、ファイルに承認済みのユーザーがアクセスしたか、プログラムによってBoxアプリケーションがアクセスしたときにトリガーされます。

イベントタイプの詳細については、[エンタープライズイベント](g://events/for-enterprise/)に関するドキュメントを参照してください。

## 2020年7月15日/メタデータクエリAPIの構文の変更

[メタデータクエリAPI][g_mdq_api]は、**明示的に定義された応答フィールドを要求する**よう更新されました。

```curl
curl -X POST https://api.box.com/2.0/metadata_queries/execute_read \
      -H 'Authorization: Bearer <ACCESS_TOKEN>" '
      -H 'Content-Type: application/json'
      -d '{
        "from": "enterprise_123456.customerInfo",
        "query": "tav >= :value",
        "query_params": {
          "value": 200000
        },
        "fields": [
          "name",
          "metadata.enterprise_123456.customerInfo.tav"
       ],
        "ancestor_folder_id": "0"
      }'
```

さらに、**この応答形式では項目のリストが返されるようになり**、クエリ結果のリストは返されなくなりました。メタデータは、項目と並んで表示されるのではなく、項目内にネストされるようになりました。応答で返されるのは、`field`配列で指定されたフィールドとすべての基本フィールドだけです。

```json
{
  "entries": [{
    "id": "394384323",
    "type": "file",
    "etag": 1,
    "name": "Contract.pdf",
    "metadata": {
      "enterprise_123456": {
        "customerInfo": {
          "$parent": "folder_12345,",
          "$scope": "enterprise_123456",
          "$template": "customerInfo",
          "$version": 1,
          "tav": 1000000
        }
      }
    }
  }]
}
```

### 従来の構文

参考までに、このAPIでは、これまで、項目のすべての標準フィールドのほか、一致したメタデータが返されていました。

```curl
curl -X POST https://api.box.com/2.0/metadata_queries/execute_read \
      -H 'Authorization: Bearer <ACCESS_TOKEN>" '
      -H 'Content-Type: application/json'
      -d '{
        "from": "enterprise_123456.customerInfo",
        "query": "tav >= :value",
        "query_params": {
          "value": 200000
        },
        "ancestor_folder_id": "0"
      }'
```

応答の本文では、項目がエントリのリストとして返され、それぞれに`item`と`metadata`インスタンスが含まれていました。

```json
{
  "entries":[
    {
      "item":{
        "type":"file",
        "id":"394384323",
        "name": "Contract.pdf",
        "file_version":{
          "type":"file_version",
          "id":"33482348",
          "sha1":"69888bb1bff455d1b2f8afea75ed1ff0b4879bf6"
        },
        ...
      },
      "metadata":{
        "enterprise_123456":{
          "customerInfo":{
            "tav": 1000000,
            "$id": "01234500-12f1-1234-aa12-b1d234cb567e",
            "$parent": "folder_12345,",
            "$scope": "enterprise_123456",
            "$template": "customerInfo",
            "$type": "customerInfo-6bcba49f-ca6d-4d2a-a758-57fe6edf44d0",
            "$typeVersion": 2,
            "$version": 1,
            "$canEdit": true
          }
        }
      }
    },
    ...
  ],
  "limit": 20,
  "next_marker":"AAAAAmVYB1FWec8GH6yWu2nwmanfMh07IyYInaa7DZDYjgO1H4KoLW29vPlLY173OKsci6h6xGh61gG73gnaxoS+o0BbI1/h6le6cikjlupVhASwJ2Cj0tOD9wlnrUMHHw3/ISf+uuACzrOMhN6d5fYrbidPzS6MdhJOejuYlvsg4tcBYzjauP3+VU51p77HFAIuObnJT0ff"
}
```

この従来の構文は、**既存のメタデータクエリAPIユーザーだけが引き続き利用できます**。既存のユーザー全員が新しい構文に移行すると、従来の構文は無効になります。

## 2020年6月11日/タスクのエンタープライズイベントの変更

本日から、[エンタープライズイベントストリーム](g://events/for-enterprise/)では、新しいタスクとタスク割り当てイベントが生成されるようになりました。また、一部の既存のタスクイベントでは追加のフィールドが返されるようになります。

### 新しいイベント

* タスクが更新されると、新しい`TASK_UPDATE`イベントがトリガーされます。
* ユーザーからタスクの割り当てが解除されると、新しい`TASK_ASSIGNMENT_DELETE`イベントがトリガーされます。

### 更新されたイベント

#### `TASK_CREATE`の変更

タスクが作成されると、このイベントでは、タスクのID (`task.id`)、タスクを作成したユーザーのID (`task.created_by.id`)、タスクの説明 (`task.message`)、およびタスクの期日 (`task.due_date`、省略可) が`additional_details`オブジェクト内に含まれるようになりました。

```json
"additional_details": {
  "task": {
    "id": "1234567",
    "due_at": "2020-07-06T10:49:44-07:00",
    "message": "task description",
    "created_by": {
      "id": 123456,
      "login": "email@example.com"
    },
    ...
  }
}
```

#### `TASK_ASSIGNMENT_CREATE`および`TASK_ASSIGNMENT_UPDATE`の変更

タスク割り当てが作成または更新されると、このイベントでは、タスクのID (`task.id`)、タスクを割り当てられたユーザーのID (`task_assignment.assigned_to.id`) とそのログイン (`task_assignment.assigned_to.login`)、タスクの説明 (`task.message`)、およびタスクの期日 (`task.due_date`、省略可) が`additional_details`オブジェクト内に含まれるようになりました。

```json
"additional_details": {
  "task": {
    "id": "1234567",
    "created_by": {
      "id": 12345,
      "login": "email@example.com"
    }
  },
  "task_assignment": {
    "assigned_to": {
      "id": 12346,
      "login": "email+2@example.com"
    },
    "status": "NOT_STARTED",
    "message": "assignee message"
  },
  ...
}
```

イベントタイプの詳細については、[エンタープライズイベント](g://events/for-enterprise/)に関するドキュメントを参照してください。

## 2020年5月12日/新しいShieldアラートイベント

本日をもって、[Box Shield][box-shield]は、新しいイベントをリッスンするよう構成されている、Shieldをご利用のお客様向けに、[エンタープライズイベントストリーム](g://events/for-enterprise/)内で新しいセキュリティイベントの生成を開始します。

Shieldによって生成される可能性があるインシデントイベントは以下のとおりです。

* 不審な場所
* 不審なセッション
* 異常なダウンロード
* 悪意のあるコンテンツ

これらのイベント内で生成されるペイロードの詳細については、[Shieldアラートイベント](g://events/shield-alert-events/)ドキュメントを参照してください。

## 2020年4月23日/ファイルの新しい`classification`フィールド

`Field`オブジェクト内で、新しいオプションフィールドである`classification`が使用可能になりました。このフィールドは、現在ファイルに適用されている分類を表します。

分類は、[ファイル情報の取得](endpoint://get-files-id)など、ファイルを返す任意のエンドポイントを介してリクエストでき、[追加`fields`のリクエスト](g://api-calls/request-extra-fields)をサポートします。

```js
{
  "id": "123456789",
  "type": "file",
  "etag": "1",
  "classification": {     
    "name": "Top Secret",     
    "definition": "Content that should not be shared outside the company.",
    "color": "#FF0000"
  },
  ...
}
```

分類は、APIまたはBox Shieldを介して設定することも、ユーザーがウェブアプリケーションを使用して設定することもできます。

## 2020年4月23日/ファイルおよびファイルバージョンに新しい`uploader_display_name`を追加

新しいフィールド`uploader_display_name`がファイルリソースとファイルバージョンリソースの両方に追加されました。このフィールドでは、アップロード時にユーザーの名前を指定します。

```json
{
  ...
  "uploader_display_name": "Ellis Wiggins"
}
```

このフィールドをファイルおよびファイルバージョンのエンドポイントのいずれかでリクエストするには、`fields`クエリパラメータを指定します。以下に例を示します。

```bash
curl -X GET https://api.box.com/2.0/files/12345?fields=uploader_display_name \
     -H 'Authorization: Bearer <ACCESS_TOKEN>'
```

ログアウトした匿名ユーザーがファイルをアップロードすると、そのユーザーのメールアドレスが返されます。使用可能なメールアドレスがない場合、このフィールドはデフォルトで`Someone`というテキストが設定されます。

## 2020年4月21日/新しいメタデータクエリAPI

新しいAPIが使用可能になりました。開発者はこのAPIを使用して、ファイルやフォルダに追加されているメタデータによってそのファイルやフォルダに対してクエリを実行できます。Boxは、こちらの[メタデータクエリAPI][e_mdq_api]のリファレンスドキュメントを更新したほか、新しい[ガイド][g_mdq_api]と[メタデータのクイックスタートガイド][qs_mdq_api]の更新をリリースしました。

[e_mdq_api]: e://post_metadata_queries_execute_read

[g_mdq_api]: g://metadata/queries

[qs_mdq_api]: g://metadata/quick-start

## 2020年3月30日/メタデータの`date`フィールドの形式に影響する可能性のある変更

Boxのメタデータインフラストラクチャに対する継続的な改善の一環として、メタデータテンプレートの`date`フィールドの形式に大きく影響する可能性のある3つの変更をリリースする予定です。これらの変更により、Box APIで返される形式は、API呼び出し間でより一貫性のあるものになります。

最初の変更は、日付におけるタイムゾーンオフセットの使用に影響します。これまでは、タイムゾーンオフセットを含めるように日付を設定した場合でも、APIによって返される日付には一貫性がなく、タイムゾーンオフセットが含まれるものもあれば、含まれないものもありました。今後は、すべての日付がUTC (協定世界時)に変換され、タイムゾーンオフセットは削除されます。

例:

* 日付が`2020-02-20T12:00:00.000-01:00`に設定されたと想定します。
* これまでは、APIでは`2020-02-20T12:00:00.000-01:00` (元の値)または`2020-02-20T13:00:00.000Z` (UTCに調整された値)が返されました。
* 今後は、常に`2020-02-20T13:00:00.000Z` (UTCに調整された値)が返されます。

2つ目の変更は、メタデータAPIによって返される日付の秒未満の精度に影響します。これまでは、メタデータAPIで返される値の秒未満の精度は0～3桁でした。今後、Boxから返されるメタデータの日時値は、常にミリ秒まで正確になります。

例:

* これまで、APIでは`2020-02-20T12:00:00Z`、`2020-02-20T12:00:00.0Z`、`2020-02-20T12:00:00.00Z`または`2020-02-20T12:00:00.000Z`が返される可能性がありました。
* 今後は、常に`2020-02-20T12:00:00.000Z`が返されます。

最後の変更は、メタデータインスタンスの更新時の[`test`](g/metadata/instances/update/#Test-a-value)操作の使用に影響します。これまでのテストでは、リテラル文字列値を使用して日時の値が比較されていました。今回の更新後は、UNIXのタイムスタンプ(ミリ秒単位)を使用して比較されます。

例:

* これまでは、`2020-01-21T19:20:00.123-08:00`は`2020-01-22T03:20:00.123Z`と同じではありませんでした。
* 今後、`2020-01-21T19:20:00.123-08:00`は`2020-01-22T03:20:00.123Z`と同じになります。

### 今回の変更がアプリケーションに及ぼす影響

`RFC3339`に準拠した日時の解析を実装するアプリケーションでは、これらはすべて同じ日付を表す有効な`RFC3339`値であるため、何も実行する必要はありません。`RFC3339`に準拠した日時の解析を実装していないアプリケーションは、解析を実行できるように更新する必要があります。

すべてのBox公式SDKでは、`RFC3339`に準拠した日時の解析をサポートしているため、Box公式SDKの最新バージョンを使用しているアプリケーションの場合、更新は不要です。

### 今回の変更がアプリケーションに影響を及ぼした場合の対応

今回の変更は、今後数週間かけて段階的にリリースする予定です。メタデータチームは潜在的な影響を監視しますが、ご自身が影響を受けていることに気付いた場合は、カスタマーサクセスマネージャまたは当社のサポートチャネルまでお問い合わせください。

## 2020年3月30日/メタデータインスタンスのバージョンに対する変更

メタデータインフラストラクチャに対する継続的な改善の一環として、メタデータインスタンスの[`version`](r:/metadata/#param-$version)値に対する小規模な変更をリリースします。

これまで、メタデータインスタンスに関連付けられたバージョン番号は、関連付けられたメタデータテンプレートのフィールドが削除された場合または関連付けられたメタデータテンプレートで`enum`フィールドのオプションが削除された場合に増加していました。

今後は、こうした変更により、メタデータインスタンスのバージョン番号が増加することはありません。

バージョン番号の増加は常に暗黙的に行われてきたため、Boxでは、メタデータインスタンスのバージョンから得られる価値はほとんどないと認識しています。そのため、この変更がお客様に影響を及ぼすとは考えておりません。それでも、ご自身が影響を受けていることに気付いた場合は、カスタマーサクセスマネージャまたは当社のサポートチャネルまでお問い合わせください。

## 2020年2月3日/Preview SDK `v2.34.0`のリリース

Preview SDKのバージョン`2.34.0`がリリースされ、新しいJavaScriptとCSSのPreviewファイルが使用可能になりました。新しい変更を導入するには、Content Preview用のリンクを[UI Elementの手動によるインストール][ui-elements-manual-install]に関する記事で確認してください。

機能の変更点の全一覧については、`v2.34.0`の[リリースノート][preview-2.34-release-notes]を参照してください。

## 2020年1月22日/Preview SDK `v2.33.1`のリリース

Preview SDKのバージョン`2.33.1`がリリースされ、新しいJavaScriptとCSSのPreviewファイルが使用可能になりました。新しい変更を導入するには、Content Preview用のリンクを[UI Elementの手動によるインストール][ui-elements-manual-install]に関する記事で確認してください。

機能の変更点の全一覧については、`v2.33.1`の[リリースノート][preview-2.33-release-notes]を参照してください。

## 2020年1月20日/Postmanコレクションとクイックスタートを更新

Box Postmanコレクションは、新機能と、統合されたクイックスタートガイドによって更新されました。主な機能は以下のとおりです。

* エンドツーエンドの[Postmanクイックスタートガイド][postman-quick-start-guide]。これは、ユーザーがPostmanのインストール、Boxアプリの設定、PostmanへのAPI資格情報の読み込みを行う際に役立ちます。
* Box API用に[再編成されたPostmanコレクション][postman-collection]。これにより、API資格情報の期限切れが自動的に検出され、必要に応じてこれらの資格情報を更新するための統合ソリューションが提供されます。

しばらくの間は[従来のPostmanコレクション][legacy-postman-collection]も引き続きご利用いただけます。

[box-shield]: https://www.box.com/shield

[postman-quick-start-guide]: g://tooling/postman/quick-start

[postman-collection]: g://tooling/postman/install

[legacy-postman-collection]: g://tooling/postman/legacy

[ui-elements-manual-install]: g://embed/ui-elements/installation/#manual-installation

[preview-2.34-release-notes]: https://github.com/box/box-content-preview/releases/tag/v2.34.0

[preview-2.33-release-notes]: https://github.com/box/box-content-preview/releases/tag/v2.33.1
