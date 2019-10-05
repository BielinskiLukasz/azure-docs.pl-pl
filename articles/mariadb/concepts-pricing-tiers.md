---
title: Warstwy cenowe dla Azure Database for MariaDB
description: Poznaj różne warstwy cenowe dla Azure Database for MariaDB, w tym generacji obliczeń, typy magazynów, rozmiar magazynu, rdzeni wirtualnych, pamięć i okresy przechowywania kopii zapasowych.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: ca9552ee60237158bd570ede4a13dbc349b6d1d1
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973578"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB warstw cenowych

Serwer Azure Database for MariaDB można utworzyć w jednej z trzech różnych warstw cenowych: podstawowe, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości obliczeń w rdzeni wirtualnych, które mogą być inicjowane, pamięć na rdzeń wirtualny i technologia magazynowania służąca do przechowywania danych. Wszystkie zasoby są obsługiwane na poziomie serwera MariaDB. Serwer może mieć jedną lub wiele baz danych.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeń | 5\. generacja |5\. generacja | 5\. generacja |
| Rdzeni wirtualnych | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Pamięć na rdzeń wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 4 TB | od 5 GB do 4 TB |
| Typ usługi Storage | Magazyn w warstwie Standardowa Azure | Azure Premium Storage | Azure Premium Storage |
| Okres przechowywania kopii zapasowej bazy danych | od 7 do 35 dni | od 7 do 35 dni | od 7 do 35 dni |

Aby wybrać warstwę cenową, należy użyć poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Basic | Obciążenia, które wymagają lekkich obliczeń i wydajności operacji we/wy. Przykłady obejmują serwery używane do programowania lub testowania oraz nierzadko używane aplikacje. |
| Ogólne zastosowanie | Większość obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy. Przykłady obejmują serwery do hostowania aplikacji internetowych i mobilnych oraz inne aplikacje dla przedsiębiorstw.|
| Zoptymalizowane pod kątem pamięci | Obciążenia baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności. Przykładami mogą być serwery do przetwarzania danych w czasie rzeczywistym oraz aplikacji transakcyjnych lub analitycznych o wysokiej wydajności.|

