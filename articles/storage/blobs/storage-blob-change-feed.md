---
title: Źródło zmian w usłudze Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się więcej o dziennikach źródeł zmian na platformie Azure Blob Storage i sposobach ich użycia.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 19a65e688d66738db0b6e4dcca383c6e4abed262
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974409"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage (wersja zapoznawcza)

Celem źródła zmian jest dostarczenie dzienników transakcji wszystkich zmian, które występują w obiektach Blob i metadanych obiektów BLOB na koncie magazynu. Kanał informacyjny zmiany zawiera **uporządkowany**, **gwarantowany**, **trwały**, **niezmienny** **Dziennik tych** zmian. Aplikacje klienckie mogą odczytywać te dzienniki w dowolnym momencie, w ramach przesyłania strumieniowego lub w trybie wsadowym. Kanał informacyjny zmiany umożliwia tworzenie wydajnych i skalowalnych rozwiązań, które przetwarzają zdarzenia zmiany występujące na koncie Blob Storage przy niskich kosztach.

Źródło zmian jest przechowywane jako [obiekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) w specjalnym kontenerze na koncie magazynu przy użyciu standardowego kosztu [cennika obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) . Możesz kontrolować okres przechowywania tych plików zgodnie z wymaganiami (zobacz [warunki](#conditions) bieżącej wersji). Zdarzenia zmiany są dołączane do źródła zmian jako rekordy w specyfikacji formatu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) : kompaktowy, szybki i binarny format, który zapewnia rozbudowane struktury danych z wbudowanym schematem. Ten format jest powszechnie używany w ekosystemie usługi Hadoop, usługi Stream Analytics i Azure Data Factory.

Można przetwarzać te dzienniki asynchronicznie, przyrostowo lub w całości. Dowolna liczba aplikacji klienckich może niezależnie odczytywać Źródło zmian, równolegle i we własnym tempie. Aplikacje analityczne, takie jak [Apache drążenie](https://drill.apache.org/docs/querying-avro-files/) lub [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) , mogą zużywać dzienniki bezpośrednio jako pliki Avro, dzięki czemu można przetwarzać je przy niskich kosztach, z wysoką przepustowością i bez konieczności pisania niestandardowej aplikacji.

Obsługa kanałów informacyjnych zmian jest odpowiednia dla scenariuszy, które przetwarzają dane na podstawie obiektów, które uległy zmianie. Na przykład aplikacje mogą:

  - Zaktualizuj indeks pomocniczy, zsynchronizuj go z pamięcią podręczną, aparatem wyszukiwania lub innymi scenariuszami zarządzania zawartością.
  
  - Wyodrębnij szczegółowe informacje o analizie biznesowej i metryki na podstawie zmian, które występują w obiektach, w formie przesyłania strumieniowego lub w trybie wsadowym.
  
  - Przechowuj, przeprowadzaj inspekcję i Analizuj zmiany w obiektach, w dowolnym czasie, pod kątem bezpieczeństwa, zgodności lub analizy dla zarządzania danymi w przedsiębiorstwie.

  - Twórz rozwiązania do tworzenia kopii zapasowych, dublowania lub replikowania stanu obiektów na koncie w celu zarządzania awaryjnego lub zapewnienia zgodności.

  - Twórz połączone potoki aplikacji, które reagują na zmiany zdarzeń lub Zaplanuj wykonania na podstawie utworzonego lub zmienionego obiektu.

> [!NOTE]
> Źródło zmian zawiera trwały, uporządkowany model dziennika zmian, które występują w obiekcie blob. Zmiany są zapisywane i udostępniane w dzienniku kanału informacyjnego zmiany w kolejności kilku minut od zmiany. Jeśli aplikacja musi reagować na zdarzenia znacznie szybciej niż tutaj, zamiast tego Rozważ użycie [zdarzeń BLOB Storage](storage-blob-event-overview.md) . [Zdarzenia BLOB Storage](storage-blob-event-overview.md) zawierają zdarzenia jednorazowe w czasie rzeczywistym, które umożliwiają Azure Functions lub aplikacjom szybkie reagowanie na zmiany, które wystąpiły w obiekcie blob. 

## <a name="enable-and-disable-the-change-feed"></a>Włączanie i wyłączanie kanału informacyjnego zmiany

Aby rozpocząć przechwytywanie i rejestrowanie zmian, należy włączyć kanał informacyjny zmiany na koncie magazynu. Wyłącz kanał informacyjny zmiany, aby zatrzymać przechwytywanie zmian. Zmiany można włączać i wyłączać za pomocą szablonów Azure Resource Manager w portalu lub programie PowerShell.

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę po włączeniu źródła zmian.

- Dla usługi BLOB w ramach każdego konta magazynu istnieje tylko jeden kanał informacyjny zmiany, który jest przechowywany w kontenerze **$blobchangefeed** .

- Tworzenie, aktualizowanie i usuwanie zmian są przechwytywane tylko na poziomie usługi BLOB.

- Kanał informacyjny zmiany przechwytuje *wszystkie* zmiany dla wszystkich dostępnych zdarzeń występujących na tym koncie. Aplikacje klienckie mogą odfiltrować typy zdarzeń zgodnie z wymaganiami. (Zobacz [warunki](#conditions) bieżącej wersji).

- Tylko konta GPv2 i BLOB Storage mogą włączać Źródło zmian. Konta BlockBlobStorage Premium i hierarchiczne konta z obsługą nazw nie są obecnie obsługiwane. Konta magazynu GPv1 nie są obsługiwane, ale można je uaktualnić do GPv2 bez przestojów. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu GPv2](../common/storage-account-upgrade.md) .

> [!IMPORTANT]
> Kanał informacyjny zmiany jest w publicznej wersji zapoznawczej i jest dostępny w regionach **westcentralus** i **westus2** . Zobacz sekcję [warunki](#conditions) w tym artykule. Aby zarejestrować się w wersji zapoznawczej, zobacz sekcję [Rejestrowanie subskrypcji](#register) w tym artykule. Musisz zarejestrować swoją subskrypcję, aby można było włączyć funkcję źródła zmian na kontach magazynu.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Włącz źródło zmian na koncie magazynu przy użyciu Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu. 

2. Przejdź do opcji **Ochrona danych** w obszarze **BLOB Service**.

3. Kliknij pozycję **włączone** w obszarze **źródła zmian obiektu BLOB**

4. Wybierz przycisk **Zapisz** , aby potwierdzić ustawienia ochrony danych

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Włącz źródło zmian przy użyciu programu PowerShell:

1. Zainstaluj najnowszą PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zamknij program, a następnie ponownie otwórz konsolę programu PowerShell.

3. Zainstaluj moduł **AZ. Storage** Preview.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami do uwierzytelniania.

   ```powershell
   Connect-AzAccount
   ```

5. Włącz źródło zmian dla konta magazynu.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

### <a name="templatetabtemplate"></a>[Szablon](#tab/template)
Użyj szablonu Azure Resource Manager, aby włączyć Źródło zmian na istniejącym koncie magazynu za pośrednictwem Azure Portal:

1. W Azure Portal wybierz pozycję **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz **[Wdróż szablon niestandardowy](https://portal.azure.com/#create/Microsoft.Template)** , a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.

4. W edytorze szablonów wklej poniższy kod JSON. Zastąp symbol zastępczy `<accountName>` nazwą konta magazynu.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Wybierz przycisk **Zapisz** , określ grupę zasobów konta, a następnie wybierz przycisk **Kup** , aby wdrożyć szablon i włączyć Źródło zmian.

---

## <a name="consume-the-change-feed"></a>Korzystanie ze źródła zmian

Kanał informacyjny zmiany generuje kilka plików metadanych i dzienników. Te pliki znajdują się w kontenerze **$blobchangefeed** konta magazynu. 

> [!NOTE]
> W bieżącej wersji kontener **$blobchangefeed** nie jest widoczny w Eksplorator usługi Azure Storage lub Azure Portal. Nie jest obecnie widoczny kontener $blobchangefeed podczas wywoływania interfejsu API ListContainers, ale można wywołać interfejs API ListBlobs bezpośrednio w kontenerze, aby wyświetlić obiekty blob.

Aplikacje klienckie mogą korzystać ze źródła zmian przy użyciu biblioteki procesora kanału informacyjnego zmiany obiektów BLOB dostarczonej z zestawem SDK procesora źródła zmian. 

Zobacz [dzienniki źródła zmian procesów na platformie Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Zrozumienie organizacji kanału informacyjnego zmiany

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Kanał informacyjny zmiany jest dziennikiem zmian zorganizowanych na *segmenty* **godzinowe** , ale dołączane i aktualizowane co kilka minut. Te segmenty są tworzone tylko wtedy, gdy istnieją zdarzenia zmiany obiektów blob, które wystąpiły w danej godzinie. Dzięki temu aplikacja kliencka może zużywać zmiany, które wystąpiły w określonych zakresach czasu bez konieczności przeszukiwania całego dziennika. Aby dowiedzieć się więcej, zobacz [specyfikacje](#specifications).

Dostępny segment godzinowy kanału informacyjnego zmiany jest opisany w pliku manifestu, który określa ścieżki do plików kanału informacyjnego zmiany dla tego segmentu. Lista `$blobchangefeed/idx/segments/` katalogu wirtualnego pokazuje te segmenty uporządkowane według czasu. Ścieżka segmentu opisuje początek okresu godzinowego, który reprezentuje segment. Za pomocą tej listy można odfiltrować segmenty dzienników, które są dla Ciebie ważne.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` jest tworzony automatycznie po włączeniu źródła zmian. Możesz bezpiecznie zignorować ten plik. Jest to zawsze pusty plik inicjujący. 

Plik manifestu segmentu (`meta.json`) pokazuje ścieżkę plików źródła zmian dla tego segmentu we właściwości `chunkFilePaths`. Oto przykład pliku manifestu segmentu.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Kontener `$blobchangefeed` pojawia się tylko po włączeniu funkcji kanału informacyjnego zmiany na Twoim koncie. Przed wyświetleniem listy obiektów BLOB w kontenerze należy poczekać kilka minut po włączeniu kanału informacyjnego zmiany. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Zmiana rekordów zdarzeń

Pliki kanału informacyjnego zmiany zawierają serię rekordów zdarzeń zmiany. Każdy rekord zdarzenia zmiany odnosi się do jednej zmiany w pojedynczym obiekcie blob. Rekordy są serializowane i zapisywane w pliku przy użyciu specyfikacji formatu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) . Rekordy można odczytać przy użyciu specyfikacji formatu pliku Avro. Istnieje kilka bibliotek dostępnych do przetwarzania plików w tym formacie.

Pliki kanału informacyjnego zmiany są przechowywane w `$blobchangefeed/log/` katalogu wirtualnym jako [Dołącz obiekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Pierwszy plik kanału informacyjnego zmiany w każdej ścieżce będzie miał `00000` w nazwie pliku (na przykład `00000.avro`). Nazwa każdego kolejnego pliku dziennika dodanego do tej ścieżki zostanie zwiększona o 1 (na przykład: `00001.avro`).

Oto przykład zmiany rekordu zdarzenia z pliku źródła zmian konwertowanego na format JSON.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Aby uzyskać opis każdej właściwości, zobacz [Azure Event Grid schemacie zdarzenia dla BLOB Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Pliki źródła zmian dla segmentu nie są natychmiast wyświetlane po utworzeniu segmentu. Długość opóźnienia jest w normalnym interwale opóźnienia publikowania źródła zmian, które jest w ciągu kilku minut od zmiany.

<a id="specifications"></a>

## <a name="specifications"></a>Specyfikacje

- Rekordy zdarzeń zmiany są dołączane tylko do źródła zmian. Po dołączeniu tych rekordów są one niezmienne, a pozycja rekordu jest stabilna. Aplikacje klienckie mogą obsługiwać własne punkty kontrolne na pozycji odczytu źródła zmian.

- Zmiany rekordów zdarzeń są dołączane w kolejności kilku minut od zmiany. Aplikacje klienckie mogą korzystać z rekordów, ponieważ są one dołączane do uzyskiwania strumieniowego dostępu lub luzem w dowolnym innym czasie.

- Zmiany rekordów zdarzeń są uporządkowane według kolejności modyfikacji **na obiekt BLOB**. Kolejność zmian w obiektach Blob nie jest zdefiniowana w Blob Storage platformy Azure. Wszystkie zmiany w poprzednim segmencie są przed zmianami w kolejnych segmentach.

- Zmiany rekordów zdarzeń są serializowane w pliku dziennika przy użyciu specyfikacji formatu [1.8.2 Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) .

- Zmień rekordy zdarzeń, w których `eventType` ma wartość `Control` są wewnętrznymi rekordami systemowymi i nie odzwierciedlają zmiany w obiektach na Twoim koncie. Można bezpiecznie zignorować te rekordy.

- Wartości w zbiorze właściwości `storageDiagnonstics` są przeznaczone wyłącznie do użytku wewnętrznego i nie są przeznaczone do użytku przez aplikację. Twoje aplikacje nie powinny mieć umownej zależności od tych danych. Można bezpiecznie zignorować te właściwości.

- Czas reprezentowany przez segment jest **przybliżony** z granicami wynoszącymi 15 minut. Aby zapewnić użycie wszystkich rekordów w określonym czasie, użyj kolejnego i następnego segmentu godzin.

- Każdy segment może mieć inną liczbę `chunkFilePaths` ze względu na wewnętrzne partycjonowanie strumienia dziennika w celu zarządzania przepływności publikowania. Pliki dziennika w każdej `chunkFilePath` są gwarantowane, że zawierają wzajemnie wykluczające się obiekty blob i mogą być używane i przetwarzane równolegle bez naruszania kolejności modyfikacji na obiekt BLOB podczas iteracji.

- Segmenty rozpoczynają się w stanie `Publishing`. Po zakończeniu dołączania rekordów do segmentu zostanie `Finalized`. Pliki dziennika w dowolnym segmencie, który przypada po dacie `LastConsumable` właściwości w pliku `$blobchangefeed/meta/Segments.json`, nie powinny być używane przez aplikację. Oto przykład właściwości `LastConsumable`w pliku `$blobchangefeed/meta/Segments.json`:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Zarejestruj swoją subskrypcję (wersja zapoznawcza)

Ponieważ kanał informacyjny zmiany jest tylko w publicznej wersji zapoznawczej, należy zarejestrować swoją subskrypcję, aby korzystać z tej funkcji.

### <a name="register-by-using-powershell"></a>Rejestrowanie przy użyciu programu PowerShell

W konsoli programu PowerShell uruchom następujące polecenia:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Rejestrowanie przy użyciu interfejsu wiersza polecenia platformy Azure

W Azure Cloud Shell Uruchom następujące polecenia:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Warunki i znane problemy (wersja zapoznawcza)

W tej sekcji opisano znane problemy i warunki w bieżącej publicznej wersji zapoznawczej źródła zmian. 
- W przypadku wersji zapoznawczej musisz najpierw [zarejestrować swoją subskrypcję](#register) , aby można było włączyć funkcję źródła zmian dla konta magazynu w regionach westcentralus lub westus2. 
- Źródło zmian przechwytuje tylko operacje tworzenia, aktualizowania, usuwania i kopiowania. Aktualizacje metadanych nie są obecnie przechwytywane w wersji zapoznawczej.
- Zmiany rekordów zdarzeń dla jednej zmiany mogą pojawić się więcej niż jeden raz w kanale zmian.
- Nie można jeszcze zarządzać okresem istnienia plików dziennika kanału informacyjnego zmian przez ustawienie zasad przechowywania opartych na czasie i nie można usunąć obiektów BLOB 
- Właściwość `url` pliku dziennika jest obecnie zawsze pusta.
- Właściwość `LastConsumable` w pliku Segments. JSON nie zawiera pierwszego segmentu, który kończy się podawaniem zmian. Ten problem występuje tylko po sfinalizowaniu pierwszego segmentu. Wszystkie kolejne segmenty po pierwszej godzinie są dokładnie przechwytywane we właściwości `LastConsumable`.
- Nie widzisz obecnie kontenera **$blobchangefeed** podczas wywoływania interfejsu API ListContainers, a kontener nie jest wyświetlany na Azure Portal lub Eksplorator usługi Storage
- Konta magazynu, w przypadku których wcześniej zainicjowano [pracę w trybie failover](../common/storage-disaster-recovery-guidance.md) , mogą mieć problemy z plikiem dziennika, które nie są wyświetlane. Wszystkie przyszłe przełączenia w tryb failover może mieć wpływ na plik dziennika w wersji zapoznawczej.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaka jest różnica między źródłem zmian i rejestrowaniem analityka magazynu?
Dzienniki analityczne zawierają rekordy wszystkich operacji odczytu, zapisu, listy i usuwania z żądaniami zakończonymi powodzeniem i niepomyślnymi przez wszystkie operacje. Dzienniki analizy są najlepszym nakładem pracy i kolejność nie jest gwarantowana.

Źródło zmian to rozwiązanie, które zapewnia dziennik transakcyjny pomyślnych mutacji lub zmiany na koncie, takie jak tworzenie obiektów blob, modyfikowanie i usuwanie. Funkcja kanału informacyjnego zmiany gwarantuje, że wszystkie zdarzenia mają być rejestrowane i wyświetlane w kolejności pomyślnych zmian na obiekt BLOB, co oznacza, że nie trzeba odfiltrować szumu od dużej ilości operacji odczytu lub nieudanych żądań. Kanał informacyjny zmiany jest w sposób zasadniczy zaprojektowany i zoptymalizowany pod kątem tworzenia aplikacji, które wymagają pewnych gwarancji.

### <a name="should-i-use-change-feed-or-storage-events"></a>Czy należy używać kanału informacyjnego zmian czy zdarzeń magazynu?
Można korzystać ze wszystkich funkcji jako źródła danych zmian i [zdarzeń magazynu obiektów BLOB](storage-blob-event-overview.md) , aby zapewnić te same informacje z gwarancją niezawodności dostarczania, z główną różnicą opóźnienia, kolejnością i przechowywaniem rekordów zdarzeń. Kanał informacyjny zmiany publikuje rekordy w dzienniku w ciągu kilku minut od zmiany, a także gwarantuje kolejność operacji zmiany na obiekt BLOB. Zdarzenia magazynu są wypychane w czasie rzeczywistym i mogą nie być uporządkowane. Zdarzenia dotyczące zmiany źródła danych są przechowywane w ramach konta magazynu jako stabilne dzienniki z własnym określonym przechowywaniem, podczas gdy zdarzenia magazynu są przejściowe używane przez program obsługi zdarzeń, chyba że zostaną jawnie zapisane. Dzięki użyciu kanału informacyjnego zmiany dowolna liczba aplikacji może korzystać z dzienników we własnych wygodach przy użyciu interfejsów API lub zestawów SDK obiektów BLOB. 

## <a name="next-steps"></a>Następne kroki

- Zobacz przykład sposobu odczytywania źródła zmian za pomocą aplikacji klienckiej platformy .NET. Zobacz [dzienniki źródła zmian procesów na platformie Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Dowiedz się więcej na temat reagowania na zdarzenia w czasie rzeczywistym. Zobacz [, jak BLOB Storage zdarzenia](storage-blob-event-overview.md)
- Dowiedz się więcej na temat szczegółowych informacji rejestrowania dla operacji zakończonych powodzeniem i zakończonych niepowodzeniem dla wszystkich żądań. Zobacz [Rejestrowanie analizy usługi Azure Storage](../common/storage-analytics-logging.md)
