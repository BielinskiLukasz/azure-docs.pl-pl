<properties
 pageTitle="IoT Hub に関するガイダンス | Microsoft Azure"
 description="Azure IoT Hub を使用して IoT ソリューションを開発するためのゲートウェイ、デバイス プロビジョニング、認証に関するガイダンス トピックです。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="dobett"/>


# ソリューションの設計

この記事では、IoT ソリューションで次の機能を設計する方法について説明します。

- デバイス プロビジョニング
- フィールド ゲートウェイ
- デバイスの認証

## デバイス プロビジョニング

IoT ソリューションでは、次のような個々のデバイスに関するデータを格納します。

- デバイスの ID と認証キー
- デバイス ハードウェアの種類とバージョン
- デバイスの状態
- ソフトウェアのバージョンと機能
- デバイス コマンドの履歴

特定の IoT ソリューションに格納されるデバイス データは、そのソリューションの具体的な要件によって異なりますが、ソリューションは、少なくともデバイスの ID と認証キーを格納する必要があります。 IoT Hub を含む、 [identity レジストリ ][lnk-devguide-identityregistry] 各デバイス id、認証キー、ステータス コードなどの値を格納することができます。 ソリューションでは、テーブル、BLOB、DocumentDB など、その他の Azure サービスを使用して、追加のデバイス データを格納できます。

*デバイス プロビジョニング*とは、最初のデバイス データをソリューション内のストアに追加するプロセスです。 新しいハブに接続するためにデバイスを有効にするのには、新しいデバイス id とキーを追加する必要があります、 
[IoT Hub identity レジストリ ][lnk-devguide-identityregistry]します。 プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

記事 [IoT Hub デバイス管理のガイダンス ][lnk-device-management] デバイスのプロビジョニング用の一般的な戦略について説明します。  [IoT Hub identity レジストリ Api ][lnk-devguide-identityregistry] 、プロビジョニング プロセスに IoT Hub を統合することできます。

## フィールド ゲートウェイ

IoT ソリューションでは、*フィールド ゲートウェイ*はデバイスと IoT Hub の間に配置され、通常はデバイスの近くにあります。 デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信し、フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub と通信します。 フィールド ゲートウェイには、専用のスタンドアロン デバイスを使用することも、既存のハードウェアで動作するソフトウェアを使用することもできます。

フィールド ゲートウェイは、通常、ソリューションにおけるアクセスと情報フローの管理において大きな役割を果たすため、単純なトラフィック ルーティング デバイス (NAT デバイスやファイアウォールなど) とは異なります。 たとえば、フィールド ゲートウェイでは次のことができます。

- ローカル デバイスを管理する。 たとえば、フィールド ゲートウェイは、イベント ルールの処理を実行したり、特定のテレメトリ データに応答してデバイスにコマンドを送信したりできます。
- テレメトリ データを IoT Hub に転送する前にフィルター処理または集計する。 これにより、IoT Hub に送信されるデータの量を減らし、場合によってはソリューションのコストを削減することができます。
- デバイスのプロビジョニングを支援する。
- テレメトリ データを変換し、ソリューションのバックエンドでの処理を容易にする。
- プロトコルの変換を実行し、デバイスが IoT Hub でサポートされているトランスポート プロトコルを使用しない場合でも IoT Hub と通信できるようにする。

> [AZURE.NOTE] 通常、デバイスにローカル フィールド ゲートウェイを展開するときに一部のシナリオで展開する、 [プロトコル ゲートウェイ ][lnk-gateway] クラウドにします。

### フィールド ゲートウェイのタイプ

フィールド ゲートウェイは、*透過的*または*非透過的*にすることができます。

| &nbsp;| 透過的| 非透過的|
|--------|-------------|--------|
| IoT Hub ID レジストリに格納される ID| 接続されているすべてのデバイス| フィールド ゲートウェイの ID のみ|
| IoT Hub はを提供できる [デバイス アンチ-なりすまし ][lnk-devguide-antispoofing]| あり| いいえ|
| [スロットルおよびクォータ ][lnk-throttles-quotas]| 各デバイスに適用| フィールド ゲートウェイに適用します。|

### その他の考慮事項