Po utworzeniu serwera można zmienić liczbę rdzeni wirtualnych i warstwę cenową (oprócz i z podstawowa) w ciągu kilku sekund. Można także niezależnie dostosować ilość miejsca do magazynowania i okres przechowywania kopii zapasowych w górę lub w dół bez przestojów aplikacji. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Aby uzyskać więcej informacji, zobacz sekcję [skalowanie zasobów](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Generacja obliczeń i rdzeni wirtualnych

Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Logiczne procesory CPU w generacji 5 są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Usługa Storage

Zapewniana ilość miejsca w magazynie to pojemność magazynu dostępna dla serwera Azure Database for MariaDB. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera MariaDB. Całkowita ilość dostępnego miejsca w magazynie określa również wydajność we/wy dostępną dla serwera.

|    | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Typ usługi Storage | Magazyn w warstwie Standardowa Azure | Azure Premium Storage | Azure Premium Storage |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 4 TB | od 5 GB do 4 TB |
| Rozmiar przyrostu pamięci masowej | 1 GB | 1 GB | 1 GB |
| Operacje wejścia/wyjścia | Zmienna |3 IOPS/GB<br/>Minimalna liczba operacji we/wy 100<br/>Maksymalna liczba operacji we/wy 6000 | 3 IOPS/GB<br/>Minimalna liczba operacji we/wy 100<br/>Maksymalna liczba operacji we/wy 6000 |

Można dodać dodatkową pojemność magazynu podczas i po utworzeniu serwera oraz pozwolić systemowi na automatyczne zwiększanie ilości miejsca w oparciu o użycie magazynu w ramach obciążenia.

>[!NOTE]
> Magazyn można skalować w górę, nie w dół.

Warstwa Podstawowa nie oferuje gwarancji operacji we/wy na sekundę. W warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci liczba operacji we/wy z zainicjowanym rozmiarem magazynu wynosi 3:1.

Możesz monitorować użycie we/wy w Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [Limit magazynu, procent magazynu, użycie magazynu i procent operacji we/wy](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Osiąganie limitu magazynu

Serwery z aprowizowanym magazynem o rozmiarze mniejszym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 512 MB lub 5% rozmiaru aprowizowanego magazynu. Serwery z aprowizowanym magazynem o rozmiarze większym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GB.

Jeśli na przykład Zainicjowano obsługę administracyjną 110 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 105 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli masz zainicjowany 5 GB miejsca w magazynie, serwer jest oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca osiągnie mniej niż 256 MB.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. Gdy zwiększysz zaaprowizowaną pojemność magazynu, serwer będzie ponownie gotowy do akceptowania transakcji zapisu.

Zalecamy włączenie opcji autowzrostu magazynu lub skonfigurowanie alertu w celu powiadomienia użytkownika o tym, że magazyn serwera zbliża się do progu, aby można było uniknąć przekroczenia stanu tylko do odczytu. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [sposobu konfigurowania alertu](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Autouzupełnianie magazynu

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi wyjście z magazynu i staje się tylko do odczytu. Jeśli funkcja automatycznego zwiększania rozmiaru magazynu jest włączona, magazyn automatycznie rośnie bez wpływu na obciążenie. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy ilość wolnego miejsca w magazynie jest mniejsza niż 10% magazynu zasobów. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza niż 10% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone powyżej.

Jeśli na przykład Zainicjowano obsługę administracyjną 1000 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 900 GB, rozmiar magazynu serwera zostanie zwiększony do 1050 GB. Alternatywnie, jeśli masz zainicjowany 10 GB miejsca w magazynie, rozmiar magazynu zostanie zwiększony do 15 GB, gdy jest mniej niż 1 GB miejsca w magazynie.

Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="backup"></a>Backup

Usługa automatycznie pobiera kopie zapasowe serwera. Minimalny okres przechowywania kopii zapasowych wynosi siedem dni. Możesz ustawić okres przechowywania na maksymalnie 35 dni. Przechowywanie można zmienić w dowolnym momencie w okresie istnienia serwera. Można wybrać między lokalnie nadmiarowe i geograficznie nadmiarowe kopie zapasowe. Dublowane nadmiarowe kopie zapasowe są również przechowywane w [obszarze geograficznym](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) regionu obszaru, w którym został utworzony serwer. Ta nadmiarowość zapewnia poziom ochrony w przypadku awarii. Możliwe jest również przywrócenie serwera do dowolnego innego regionu świadczenia usługi Azure, w którym usługa jest dostępna w geograficznie nadmiarowych kopiach zapasowych. Po utworzeniu serwera nie jest możliwe przeprowadzenie zmiany między tymi dwiema opcjami magazynu kopii zapasowych.

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rdzeni wirtualnych, warstwę cenową (poza i z podstawowa), ilość miejsca do magazynowania oraz okres przechowywania kopii zapasowych. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Liczbę rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Rozmiar magazynu można zwiększyć tylko. Skalowanie zasobów można przeprowadzić za pomocą portalu lub interfejsu wiersza polecenia platformy Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

W przypadku zmiany liczby rdzeni wirtualnych lub warstwy cenowej kopia oryginalnego serwera zostanie utworzona przy użyciu nowej alokacji obliczeniowej. Gdy nowy serwer zostanie uruchomiony, połączenia zostaną przełączone na nowy serwer. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Długość tego okresu może być różna, lecz w większości przypadków jest to mniej niż minuta.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowych to prawdziwe operacje online. Brak przestoju i nie ma to znaczenia dla Twojej aplikacji. Jako że liczba operacji we/wy na sekundę jest skalowana wraz z rozmiarem magazynu, można zwiększyć liczbę operacji we/wy dla serwera, skalowanie w górę magazynu.

## <a name="pricing"></a>Cennik

Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/mariadb/)usługi. Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) przedstawia miesięczny koszt na karcie **warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** i wybierz **Azure Database for MariaDB** , aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [ograniczeniach usługi](concepts-limits.md).
- Dowiedz się [, jak utworzyć serwer MariaDB w Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
