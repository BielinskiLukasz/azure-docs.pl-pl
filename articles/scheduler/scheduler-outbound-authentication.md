<properties 
 pageTitle="Scheduler 送信認証" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>


# Scheduler 送信認証

Scheduler ジョブでは、認証を必要とするサービスを呼び出すことが必要になる場合があります。 このようにして、呼び出されたサービスは、Scheduler ジョブがそのリソースにアクセスできるかどうかを確認できます。 このようなサービスには、他の Azure サービス、Salesforce.com、Facebook、およびセキュリティで保護されたカスタム Web サイトが含まれます。

## 認証の追加と削除

スケジューラ ジョブへの認証は単純です、JSON の子要素の追加を追加する `認証` に、 `要求` 要素を作成すると、ジョブを更新します。 一部として、PUT、PATCH、または POST 要求スケジューラ サービスに渡されたシークレット、 `認証` オブジェクト – は応答で返されません。 応答では、シークレット情報は null に設定されます。または、認証済みのエンティティを表す公開トークンが含まれる場合があります。

認証を削除する PUT または PATCH ジョブを明示的に設定する、 `認証` オブジェクトを null にします。 応答に認証プロパティが含まれることはありません。

現在、唯一サポートされている認証の種類は、 `ClientCertificate` モデル (SSL/TLS クライアント証明書を使用)、 `基本的な` モデル (基本認証の場合) および `ActiveDirectoryOAuth` モデル (Active Directory の OAuth 認証します)。

## ClientCertificate 認証の要求本文

使用して認証を追加するときに、 `ClientCertificate` モデル、要求本文に次の要素を指定します。

| 要素| 説明|
|:---|:---|
| (親要素) (_a) _| SSL クライアント証明書を使用するための認証オブジェクト。|
| _type_| 必須。認証の種類。SSL クライアント証明書の値がある必要があります `ClientCertificate`します。|
| _pfx_| 必須。Base64 でエンコードされた PFX ファイルのコンテンツ。|
| _password_| 必須。PFX ファイルにアクセスするためのパスワード。|


## ClientCertificate 認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素| 説明|
|:--|:--|
| (親要素) (_a) _| SSL クライアント証明書を使用するための認証オブジェクト。|
| _type_| 認証の種類。SSL クライアント証明書、値は `ClientCertificate`します。|
| _certificateThumbprint_| 証明書の拇印。|
| _certificateSubjectName_| 証明書のサブジェクト識別名。|
| _certificateExpiration_| 証明書の有効期限日。|

## ClientCertificate 認証の要求と応答の例

次のサンプル要求が組み込まれた PUT 要求は、 `ClientCertificate` 認証します。 要求は、次のとおりです。


    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Content-Length: 4013
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication": {
            "type": "clientcertificate",
            "password": "test",
            "pfx": "long-pfx-key”
          }
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

この要求を送信すると、次のような応答が返されます。

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication": {
            "type": "ClientCertificate",
            "certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
            "certificateExpiration": "2021-01-01T08:00:00Z",
            "certificateSubjectName": "CN=Scheduler Management"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## 基本認証の要求本文

使用して認証を追加するときに、 `基本的な` モデル、要求本文に次の要素を指定します。

| 要素| 説明|
|:--|:--|
| (親要素) (_a) _| 基本認証を使用するための認証オブジェクト。|
| _type_| 必須。認証の種類。基本認証の場合、値がある必要があります `基本的な`します。|
| _username_| 必須。認証するユーザー名。|
| _password_| 必須。認証するパスワード。|

## 基本認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素| 説明|
|:--|:--|
| (親要素) (_a) _| 基本認証を使用するための認証オブジェクト。|
| _type_| 認証の種類。基本認証の場合、値は `基本的な`します。|
| _username_| 認証されたユーザー名。|

## 基本認証の要求と応答の例

次のサンプル要求が組み込まれた PUT 要求は、 `基本的な` 認証します。 要求は、次のとおりです。

    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
        "authentication":{  
          "username":"user1",
          "password":"password",
          "type":"basic"
          }           
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

この要求を送信すると、次のような応答が返されます。

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "username":"user1",
            "type":"Basic"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## ActiveDirectoryOAuth 認証の要求本文

使用して認証を追加するときに、 `ActiveDirectoryOAuth` モデル、要求本文に次の要素を指定します。

| 要素| 説明|
|:--|:--|
| (親要素) (_a) _| ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。|
| _type_| 必須。認証の種類。ActiveDirectoryOAuth 認証の場合、値がある必要があります `ActiveDirectoryOAuth`します。|
| _tenant_| 必須。Azure AD テナントのテナント ID です。|
| _audience_| 必須。これは、https://management.core.windows.net/に設定されます。|
| _clientId_| 必須。Azure AD アプリケーションのクライアント識別子を指定します。|
| _secret_| 必須。トークンを要求しているクライアントのシークレット。|

### テナント ID の確認

実行して、Azure AD テナントのテナント id を検索できる `Get-azureaccount` Azure PowerShell でします。

## ActiveDirectoryOAuth 認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素| 説明|
|:--|:--|
| (親要素) (_a) _| ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。|
| _type_| 認証の種類。ActiveDirectoryOAuth 認証の場合、値は `ActiveDirectoryOAuth`します。|
| _tenant_| Azure AD テナントのテナント ID です。|
| _audience_| これは、https://management.core.windows.net/に設定されます。|
| _clientId_| Azure AD アプリケーションのクライアント識別子。|

## ActiveDirectoryOAuth 認証の要求と応答の例

次のサンプル要求が組み込まれた PUT 要求は、 `ActiveDirectoryOAuth` 認証します。 要求は、次のとおりです。

    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "tenant":"01234567-89ab-cdef-0123-456789abcdef",
            "audience":"https://management.core.windows.net/",
            "clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
            "secret": "&lt;secret-key&gt;",
            "type":"ActiveDirectoryOAuth"
          }                      
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

この要求を送信すると、次のような応答が返されます。

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "tenant":"01234567-89ab-cdef-0123-456789abcdef",
            "audience":"https://management.core.windows.net/",
            "clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
            "type":"ActiveDirectoryOAuth"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## 関連項目

 [Scheduler とは何ですか。](scheduler-intro.md)

 [Azure Scheduler の概念、用語、およびエンティティ階層](scheduler-concepts-terms.md)

 [Azure ポータルでのスケジューラの使用の概要します。](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Azure のスケジューラの PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、規定値、およびエラー コード](scheduler-limits-defaults-errors.md)











