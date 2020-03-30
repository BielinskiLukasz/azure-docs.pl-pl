---
title: Warstwy cenowe — Azure Database for MySQL
description: Dowiedz się więcej o różnych warstwach cenowych usługi Azure Database dla mysql, w tym generacjach obliczeniowych, typach magazynu, rozmiarze magazynu, języka wirtualnego, pamięci i okresach przechowywania kopii zapasowych.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: cf959112a2a717da1005be062dda3b83163d8d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269447"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Warstwy cenowe usługi Azure Database for MySQL

Można utworzyć usługę Azure Database dla serwera MySQL w jednej z trzech różnych warstw cenowych: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowana pod kątem pamięci. Warstwy cenowe są zróżnicowane przez ilość obliczeń w rach wirtualnych, które mogą być aprowizowane, pamięci na rzę wirtualną i technologii magazynu używanej do przechowywania danych. Wszystkie zasoby są aprowiedna na poziomie serwera MySQL. Serwer może mieć jedną lub wiele baz danych.

|    | **Podstawowa (Basic)** | **Cel ogólny** | **Zoptymalizowana pamięć** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeń | Gen 4, Gen 5 | Gen 4, Gen 5 | Rdz. |
| Rejy wirtualne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Pamięć na rów wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | Od 5 GB do 1 TB | Od 5 GB do 16 TB | Od 5 GB do 16 TB |
| Okres przechowywania kopii zapasowej bazy danych | 7 do 35 dni | 7 do 35 dni | 7 do 35 dni |

Aby wybrać warstwę cenową, użyj poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa (Basic) | Obciążenia, które wymagają lekkich obliczeń i wydajności we/wy. Przykłady obejmują serwery używane do tworzenia lub testowania lub rzadko używanych aplikacji na małą skalę. |
| Ogólnego przeznaczenia | Większość obciążeń biznesowych, które wymagają zrównoważonych obliczeń i pamięci o skalowalnej przepływności we/wy. Przykłady obejmują serwery do obsługi aplikacji internetowych i mobilnych oraz innych aplikacji dla przedsiębiorstw.|
| Optymalizacja pod kątem pamięci | Obciążenia bazy danych o wysokiej wydajności, które wymagają wydajności w pamięci dla szybszego przetwarzania transakcji i wyższej współbieżności. Przykłady obejmują serwery do przetwarzania danych w czasie rzeczywistym oraz aplikacje transakcyjne lub analityczne o wysokiej wydajności.|

