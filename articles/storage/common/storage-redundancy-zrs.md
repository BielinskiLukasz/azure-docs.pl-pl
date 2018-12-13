---
title: Tworzenie wysoko dostępnych aplikacji usługi Azure Storage na magazyn strefowo nadmiarowy (ZRS) | Dokumentacja firmy Microsoft
description: Magazyn strefowo nadmiarowy (ZRS) oferuje prosty sposób tworzyć aplikacje o wysokiej dostępności. Magazyn ZRS zapewnia ochronę przed awariami sprzętu w centrum danych i przed pewnych regionalnej awarii.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 1b39de45d5046ce5a59dcaf0648b87aca2a5c6f5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868348"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Magazyn strefowo nadmiarowy (ZRS): wysoko dostępnych aplikacji usługi Azure Storage
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Zakres pomocy technicznej i dostępność regionalna
Magazyn ZRS obecnie obsługuje typy kont ogólnego przeznaczenia standard w wersji 2. Aby uzyskać więcej informacji na temat typów kont magazynu, zobacz [Przegląd konta usługi Azure storage](storage-account-overview.md).

Magazyn ZRS jest dostępny dla blokowych obiektów blob, -disk stronicowe obiekty BLOB, pliki, tabele i kolejki.

Magazyn ZRS jest ogólnie dostępna w następujących regionach:

- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnie stany USA 2
- Środkowe stany USA
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Azja Południowo-Wschodnia

