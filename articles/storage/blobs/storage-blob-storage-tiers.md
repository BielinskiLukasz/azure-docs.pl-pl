---
title: Warstwy dostępu gorąca, chłodna i archiwalna dla obiektów Blob — Azure Storage
description: Warstwy dostępu gorąca, chłodna i archiwalna dla kont usługi Azure Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389087"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna

Usługa Azure Storage oferuje różne warstwy dostępu, dzięki którym można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Dostępne warstwy dostępu obejmują:

- Zoptymalizowany pod kątem przechowywania danych, które są często dostępne.
- **Chłodnie** zoptymalizowane pod kątem przechowywania danych, które są rzadko używane i są przechowywane przez co najmniej 30 dni.
- Optymalizacja zoptymalizowana pod kątem przechowywania danych, które są rzadko używane i przechowywane przez co najmniej 180 dni z elastycznymi wymaganiami opóźnienia (w kolejności godzin).

Poniższe uwagi dotyczą różnych warstw dostępu:

- Na poziomie konta można ustawić tylko warstwy dostępu gorąca i chłodna. Warstwa dostępu archiwalnego nie jest dostępna na poziomie konta.
- Warstwy gorąca, chłodna i archiwalna można ustawić na poziomie obiektu BLOB podczas przekazywania lub po przekazaniu.
- Dane w warstwie dostępu chłodnego mogą być nieznacznie niższej dostępności, ale nadal wymagają dużej trwałości, opóźnienia pobierania i charakterystyki przepływności podobne do danych gorąca. W przypadku danych chłodnych nieznacznie niższa umowa dotycząca poziomu usług dostępności i wyższe koszty dostępu w porównaniu z gorącą usługą są akceptowalnymi kosztami niższych kosztów magazynowania.
- Funkcja archiwalny magazyn przechowuje dane w trybie offline i oferuje najniższe koszty magazynowania, ale również najwyższą ilość danych i dostęp do nich.

Dane przechowywane w chmurze zwiększają się w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne w zależności od tego, jak są generowane, przetwarzane i dostępne w okresie istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają w stanie bezczynności w chmurze i są rzadko używane, jeśli kiedykolwiek są do niego dostępne.

Każdy z tych scenariuszy dostępu do danych korzysta z innej warstwy dostępu, która jest zoptymalizowana pod kątem określonego wzorca dostępu. Dzięki warstwom dostępu gorąca, chłodna i archiwalna usługa Azure Blob Storage jest wymagana w przypadku zróżnicowanych warstw dostępu z oddzielnymi modelami cenowymi.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Przechowywanie warstw danych między gorącą, chłodną i archiwum jest obsługiwane tylko w przypadku kont BLOB Storage i Ogólnego przeznaczenia v2 (GPv2). Konta Ogólnego przeznaczenia V1 (GPv1) nie obsługują warstw. Klienci mogą łatwo przekonwertować istniejące konta GPv1 lub BLOB Storage na konta GPv2 przy użyciu Azure Portal. GPv2 udostępnia nowe ceny i funkcje dla obiektów blob, plików i kolejek. Niektóre funkcje i ceny są oferowane tylko w ramach kont GPv2. Oceń przy użyciu kont GPv2 po pełnym przeglądzie cen. Niektóre obciążenia mogą być droższe w GPv2 niż GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Konta magazynu obiektów blob i GPv2 uwidaczniają atrybut **Warstwa dostępu** na poziomie konta. Ten atrybut umożliwia określenie domyślnej warstwy dostępu dla dowolnego obiektu BLOB, który nie jest jawnie ustawiony na poziomie obiektu. W przypadku obiektów z zestawem warstwy na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można stosować tylko na poziomie obiektu. W każdej chwili można przełączać się między tymi warstwami dostępu.

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Warstwa dostępu gorąca ma wyższe koszty magazynowania niż warstwy chłodna i archiwum, ale najtańsze koszty dostępu. Przykładowe scenariusze użycia dla warstwy dostępu gorąca obejmują:

- Dane, które są używane w aktywnym lub oczekiwanie, są często dostępne (odczyt i zapis).
- Dane przygotowane do przetworzenia i ostatecznej migracji do warstwy dostępu chłodna.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa dostępu chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu z magazynem gorąca. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dla warstwy dostępu chłodnego obejmują:

- Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
- Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
- Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Warstwa dostępu archiwalnego ma najniższy koszt magazynowania. Jednak ma wyższy koszt pobierania danych w porównaniu do warstw gorąca i chłodna. Pobieranie danych w warstwie archiwum może potrwać kilka godzin. Dane muszą pozostać w warstwie archiwum przez co najmniej 180 dni lub podlegają opłacie za wczesne usunięcie.

