<properties
  pageTitle="Mobile Apps と Mobile Services のクライアントとサーバーの SDK バージョン管理 | Azure App Service"
  description="Mobile Services と Azure Mobile Apps のクライアント SDK およびサーバー SDK バージョンとの互換性の一覧"
  services="app-service\mobile"
  documentationCenter=""
  authors="lindydonna" 
  manager="dwrede"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="12/15/2015"
  ms.author="donnam"/>


# Mobile Apps と Mobile Services のクライアントとサーバーのバージョン管理

Azure Mobile Services の最新版は Azure App Service の **Mobile Apps** 機能です。



Mobile Apps のクライアント/サーバー SDK は Mobile Services のクライアント/サーバー SDK にもともと基づいていますが、互いに互換性が*ありません*。 つまり、*Mobile Apps* クライアント SDK と共に *Mobile Apps* サーバー SDK を使用する必要があり、*Mobile Services* の場合も同様です。 このコントラクトは、クライアントとサーバーの Sdk で使用される特殊なヘッダーの値によって設定 `ZUMO API バージョン`します。

注: 本書で *Mobile Services* に言及するとき、それは必ずしも Mobile Services にホストすることを意味しません。 これはコード変更を加えずに App Service で実行するモバイル サービスを移行することですが、サービスを使用する場合 *Mobile Services*  SDK のバージョン。

コード変更を加えず App Service への移行に関する詳細については、「[Azure App Service へのモバイル サービスの移行]」を参照してください。

## ヘッダーの仕様

キー `ZUMO API バージョン` HTTP ヘッダーまたはクエリ文字列のいずれかで指定することがあります。 値は「**x.y.z**」形式のバージョン文字列です。

次に例を示します。

Https://service.azurewebsites.net/tables/TodoItem を取得します。

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## バージョン チェックの除外

バージョン チェックを除外できます。その場合、アプリ設定 **MS_SkipVersionCheck** に **true** 値を設定します。 これは web.config か Azure ポータルの [アプリケーション設定] セクションで指定します。
> [AZURE.NOTE] Mobile Services と Mobile Apps の間には、特にオフライン同期、認証、プッシュ通知の領域で、さまざまな動作変更があります。 テストを完了し、動作変更によりアプリの機能停止がないことを確認してからバージョン チェックを除外してください。

## すべてのバージョンの互換性のまとめ

以下の表は、あらゆる種類のクライアント/サーバー間の互換性をまとめたものです。 バックエンドは、それが使用するサーバー SDK に基づき、Mobile **Services** または Mobile **Apps** として分類されます。

| | **Mobile Services** Node.js または .NET| **Mobile Apps** Node.js または .NET|
| ----------                | -----------------------             |   ----------------              |
| [モバイル サービス クライアント]| [OK]| エラー *|
| [モバイル アプリ クライアント]| エラー *| [OK]|

指定することによって制御できます \*This **MS_SkipVersionCheck**します。





## <a name="1.0.0"></a>モバイル サービス クライアントとサーバー

下の表にあるクライアント SDK は **Mobile Services** と互換性があります。

注: Mobile Services クライアント Sdk *しない* ヘッダー値を送る `ZUMO API バージョン`します。 サービスがこのヘッダーやクエリ文字列値を受け取った場合、前述のように明示的に除外しない限り、エラーが返されます。

### <a name="MobileServicesClients"></a> モバイル *サービス* クライアント Sdk

| クライアント プラットフォーム| バージョン| バージョンのヘッダー値|
| -------------------               | ------------------------                                                  | -------------------  |
| 管理されたクライアント (Windows、Xamarin)| [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2)| 該当なし|
| iOS| [2.2.2](http://aka.ms/gc6fex)| 該当なし|
| Android| [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)| 該当なし|
| HTML| [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js)| 該当なし|

### Mobile *Services* サーバー SDK

| サーバー プラットフォーム| バージョン| 同意済みのバージョン ヘッダー|
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET| [WindowsAzure.MobileServices.Backend.* バージョン 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/)| ** バージョン ヘッダーなし **|
| Node.js| (近日対応予定)| **バージョン ヘッダーなし**|



### Mobile Services バックエンドの動作

| ZUMO-API-VERSION| MS_SkipVersionCheck の値| 応答|
| ---------------- | ---------------------------- | -------- |
| 指定なし| 任意| 200 - OK|
| 任意の値| True| 200 - OK|
| 任意の値| 偽/指定なし| 400 - 正しくない要求|

## <a name="2.0.0"></a>Azure のモバイル アプリのクライアントとサーバー

### <a name="MobileAppsClients"></a> モバイル *アプリ* クライアント Sdk

バージョン チェックは **Azure Mobile Apps** の次のバージョンのクライアント SDK から導入されました。

| クライアント プラットフォーム| バージョン| バージョンのヘッダー値|
| -------------------               | ------------------------  | -----------------    |
| 管理されたクライアント (Windows、Xamarin)| [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0)| 2.0.0|
| iOS| [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823)| 2.0.0|
| Android| [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409)| 3.0.0|



### Mobile *Apps* サーバー SDK

バージョン チェックは次のサーバー SDK バージョンに含まれています。

| サーバー プラットフォーム| SDK| 同意済みのバージョン ヘッダー|
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET| [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)| 2.0.0|
| Node.js| [azure モバイル アプリ バージョン 1.0-ベータ 1 (またはそれ以降)](https://www.npmjs.com/package/azure-mobile-apps)| 2.0.0|

### Mobile Apps バックエンドの動作

| ZUMO-API-VERSION| MS_SkipVersionCheck の値| 応答|
| ---------------- | ---------------------------- | -------- |
| x.y.z または Null| True| 200 - OK|
| Null| 偽/指定なし| 400 - 正しくない要求|
| 1.x.y| 偽/指定なし| 400 - 正しくない要求|
| 2.0.0-2.x.y| 偽/指定なし| 200 - OK|
| 3.0.0-3.x.y| 偽/指定なし| 400 - 正しくない要求|


## 次のステップ

- [モバイル サービスを Azure App Service に移行する]



[mobile services clients]: #MobileServicesClients 
[mobile apps clients]: #MobileAppsClients 
[mobile app server sdk]: http://www.nuget.org/packages/microsoft.azure.mobile.server 
[migrate a mobile service to azure app service]: app-service-mobile-migrating-from-mobile-services.md 

