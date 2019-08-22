---
title: Indeksuj zawartość z usługi Azure Table Storage na potrzeby wyszukiwania pełnotekstowego — Azure Search
description: Dowiedz się, jak indeksować dane przechowywane w usłudze Azure Table Storage za pomocą indeksatora Azure Search.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: dffb0a41dbf33cd86014115b089036d69a8e4718
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648187"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Indeksowanie usługi Azure Table Storage za pomocą Azure Search
W tym artykule pokazano, jak używać Azure Search do indeksowania danych przechowywanych w usłudze Azure Table Storage.

## <a name="set-up-azure-table-storage-indexing"></a>Konfigurowanie indeksowania usługi Azure Table Storage

Indeksator usługi Azure Table Storage można skonfigurować przy użyciu następujących zasobów:

* [Azure Portal](https://ms.portal.azure.com)
* [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Azure Search
* [Zestaw SDK Azure Search .NET](https://aka.ms/search-sdk)

W tym miejscu zademonstrowano przepływ przy użyciu interfejsu API REST. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Tworzenie źródła danych

Źródło danych określa dane do indeksowania, poświadczenia potrzebne do uzyskania dostępu do danych oraz zasady, które umożliwiają Azure Search efektywne identyfikowanie zmian w danych.

W przypadku indeksowania tabeli źródło danych musi mieć następujące właściwości:

- **Nazwa** jest unikatową nazwą źródła danych w ramach usługi wyszukiwania.
- **Typ** musi być `azuretable`.
- parametr **Credentials** zawiera parametry połączenia konta magazynu. Aby uzyskać szczegółowe informacje, zobacz sekcję [Określanie poświadczeń](#Credentials) .
- **kontener** ustawia nazwę tabeli i opcjonalne zapytanie.
    - Określ nazwę tabeli przy użyciu `name` parametru.
    - Opcjonalnie można określić zapytanie przy użyciu `query` parametru. 

> [!IMPORTANT] 
> Jeśli to możliwe, Użyj filtru na PartitionKey w celu uzyskania lepszej wydajności. Każde inne zapytanie wykonuje pełne skanowanie tabeli, co skutkuje niską wydajnością dużych tabel. Zobacz sekcję [zagadnienia dotyczące wydajności](#Performance) .


Aby utworzyć źródło danych:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia źródła danych, zobacz [Create DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Sposoby określania poświadczeń ####

Poświadczenia dla tabeli można podać w jeden z następujących sposobów: 

- **Parametry połączenia z pełnymi dostępem do konta magazynu**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`Parametry połączenia można uzyskać z Azure Portal, przechodząc do**kluczy** **ustawień** >  >  **bloku konta magazynu**(dla klasycznych kont magazynu) lub > **kluczy dostępu do ustawień.** (w przypadku kont usługi Azure Resource Manager Storage).
- **Parametry połączenia sygnatury dostępu współdzielonego konta magazynu**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`Sygnatura dostępu współdzielonego powinna mieć uprawnienia do listy i odczytu kontenerów (w tym przypadku tabel w tym przypadku) oraz obiektów (wiersze tabeli).
-  **Sygnatura dostępu współdzielonego tabeli**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`Sygnatura dostępu współdzielonego powinna mieć uprawnienia zapytania (odczyt) w tabeli.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego magazynu, zobacz [Używanie sygnatur dostępu](../storage/common/storage-dotnet-shared-access-signature-part-1.md)współdzielonego.

> [!NOTE]
> W przypadku korzystania z poświadczeń sygnatury dostępu współdzielonego należy okresowo zaktualizować poświadczenia źródła danych za pomocą odnowionych podpisów, aby zapobiec ich wygaśnięciu. Jeśli poświadczenia sygnatury dostępu współdzielonego wygasną, indeksator kończy się niepowodzeniem z komunikatem o błędzie podobnym do "poświadczenia podane w parametrach połączenia są nieprawidłowe lub wygasły".  

### <a name="step-2-create-an-index"></a>Krok 2: Tworzenie indeksu
Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Aby utworzyć indeks:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [create index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Utwórz indeksator
Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram służący do automatyzowania odświeżania danych. 

Po utworzeniu indeksu i źródła danych możesz utworzyć indeksator:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ten indeksator działa co dwie godziny. (Interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał wynosi pięć minut. Harmonogram jest opcjonalny; w przypadku pominięcia indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zobacz [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla Azure Search](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Postępowanie z różnymi nazwami pól
Czasami nazwy pól w istniejącym indeksie różnią się od nazw właściwości w tabeli. Mapowania pól można użyć do mapowania nazw właściwości z tabeli do nazw pól w indeksie wyszukiwania. Aby dowiedzieć się więcej na temat mapowania pól, zobacz [Azure Search Indexer Mappings Bridge różnice między źródłami danych i indeksami wyszukiwania](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Obsługuj klucze dokumentów
W Azure Search klucz dokumentu jednoznacznie identyfikuje dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String`. Pole klucza jest wymagane dla każdego dokumentu, który jest dodawany do indeksu. (W rzeczywistości jest to jedyne pole wymagane).

Ponieważ wiersze tabeli mają klucz złożony, Azure Search generuje pole syntetyczne o nazwie `Key` , które jest połączeniem klucza partycji i wartości klucza wiersza. Na przykład, `PK1` Jeśli PartitionKey wiersza to i RowKey is `RK1`, `Key` wartość pola to `PK1RK1`.

> [!NOTE]
> `Key` Wartość może zawierać znaki, które są nieprawidłowe w kluczach dokumentów, takich jak łączniki. Za pomocą `base64Encode` [funkcji mapowania pól](search-indexer-field-mappings.md#base64EncodeFunction)można poradzić sobie z nieprawidłowymi znakami. Jeśli to zrobisz, pamiętaj, aby przy przekazywaniu kluczy dokumentów w wywołaniach interfejsu API, takich jak Lookup, używać bezpiecznego kodowania base64 z bezpiecznymi adresami URL.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Wykrywanie przyrostowe i usuwanie
Po skonfigurowaniu indeksatora tabeli do uruchamiania zgodnie z harmonogramem program ponownie indeksuje tylko nowe lub zaktualizowane wiersze, zgodnie z `Timestamp` wartością wiersza. Nie musisz określać zasad wykrywania zmian. Indeksowanie przyrostowe jest automatycznie włączone.

Aby wskazać, że niektóre dokumenty muszą zostać usunięte z indeksu, można użyć strategii usuwania nietrwałego. Zamiast usuwać wiersz, Dodaj właściwość, aby wskazać, że została usunięta, i skonfiguruj zasady wykrywania usuwania nietrwałego dla źródła danych. Na przykład następujące zasady uważają, że wiersz jest usuwany, jeśli wiersz ma właściwość `IsDeleted` o wartości: `"true"`

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Domyślnie Azure Search używa następującego filtra zapytania: `Timestamp >= HighWaterMarkValue`. Ponieważ tabele platformy Azure nie mają pomocniczego indeksu dla `Timestamp` tego pola, ten typ zapytania wymaga pełnego skanowania tabeli i dlatego jest wolny dla dużych tabel.


Poniżej przedstawiono dwie możliwe podejścia do usprawnienia działania indeksowania tabeli. Oba te podejścia polegają na użyciu partycji tabel: 

- Jeśli dane można w naturalny sposób podzielić na kilka zakresów partycji, Utwórz źródło danych i odpowiedni indeksator dla każdego zakresu partycji. Każdy indeksator musi teraz przetwarzać tylko określony zakres partycji, co zwiększa wydajność zapytań. Jeśli dane, które muszą być indeksowane, mają niewielką liczbę stałych partycji, jeszcze lepszy: każdy indeksator wykonuje tylko skanowanie partycji. Na przykład, aby utworzyć źródło danych do przetwarzania zakresu partycji z kluczami z `000` do `100`, należy użyć zapytania w następujący sposób: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Jeśli dane są partycjonowane według czasu (na przykład można utworzyć nową partycję każdego dnia lub tygodnia), należy wziąć pod uwagę następujące podejście: 
    - Użyj zapytania dotyczącego formularza: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitoruj postęp indeksatora przy użyciu [interfejsu Get indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status), a następnie okresowo Aktualizuj `<TimeStamp>` warunek zapytania w oparciu o ostatnią pomyślną wartość ze znakiem końca wody. 
    - W przypadku konieczności wyzwolenia kompletnego ponownego indeksowania należy zresetować zapytanie DataSource oprócz resetowania indeksatora. 


## <a name="help-us-make-azure-search-better"></a>Pomóż nam w ulepszaniu Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, prześlij je w naszej [witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
