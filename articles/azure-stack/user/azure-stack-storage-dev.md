---
title: "Wprowadzenie do narzędzi programistycznych magazyn Azure stosu"
description: "Aby rozpocząć używanie narzędzi programistycznych magazyn Azure stosu"
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 02/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 81c62fc569e9f758d08bfca0bdfc5bcc9ed5860f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Wprowadzenie do narzędzi programistycznych magazyn Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Microsoft Azure stosu zapewnia zbiór usług magazynowania, włącznie z magazynu obiektów Blob platformy Azure, tabel i kolejek.

Ten artykuł zawiera szybki wskazówki dotyczące sposobu uruchamiania przy użyciu narzędzi programistycznych magazyn Azure stosu. W odpowiednich samouczków usługi Azure Storage można znaleźć bardziej szczegółowe informacje, a przykładowy kod.

Istnieją znane różnice między usługi Azure Storage i Azure stosu magazynu, w tym niektóre określone wymagania dotyczące każdej platformy. Na przykład istnieją bibliotek określonego klienta i określonego końcowego sufiks wymagania dotyczące stosu Azure. Aby uzyskać więcej informacji, zobacz [usługi Azure Storage stosu: różnice i zagadnienia dotyczące](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotek klienta platformy Azure

Obsługiwane wersje interfejsu API REST usługi Azure Storage stosu są 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08, 2015-04-05 1802 aktualizacji lub nowsze wersje i 2015-04-05 w poprzednich wersjach. Punkty końcowe platformy Azure stosu nie mają pełnego parzystości z najnowszą wersją interfejsu API REST magazynu Azure. Biblioteki klienta magazynu musisz znać wersji, która jest zgodna z interfejsu API REST.

### <a name="1802-update-or-newer-versions"></a>1802 aktualizacji lub nowszych wersji

| Biblioteka kliencka | Azure stosu obsługiwana wersja | Link | Specyfikacja punktu końcowego |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pakiet Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | app.config file |
| Java | 6.1.0 | Pakiet maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Ustawienia połączenia |
| Node.js | 2.7.0 | Łącze NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Uruchom: `npm install azure-storage@2.7.0`)<br> <br>Wersja usługi Github:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Deklaracja wystąpienia usługi |
| C++ | 3.1.0 | Pakiet Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Ustawienia połączenia |
| PHP | 1.0.0 | Wersja usługi GitHub:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Kolejki:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabela: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Zainstaluj za pośrednictwem Composer (Aby dowiedzieć się więcej, [szczegóły poniżej](#install-php-client-via-composer---current).) | Ustawienia połączenia |
| Python | 1.0.0 | Wersja usługi GitHub:<br>Wspólne:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Kolejki:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Deklaracja wystąpienia usługi |
| Ruby | 1.0.1 | Pakiet RubyGems:<br>Wspólne:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Wersja usługi GitHub:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Queue: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Table: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Ustawienia połączenia |

#### <a name="install-php-client-via-composer---current"></a>Instalowanie PHP klienta za pośrednictwem Composer — bieżący

Aby zainstalować za pomocą Composer: (podjęcia obiektu blob jako przykład).

1. Utwórz plik o nazwie **composer.json** w folderze głównym projektu z następującym kodem:
  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```
2. Pobierz [composer.phar](http://getcomposer.org/composer.phar) w katalogu głównym projektu.
3. Instalacja: `php composer.phar install`.

### <a name="previous-versions"></a>Poprzednie wersje

|Biblioteka kliencka|Azure stosu obsługiwana wersja|Link|Specyfikacja punktu końcowego|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config file|
|Java|4.1.0|Pakiet maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Ustawienia połączenia|
|Node.js     |1.1.0|Łącze NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(Uruchom: `npm install azure-storage@1.1.0)`<br><br>Wersja usługi Github:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Deklaracja wystąpienia usługi||C++|2.4.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Ustawienia połączenia|
|C++|2.4.0|Pakiet Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Ustawienia połączenia|
|PHP|0.15.0|Wersja usługi GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Zainstaluj za pośrednictwem Composer (Zobacz szczegóły poniżej)|Ustawienia połączenia|
|Python     |0.30.0|Pakiet PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Uruchom: `pip install -v azure-storage==0.30.0)`<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Deklaracja wystąpienia usługi|
|Ruby|0.12.1<br>Wersja zapoznawcza|Pakiet RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Wersja usługi GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Ustawienia połączenia|

#### <a name="install-php-client-via-composer---previous"></a>Instalowanie PHP klienta za pośrednictwem Composer - poprzedniej

Aby zainstalować za pomocą Composer:

1. Utwórz plik o nazwie **composer.json** w folderze głównym projektu z następującym kodem:
  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```
2. Pobierz [composer.phar](http://getcomposer.org/composer.phar) w katalogu głównym projektu.
3. Instalacja: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Deklarację punktu końcowego

Punkt końcowy stosu Azure zawiera dwie części: Nazwa regionu i domena stosu Azure.
W zestawie Azure stosu Development Kit jest domyślny punkt końcowy **local.azurestack.external**.
Jeśli nie wiesz o punktu końcowego, skontaktuj się z administratorem chmury.

## <a name="examples"></a>Przykłady


### <a name="net"></a>.NET

Stosu Azure sufiks punkt końcowy jest określony w pliku app.config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Dla stosu Azure sufiks punktu końcowego określono w ustawieniach parametry połączenia:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Stosu Azure w deklaracji wystąpienia określono sufiks punktu końcowego:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Dla stosu Azure sufiks punktu końcowego określono w ustawieniach parametry połączenia:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Dla stosu Azure sufiks punktu końcowego określono w ustawieniach parametry połączenia:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Stosu Azure w deklaracji wystąpienia określono sufiks punktu końcowego:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Dla stosu Azure sufiks punktu końcowego określono w ustawieniach parametry połączenia:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Następujące samouczki magazynu obiektów Blob platformy Azure są stosowane do stosu Azure. Należy zwrócić uwagę konieczność sufiks określonego końcowego stosu Azure opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w oprogramowaniu Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Jak używać magazynu obiektów Blob w języku C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Używanie usługi Blob Storage w języku PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Jak używać magazynu obiektów Blob platformy Azure w języku Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Używanie usługi Blob Storage w języku Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Następujące samouczki magazynu kolejek Azure mają zastosowanie do stosu Azure. Należy zwrócić uwagę konieczność sufiks określonego końcowego stosu Azure opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Queue Storage przy użyciu platformy .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Używanie usługi Queue Storage w oprogramowaniu Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Jak używać magazynu kolejek w języku C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Używanie usługi Queue Storage w języku Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Używanie usługi Queue Storage w języku Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Magazyn tabel

Następujące samouczki magazynu tabel Azure mają zastosowanie do stosu Azure. Należy zwrócić uwagę konieczność sufiks określonego końcowego stosu Azure opisane w poprzedniej [przykłady](#examples) sekcji.

* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Używanie usługi Table Storage w języku Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [How to use Azure Table storage from Node.js (Używanie usługi Azure Table Storage w oprogramowaniu Node.js)](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Używanie usługi Table Storage w języku PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Jak używać magazynu tabel w języku Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Używanie usługi Table Storage w języku Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md)