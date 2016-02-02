<properties
    pageTitle="REST を使用し、Azure Web アプリをバックアップし、復元する"
    description="RESTful API 呼び出しを使用し、Azure App Service で Web アプリをバックアップし、復元する方法について説明します"
    services="app-service"
    documentationCenter=""
    authors="nking92"
    manager="edlauare"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="nicking"/>


# REST を使用し、Azure Web アプリをバックアップし、復元する

[Azure Web Apps](https://azure.microsoft.com/services/app-service/web/) blob として Azure ストレージにバックアップすることができます。 バックアップには、アプリのデータベースを含めることもできます。 アプリが誤って削除された場合、あるいは以前のバージョンに戻す必要がある場合、前のバックアップから復元できます。 バックアップは必要に応じていつでも実行できます。あるいは、適切な間隔でバックアップのスケジュールを作成できます。

この記事では、RESTful API 要求で Azure Web アプリをバックアップし、復元する方法について説明します。 作成して、Azure ポータルでグラフィカルに web アプリのバックアップを管理する場合は、を参照してください [Azure App Service で web アプリのバックアップ](web-sites-backup.md)。

<a name="gettingstarted"></a>
## Getting Started (概要)

REST 要求を送信するには、Web アプリの**名前**、**リソース グループ**、**サブスクリプション ID** を把握しておく必要があります。 Web アプリをクリックしてこの情報が見つかりません、 **Web Apps** のブレード、 [Azure プレビュー ポータル](https://portal.azure.com)します。 この記事の例については、私たちを構成する web サイト `backuprestoreapiexamples.azurewebsites.net`します。 これは Default-Web-WestUS リソース グループに保存され、ID 00001111-2222-3333-4444-555566667777 のサブスクリプションで実行されます。

![サンプル Web サイト情報][samplewebsiteinformation]

<a name="backup-restore-rest-api"></a>
## REST API のバックアップと復元

REST API を利用して Azure Web アプリをバックアップし、復元する方法の例をこれからいくつか取り上げます。 それぞれの例に、URL と HTTP の要求本文が含まれます。 サンプル URL には、{subscriptionId} のように、中括弧で閉じられたプレースホルダーが含まれます。 このプレースホルダーを Web アプリのそれに該当する情報で置き換えます。 参照のために、サンプル URL の各プレースホルダーの説明を記載します。

* subscriptionId – Web アプリを含む Azure サブスクリプションの ID
* resourceGroupName – Web アプリを含むリソース グループの名前
* sitename – Azure Web アプリの名前
* backupId – Web アプリのバックアップの ID

HTTP 要求に含めることがいくつかの省略可能なパラメーターを含む、API の詳細なドキュメントを参照してください、 [Azure リソース エクスプ ローラー](https://resources.azure.com/)します。

<a name="backup-on-demand"></a>
## Web アプリのオンデマンド バックアップ

Web アプリをすぐにバックアップするには、送信、 **POST** 要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`します。

URL は次の例 web サイトを使用するようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

バックアップの保存に使用するストレージ アカウントを指定するには、要求の本文に JSON オブジェクトを指定する必要があります。 JSON オブジェクトはという名前のプロパティを持つ必要があります **storageAccountUrl**, 、どのが保持している、 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) バックアップ blob を保持する Azure ストレージ コンテナーへの書き込みアクセスを許可します。 データベースをバックアップする場合、バックアップするデータベースの名前、種類、接続文字列を含む一覧を提供する必要もあります。

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

要求が受信された直後に Web アプリのバックアップが開始します。 バックアップ プロセスの完了には時間がかかる場合があります。 HTTP 応答には ID が含まれます。この ID を別の要求で利用し、バックアップの状態を確認できます。 次に、バックアップ要求に対する HTTP 応答の本文の例を示します。

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] エラー メッセージは HTTP 応答のログ プロパティにあります。

<a name="schedule-automatic-backups"></a>
## 自動バックアップのスケジュールを作成する

Web アプリをオンデマンド バックアップするだけでなく、バックアップのスケジュールを作成し、自動実行することもできます。

### 新しい自動バックアップ スケジュールを設定する

バックアップのスケジュールを設定するには、送信、 **配置** 要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`します。

例の web サイトの URL は次のようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

要求本文には、バックアップ構成を指定する JSON オブジェクトを含める必要があります。 次の例にはすべての必須パラメーターが含まれています。

```
{
    "location": "WestUS",
    "properties": // Represents a web app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

この例では、7 日ごとに自動的にバックアップするようにアプリが設定されます。 パラメーターの **frequencyInterval** と **frequencyUnit** の両方によりバックアップの頻度が決定されます。 **frequencyUnit** の有効な値は **hour** と **day** です。 たとえば、12 時間ごとにアプリをバックアップするには、frequencyInterval を「12」に設定し、frequencyUnit を「hour」に設定します。

古いバックアップはストレージ アカウントから自動的に削除されます。 **retentionPeriodInDays** パラメーターでバックアップの保存期間を指定できます。 保存期間に関係なく、常に少なくとも 1 つのバックアップを保存する場合、**keepAtLeastOneBackup** を「true」に設定します。

### 自動バックアップ スケジュールを取得する

Web アプリのバックアップの構成を取得するには、送信、 **POST** URL に要求 ` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`します。

この例のサイトの URL は、 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`します。

<a name="get-backup-status"></a>
## バックアップのステータスを取得する

Web アプリの規模によっては、バックアップの完了に時間がかかる場合があります。 バックアップは失敗したり、タイムアウトになったり、部分的に成功したりすることもあります。 すべての web アプリのバックアップの状態を表示するには、送信、 **取得** URL に要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`します。

特定のバックアップの状態を表示するには、URL に GET 要求を送信 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`します。

例の web サイトの URL は次のようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

応答本文には、この例のような JSON オブジェクトが含まれます。

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

バックアップの状態は列挙型です。 次のような状態があります。

* 0 – InProgress: バックアップは開始されていますが、まだ完了していません。
* 1 – Failed: バックアップは失敗しました。
* 2 – Succeeded: バックアップは完了しました。
* 3 – TimedOut: バックアップは時間どおり完了せず、取り消されました。
* 4 – Created: バックアップ要求が待ち行列に入っていますが、開始されていません。
* 5 – Skipped: スケジュールで始動したバックアップの数が多すぎたため、バックアップが進行しませんでした。
* 6 – PartiallySucceeded: バックアップは完了しましたが、読み取れなかったため、バックアップされなかったファイルがあります。 これは通常、ファイルの排他ロックに起因して発生します。
* 7 – DeleteInProgress: バックアップの削除が要求されていますが、まだ削除されていません。
* 8 – DeleteFailed: バックアップを削除できませんでした。 バックアップの作成に使用された SAS URL が失効している可能性があります。
* 9 – Deleted: バックアップは削除されました。

<a name="restore-web-app"></a>
## Web アプリをバックアップから復元する

Web アプリが削除された場合、または以前のバージョンに Web アプリを戻す場合、バックアップからアプリを復元できます。 呼び出すには、復元、送信、 **POST** URL に要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`します。

例の web サイトの URL は次のようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

要求本文で、復元操作のプロパティを含む JSON オブジェクトを送信します。 次の例にすべての必須プロパティが含まれています。

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### 新しい Web アプリに復元する

バックアップを復元するとき、既存の Web アプリを上書きする代わりに、新しい Web アプリを作成したほうが便利な場合があります。 その場合、作成する新しい Web アプリを指すように要求 URL を変更し、JSON の **overwrite** プロパティを「**false**」に変更します。

<a name="delete-app-backup"></a>
## Web アプリ バックアップの削除

バックアップを削除したい場合は、送信、 **削除** URL に要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`します。

例の web サイトの URL は次のようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## バックアップの SAS URL を管理する

Azure Web アプリは、バックアップの作成時に提供された SAS URL を利用して Azure Storage からバックアップを削除しようとします。 この SAS URL が無効になっている場合、REST API でバックアップを削除することはできません。 ただし、送信することによって、バックアップに関連付けられた SAS URL を更新することができます、 **POST** URL に要求 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list`します。

例の web サイトの URL は次のようになります。 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

要求本文で、新しい SAS URL を含む JSON オブジェクトを送信します。 たとえば次のようになります。

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] セキュリティ上の理由から、特定のバックアップの GET 要求を送信するとき、バックアップに関連付けられている SAS URL は返されません。 バックアップに関連付けられている SAS URL を表示する場合、POST 要求を上記と同じ URL に送信し、空の JSON オブジェクトを要求本文に追加します。 サーバーからの応答に、SAS URL を含む、そのバックアップのすべての情報が含まれます。



[samplewebsiteinformation]: ./media/websites-csm-backup/01siteconfig.png 