使用することができます、 [Azure IoT デバイス Sdk ][lnk-device-sdks] フィールド ゲートウェイを実装します。 一部のデバイス SDK には、複数のデバイスから同じ接続を経由した IoT Hub への通信を多重化する機能など、フィールド ゲートウェイの実装に役立つ固有の機能が用意されています。 説明したよう [通信プロトコル ][lnk-devguide-protocol], 、トランスポート プロトコルとして HTTP/1 を使用してフィールド ゲートウェイを避ける必要があります。

## カスタム デバイスの認証

IoT Hub を使用する [デバイス identity レジストリ ][lnk-devguide-identityregistry] デバイスごとのセキュリティ資格情報を構成して、アクセス制御をします。 ただし、IoT ソリューションで既にカスタム デバイス ID レジストリや認証スキームにかなり投資している場合、*トークン サービス*を作成すると、この既存のインフラストラクチャを IoT Hub と統合できます。 この方法により、ソリューションで他の IoT 機能を使用できます。

トークン サービスはカスタム クラウド サービスの 1 つです。このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub *共有アクセス ポリシー*を使用して、デバイスが IoT Hub に接続できるようにする、*デバイスを対象とする*トークンを作成します。

  ![][img-tokenservice]

トークン サービス パターンの主な手順を次に示します。

1. 作成、 [IoT Hub 共有アクセス ポリシー ][lnk-devguide-security] と **DeviceConnect** IoT hub のアクセスを許可します。 このポリシーを作成することができます、 [Azure ポータルの ][lnk-portal] またはプログラムです。 トークン サービスは、このポリシーを使用して、作成されるトークンに署名します。
2. IoT Hub にアクセスする必要があるデバイスは、トークン サービスに署名付きトークンを要求します。 デバイスは、カスタム デバイス ID レジストリ/認証スキームで認証し、トークン サービスがトークンの作成に使用するデバイス ID を特定できます。
3. として 1 がトークン、トークンのサービスを返しますが作成された [IoT Hub 開発者ガイド - セキュリティ ][lnk-devguide-security], を使用して、 `/devices/{deviceid}` として `resourceURI`, と `deviceId` として認証されるデバイス。 トークン サービスは、共有アクセス ポリシーを使用してトークンを作成します。
4. デバイスは、IoT Hub で直接トークンを使用します。

> [AZURE.NOTE] .NET クラスを使用して [SharedAccessSignatureBuilder ][lnk-dotnet-sas] または Java クラス [IotHubServiceSasToken ][lnk-java-sas] トークン サービスにトークンを作成します。

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。 トークンの期限が切れた時点で、IoT Hub はデバイスへの接続を切断するため、デバイスは新しいトークンをトークン サービスに要求する必要があります。 有効期限までの期間を短くすると、デバイスとトークン サービスの両方に対する負荷が増加します。

デバイスをハブに接続するには、デバイスが接続にデバイス キーではなくトークンを使用している場合でも、引き続き IoT Hub のデバイス ID レジストリにデバイスを追加する必要があります。 そのため、引き続きできますデバイスごとのアクセス制御を使用するには、有効化または内のデバイス id を無効にすると、 [IoT Hub identity レジストリ ][lnk-devguide-identityregistry] トークンを使用して、デバイスを認証するとき。 これにより、有効期限までの期間が長い場合にトークンを使用するリスクが軽減されます。

### カスタム ゲートウェイとの比較

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。これは、IoT Hub がほとんどのソリューション トラフィックの処理を続行するためです。 ただし、カスタム認証方式がプロトコルで絡み合ってので場合もあります (たとえば、 [TLS PSK ][lnk-tls-psk]) をすべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要です。 詳細については、次を参照してください。、 [プロトコル ゲートウェイ ][lnk-gateway] トピックです。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [IoT Hub (チュートリアル) ][lnk-get-started]
- [Azure の IoT Hub とは何ですか。][lnk-what-is-hub]


[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png 
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry 
[lnk-device-management]: iot-hub-device-management.md 
[lnk-device-sdks]: iot-hub-sdks-summary.md 
[lnk-devguide-security]: iot-hub-devguide.md#security 
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279 
[lnk-gateway]: iot-hub-protocol-gateway.md 
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md 
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md 
[lnk-portal]: https://portal.azure.com 
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits 
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing 
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp 
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx 
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html 

