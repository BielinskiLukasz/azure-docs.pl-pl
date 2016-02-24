<properties 
   pageTitle="Azure Automation の証明書資産 | Microsoft Azure"
   description="証明書を Azure Automation に安全に保存し、Runbook または DSC 構成でアクセスして Azure およびサードパーティのリソースで認証できます。  この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/23/2015"
   ms.author="bwren" />

# Azure Automation の証明書資産

Runbook または DSC 構成を使用してアクセスできるように、Azure Automation で証明書を安全に保存することができます、 **Get-automationcertificate** アクティビティ。 そのため、認証に証明書を使用する Runbook または DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

>[AZURE.NOTE] Azure Automation でセキュリティで保護された資産には、資格情報、証明書、接続、および暗号化された変数が含まれます。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。 このキーはマスター証明書によって暗号化され、Azure Automation に保存されます。 セキュリティで保護された資産を格納する前に、Automation アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。

## Windows PowerShell コマンドレット

Windows PowerShell で Automation 証明書資産を作成および管理するには、次の表のコマンドレットを使用します。 一部として出荷される、 [Azure PowerShell モジュール](../powershell-install-configure.md) Automation の runbook と DSC 構成で使用することができます。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|証明書に関する情報を取得します。 Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。|
|[New-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|新しい証明書を Azure Automation にインポートします。|
|[Remove-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|証明書を Azure Automation から削除します。|
|[Set-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。|

## 証明書にアクセスするアクティビティ

Runbook または DSC 構成で証明書にアクセスするために、次の表のアクティビティが使用されます。

|アクティビティ|説明|
|:---|:---|
|Get-AutomationCertificate|Runbook または DSC 構成で使用する証明書を取得します。|

>[AZURE.NOTE] 変数の使用を避ける必要がありますので、これは、runbook または DSC 構成の間の依存関係の検出が複雑になるし、証明書資産のデザイン時に GetAutomationCertificate の – Name パラメーターでします。

## 新しい証明書の作成

新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。 証明書をエクスポート可能とマークすると、Azure Automation の証明書ストアから転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。

### Azure ポータルで新しい証明書を作成するには

1. オートメーション アカウントからクリックして **資産** ウィンドウの上部にあります。
1. At the bottom of the window, click **Add Setting**.
1. Click **Add Credential**.
2. In the **Credential Type** dropdown, select **Certificate**.
3. Type a name for the certificate in the **Name** box and click the right arrow.
4. Browse for a .cer or .pfx file.  .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
1. チェック マークをクリックして証明書ファイルをアップロードし、新しい証明書資産を保存します。


### Azure プレビュー ポータルで新しい証明書を作成するには

1. クリックして、オートメーション アカウントから、 **資産** を開くには一部、 **資産** ブレードです。
1. クリックして、 **証明書** を開くには一部、 **証明書** ブレードです。
1. クリックして **証明書を追加** ブレードの上部にあります。
2. Type a name for the certificate in the **Name** box.
2. Click **Select a file** under **Upload a certificate file** to browse for a .cer or .pfx file.  .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
1. Click **Create** to save the new certificate asset.


### Windows PowerShell で新しい証明書を作成するには

次のサンプル コマンドでは、新しいオートメーション証明書を作成してエクスポート可能に指定する方法について説明します。 これは既存の pfx ファイルをインポートします。

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    
    New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## 証明書の使用

使用する必要があります、 **Get-automationcertificate** 証明書を使用するアクティビティ。 使用することはできません、 [Get-azureautomationcertificate](http://msdn.microsoft.com/library/dn913765.aspx) コマンドレットでは証明書自体ではない証明書資産に関する情報を返すためです。

### テキストの Runbook のサンプル

次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AutomationVariable –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### グラフィカルな Runbook のサンプル

追加する、 **Get-automationcerticiate** をグラフィカル エディターの [ライブラリ] ウィンドウで証明書を右クリックし、グラフィカルな runbook に **[キャンバスに追加**します。

![](media/automation-certificates/certificate-add-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。  これは、テキストの Runbook からクラウド サービスに証明書を追加する前述の例と同じです。  

この例では、 **UseConnectionObject** パラメーターは、送信の設定**TwilioSMS アクティビティ** 、サービスに対する認証に接続オブジェクトを使用します。  A [パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow) シーケンス リンクは Connection パラメーターが予期しないする 1 つのオブジェクトを含むを含むコレクションを返すためここで使用する必要があります。

![](media/automation-certificates/add-certificate.png)


## 関連項目

- [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow) 

