---
title: Wprowadzenie do usługi Azure Storage — magazynu w chmurze na platformie Azure | Microsoft Docs
description: Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft. Usługa Azure Storage oferuje magazyn dla obiektów danych cechujący się wysoką dostępnością, bezpieczeństwem, trwałością, wysoką skalowalnością i nadmiarowością.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 612da322e0d248f9b0000c8f0006893b87b74e44
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084643"
---
# <a name="introduction-to-azure-storage"></a>Wprowadzenie do usługi Azure Storage

Usługa Azure Storage to rozwiązanie do magazynowania w chmurze firmy Microsoft dla nowoczesnych scenariuszy magazynowania danych. Usługa Azure Storage oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn obsługi komunikatów dla niezawodnej obsługi komunikatów i magazyn danych NoSQL. Usługa Azure Storage to:

- **Niezawodność i wysoka dostępność.** Nadmiarowość zapewnia bezpieczeństwo danych w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.
- **Bezpieczeństwo.** Wszystkie dane zapisane w usłudze Azure Storage są przez nią szyfrowane. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalność.** Usługa Azure Storage została zaprojektowana jako wysoce skalowalne rozwiązanie spełniające potrzeby związane z magazynowaniem danych i wydajnością współczesnych aplikacji. 
- **Zarządzanie.** Platforma Microsoft Azure obsługuje konserwację sprzętu, aktualizacje i krytyczne problemy za Ciebie.
- **Dostępność.** Dane w usłudze Azure Storage są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia biblioteki klienckie usługi Azure Storage w różnych językach, takich jak .NET, Java, Node. js, Python, PHP, Ruby, go i inne, a także interfejs API REST. Usługa Azure Storage obsługuje skrypty programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Witryna Azure Portal oraz Eksplorator usługi Azure Storage oferują proste rozwiązania wizualne do pracy z danymi.  

## <a name="azure-storage-services"></a>Usługi Azure Storage

Usługa Azure Storage obejmuje następujące usługi danych:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): wysoce skalowalny magazyn obiektów dla danych tekstowych i binarnych.
- [Azure Files](../files/storage-files-introduction.md): zarządzane udziały plików dla wdrożeń lokalnych lub w chmurze.
- [Azure Queues](../queues/storage-queues-introduction.md): magazyn obsługi komunikatów zapewniający niezawodną obsługę komunikatów między składnikami aplikacji. 
- [Azure Tables](../tables/table-storage-overview.md): magazyn obiektów NoSQL do magazynowania ustrukturyzowanych danych bez użycia schematu.

