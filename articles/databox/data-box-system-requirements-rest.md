---
title: Wymagania dotyczące magazynu Microsoft Azure Data Box Blob | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obsługiwanych wersjach interfejsów API, zestawów SDK i bibliotek klienckich usługi Azure Data Box Blob storage
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2018
ms.author: alkohli
ms.openlocfilehash: e7c2cc0c0ffaae11bd7bf5113c942cdb98397201
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551427"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Wymagania dotyczące magazynu Azure Blob pole danych

W tym artykule wymieniono wersje interfejsów API platformy Azure, zestawy SDK i narzędzi dostarczonych z magazynu obiektów Blob pola danych. Magazyn obiektów Blob pole danych zapewnia funkcje zarządzania obiektu blob z semantyką spójnych z platformą Azure. Ten artykuł zawiera podsumowanie również znane różnice magazynu obiektów Blob pole danych platformy Azure z usługami Azure Storage.

Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed łączenie się z magazynem obiektów Blob pole danych, a następnie wrócić do niego zgodnie z potrzebami.


## <a name="storage-differences"></a>Różnice magazynu

|     Cecha                                             |     Azure Storage                                     |     Magazyn obiektów Blob pole danych |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Usługa Azure File storage                                   |    Oparte na chmurze udziałów plików SMB obsługiwane              |    Nieobsługiwane      |
|    Szyfrowanie usługi dla danych magazynowanych                  |    256-bitowego szyfrowania AES                             |    256-bitowego szyfrowania AES |
|    Typ konta magazynu                                 |    Konta magazynu ogólnego przeznaczenia i platforma Azure obiektów blob    |    Ogólnego przeznaczenia w wersji 1 tylko|
|    Nazwa obiektu blob                                            |    1024 znaków (2048 bajtów)                     |    880 znaków (1,760 w bajtach)|
|    Maksymalny rozmiar obiektu blob bloku                              |    4.75 TB (100 MB X 50 000 bloków)                   |    4.75 TB (100 MB x 50 000 bloków) dla usługi Azure Data Box wersja 1.7 i nowszych wersjach.|
|    Rozmiar maksymalny stronicowego obiektu blob                               |    8 TB                                               |    1 TB                   |
|    Rozmiar strony stronicowego obiektu blob                                  |    512 bajtów                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API

Następujące wersje interfejsów API usług Azure Storage są obsługiwane za pomocą magazynu obiektów Blob pole danych:

Publiczna wersja zapoznawcza (usługa Azure Data Box 1.7 lub nowszy)

- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>Obsługiwane wersje zestawu SDK

|     Biblioteka kliencka     |     Magazyn obiektów Blob pole danych jest obsługiwana wersja     |     Link             |     Specyfikacja punktu końcowego         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    Z 6.2.0 do 8.7.0.                         |    Pakiet Nuget:   https://www.nuget.org/packages/WindowsAzure.Storage/ <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-net/releases                                                                      |    pliku App.config                 |
|    Java                |    Z 4.1.0 do 6.1.0                          |    Pakiet maven:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-java/releases                                                      |    Ustawienia parametrów połączenia         |
|    Node.js             |    Z 1.1.0 do 2.7.0                          |    NPM link:   https://www.npmjs.com/package/azure-storage   (Na przykład: Uruchom "npm zainstalować azure-storage@2.7.0")   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-node/releases                            |    Deklaracja wystąpienia usługi    |
|    C++                 |    Z 2.4.0 do 3.1.0                          |    Pakiet Nuget:   https://www.nuget.org/packages/wastorage.v140/   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-cpp/releases                                                                            |    Ustawienia parametrów połączenia         |
|    PHP                 |    Z 0.15.0 1.0.0                         |    Wersja usługi GitHub:   https://github.com/Azure/azure-storage-php/releases   <br>Instalowanie za pomocą Composer (Zobacz szczegóły poniżej)                                                                                                   |    Ustawienia parametrów połączenia         |
|    Python              |    Z 0.30.0 1.0.0                         |    Wersja usługi GitHub:   https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    Deklaracja wystąpienia usługi    |
|    Ruby                |    Z 0.12.1 na 1.0.1                         |    Pakiet RubyGems:<br>Wspólne:   https://rubygems.org/gems/azure-storage-common/   <br>Obiekt blob: https://rubygems.org/gems/azure-storage-blob/      <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>Obsługiwane biblioteki klienckie systemu Azure

