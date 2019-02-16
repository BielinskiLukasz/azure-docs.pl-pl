---
title: Przegląd konta usługi Azure storage | Dokumentacja firmy Microsoft
description: Informacje o opcjach dotyczące tworzenia i używania konta usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/13/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 430aa6e60efe63e6741436e53152126bc15798fc
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327854"
---
# <a name="azure-storage-account-overview"></a>Przegląd konta usługi Azure storage

Konto magazynu platformy Azure zawiera wszystkie obiekty danych usługi Azure Storage: obiekty BLOB, pliki, kolejki, tabele i dyski. Dane na Twoim koncie usługi Azure storage jest trwałe i o wysokiej dostępności, bezpiecznego, wysoce skalowalna i dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. 

Aby dowiedzieć się, jak utworzyć konto usługi Azure storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Konta ogólnego przeznaczenia, wersja 2

Konta magazynu ogólnego przeznaczenia v2 obsługi najnowszych funkcji usługi Azure Storage i uwzględnić je wszystkie funkcje ogólnego przeznaczenia w wersji 1 i kont usługi Blob storage. Konta ogólnego przeznaczenia w wersji 2 dostarczanie najniższy gigabajt ceny pojemności dla usługi Azure Storage oraz konkurencyjne w branży ceny transakcji. Konta magazynu ogólnego przeznaczenia v2 obsługują te usługi Azure Storage:

- Obiekty BLOB (wszystkie typy: Blokuj, Dołącz strony)
- Pliki
- Dyski
- Kolejki
- Tabele

> [!NOTE]
> Firma Microsoft zaleca używania konta magazynu ogólnego przeznaczenia w wersji 2 w przypadku większości scenariuszy. Ogólnego przeznaczenia w wersji 1 lub konta usługi Blob storage można łatwo uaktualnić do konta ogólnego przeznaczenia w wersji 2, bez przestojów i bez potrzeby kopiowania danych.
>
> Aby uzyskać więcej informacji na temat uaktualniania do konta ogólnego przeznaczenia w wersji 2, zobacz [uaktualnić do konta magazynu ogólnego przeznaczenia v2](storage-account-upgrade.md). 