Po utworzeniu serwera liczbę podstawowych, generowania sprzętu i warstwy cenowej (z wyjątkiem i z poziomu basic) można zmienić w górę lub w dół w ciągu kilku sekund. Można również niezależnie dostosować ilość miejsca w górę i okres przechowywania kopii zapasowej w górę lub w dół bez przestojów aplikacji. Nie można zmienić typu magazynu kopii zapasowej po utworzeniu serwera. Aby uzyskać więcej informacji, zobacz sekcję [Skalowanie zasobów.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Obliczanie pokoleń i corów wirtualnych

Zasoby obliczeniowe są dostarczane jako rdzenie wirtualne, które reprezentują logiczny procesor procesora podstawowego sprzętu. China East 1, China North 1, US DoD Central i US DoD East wykorzystują logiczne procesory Gen 4 oparte na procesorach Intel E5-2673 v3 (Haswell) 2,4 GHz. Wszystkie inne regiony wykorzystują procesory logiczne Gen 5 oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Magazyn

Magazyn, który aprowizować jest ilość pojemności dostępnej dla usługi Azure Database dla serwera MySQL. Magazyn jest używany do plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera MySQL. Całkowita ilość udostępnianego miejsca określa również pojemność we/wy dostępną dla serwera.

|    | **Podstawowa (Basic)** | **Cel ogólny** | **Zoptymalizowana pamięć** |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Podstawowa pamięć masowa | Magazyn ogólnego przeznaczenia | Magazyn ogólnego przeznaczenia |
| Rozmiar magazynu | Od 5 GB do 1 TB | Od 5 GB do 16 TB | Od 5 GB do 16 TB |
| Rozmiar przyrostu pamięci masowej | 1 GB | 1 GB | 1 GB |
| Liczba operacji we/wy na sekundę | Zmienna |3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalnie 20 000 IOP | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maksymalnie 20 000 IOP |

> [!NOTE]
> Pamięć masowa do 16 TB i 20 000 IOPS jest obsługiwana w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Środkowe stany USA, Zachodnie stany USA, Północno-Środkowe stany USA, Południowo-Środkowe STANY USA, Europa Północna, Europa Zachodnia, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Azja Południowo-Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa , Korea Południowa, Australia Wschodnia, Australia Południowo-Wschodnia.
>
> Wszystkie inne regiony obsługują do 4 TB pamięci masowej i do 6000 we/wy.
>

Można dodać dodatkową pojemność magazynu podczas i po utworzeniu serwera i zezwolić systemowi na automatyczne zwiększanie pamięci masowej na podstawie zużycia magazynu przez obciążenie. 

>[!NOTE]
> Magazyn można skalować tylko w górę, a nie w dół.

Warstwa Podstawowa nie zapewnia gwarancji we/wy. W warstwach cenowych ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci skala we/wy z aprowizowanym rozmiarem magazynu w stosunku 3:1.

Zużycie we/wy można monitorować w witrynie Azure portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [limit magazynowania, procent magazynowania, użyty magazyn i procent we/wy.](concepts-monitoring.md)

### <a name="reaching-the-storage-limit"></a>Osiągnięcie limitu miejsca

Serwery o pojemności mniejszej niż 100 GB są oznaczone tylko do odczytu, jeśli wolny magazyn jest mniejszy niż 5% aprowizowanego rozmiaru magazynu. Serwery z aprowizowanym magazynem o rozmiarze większym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GB.

Na przykład jeśli aprowizacja 110 GB miejsca na przestrzeni, a rzeczywiste wykorzystanie wykracza ponad 105 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli aprowizacji 5 GB miejsca, serwer jest oznaczony tylko do odczytu, gdy wolny magazyn osiągnie mniej niż 256 MB.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. Gdy zwiększysz zaaprowizowaną pojemność magazynu, serwer będzie ponownie gotowy do akceptowania transakcji zapisu.

Zaleca się włączenie automatycznego powiększania magazynu lub skonfigurowanie alertu informującego o zbliżaniu się magazynu serwera do progu, dzięki czemu można uniknąć uzyskania dostępu do stanu tylko do odczytu. Aby uzyskać więcej informacji, zobacz dokumentację [dotyczącą konfigurowania alertu](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Automatyczne powiększanie pamięci masowej

Automatyczne powiększanie pamięci masowej zapobiega wyczerpaniu się pamięci masowej serwera i stanie się tylko do odczytu. Jeśli automatyczne powiększanie magazynu jest włączone, magazyn automatycznie rośnie bez wpływu na obciążenie. W przypadku serwerów o pojemności mniejszej niż 100 GB aprowizowana rozmiar magazynu jest zwiększany o 5 GB, gdy wolny magazyn jest mniejszy niż 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 10 GB rozmiaru aprowizowanego magazynu. Obowiązują maksymalne limity przechowywania, jak określono powyżej.

Na przykład jeśli aprowizacja 1000 GB miejsca na przestrzeni, a rzeczywiste wykorzystanie wykracza ponad 990 GB, rozmiar magazynu serwera zwiększa się do 1050 GB. Alternatywnie, jeśli aprowizacji 10 GB miejsca, rozmiar magazynu jest zwiększona do 15 GB, gdy mniej niż 1 GB miejsca jest bezpłatny.

Należy pamiętać, że magazyn można skalować tylko w górę, a nie w dół.

## <a name="backup"></a>Tworzenie kopii zapasowych

Usługa automatycznie wykonuje kopie zapasowe serwera. Można wybrać okres przechowywania z zakresu od 7 do 35 dni. Serwery ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci mogą wybrać magazyn geograficznie nadmiarowy dla kopii zapasowych. Dowiedz się więcej o kopiach zapasowych w [artykule pojęcia](concepts-backup.md).

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rów wirtualnych, generowanie sprzętu, warstwę cenową (z wyjątkiem i z poziomu podstawowego), ilość miejsca do magazynowania i okres przechowywania kopii zapasowej. Nie można zmienić typu magazynu kopii zapasowej po utworzeniu serwera. Liczbę korów wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół z 7 do 35 dni. Rozmiar magazynu można tylko zwiększyć. Skalowanie zasobów można wykonać za pośrednictwem portalu lub interfejsu wiersza polecenia platformy Azure. Na przykład skalowania przy użyciu interfejsu wiersza polecenia platformy Azure zobacz [Monitorowanie i skalowanie usługi Azure Database dla serwera MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.](scripts/sample-scale-server.md)

Po zmianie liczby koperów wirtualnych, generowania sprzętu lub warstwy cenowej kopia oryginalnego serwera jest tworzona przy nowej alokacji obliczeń. Gdy nowy serwer zostanie uruchomiony, połączenia zostaną przełączone na nowy serwer. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Długość tego okresu może być różna, lecz w większości przypadków jest to mniej niż minuta.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowej są prawdziwe operacje online. Nie ma żadnych przestojów, a aplikacja nie ma wpływu. W skali We/Wy o rozmiarze aprowizowanego magazynu można zwiększyć liczbę usług We/Wy dostępne dla serwera, skalując magazyn.

## <a name="pricing"></a>Cennik

Aby uzyskać najbardziej aktualne informacje o cenach, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/mysql/)usług . Aby wyświetlić koszt żądanej konfiguracji, [witryna Azure portal](https://portal.azure.com/#create/Microsoft.MySQLServer) zawiera miesięczny koszt na karcie **Warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz użyć kalkulatora cen platformy Azure, aby uzyskać szacunkową cenę. W witrynie [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **Bazy danych** i wybierz pozycję Azure Database **for MySQL,** aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć serwer MySQL w portalu](howto-create-manage-server-portal.md).
- Dowiedz się więcej o [limitach usług](concepts-limits.md).
- Dowiedz się, jak [skalować w poziomie za pomocą replik odczytu](howto-read-replicas-portal.md).
