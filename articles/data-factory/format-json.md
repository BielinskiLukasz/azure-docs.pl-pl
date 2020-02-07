---
title: Format JSON w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem JSON w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049845"
---
# <a name="json-format-in-azure-data-factory"></a>Format JSON w Azure Data Factory

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki JSON lub zapisać dane w formacie JSON**. 

Format JSON jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Magazyn w chmurze Google](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych JSON.

| Właściwość         | Opis                                                  | Wymagany |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Właściwość Type zestawu danych musi być ustawiona na wartość **JSON**. | Yes      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w obszarze `location`. **Zobacz szczegóły w sekcji łącznik — > Właściwości zestawu danych**. | Yes      |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "WINDOWS-8859-15", "WINDOWS-874", "WINDOWS-1250", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Nie       |
| compressionCodec | Koder-dekoder kompresji używany do odczytu/zapisu plików tekstowych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **przyciąganie**lub **lz4**. do użycia podczas zapisywania pliku. <br>Działanie kopiowania obecnie nie obsługuje "przyciągania" & "lz4".<br>Uwaga w przypadku używania działania kopiowania do dekompresowania plików ZipDeflate i zapisywania w magazynie danych ujścia opartych na plikach pliki zostaną wyodrębnione do folderu: `<path specified in dataset>/<folder named as source zip file>/`. | Nie       |
| compressionLevel | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **najszybciej:** operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykład zestawu danych JSON w usłudze Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia danych JSON.

### <a name="json-as-source"></a>KOD JSON jako źródło

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*źródło\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSource**. | Yes      |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

### <a name="json-as-sink"></a>JSON jako ujścia

Następujące właściwości są obsługiwane w sekcji działanie kopiowania ***\*ujścia\**** .

| Właściwość      | Opis                                                  | Wymagany |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **JSONSink**. | Yes      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień zapisu JSON** poniżej. | Nie       |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings`. **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia zapisu** w formacie JSON w `formatSettings`:

| Właściwość      | Opis                                                  | Wymagany                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musi być ustawiony na **JsonWriteSettings**. | Yes                                                   |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może automatycznie wykrywać i analizować następujące wzorce plików JSON. 

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. 
    Po wybraniu tej opcji w obiekcie ujścia działania kopiowania działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w wierszu (rozdzielonym wierszem).

    * **przykład kodu JSON z pojedynczym obiektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **przykład kodu JSON z obiektami rozdzielonymi wierszami**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **przykład kodu JSON z obiektami połączonymi**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Każdy plik zawiera tablicę obiektów.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Typy plików JSON mogą być używane jako ujścia i źródło w przepływie danych mapowania.

### <a name="creating-json-structures-in-a-derived-column"></a>Tworzenie struktur JSON w kolumnie pochodnej

Do przepływu danych można dodać kolumnę złożoną za pośrednictwem konstruktora wyrażeń kolumn pochodnych. W transformację kolumn pochodnych Dodaj nową kolumnę, a następnie otwórz konstruktora wyrażeń, klikając niebieską ramkę. Aby utworzyć kolumnę złożoną, można wprowadzić strukturę JSON ręcznie lub użyć środowiska użytkownika do interaktywnego dodawania podkolumn.

#### <a name="using-the-expression-builder-ux"></a>Korzystanie z środowiska programu Expression Builder

W okienku po stronie schematu danych wyjściowych Umieść kursor nad kolumną i kliknij ikonę znaku plus. Wybierz pozycję **Dodaj podkolumnę** , aby utworzyć kolumnę typu złożonego.

![Dodaj podkolumnę](media/data-flow/addsubcolumn.png "Dodaj podkolumnę")

W ten sam sposób można dodać dodatkowe kolumny i podkolumny. W przypadku każdego niezłożonej pola wyrażenie może być dodane w edytorze wyrażeń z prawej strony.

![Kolumna złożona](media/data-flow/complexcolumn.png "Kolumna złożona")

#### <a name="entering-the-json-structure-manually"></a>Ręczne wprowadzanie struktury JSON

Aby ręcznie dodać strukturę JSON, Dodaj nową kolumnę i wprowadź wyrażenie w edytorze. Wyrażenie jest zgodne z następującym formatem ogólnym:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Jeśli to wyrażenie zostało wprowadzone dla kolumny o nazwie "complexColumn", zostanie ona zapisywana w ujścia jako następujący kod JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Przykładowy skrypt ręczny dla pełnej definicji hierarchicznej
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Opcje formatu źródła

Używanie zestawu danych JSON jako źródła w przepływie danych pozwala na ustawienie pięciu dodatkowych ustawień. Te ustawienia można znaleźć w obszarze **Ustawienia JSON** zgodnie z opisem na karcie **Opcje źródła** .  

![Ustawienia JSON](media/data-flow/json-settings.png "Ustawienia JSON")

#### <a name="default"></a>Domyślne

Domyślnie dane JSON są odczytywane w następującym formacie.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Pojedynczy dokument

W przypadku wybrania **jednego dokumentu** mapowanie przepływów danych odczytuje jeden dokument JSON z każdego pliku. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Nazwy kolumn bez cytowania

Jeśli wybrano **nazwy kolumn bez cudzysłowów** , mapowanie przepływów danych odczytuje kolumny JSON, które nie są ujęte w cudzysłowy. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Ma Komentarze

Zaznacz pole **ma Komentarze** , jeśli dane JSON mają komentarz C++ C lub style.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Pojedyncze cudzysłowy

Wybierz opcję **pojedyncze cudzysłowy** , jeśli pola i wartości JSON używają apostrofów zamiast podwójnych cudzysłowów.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Odwrócony ukośnik odwrotny

Wybierz opcję **pojedyncze cudzysłowy** , jeśli ukośniki odwrotne są używane do ucieczki znaków w danych JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