Kont magazynu ogólnego przeznaczenia w wersji 2 oferuje wiele warstw dostępu do przechowywania danych na podstawie Twojej wzorców użycia. Aby uzyskać więcej informacji, zobacz [warstwy dostępu dla blokowych obiektów blob danych](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Konta ogólnego przeznaczenia, wersja 1

Konta ogólnego przeznaczenia w wersji 1 zapewniają dostęp do wszystkich usług Azure Storage, ale nie może mieć najnowszych funkcji lub najniższych cen za gigabajt. Konta magazynu ogólnego przeznaczenia w wersji 1 obsługują te usługi Azure Storage:

- Obiekty BLOB (wszystkie typy)
- Pliki
- Dyski
- Kolejki
- Tabele

Mimo, że kont ogólnego przeznaczenia v2 są zalecane w większości przypadków, kont ogólnego przeznaczenia w wersji 1 są najlepiej nadaje się do tych scenariuszy: 

* Wymagane przez aplikacje platformy Azure klasycznego modelu wdrażania. Konta ogólnego przeznaczenia v2 i konta usługi Blob storage obsługują tylko model wdrażania usługi Azure Resource Manager.

* Aplikacje są intensywnie korzystających z transakcji lub użyć dużej przepustowości replikacji geograficznej w znaczący, ale nie wymagają dużej pojemności. W tym przypadku ogólnego przeznaczenia w wersji 1, może być najbardziej ekonomiczne wybór.

* Używasz wersji [interfejsu API REST usług Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) wcześniejsza niż 2014-02-14 lub biblioteka klienta w wersji wcześniejszej niż 4.x i nie można uaktualnić aplikacji.

### <a name="blob-storage-accounts"></a>Konta usługi Blob Storage

Konta usługi Blob storage to specjalne konto magazynu do przechowywania danych obiektów bez struktury jako blokowe obiekty BLOB. Konta usługi blob storage zapewnić samą trwałość, dostępność, skalowalność i funkcje wydajności, które są dostępne z kontami magazynu ogólnego przeznaczenia w wersji 2. Konta usługi blob storage obsługuje przechowywania blokowych obiektów blob i uzupełnialnych obiektów blob, ale nie stronicowych obiektów blob.

Konta usługi blob storage oferują wiele warstw dostępu do przechowywania danych na podstawie Twojej wzorców użycia. Aby uzyskać więcej informacji, zobacz [warstwy dostępu dla blokowych obiektów blob danych](#access-tiers-for-block-blob-data).

## <a name="naming-storage-accounts"></a>Nazw kont magazynu

Podczas określania nazwy konta magazynu należy pamiętać o następujących regułach:

- Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
- Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Każde konto magazynu musi mieć inną nazwę.

## <a name="performance-tiers"></a>Warstwy wydajności

Kont magazynu ogólnego przeznaczenia można skonfigurować dla jednej z następujących warstw wydajności:

* Warstwa wydajności warstwy standardowa do przechowywania obiektów blob, plików, tabel, kolejek i dysków maszyny wirtualnej platformy Azure.
* Warstwa wydajności premium do przechowywania tylko dysków niezarządzanych maszyn wirtualnych.

## <a name="access-tiers-for-block-blob-data"></a>Warstwy dostępu dla bloku danych obiektu blob

Usługa Azure Storage udostępnia różne opcje do uzyskiwania dostępu do danych na podstawie wzorców użycia. Każda warstwa dostępu w usłudze Azure Storage jest zoptymalizowany pod kątem określonego wzorca użycia danych. Wybierając dla Twoich potrzeb warstwę odpowiednich uprawnień dostępu, można przechowywać danych blokowych obiektów blob w sposób najbardziej efektywny.

Są warstwy dostępu:

> [!NOTE]
> [Dostępu w warstwie Premium](../blobs/storage-blob-storage-tiers.md#premium-access-tier) jest dostępna w ograniczonej wersji zapoznawczej jako konto magazyn lokalnie nadmiarowy (LRS) w regionach: Europa Północna, wschodnie stany USA 2, środkowe stany USA i zachodnie stany USA. Aby dowiedzieć się, jak zarejestrować się w wersji zapoznawczej, zobacz [wprowadzenie do magazynu obiektów Blob platformy Azure — wersja Premium](https://aka.ms/premiumblob).

* **Gorąca** warstwy dostępu, które jest zoptymalizowane pod kątem częstego dostępu do obiektów na koncie magazynu. Uzyskiwanie dostępu do danych w warstwie gorąca jest najbardziej ekonomiczne, a nieco wyższe koszty magazynowania. Tworzy nowego konta magazynu w warstwie gorąca warstwa domyślnie.
* **Chłodna** warstwy dostępu, które jest zoptymalizowane pod kątem przechowywania dużych ilości danych, które rzadko uzyskuje dostęp i przechowywane przez co najmniej 30 dni. Przechowywanie danych w warstwie chłodna jest bardziej opłacalna, ale dostęp do tych danych może być nieco bardziej kosztowne niż dostęp do danych w warstwie gorąca.
* **Archiwum** warstwę, która jest dostępna tylko dla poszczególnych blokowych obiektów blob. Warstwę archiwum jest zoptymalizowany pod kątem danych, które mogą tolerować kilka godzin opóźnienia w pobieraniu i pozostaną w warstwie archiwum przez co najmniej 180 dni. Warstwę archiwum jest najbardziej ekonomiczna opcja do przechowywania danych, ale dostęp do tych danych jest droższe niż dostęp do danych w gorąca lub chłodna. 


W przypadku zmiany wzorca użycia danych można przełączać się między tymi warstwami dostępu w dowolnym momencie. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [usługi Azure Blob storage: Premium (wersja zapoznawcza), gorąca, chłodna i archiwalna magazynu](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Zmiana warstwy dostępu dla istniejącego konta magazynu lub obiektu blob może spowodować naliczenie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [konta magazynu, w sekcji rozliczeń](#storage-account-billing).



## <a name="replication"></a>Replikacja

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Aby uzyskać więcej informacji na temat replikowania magazynu, zobacz [replikacja usługi Azure Storage](storage-redundancy.md).

## <a name="encryption"></a>Szyfrowanie

Wszystkie dane na koncie magazynu są szyfrowane po stronie usługi. Aby uzyskać więcej informacji o szyfrowaniu zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Punkty końcowe konta usługi Storage

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego usługi Azure Storage stanowi punkty końcowe konta magazynu.

Na przykład, jeśli nosi nazwę konta magazynu ogólnego przeznaczenia *mystorageaccount*, znajdują się domyślne punkty końcowe dla tego konta:

* Magazynu obiektów blob: http://*mystorageaccount*. blob.core.windows.net
* Magazyn tabel: http://*mystorageaccount*. table.core.windows.net
* Kolejki magazynu: http://*mystorageaccount*. queue.core.windows.net
* Usługa Azure Files: http://*mystorageaccount*. file.core.windows.net

> [!NOTE]
> Konto magazynu obiektów Blob przedstawia tylko punkt końcowy usługi Blob.

Adres URL dla dostępu do obiektu na koncie magazynu jest tworzony przez dodanie lokalizacji obiektu na koncie magazynu do punktu końcowego. Przykładowo adres obiektu Blob może mieć następujący format: http://*mojekontomagazynu*.blob.core.windows.net/*mojkontener*/*mojblob*.

Można również skonfigurować konta magazynu, aby używać domeny niestandardowej dla obiektów blob. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrola dostępu do danych konta

Domyślnie dane na Twoim koncie są dostępne tylko dla Ciebie, tj. właściciela konta. Masz pełną kontrolę nad mogą uzyskiwać dostęp do danych i jakie uprawnienia mają.

Musi być autoryzowana każdego żądania skierowanego do swojego konta magazynu. Na poziomie usługi, żądanie musi zawierać prawidłowy *autoryzacji* nagłówek, który zawiera wszystkie informacje niezbędne do zweryfikowania żądania, przed wykonaniem jego usługi.

Możesz udzielić dostępu do danych na koncie magazynu przy użyciu dowolnej z następujących metod:

- **Azure Active Directory:** Użyj poświadczeń usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkownika, grupy lub innych tożsamości do uzyskiwania dostępu do danych obiektów blob i kolejek (wersja zapoznawcza). W przypadku pomyślnego uwierzytelnienia tożsamości usługi Azure AD zwraca token do użycia w autoryzowania żądania do usługi Azure Blob storage i Queue storage. Aby uzyskać więcej informacji, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
- **Udostępniony klucz autoryzacji:** Klucz dostępu konta magazynu należy używać do tworzenia parametrów połączenia używanych przez aplikację w środowisku uruchomieniowym dostępu do magazynu Azure. Wartości w parametrach połączenia są używane do konstruowania *autoryzacji* nagłówek, który jest przekazywany do usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure Storage, parametry połączenia](storage-configure-connection-string.md).
- **Sygnatura dostępu współdzielonego:** Jeśli nie używasz uwierzytelniania usługi Azure AD można delegować dostępu do zasobów na koncie magazynu za pomocą sygnatury dostępu współdzielonego. Sygnatury dostępu współdzielonego to token, który hermetyzuje wszystkie informacje wymagane do autoryzowania żądania do usługi Azure Storage w polu adres URL. Można określić zasobów magazynu, uprawnień i interwał, które uprawnienia są prawidłowe w ramach sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [Using shared access signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Uwierzytelnianie użytkowników lub aplikacji przy użyciu poświadczeń usługi Azure AD udostępnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z innej metody autoryzacji. Gdy możesz kontynuować klucza wspólnego autoryzacji za pomocą aplikacji, za pomocą usługi Azure AD zmierzone konieczności przechowywania klucza dostępu do konta za pomocą kodu. Nadal m ożna również udzielić szczegółową kontrolę dostępu do zasobów na koncie magazynu przy użyciu sygnatury dostępu współdzielonego (SAS), ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokeny sygnatur dostępu Współdzielonego ani martwić się o odwołaniu ze złamanymi zabezpieczeniami sygnatury dostępu Współdzielonego. 
>
> Firma Microsoft zaleca, przy użyciu uwierzytelniania usługi Azure AD dla usługi Azure Storage blob kolejki aplikacji i gdy jest to możliwe.

## <a name="copying-data-into-a-storage-account"></a>Kopiowanie danych do konta magazynu

Firma Microsoft udostępnia narzędzia i biblioteki do importowania danych z lokalnych urządzeń magazynujących lub dostawców magazynu w chmurze innych firm. Które rozwiązanie, możesz użyć zależy od ilości danych, które przenosisz. 

Podczas uaktualniania do konta ogólnego przeznaczenia v2 z ogólnego przeznaczenia w wersji 1 lub konta usługi Blob storage dane są automatycznie migrowane. Firma Microsoft zaleca tej ścieżki uaktualniania swojego konta. Jednak jeśli zdecydujesz się do przenoszenia danych z konta ogólnego przeznaczenia w wersji 1 z kontem magazynu obiektów Blob, a następnie będziesz musiał ręcznie przeprowadzić migrację danych, za pomocą narzędzi i bibliotek, które opisano poniżej. 

### <a name="azcopy"></a>Narzędzie AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia systemu Windows przeznaczone do kopiowania z wysoką wydajnością danych z i do usługi Azure Storage. Narzędzia AzCopy można użyć do skopiowania danych na koncie usługi Blob storage z istniejących kont magazynu ogólnego przeznaczenia lub do przekazania danych z lokalnych urządzeń magazynujących. Aby uzyskać więcej informacji, zobacz [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteka przenoszenia danych

Biblioteka przenoszenia danych usługi Azure Storage dla programu .NET jest oparta na podstawowym środowisku przenoszenia danych, w którym działa narzędzie AzCopy. Biblioteka została zaprojektowana na potrzeby przeprowadzania wysokowydajnych, niezawodnych i prostych operacji transferu danych w sposób podobny do narzędzia AzCopy. Umożliwia to wykorzystanie funkcji oferowanych przez narzędzie AzCopy natywnie w aplikacji bez konieczności uruchamiania i monitorowania zewnętrznych wystąpień narzędzia AzCopy. Aby uzyskać więcej informacji, zobacz [Azure Storage Data Movement Library for .Net (Biblioteka przenoszenia danych usługi Magazyn Azure dla platformy .NET)](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>Interfejs API REST lub biblioteka klienta

Można utworzyć aplikację niestandardową służącej do migracji danych do konta Magazynu obiektów blob przy użyciu jednej z bibliotek klienta platformy Azure lub interfejsu API REST usług magazynu platformy Azure. Usługa Azure Storage udostępnia rozbudowane biblioteki dla wielu języków programowania i platform, takich jak .NET, Java, C++, Node.JS, PHP, Ruby i Python. Biblioteki klienta oferują zaawansowane możliwości, takie jak logika ponowień, rejestrowanie i przekazywanie równoległe. Możliwe jest również programowanie bezpośrednio przy użyciu interfejsu API REST, który może być wywoływany przez dowolny język programowania mający możliwość wysyłania żądań HTTP lub HTTPS.

Aby uzyskać więcej informacji na temat interfejsu API REST usługi Azure Storage, zobacz [dokumentacja interfejsu API REST usługi Storage Azure](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Obiekty blob zaszyfrowane za pomocą szyfrowania po stronie klienta przechowują metadane związane z szyfrowaniem w ramach obiektu blob. W przypadku kopiowania obiektu blob zaszyfrowanego przy użyciu szyfrowania po stronie klienta upewnij się, że podczas operacji kopiowania są zachowywane metadane obiektu blob, a w szczególności metadane związane z szyfrowaniem. W przypadku kopiowania obiektu blob bez metadanych szyfrowania nie można ponownie pobrać zawartości tego obiektu. Aby uzyskać więcej informacji na temat metadanych związanych z szyfrowaniem, zobacz [Azure Storage Client-Side Encryption (Szyfrowanie po stronie klienta usługi Azure Storage)](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Usługa Azure Import/Export

Jeśli masz dużą ilość danych do zaimportowania do swojego konta magazynu, należy wziąć pod uwagę usługa Azure Import/Export. Usługa Import/Export umożliwia bezpieczne importowania dużych ilości danych do usługi Azure Blob storage i Azure Files przez wysyłanie dysków do centrum danych platformy Azure. 

Usługa Import/Export można również przenoszenia danych z usługi Azure Blob storage do stacji dysków do wysłania do lokacji lokalnej. Można zaimportować dane z jednego lub więcej dysków do usługi Azure Blob storage lub Azure Files. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Import/Export?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Rozliczanie konta usługi Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak utworzyć konto usługi Azure storage, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).
* Aby zarządzać, lub usunąć istniejące konto magazynu, zobacz [kontach magazynu Azure zarządzać](storage-account-manage.md).
