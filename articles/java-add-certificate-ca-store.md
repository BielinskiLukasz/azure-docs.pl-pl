<properties 
    pageTitle="Java CA ストアへの証明書の追加 | Microsoft Azure" 
    description="Twilio サービスまたは Azure Service Bus に必要な証明機関 (CA) の証明書を Java CA 証明書 (cacerts) ストアに追加する方法について説明します。" 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="robmcm"/>


# 証明書を Java CA 証明書ストアに追加する方法

次のステップでは、証明機関 (CA) の証明書を Java CA 証明書 (cacerts) ストアに追加する方法について説明します。 例として使用するのは Twilio サービスに必要な CA 証明書です。 このトピックの後半では、Azure Service Bus 用の CA 証明書をインストールする方法について説明します。

JDK を圧縮して Azure プロジェクトの **approot** フォルダーに追加する前に、keytool を使用して CA 証明書を追加できます。または、keytool を使用して証明書を追加する Azure スタートアップ タスクを実行することもできます。 この例では、JDK を圧縮する前に CA 証明書を追加するとします。 また、特定の CA 証明書をこの例で使用しますが、別の CA 証明書を取得して使用する場合でも、証明書を cacerts ストアにインポートする手順は同様です。

## 証明書を cacerts ストアに追加するには

1. JDK の **jdk\jre\lib\security** フォルダーに設定されたコマンド プロンプトで、次のコマンドを実行して、インストールされている証明書を確認します。

    `keytool -list -keystore cacerts`

    ストアのパスワードを入力するように求められます。既定のパスワードは **changeit** です(パスワードを変更する場合にある keytool のドキュメントを参照してください <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>。)。この例では、MD5 フィンガープリントが 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 の証明書が一覧に表示されないため、その証明書をインポートするとします (この証明書は Twilio API サービスに必要)。
2. 記載されている証明書の一覧から証明書を入手 [GeoTrust Root Certificates](http://www.geotrust.com/resources/root-certificates/)します。 シリアル番号が 35:DE:F4:CF の証明書のリンクを右クリックし、その証明書を **jdk\jre\lib\security** フォルダーに保存します。 この例の目的で、という名前のファイルに保存された **Equifax\_Secure\_Certificate\_Authority.cer**します。
3. 次のコマンドを使用して証明書をインポートします。

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    この証明書を信頼するかどうかをたずねられたら、証明書の MD5 フィンガープリントが 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 であれば、「**y**」と入力して応答します。
4. 次のコマンドを実行して CA 証明書が正常にインポートされたことを確認します。

    `keytool -list -keystore cacerts`

5. JDK を圧縮し、Azure プロジェクトの **approot** フォルダーに追加します。

Keytool の詳細については、次を参照してください。 <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>します。

## Azure のルート証明書

Azure のサービス (Azure Service Bus など) を使用するアプリケーションでは、Baltimore CyberTrust Root 証明書を信頼する必要があります (2013 年 4 月 15 日より、Azure は GTE CyberTrust Global Root から Baltimore CyberTrust Root への移行を開始しました。 移行は数か月かけて完了しました)。

Baltimore 証明書が cacerts ストアに既にインストールされている場合があるため、まず **keytool -list** コマンドを実行して、それらの証明書が既に存在するかどうかを確認することを忘れないでください。

Baltimore CyberTrust Root を追加する必要がある場合、そのシリアル番号は ‎02:00:00:b9、SHA1 フィンガープリントは d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74 です。ダウンロードできます <https://cacert.omniroot.com/bc2025.crt>, 拡張子を持つローカル ファイルに保存して、 **.cer**, を使用して、インポート **keytool** 上記のようにします。

## 次のステップ

Azure によって使用されるルート証明書の詳細については、次を参照してください。 [Azure ルート証明書の移行](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)します。

Java の詳細については、次を参照してください。、 [Java デベロッパー センター](/develop/java/)します。





