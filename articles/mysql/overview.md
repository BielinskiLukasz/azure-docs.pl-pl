---
title: Omówienie usługi relacyjnej bazy danych Azure Database for MySQL
description: Omówienie usługi relacyjnej bazy danych Azure Database for MySQL.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 1299f82c28181d1c24f68a0d36ba13c6b40d21ee
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013402"
---
# <a name="what-is-azure-database-for-mysql"></a>Co to jest Azure Database for MySQL?

Azure Database for MySQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na programie [MySQL Community Edition](https://www.mysql.com/products/community/) (dostępnym w ramach GPLv2 licencji), wersje 5,6, 5,7 i 8,0. Azure Database for MySQL oferuje:

- Wbudowana wysoka dostępność bez dodatkowych kosztów
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- Skalowanie zgodnie z potrzebami w ciągu kilku sekund.
- Zabezpieczenia zapewniające ochronę przesyłanych oraz magazynowanych poufnych danych
- Automatyczne tworzenie kopii zapasowych i funkcja przywracania do punktu w czasie do 35 dni
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Dzięki nim możesz skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy w celu dostarczania zawartości z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności.

![Diagram koncepcyjny Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ten artykuł stanowi wprowadzenie do Azure Database for MySQL podstawowych pojęć i funkcji związanych z wydajnością, skalowalnością i możliwościami zarządzania, z linkami do szczegółowych informacji. Zobacz te przewodniki Szybki start, aby rozpocząć pracę:

- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md)

Aby uzyskać zestaw przykładów interfejsu wiersza polecenia platformy Azure, zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund
Usługa Azure Database for MySQL oferuje kilka warstw usług: Podstawowa, Ogólnego przeznaczenia i Zoptymalizowane pod kątem pamięci. Każda z warstw oferuje inną wydajność i różne możliwości w celu obsługi różnych obciążeń bazy danych, zarówno niewielkich, jak i ogromnych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów
Jak podjąć decyzję o tym, kiedy należy regulować w górę i w dół? Możesz użyć wbudowanych funkcji monitorowania wydajności i alertów w połączeniu z ocenami wydajności opartymi na rdzeniach wirtualnych. Za pomocą tych narzędzi możesz szybko ocenić wpływ skalowania rdzeni wirtualnych w górę lub w dół na podstawie bieżących lub przewidywanych wymagań dotyczących wydajności. Aby uzyskać szczegółowe informacje, zobacz [Alerty](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Zapewnienie działania aplikacji i firmy
Umowa dotycząca poziomu usług (SLA) o czołowej w branży dostępności 99,99% dla platformy Azure, która jest obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić działanie aplikacji przez 24 godziny na dobę, 7 dni w tygodniu. Każdy serwer Azure Database for MySQL korzysta z wbudowanych zabezpieczeń, odporności na uszkodzenia i ochrony danych, które w przeciwnym razie trzeba będzie zakupić lub zaprojektować, skompilować i zarządzać. Za pomocą Azure Database for MySQL można użyć przywracania do punktu w czasie, aby odzyskać serwer do wcześniejszego stanu, o ile nie jest to 35 dni.

## <a name="secure-your-data"></a>Zabezpieczanie danych
Usługi Azure Database Services mają tradycję zabezpieczeń danych, która Azure Database for MySQL się z funkcjami, które ograniczają dostęp, chronią i obniżają dane oraz ułatwiają monitorowanie aktywności. Odwiedź [Centrum zaufania Azure](https://www.microsoft.com/en-us/trustcenter/security), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Usługa Azure Database for MySQL używa szyfrowania magazynu dla danych w trybie spoczynku i jest zgodna ze standardem FIPS 140-2. Dane, w tym kopie zapasowe, są szyfrowane na dysku (z wyjątkiem plików tymczasowych utworzonych przez aparat podczas uruchamiania zapytań). Usługa korzysta z 256-bitowego szyfru AES zawartego w szyfrowaniu magazynu platformy Azure, a klucze są zarządzane przez system. Szyfrowanie magazynu jest zawsze włączone i nie można go wyłączyć.

Domyślnie usługa Azure Database for MySQL jest skonfigurowana tak, aby wymagała [zabezpieczeń połączeń SSL](./concepts-ssl-connection-security.md) na potrzeby ruchu danych w sieci. Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL dla połączeń z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL.

## <a name="contacts"></a>Kontakty
Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for MySQL, Wyślij wiadomość e-mail do zespołu Azure Database for MySQL ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Należy pamiętać, że nie jest to alias pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Następne kroki
Teraz, po zapoznaniu się z wprowadzeniem do Azure Database for MySQL i udzieleniem odpowiedzi na pytanie "co to jest Azure Database for MySQL?", wszystko jest gotowe do:

- Odwiedzenie strony cennika zawierającej porównania kosztów i kalkulatory. [Cennik](https://azure.microsoft.com/pricing/details/mysql/)
- Rozpoczęcie pracy przez utworzenie pierwszego serwera. [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- Kompiluj swoją pierwszą aplikację przy użyciu preferowanego języka: [Środowisko Python](./connect-python.md) | [](./connect-ruby.md)[Node. js](./connect-nodejs.md) | [Java języka](./connect-java.md) | [php](./connect-php.md)[.NET (C#](./connect-csharp.md))— | [](./connect-go.md) przejdź |  | 