Dostęp do każdej usługi uzyskuje się za pośrednictwem konta magazynu. Aby rozpocząć, zobacz artykuł [Tworzenie konta magazynu](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury, takich jak dane tekstowe lub binarne. 

Usługa Blob Storage to idealne rozwiązanie w następujących przypadkach:

- Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
- Przechowywanie plików do dostępu rozproszonego.
- Przesyłanie strumieniowe audio i wideo.
- Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
- Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Dostęp do obiektów w usłudze Blob Storage można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) i [Ruby](https://azure.github.io/azure-storage-ruby).

Aby uzyskać więcej informacji dotyczących usługi Blob Storage, zobacz [Introduction to Blob storage (Wprowadzenie do usługi Blob Storage)](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

Usługa [Azure Files](../files/storage-files-introduction.md) umożliwia konfigurowanie wysoce dostępnych udziałów plików sieciowych, do których można uzyskiwać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB, Server Message Block). Oznacza to, że wiele maszyn wirtualnych może współdzielić te same pliki z dostępem zarówno do odczytu, jak i do zapisu. Pliki można także odczytywać przy użyciu interfejsu REST lub bibliotek klienckich magazynu.

Jedną z różnic między usługą Azure Files i plikami w firmowym udziale plików jest możliwość dostępu do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego. Tokeny sygnatury dostępu współdzielonego można generować. Umożliwiają one uzyskanie określonego dostępu do prywatnego zasobu przez określony czas.

Udziałów plików można używać w wielu typowych scenariuszach:

- Wiele aplikacji lokalnych korzysta z udziałów plików. Dzięki temu migracja tych aplikacji współdzielących dane na platformę Azure jest łatwiejsza. Jeśli zainstalujesz udział plików przy użyciu tej samej litery dysku, która jest używana przez aplikację lokalną, część aplikacji uzyskująca dostęp do udziału plików powinna działać bez konieczności wprowadzania zmian lub jedynie z minimalnymi zmianami.

- Pliki konfiguracji można przechowywać w udziale plików i uzyskiwać do nich dostęp z wielu maszyn wirtualnych. W udziale plików można przechowywać narzędzia i programy narzędziowe używane przez wielu deweloperów w grupie, dzięki czemu wszyscy będą mogli je znaleźć oraz będą używać tych samych wersji.

- Dzienniki diagnostyczne, metryki i zrzuty awaryjne to tylko trzy przykłady danych, które można zapisywać w udziale plików i przetwarzać lub analizować później.

Obecnie uwierzytelnianie oparte na usłudze Active Directory i listy kontroli dostępu (ACL, access control list) nie są obsługiwane, ale ich obsługa zostanie dodana w przyszłości. W celu uwierzytelniania dostępu do udziału plików są używane poświadczenia konta magazynu. Oznacza to, że każda osoba z zainstalowanym udziałem będzie miała do niego pełny dostęp do odczytu i zapisu.

Aby uzyskać więcej informacji na temat usługi Azure Files, zobacz [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue jest używana do przechowywania i pobierania komunikatów. Komunikaty kolejek mogą mieć rozmiar do 64 KB, a jedna kolejka może zawierać miliony komunikatów. Kolejki są zazwyczaj używane do przechowywania list komunikatów, które mają zostać przetworzone asynchronicznie.

Przykładowo załóżmy, że chcesz, aby klienci mogli przekazywać obrazy, i chcesz utworzyć miniatury dla każdego obrazu. Klient może poczekać, aż utworzysz miniatury podczas przekazywania zdjęcia. Alternatywą jest zastosowanie kolejki. Po zakończeniu przekazywania przez klienta Napisz komunikat do kolejki. Następnie funkcja usługi Azure Functions pobierze ten komunikat z kolejki i utworzy miniatury. Każdą część tego przetwarzania można skalować oddzielnie, co daje większą kontrolę podczas dostosowywania.

Aby uzyskać więcej informacji na temat usługi Azure Queues, zobacz [Wprowadzenie do usługi Azure Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Magazyn tabel

Usługa Azure Table Storage jest teraz częścią usługi Azure Cosmos DB. Aby zapoznać się z dokumentacją usługi Azure Table Storage, przejdź do artykułu [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md). Oprócz istniejącej usługi Azure Table Storage dostępna jest nowa oferta interfejsu API tabel usługi Azure Cosmos DB, który zapewnia tabele zoptymalizowane pod kątem przepływności, globalną dystrybucję i automatyczne indeksy pomocnicze. Aby dowiedzieć się więcej i wypróbować nowe środowisko wersji Premium, zobacz [Interfejs API tabel usługi Azure Cosmos DB](https://aka.ms/premiumtables).

Aby uzyskać więcej informacji na temat usługi Table Storage, zobacz [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Przechowywanie na dysku

Dysk zarządzany przez platformę Azure to wirtualny dysk twardy (VHD). Można traktować go jako dysk fizyczny na serwerze lokalnym, ale zwirtualizowany. Azure Managed disks są przechowywane jako stronicowe obiekty blob, które są losowym obiektem magazynu we/wy na platformie Azure. Nazywamy zarządzany dysk zarządzany, ponieważ jest to Abstrakcja dla stronicowych obiektów blob, kontenerów obiektów blob i kont usługi Azure Storage. W przypadku dysków zarządzanych wszystkie czynności, które należy wykonać, udostępniają dysk, a platforma Azure zajmie się resztą.

Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [wprowadzenie do usługi Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Zabezpieczanie dostępu do kont magazynu

Każde żądanie do usługi Azure Storage musi być autoryzowane. Usługa Azure Storage obsługuje następujące metody autoryzacji:

- **Azure Active Directory (Azure AD) integracja dla danych obiektów blob i kolejek.** Usługa Azure Storage obsługuje uwierzytelnianie i autoryzację w usłudze Azure AD dla usług obiektów blob i kolejek za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Autoryzacja żądań za pomocą usługi Azure AD jest zalecana w celu zapewnienia bezpieczeństwa i łatwość użycia. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).
- **Autoryzacja usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files (wersja zapoznawcza).** Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem Azure Active Directory Domain Services. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie autoryzacji Azure Active Directory za pośrednictwem protokołu SMB dla Azure Files (wersja zapoznawcza)](../files/storage-files-active-directory-overview.md).
- **Autoryzacja za pomocą klucza współużytkowanego.** Usługi Azure Storage Blob, Queue i Table i Azure Files obsługują autoryzację za pomocą klucza współużytkowanego. klient korzystający z autoryzacji klucza współużytkowanego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autoryzacja przy użyciu sygnatur dostępu współdzielonego (SAS).** Sygnatura dostępu współdzielonego (SAS) jest ciągiem zawierającym token zabezpieczający, który może być dołączany do identyfikatora URI zasobu magazynu. Token zabezpieczający hermetyzuje ograniczenia, takie jak uprawnienia i interwał dostępu. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy dostęp do kontenerów i obiektów BLOB.** Kontener i jego obiekty blob mogą być publicznie dostępne. Po określeniu, że kontener lub obiekt BLOB jest publiczny, każda osoba może ją odczytać anonimowo; nie jest wymagane żadne uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>Szyfrowanie

W przypadku usług Storage dostępne są dwa podstawowe rodzaje szyfrowania. Aby uzyskać więcej informacji na temat bezpieczeństwa i szyfrowania, zobacz [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Szyfrowanie usługi Azure Storage chroni i zabezpiecza dane zgodnie ze zobowiązaniami dotyczącymi zabezpieczeń i zgodności w organizacji. Usługa Azure Storage automatycznie szyfruje wszystkie dane przed utrwaleniem do konta magazynu i odszyfrowuje je przed pobraniem. Procesy szyfrowania, odszyfrowywania i zarządzania kluczami są całkowicie niewidoczne dla użytkowników. Klienci mogą również zdecydować się na zarządzanie własnymi kluczami przy użyciu Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Biblioteki klienta usługi Azure Storage zapewniają metody szyfrowania danych z biblioteki klienta przed wysłaniem ich przez sieć i Odszyfrowanie odpowiedzi. Dane szyfrowane za pomocą szyfrowania po stronie klienta są również szyfrowane przez usługę Azure Storage. Aby uzyskać więcej informacji o szyfrowaniu po stronie klienta, zobacz [szyfrowanie po stronie klienta za pomocą platformy .NET dla usługi Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Nadmiarowość

Aby zapewnić trwałość danych, usługa Azure Storage przeprowadza replikację wielu kopii danych. Podczas konfigurowania konta magazynu należy wybrać opcję nadmiarowości.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Aby uzyskać więcej informacji na temat odzyskiwania po awarii, zobacz [Disaster recovery and storage account failover (preview) in Azure Storage](storage-disaster-recovery-guidance.md) (Odzyskiwanie po awarii i tryb failover konta magazynu w wersji zapoznawczej w usłudze Azure Storage).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferowanie danych do i z usługi Azure Storage

Jest dostępnych kilka opcji umożliwiających przenoszenie danych do usługi Azure Storage lub poza nią. Wybór opcji zależy od rozmiaru zestawu danych i przepustowości sieci. Aby uzyskać więcej informacji, zobacz [Wybieranie rozwiązania platformy Azure do transferu danych](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach usługi Azure Storage, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>Narzędzia, biblioteki oraz interfejsy API usługi Storage

Zasoby usługi Azure Storage są dostępne za pomocą dowolnego języka, który obsługuje żądania HTTP/HTTPS. Dodatkowo Magazyn Azure oferuje biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają wiele aspektów pracy z usługą Azure Storage dzięki obsłudze szczegółów, takich jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, zarządzanie wyjątkami, automatyczne ponawianie, zachowania podczas działania itd. Biblioteki są obecnie dostępne dla następujących języków i platform, a kolejne są planowane:

### <a name="azure-storage-data-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API danych usługi Azure Storage

- [Interfejs API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/)
- [Biblioteka klienta usługi Azure Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Biblioteka klienta usługi Azure Storage dla środowiska Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Biblioteka klienta usługi Azure Storage dla środowiska Node. js](https://docs.microsoft.com/javascript/api/overview/azure/storage)
- [Biblioteka klienta usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python)
- [Biblioteka klienta usługi Azure Storage dla języka PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteka klienta usługi Azure Storage dla języka Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteka klienta usługi Azure Storage dla programuC++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API zarządzania usługi Azure Storage

- [Interfejs API REST dostawcy zasobów usługi Storage](https://docs.microsoft.com/rest/api/storagerp/)
- [Biblioteka klienta dostawcy zasobów usługi Storage dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [Interfejs API REST zarządzania usługą Storage (klasyczny)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API ruchu danych usługi Azure Storage

- [Interfejs API REST usługi Import/Eksport usługi Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Biblioteka klienta przenoszenia danych usługi Storage dla platformy .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Narzędzia i programy narzędziowe

- [Polecenia cmdlet programu Azure PowerShell dla usługi Storage](https://docs.microsoft.com/powershell/module/az.storage)
- [Polecenia cmdlet interfejsu wiersza polecenia platformy Azure dla usługi Storage](https://docs.microsoft.com/cli/azure/storage)
- [Narzędzie wiersza polecenia AzCopy](https://aka.ms/downloadazcopy)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest bezpłatną, autonomiczną aplikacją oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
- [Azure Storage Client Tools](../storage-explorers.md)
- [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure Storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).