Gdy obiekt BLOB znajduje się w magazynie archiwum, dane obiektów BLOB są w trybie offline i nie można ich odczytać, zastąpić ani modyfikować. Aby odczytać lub pobrać obiekt BLOB w archiwum, należy najpierw go przetworzyć w warstwie online. Nie można wykonać migawek obiektu BLOB w magazynie archiwum. Jednak metadane obiektu BLOB pozostają w trybie online i są dostępne, umożliwiając wyświetlenie listy obiektów blob i jego właściwości. W przypadku obiektów BLOB w archiwum jedynymi prawidłowymi operacjami są GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob i DeleteBlob. Aby dowiedzieć się więcej, zobacz informacje dotyczące [danych obiektów BLOB w warstwie archiwum](storage-blob-rehydration.md) .

Przykładowe scenariusze użycia dla warstwy dostępu archiwalnego obejmują:

- Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
- Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci
- Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp

## <a name="account-level-tiering"></a>Obsługa warstw na poziomie konta

Obiekty blob we wszystkich trzech warstwach dostępu mogą współistnieć w ramach tego samego konta. Wszystkie obiekty blob, które nie mają jawnie przypisanej warstwy, są uwzględniane w ustawieniach warstwy dostępu konta. Jeśli warstwa dostępu pochodzi z konta, zostanie wyświetlona właściwość obiektu BLOB **wywnioskowanej warstwy dostępu** ustawiona na wartość "true", a właściwość obiektu BLOB **warstwy dostępu** jest zgodna z warstwą konta. W Azure Portal Właściwość _wywnioskowana Warstwa dostępu_ jest wyświetlana z warstwą dostępu obiektu BLOB jako **gorąca (wywnioskowana)** lub **chłodna (wywnioskowana)** .

