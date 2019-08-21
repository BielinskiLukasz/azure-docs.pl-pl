---
title: Konfigurowanie parametrów połączenia dla usługi Azure Storage
description: Skonfiguruj parametry połączenia dla konta usługi Azure Storage. Parametry połączenia zawierają informacje potrzebne do autoryzacji dostępu do konta magazynu z aplikacji w czasie wykonywania przy użyciu autoryzacji klucza współużytkowanego.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d1106865b3a2ea3164090896c5b90ab08f996f3d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640505"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfiguracja parametrów połączenia usługi Azure Storage

Parametry połączenia zawierają informacje o autoryzacji wymagane przez aplikację do uzyskiwania dostępu do danych na koncie usługi Azure Storage w środowisku uruchomieniowym przy użyciu autoryzacji klucza współużytkowanego. Parametry połączenia można skonfigurować w taki sposób, aby:

* Nawiązywanie połączenia z emulatorem usługi Azure Storage.
* Uzyskaj dostęp do konta magazynu na platformie Azure.
* Dostęp do określonych zasobów na platformie Azure za pośrednictwem sygnatury dostępu współdzielonego (SAS).

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Wyświetlanie i kopiowanie parametrów połączenia

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Przechowywanie parametrów połączenia

Aplikacja musi uzyskać dostęp do parametrów połączenia w czasie wykonywania, aby autoryzować żądania kierowane do usługi Azure Storage. Istnieje kilka opcji przechowywania parametrów połączenia:

* Parametry połączenia można przechowywać w zmiennej środowiskowej.
* Aplikacja działająca na komputerze stacjonarnym lub na urządzeniu może przechowywać parametry połączenia w pliku **App. config** lub **Web. config** . Dodaj parametry połączenia do sekcji **AppSettings** w tych plikach.
* W aplikacji uruchomionej w usłudze w chmurze platformy Azure można przechowywać parametry połączenia w [pliku schematu konfiguracji usługi platformy Azure (cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Dodaj parametry połączenia do sekcji **ConfigurationSettings** w pliku konfiguracji usługi.

Przechowywanie parametrów połączenia w pliku konfiguracji ułatwia aktualizowanie parametrów połączenia w celu przełączania się między emulatorem magazynu a kontem usługi Azure Storage w chmurze. Wystarczy edytować parametry połączenia, aby wskazywały na środowisko docelowe.

Możesz użyć [Configuration Manager Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) , aby uzyskać dostęp do parametrów połączenia w czasie wykonywania, niezależnie od miejsca, w którym działa aplikacja.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie parametrów połączenia dla emulatora magazynu

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Aby uzyskać więcej informacji na temat emulatora magazynu, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurowanie parametrów połączenia dla konta usługi Azure Storage

Aby utworzyć parametry połączenia dla konta usługi Azure Storage, użyj następującego formatu. Wskaż, czy chcesz nawiązać połączenie z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub `myAccountName` http, Zastąp ciąg nazwą konta magazynu i Zastąp `myAccountKey` ciąg kluczem dostępu do konta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Na przykład parametry połączenia mogą wyglądać podobnie do następujących:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Mimo że usługa Azure Storage obsługuje zarówno protokół HTTP, jak i HTTPS w parametrach połączenia, *zdecydowanie zaleca się użycie protokołu HTTPS*.

> [!TIP]
> Parametry połączenia konta magazynu można znaleźć w [Azure Portal](https://portal.azure.com). Przejdź do **ustawień** > **klucze dostępu** w bloku menu konta magazynu, aby wyświetlić parametry połączenia dla podstawowych i pomocniczych kluczy dostępu.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Tworzenie parametrów połączenia przy użyciu sygnatury dostępu współdzielonego

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Utwórz parametry połączenia dla jawnego punktu końcowego magazynu

Możesz określić jawne punkty końcowe usługi w parametrach połączenia zamiast korzystać z domyślnych punktów końcowych. Aby utworzyć parametry połączenia określające jawny punkt końcowy, określ kompletny punkt końcowy usługi dla każdej usługi, w tym specyfikację protokołu (HTTPS (zalecane) lub HTTP), w następującym formacie:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Jednym z scenariuszy, w którym można określić jawny punkt końcowy, jest zamapowanie punktu końcowego magazynu obiektów BLOB na [domenę](../blobs/storage-custom-domain-name.md)niestandardową. W takim przypadku można określić niestandardowy punkt końcowy dla magazynu obiektów BLOB w parametrach połączenia. Opcjonalnie można określić domyślne punkty końcowe dla innych usług, jeśli są one używane przez aplikację.

Oto przykład parametrów połączenia, które określają jawny punkt końcowy dla Blob service:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

W tym przykładzie określono jawne punkty końcowe dla wszystkich usług, w tym domenę niestandardową dla Blob service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Wartości punktów końcowych w parametrach połączenia są używane do konstruowania identyfikatorów URI żądań do usług magazynu, a także do podyktowania formy identyfikatorów URI zwracanych do kodu.

Jeśli punkt końcowy magazynu został zamapowany na domenę niestandardową i pominięto ten punkt końcowy z parametrów połączenia, nie będzie można używać tych parametrów połączenia do uzyskiwania dostępu do danych w tej usłudze z kodu.

> [!IMPORTANT]
> Wartości punktu końcowego usługi w parametrach połączenia muszą mieć poprawnie sformułowane identyfikatory URI `https://` , w tym ( `http://`zalecane) lub. Ponieważ usługa Azure Storage nie obsługuje jeszcze protokołu HTTPS dla domen niestandardowych, *należy* określić `http://` dla dowolnego identyfikatora URI punktu końcowego, który wskazuje na domenę niestandardową.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Tworzenie parametrów połączenia przy użyciu sufiksu punktu końcowego

Aby utworzyć parametry połączenia dla usługi magazynu w regionach lub wystąpieniach z różnymi sufiksami punktów końcowych, takimi jak Azure Chiny 21Vianet lub Azure Government, użyj następującego formatu parametrów połączenia. Wskaż, czy chcesz nawiązać połączenie z kontem magazynu za pośrednictwem protokołu HTTPS (zalecane) lub `myAccountName` protokołu HTTP, Zastąp ciąg nazwą konta magazynu `myAccountKey` , Zastąp ciąg kluczem dostępu do konta `mySuffix` i Zastąp ciąg identyfikatorem URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Oto przykładowe parametry połączenia dla usług magazynu na platformie Azure (Chiny):

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analizowanie parametrów połączenia

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z emulatora usługi Azure Storage na potrzeby tworzenia i testowania](storage-use-emulator.md)
* [Eksploratory usługi Azure Storage](storage-explorers.md)
* [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