Firma Microsoft nadal włączyć ZRS w dodatkowych regionach platformy Azure. Sprawdź [aktualizacje usługi Azure](https://azure.microsoft.com/updates/) regularnie stronę informacji o nowych regionów.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co się stanie, gdy strefa jest niedostępna?
Twoje dane są nadal dostępne, nawet wtedy, gdy strefa jest niedostępna. Firma Microsoft zaleca, nadal należy uwzględnić wskazówki dotyczące obsługi błędów przejściowych. Praktyki te obejmują Implementowanie zasad ponawiania prób przy użyciu wykładniczego wycofywania.

Gdy strefa jest niedostępna, Azure zobowiązuje sieci aktualizacji, takich jak DNS repointing. Te aktualizacje mogą wpływać na aplikację, jeśli aktualizacje zostały wykonane, są uzyskiwania dostępu do danych.

Magazyn ZRS nie może chronić dane przed regionalnej awarii, w którym trwale dotyczy wiele stref. Zamiast tego Magazyn ZRS zapewnia odporność danych staje się tymczasowo niedostępne. W celu ochrony przed regionalnej awarii firma Microsoft zaleca używanie magazyn geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji na temat GRS, zobacz [magazyn geograficznie nadmiarowy (GRS): replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konwertowanie na replikacją ZRS
Migracja do / z magazynu LRS, GRS i RA-GRS jest bardzo proste. Umożliwia zmianę typu nadmiarowości Twoje konto w witrynie Azure portal lub interfejsu API dostawcy zasobów magazynu. Azure zostanie następnie do replikowania danych odpowiednio. 

Migrowanie danych do / z magazynu ZRS wymaga innych strategii. Migracja magazynu ZRS obejmuje fizyczne przenoszenie danych z sygnaturą pojedynczy magazyn do wielu sygnatury w regionie.

Istnieją dwie podstawowe opcje migracja do / z magazynu ZRS: 

- Ręcznie skopiować lub przenieść dane do nowego konta magazynu ZRS z istniejącego konta.
- Żądanie migracji na żywo.

Firma Microsoft zaleca wykonanie ręcznej migracji. Ręcznej migracji zapewnia większą elastyczność niż migracji na żywo. Za pomocą ręcznej migracji Pełna kontrola w czasie.

Aby wykonać ręczną migrację, możesz skorzystać z opcji:
- Użyj istniejących narzędzi, takich jak narzędzia AzCopy, jeden z biblioteki klienta usługi Azure Storage lub niezawodne narzędzia innych producentów.
- Jeśli jesteś zaznajomiony z usługi Hadoop lub HDInsight, należy dołączyć źródłowym i docelowym (ZRS) konta do klastra. Następnie zrównoleglić proces kopiowania danych za pomocą narzędzia, takiego jak DistCp.
- Tworzenie własnych narzędzi przy użyciu jednej z bibliotek klienckich usługi Azure Storage.

Ręcznej migracji może spowodować przerwy w działaniu aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft oferuje także opcję migracji na żywo. Migracja na żywo jest migracja w miejscu. 

Podczas migracji na żywo można użyć konta magazynu podczas migracji danych między sygnatury magazynu źródłowego i docelowego. Podczas procesu migracji ma taki sam poziom trwałości i umowy SLA zapewniającej dostępność, jak zwykle.

Mieć na uwadze następujące ograniczenia dotyczące migracji na żywo:

- Gdy firma Microsoft podchodzi do niezwłocznego Twoje żądanie dotyczące migracji na żywo, nie ma żadnej gwarancji, aby podczas migracji na żywo zostanie ukończona. Jeśli potrzebujesz danych migracji do magazynu ZRS w określonym dniu, a następnie firma Microsoft zaleca wykonać ręczną migrację. Ogólnie rzecz biorąc, większej ilości danych ma ze swojego konta, tym dłużej trwa do migracji danych. 
- Migracja na żywo jest obsługiwana tylko dla kont magazynu, korzystających z replikacji LRS lub GRS. Jeśli Twoje konto używa RA-GRS, musisz najpierw zmienić typ replikacji konta magazynu LRS lub GRS przed kontynuowaniem. W tym kroku pośrednie usuwa określony pomocniczego tylko do odczytu punkt końcowy za magazyn RA-GRS przed migracją.
- Twoje konto musi zawierać dane.
- Można przeprowadzić migrację tylko danych w tym samym regionie. Jeśli chcesz przeprowadzić migrację danych do konta magazynu ZRS, znajdującego się w regionie, które są inne niż konto źródłowe, należy wykonać ręczną migrację.
- Tylko typy kont magazynu w warstwie standardowa obsługuje migrację na żywo. Kont usługi Premium storage muszą zostać zmigrowane ręcznie.

Możesz poprosić o migracji na żywo za pośrednictwem [portalu pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Z poziomu portalu wybierz konto magazynu, który chcesz przekonwertować ZRS.
1. Wybierz **nowe żądanie obsługi**
2. Wykonaj **podstawy** oparte na informacje o Twoim koncie. W **usługi** zaznacz **Zarządzanie kontem magazynu** i zasobów, aby przekonwertować magazyn ZRS. 
3. Wybierz opcję **Dalej**. 
4. Określ następujące wartości **Problem** sekcji: 
    - **Ważność**: pozostaw wartość domyślną jako-to.
    - **Typ problemu**: Wybierz **migracji danych**.
    - **Kategoria**: Wybierz **migracja do magazynu ZRS w obrębie regionu**.
    - **Tytuł**: Wpisz opisowy tytuł, na przykład **ZRS konta migracji**.
    - **Szczegóły**: wpisz dodatkowe informacje w **szczegóły** pole, na przykład chcę przeprowadzić migrację do magazynu ZRS z [LRS, GRS] w \_ \_ regionu. 
5. Wybierz opcję **Dalej**.
6. Sprawdź poprawność na informacje kontaktowe **informacje kontaktowe** bloku.
7. Wybierz pozycję **Utwórz**.

Działu pomocy technicznej będą z Tobą skontaktować i zapewniają pomoc, której potrzebujesz. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klasyczny magazyn ZRS: To starszy opcja w przypadku blokowych obiektów blob nadmiarowości
> [!NOTE]
> Firma Microsoft zostanie wycofana i migracji konta klasycznego magazynu ZRS na 31 marca 2021. Więcej szczegółów, zapewnia się do klasycznego magazynu ZRS klientów przed wycofywania. 
>
> Gdy stanie się ZRS [jest ogólnie dostępna](#support-coverage-and-regional-availability) w regionie, klienci będą mogli utworzyć konta klasycznego magazynu ZRS w portalu, w tym regionie. Za pomocą programu Microsoft PowerShell i wiersza polecenia platformy Azure do utworzenia konta klasycznego magazynu ZRS jest opcją, dopóki klasyczny magazyn ZRS jest przestarzała.

Klasyczny magazyn ZRS asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Replikowane dane nie mogą być dostępne, chyba że firma Microsoft zainicjuje tryb failover do regionu pomocniczego. Nie można przekonwertować konta klasycznego magazynu ZRS, do lub z magazynu LRS, GRS lub RA-GRS. Konta klasycznego magazynu ZRS nie obsługują także metryk i rejestrowania.

Klasyczny magazyn ZRS jest dostępna tylko dla **blokowe obiekty BLOB** w ogólnego przeznaczenia w wersji 1 (GPv1) konta magazynu. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](storage-account-overview.md) (Omówienie konta usługi Azure Storage).

Z konta magazynu LRS, klasycznego magazynu ZRS, GRS lub RA-GRS lub ręcznego przeprowadzania migracji danych z konta magazynu ZRS, użyj jednej z następujących narzędzi: narzędzia AzCopy, Eksplorator usługi Azure Storage, programu Azure PowerShell lub wiersza polecenia platformy Azure. Możesz także tworzyć własne rozwiązanie migracji przy użyciu jednego z biblioteki klienta usługi Azure Storage.

## <a name="see-also"></a>Zobacz także
- [Replikacja usługi Azure Storage](storage-redundancy.md)
- [Magazyn lokalnie nadmiarowy (LRS): nadmiarowość danych niedrogiej usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn geograficznie nadmiarowy (GRS): replikacji między regionami dla usługi Azure Storage](storage-redundancy-grs.md)