Zmiana warstwy dostępu do konta ma zastosowanie do wszystkich obiektów _odroczonych w warstwie dostępu_ przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Jeśli przełączysz warstwę konta z gorąca na chłodna, nastąpi naliczanie opłat za operacje zapisu (za 10 000) dla wszystkich obiektów Blob bez ustawionej warstwy tylko na kontach GPv2. Ta zmiana nie jest naliczana w ramach kont usługi BLOB Storage. Zostanie naliczona opłata za operacje odczytu (za 10 000) i pobieranie danych (za GB) w przypadku przełączenia się z chłodnej na gorącą w ramach kont usługi BLOB Storage lub GPv2.

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Obsługa warstw na poziomie obiektów BLOB umożliwia przekazywanie danych do wybranej warstwy dostępu przy użyciu operacji [Put BLOB](/rest/api/storageservices/put-blob) lub [Put Block list](/rest/api/storageservices/put-block-list) oraz zmianę warstwy danych na poziomie obiektu za pomocą funkcji [Ustaw warstwę obiektów BLOB](/rest/api/storageservices/set-blob-tier) lub funkcję [zarządzania cyklem życia](#blob-lifecycle-management) . Dane można przekazywać do wymaganej warstwy dostępu, a następnie łatwo zmienić warstwę dostępu do obiektów BLOB w warstwach gorąca, chłodna lub archiwalna w miarę zmiany wzorców użycia, bez konieczności przenoszenia danych między kontami. Wszystkie żądania zmiany warstwy są wykonywane natychmiast, a zmiany warstwy między gorącą i chłodną są chwilowo. Ponownego wypełniania obiekt BLOB z archiwum może jednak trwać kilka godzin.

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Podczas zastępowania obiektu BLOB w warstwie gorąca lub chłodna nowo utworzony obiekt BLOB dziedziczy warstwę obiektu BLOB, który został zastąpiony, chyba że nowa warstwa dostępu do obiektów BLOB jest jawnie ustawiona podczas tworzenia. Jeśli obiekt BLOB znajduje się w warstwie archiwum, nie można go zastąpić, więc przekazywanie tego samego obiektu BLOB nie jest dozwolone w tym scenariuszu. 

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Obecnie nie można zmienić warstwy blokowego obiektu BLOB, który ma migawki.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów BLOB

Blob Storage Zarządzanie cyklem życia obejmuje zaawansowane zasady oparte na regułach, których można użyć do przechodzenia danych do najlepszej warstwy dostępu oraz do wygasania danych na koniec cyklu życia. Aby dowiedzieć się więcej [, zobacz Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md) .  

> [!NOTE]
> Dane przechowywane na koncie magazynu blokowych obiektów BLOB (wydajność Premium) nie mogą być obecnie warstwami gorąca, chłodna lub archiwalna przy użyciu [warstwy obiektu BLOB](/rest/api/storageservices/set-blob-tier) lub usługi Azure Blob Storage.
> Aby przenieść dane, należy synchronicznie skopiować obiekty blob z konta blokowego usługi BLOB Storage do warstwy dostępu gorąca na innym koncie przy użyciu [funkcji Put blok z adresu URL](/rest/api/storageservices/put-block-from-url) lub wersji AzCopy, która obsługuje ten interfejs API.
> *Blok Put z adresu URL* synchronicznie kopiuje dane na serwerze, co oznacza, że wywołanie kończy się tylko wtedy, gdy wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt BLOB zostanie przekazany lub przeniesiony do warstwy gorąca, chłodna lub archiwalna, jest naliczana według odpowiedniej stawki bezpośrednio po zmianie warstwy.

Gdy obiekt BLOB jest przenoszony do warstwy chłodnicy (gorąca > chłodna, archiwum gorąca > lub archiwum chłodnych >), operacja jest rozliczana jako operacja zapisu w warstwie docelowej, w przypadku której stosuje się opłaty za operacje zapisu (za 10 000) i zapis danych (za GB) dla warstwy docelowej.

Gdy obiekt BLOB jest przenoszony do warstwy gorąca (archiwum-> chłodna, > archiwalna lub chłodna > gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej, gdzie stosowane są opłaty za operacje odczytu (za 10 000) i pobieranie danych (za GB) dla warstwy źródłowej. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum. [Dane ponownego wypełniania z archiwum](storage-blob-rehydration.md) zajmują czas, a dane będą obciążane cenami, dopóki dane nie zostaną przywrócone w trybie online, a zmiany warstwy obiektów BLOB na gorącą lub chłodną. Poniższa tabela zawiera podsumowanie sposobu rozliczania zmian warstwy:

| | **Opłaty za zapis (operacja i dostęp)** | **Opłaty za odczyt (operacja i dostęp)**
| ---- | ----- | ----- |
| **Kierunek SetBlobTier** | Chłodna > gorąca,<br> archiwum > gorąca,<br> archiwum > chłodnych | archiwum — > chłodna,<br> archiwum — > gorąca,<br> Chłodna > gorąca

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Każdy obiekt BLOB, który jest przenoszony do warstwy chłodna (tylko konta GPv2), jest objęty okresem wcześniejszego usunięcia wynoszącym 30 dni. Każdy obiekt BLOB, który jest przenoszony do warstwy archiwum, podlega okresowi wcześniejszego usunięcia z archiwum 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt BLOB zostanie przeniesiony do archiwum, a następnie usunięty lub przeniesiony do warstwy gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie, równą 135 (180 minus 45) dni przechowywania tego obiektu BLOB w archiwum.

Możesz obliczyć wczesne usunięcie przy użyciu właściwości obiektu BLOB, **ostatniej modyfikacji**, jeśli nie ma żadnych zmian w warstwie dostępu. W przeciwnym razie można użyć, gdy warstwa dostępu została ostatnio zmodyfikowana do chłodna lub archiwalna, wyświetlając właściwość obiektu BLOB: **Warstwa dostępu-zmiana-czas**. Aby uzyskać więcej informacji na temat właściwości obiektów blob, zobacz [pobieranie właściwości obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porównywanie opcji magazynu blokowych obiektów BLOB

W poniższej tabeli przedstawiono porównanie magazynu obiektów BLOB wydajności w warstwie Premium oraz warstw dostępu gorąca, chłodna i archiwalna.

|                                           | **Wydajność warstwy Premium**   | **Warstwa gorąca** | **Warstwa chłodna**       | **Warstwa Archiwum**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostępność**                          | 99,9%                     | 99,9%        | 99%                 | W trybie offline           |
| **Dostępność** <br> **(odczyty RA-GRS)**  | Nie dotyczy                       | 99,99%       | 99,9%               | W trybie offline           |
| **Opłaty za użycie**                         | Wyższe koszty magazynowania, niższy koszt dostępu i transakcji | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| **Minimalny rozmiar obiektu**                   | Nie dotyczy                       | Nie dotyczy          | Nie dotyczy                 | Nie dotyczy               |
| **Minimalny czas magazynowania**              | Nie dotyczy                       | Nie dotyczy          | 30 dni<sup>1</sup> | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | Jednocyfrowe milisekundy | milisekundy | milisekundy        | godz.<sup>2</sup> |

<sup>1</sup> obiekty w warstwie chłodna na kontach GPv2 mają minimalny okres przechowywania równy 30 dni. Konta usługi BLOB Storage nie mają minimalnego czasu trwania przechowywania dla warstwy chłodna.

<sup>2</sup> Archive Storage obecnie obsługuje 2 dwuwodne priorytety, wysoki i standardowy, który oferuje różne opóźnienia pobierania. Aby uzyskać więcej informacji, zobacz informacje o [rehydratacji danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md).

> [!NOTE]
> Konta magazynu obiektów BLOB obsługują te same cele dotyczące wydajności i skalowalności co konta magazynu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze przy użyciu Azure Portal i programu PowerShell:

- Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
- Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz pozycję **wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja** , aby wyświetlić i zmienić konfigurację konta.

1. Wybierz odpowiednią warstwę dostępu do własnych potrzeb: Ustaw **warstwę dostępu** na **chłodna** lub **gorąca**.

1. Kliknij przycisk **Zapisz** u góry.

![Zmień warstwę konta magazynu](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Za pomocą poniższego skryptu programu PowerShell można zmienić warstwę konta. Zmienna `$rgName` musi zostać zainicjowana przy użyciu nazwy grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana przy użyciu nazwy konta magazynu. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmienianie warstwy obiektu BLOB na koncie GPv2 lub BLOB Storage
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz pozycję **wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. Wybierz kontener, a następnie wybierz obiekt BLOB.

1. We **właściwościach obiektu BLOB**wybierz pozycję **Zmień warstwę**.

1. Wybierz warstwę dostępu **gorąca**, **chłodna**lub **archiwalna** . Jeśli obiekt BLOB znajduje się obecnie w archiwum i chcesz go umieścić w warstwie online, możesz również wybrać priorytet rehydratacji dla warstwy **standardowa** lub **wysoka**.

1. Wybierz pozycję **Zapisz** u dołu.

![Zmień warstwę konta magazynu](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Za pomocą poniższego skryptu programu PowerShell można zmienić warstwę obiektów BLOB. Zmienna `$rgName` musi zostać zainicjowana przy użyciu nazwy grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana przy użyciu nazwy konta magazynu. Zmienna `$containerName` musi zostać zainicjowana przy użyciu nazwy kontenera. Zmienna `$blobName` musi zostać zainicjowana przy użyciu nazwy obiektu BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cen dla magazynu blokowych obiektów BLOB na podstawie warstwy każdego obiektu BLOB. Należy pamiętać o następujących kwestiach dotyczących rozliczeń:

- **Koszty usługi Storage**: oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
- **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu chłodna i archiwalna naliczana jest opłata za dostęp do danych za każdy gigabajt dla operacji odczytu.
- **Koszty transakcji**: jest naliczana opłata za transakcję dla wszystkich warstw, które zwiększają się, gdy warstwa uzyskuje chłodnicę.
- **Koszty transferu danych replikacji geograficznej**: ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
- **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
- **Zmiana warstwy dostępu**: zmiana warstwy dostępu do konta spowoduje naliczanie opłat za zmianę warstwy dla _odroczonych obiektów BLOB warstwy dostępu_ przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Aby uzyskać informacje na temat zmiany warstwy dostępu dla pojedynczego obiektu BLOB, zapoznaj się z [rozliczeniami warstwowymi na poziomie obiektów BLOB](#blob-level-tiering-billing).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen blokowych obiektów blob, zobacz stronę z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) . Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage, czy GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty we wszystkich trzech warstwach dostępu (gorąca, chłodna i archiwalna) na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa konta, która ma zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Obsługa warstw na poziomie obiektów BLOB umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu dla konta. Obiekty blob w jednej z trzech warstw dostępu (gorąca, chłodna lub archiwalna) mogą znajdować się w ramach tego samego konta.

**Czy mogę zmienić domyślną warstwę dostępu na moim koncie magazynu obiektów blob lub GPv2?**

Tak, możesz zmienić domyślną warstwę konta, ustawiając atrybut **Warstwa dostępu** na koncie magazynu. Zmiana warstwy konta ma zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy (na przykład **gorąca (wywnioskowana)** lub **chłodna (wywnioskowana)** . Przełączenie warstwy konta z gorąca na chłodnie powoduje operacje zapisu (na 10 000) dla wszystkich obiektów Blob bez zestawu warstwy na kontach GPv2 tylko i przełączanie z chłodnej na gorącą operację odczytu (za 10 000) i pobieranie danych (za GB) dla wszystkich obiektów BLOB w usłudze BLOB Storage i konta GPv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwy dostępu gorąca i chłodna mogą być ustawione jako domyślna Warstwa dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu. W przypadku przekazywania obiektów BLOB należy określić wybraną warstwę dostępu, która ma być gorąca, chłodna lub archiwalna, niezależnie od domyślnej warstwy konta. Ta funkcja umożliwia zapisanie danych bezpośrednio w warstwie archiwum, co pozwala na wykorzystanie oszczędności kosztów od momentu utworzenia danych w magazynie obiektów BLOB.

**W jakich regionach są dostępne warstwy dostępu gorąca, chłodna i archiwalna?**

Warstwy dostępu gorąca i chłodna wraz z warstwami na poziomie obiektów BLOB są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty blob w warstwie dostępu chłodnego działają inaczej niż te w warstwie dostępu gorąca?**

Obiekty blob w warstwie dostępu gorącą mają takie samo opóźnienie jak obiekty blob na kontach GPv1, GPv2 i BLOB Storage. Obiekty blob w warstwie dostępu chłodnego mają podobne opóźnienie (w milisekundach) jako obiekty blob na kontach GPv1, GPv2 i BLOB Storage. Obiekty blob w warstwie dostępu archiwizowania mają kilka godzin opóźnienia na kontach GPv1, GPv2 i BLOB Storage.

Obiekty blob w warstwie dostępu chłodnego mają nieco niższy poziom dostępności usług (SLA) niż obiekty blob przechowywane w warstwie dostępu gorąca. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje archiwizowania, w tym GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier i DeleteBlob, 100 są spójne z gorącą i chłodną. Nie można odczytać lub zmodyfikować danych obiektu BLOB w warstwie archiwum, dopóki nie zostanie on przeodwodniony; w archiwum są obsługiwane tylko operacje odczytu metadanych obiektu BLOB.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego wypełniania można użyć operacji pobierania właściwości obiektu BLOB do sondowania atrybutu **stanu archiwum** i potwierdzić, kiedy zmiana warstwy zostanie zakończona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna. Aby dowiedzieć się więcej, zobacz informacje dotyczące [danych obiektów BLOB w warstwie archiwum](storage-blob-rehydration.md) .

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt BLOB jest zawsze rozliczany zgodnie z warstwą określoną przez właściwość **warstwy dostępu** obiektu BLOB. Po ustawieniu nowej warstwy online dla obiektu BLOB Właściwość **Warstwa dostępu** natychmiast odzwierciedla nową warstwę dla wszystkich przejść. Jednak ponownego wypełniania obiektu BLOB z warstwy archiwum offline do warstwy gorąca lub chłodna może potrwać kilka godzin. W tym przypadku opłaty są naliczane według stawek za archiwizację do momentu ukończenia ponownego wypełniania. w tym momencie Właściwość **Warstwa dostępu** odzwierciedla nową warstwę. Po ponownym przekonwertowaniu do warstwy online naliczane są opłaty za ten obiekt BLOB przy użyciu stawki gorąca lub chłodna.

**Jak mogę określić, czy po usunięciu lub przeniesieniu obiektu BLOB z warstwy chłodna lub archiwum ma być naliczana opłata za wczesne usunięcie?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Możesz określić, jak długo obiekt BLOB znajduje się w warstwie chłodna lub archiwum, sprawdzając właściwość obiektu BLOB **czasu zmiany warstwy dostępu** , która zapewnia sygnaturę ostatniej zmiany warstwy. Jeśli warstwa obiektu BLOB nigdy nie została zmieniona, można sprawdzić **ostatnią zmodyfikowaną** właściwość obiektu BLOB. Aby uzyskać więcej informacji, zobacz [chłodna i archiwum wczesne usuwanie](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Magazyn danych oraz inne limity są ustawiane na poziomie konta, a nie na warstwie dostępu. Możesz użyć wszystkich limitów w jednej warstwie lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Oceń gorące, chłodne i archiwalne na kontach GPv2 i BLOB Storage

- [Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)
- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
- [Dowiedz się więcej na temat ponownego wypełniania danych obiektów BLOB w warstwie Archiwum](storage-blob-rehydration.md)
- [Określanie, czy wydajność Premium będzie korzystna dla aplikacji](storage-blob-performance-tiers.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)
- [Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)
- [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