Dla pola danych w usłudze Blob storage istnieje biblioteki określonego klienta i wymagania sufiks określonego punktu końcowego.

Obsługiwane wersje interfejsu API REST dla magazynu obiektów Blob pola danych to 2017-04-17 2016-05-31, 2015-12-11 2015-07-08 i 2015-04-05 dla usługi Azure Data Box w wersji 1.7 i nowszych wersjach. Punkty końcowe magazynu obiektów Blob pole danych nie ma pełnej zgodności z najnowszą wersję interfejsu API REST usługi Azure Blob Storage. Biblioteki klienta magazynu należy wiedzieć o wersji, która jest zgodna z interfejsu API REST.

### <a name="azure-data-box-17-onwards"></a>Usługa Azure Data Box 1.7 lub nowszy

| Biblioteka kliencka     |Magazyn obiektów Blob pole danych jest obsługiwana wersja     | Link   |     Specyfikacja punktu końcowego      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    Pakiet Nuget:   https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    pliku App.config                 |
|    Java                |    6.1.0                                           |    Pakiet maven:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    Ustawienia parametrów połączenia         |
|    Node.js             |    2.7.0                                           |    NPM link:   https://www.npmjs.com/package/azure-storage   (Uruchom: zainstaluj Menedżera npm azure-storage@2.7.0)   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    Deklaracja wystąpienia usługi    |
|    C++                 |    3.1.0                                           |    Pakiet Nuget:   https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>Wersja usługi GitHub:   https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    Ustawienia parametrów połączenia         |
|    PHP                 |    1.0.0                                           |    Wersja usługi GitHub:<br>Wspólne: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>Obiekt blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>Instalowanie za pomocą Composer (Aby dowiedzieć się więcej, zobacz szczegóły poniżej).                                                                                                             |    Ustawienia parametrów połączenia         |
|    Python              |    1.0.0                                           |    Wersja usługi GitHub:<br>Wspólne:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Obiekt blob:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    Deklaracja wystąpienia usługi    |
|    Ruby                |    1.0.1                                           |    Pakiet RubyGems:<br>Wspólne:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Obiekt blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Wersja usługi GitHub:<br>Wspólne: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Obiekt blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Ustawienia parametrów połączenia         |



### <a name="install-php-client-via-composer---current"></a>Zainstaluj klienta PHP za pośrednictwem Composer — bieżące

Aby zainstalować za pośrednictwem Composer (kompozytor): (Wypełnij obiektów blob przykład).
Utwórz plik o nazwie composer.json w katalogu głównym projektu przy użyciu następującego kodu:

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

Pobierz `composer.phar` w katalogu głównym projektu.

Uruchom: Zainstaluj php composer.phar.

### <a name="endpoint-declaration"></a>Deklarację punktu końcowego

Punkt końcowy magazynu Azure Blob pole danych zawiera dwie części: Nazwa regionu i domeny urządzenia Data Box. W magazynie obiektów Blob pole danych zestawu SDK jest domyślny punkt końcowy <serial no. of the device>. microsoftdatabox.com.  Aby uzyskać więcej informacji na temat punkt końcowy usługi blob service, przejdź do [Połącz za pomocą magazynu obiektów Blob pole danych](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Przykłady

### <a name="net"></a>.NET

Dla pola danych w usłudze Blob storage, sufiks punktu końcowego jest określona w `app.config` pliku:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Dla pola danych w usłudze Blob storage sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Dla pola danych w usłudze Blob storage sufiks punktu końcowego określono w wystąpieniu deklaracji:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Dla pola danych w usłudze Blob storage sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Dla pola danych w usłudze Blob storage sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Dla pola danych w usłudze Blob storage sufiks punktu końcowego określono w wystąpieniu deklaracji:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Dla pola danych w usłudze Blob storage sufiks punktu końcowego jest określony w ustawieniach parametry połączenia:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie usługi Azure Data Box](data-box-deploy-ordered.md)