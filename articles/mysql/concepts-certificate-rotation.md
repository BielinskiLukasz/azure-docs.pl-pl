---
title: Rotacja certyfikatów dla Azure Database for MySQL
description: Zapoznaj się z nadchodzącymi zmianami zmian certyfikatów głównych, które wpłyną na Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: dd009542adffed2f459534c943e3a873678ecd35
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604924"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql"></a>Informacje o zmianach w katalogu głównym urzędu certyfikacji dotyczące Azure Database for MySQL

Azure Database for MySQL zmieni certyfikat główny dla aplikacji klienckiej/sterownika z włączonym protokołem SSL, który zostanie użyty do [nawiązania połączenia z serwerem bazy danych](concepts-connectivity-architecture.md). Obecnie dostępny certyfikat główny jest ustawiany na wygasa 26 października 2020 (10/26/2020) w ramach standardowej konserwacji i najlepszych rozwiązań w zakresie zabezpieczeń. Ten artykuł zawiera bardziej szczegółowe informacje o nadchodzących zmianach, zasobach, których to dotyczy, oraz o czynnościach, które należy wykonać, aby zapewnić łączność z serwerem bazy danych przez aplikację.

## <a name="what-update-is-going-to-happen"></a>Jaką aktualizację ma mieć miejsce?

W niektórych przypadkach aplikacje używają pliku certyfikatu lokalnego wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji w celu bezpiecznego nawiązywania połączenia. Obecnie klienci mogą używać wstępnie zdefiniowanego certyfikatu do nawiązywania połączenia z serwerem Azure Database for MySQL, który znajduje się w [tym miejscu](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Jednak na [forum przeglądarki](https://cabforum.org/)urzędu certyfikacji   opublikowane niedawno raporty z wielu certyfikatów wystawionych przez dostawców urzędu certyfikacji jako niezgodne.

Zgodnie z wymaganiami dotyczącymi zgodności w branży dostawcy urzędu certyfikacji rozpoczęły Odwoływanie certyfikatów urzędu certyfikacji dla niezgodnych urzędów certyfikacji, co wymaga od serwerów używania certyfikatów wystawionych przez zgodne urzędy certyfikacji i podpisanych przez certyfikaty urzędu certyfikacji z tych zgodnych urzędów certyfikacji. Ponieważ Azure Database for MySQL obecnie używa jednego z tych niezgodnych certyfikatów, których aplikacje klienckie używają do sprawdzania poprawności połączeń SSL, musimy upewnić się, że podejmowane są odpowiednie działania (opisane poniżej) w celu zminimalizowania potencjalnego wpływu na serwery MySQL.

Nowy certyfikat zostanie użyty od 26 października 2020 (10/26/2020). Jeśli podczas nawiązywania połączenia z klientem programu MySQL używasz weryfikacji urzędu certyfikacji lub pełnej weryfikacji certyfikatu serwera (sslmode = verify-ca lub sslmode = verify-Full), musisz zaktualizować konfigurację aplikacji przed 26 października 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Jak mogę dowiedzieć się, czy na mojej bazie danych ma być to naruszone?

Wszystkie aplikacje korzystające z protokołu SSL/TLS i sprawdź, czy certyfikat główny musi zaktualizować certyfikat główny. Możesz określić, czy Twoje połączenia weryfikują certyfikat główny, przeglądając parametry połączenia.
-   Jeśli parametry połączenia obejmują `sslmode=verify-ca` lub ""
-   Jeśli parametry połączenia obejmują `sslmode=disable` , nie trzeba aktualizować certyfikatów.
-   Jeśli parametry połączenia obejmują `sslmode=allow` , `sslmode=prefer` , lub `sslmode=require` , nie trzeba aktualizować certyfikatów. 
-   Jeśli parametry połączenia nie są określone w sslmode, nie trzeba aktualizować certyfikatów.

Jeśli używasz klienta, który dzieli parametry połączenia, przejrzyj dokumentację klienta, aby dowiedzieć się, czy weryfikuje certyfikaty.
Aby zrozumieć Azure Database for MySQL sslmode, przejrzyj [opisy trybu SSL](concepts-ssl-connection-security.md#ssl-default-settings).

Aby uniknąć przerwania dostępności aplikacji z powodu nieoczekiwanego odwołania certyfikatów lub zaktualizowania certyfikatu, który został odwołany, zapoznaj się z sekcją [**"co muszę zrobić, aby zachować łączność"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Co należy zrobić, aby zachować łączność

Aby uniknąć przerwania dostępności aplikacji z powodu nieoczekiwanego odwołania certyfikatów lub zaktualizowania certyfikatu, który został odwołany, wykonaj poniższe czynności. Pomysłem jest utworzenie nowego pliku *PEM* , który łączy bieżący certyfikat i nową, oraz w trakcie weryfikacji certyfikatu SSL, gdy zostaną użyte dozwolone wartości. Zapoznaj się z poniższymi krokami:

*   Pobierz BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 głównego urzędu certyfikacji z poniższych linków:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Wygeneruj połączony magazyn certyfikatów urzędu certyfikacji z certyfikatami **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .
    *   W przypadku użytkowników programu Java (łącznik MySQL/J) wykonaj następujące polecenie:

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MySQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Następnie zastąp oryginalny plik magazynu kluczy nowym wygenerowanym:
        *   System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. NET. SSL. trustStorePassword", "Password");

    *   W przypadku użytkowników platformy .NET (MySQL Connector/NET, MySQLConnector) Upewnij się, że **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** istnieją w obszarze magazyn certyfikatów systemu Windows, Zaufane główne urzędy certyfikacji. Jeśli jakieś certyfikaty nie istnieją, zaimportuj brakujący certyfikat.

        ![Azure Database for MySQL certyfikatem platformy .NET](media/overview/netconnecter-cert.png)

    *   W przypadku użytkowników platformy .NET w systemie Linux przy użyciu SSL_CERT_DIR upewnij się, że w katalogu wskazanym przez SSL_CERT_DIR istnieją zarówno **BaltimoreCyberTrustRoot** , jak i **DigiCertGlobalRootG2** . Jeśli jakieś certyfikaty nie istnieją, Utwórz plik brakującego certyfikatu.

    *   W przypadku innych użytkowników (MySQL Client/MySQL Workbench/C/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) można scalić dwa pliki certyfikatów urzędu certyfikacji, takie jak poniższy format</b>

        </br>-----ROZPOCZNIJ CERTYFIKAT-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----KOŃCOWY CERTYFIKAT-----
 </br>-----ROZPOCZNIJ CERTYFIKAT-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----KOŃCOWY CERTYFIKAT-----

*   Zastąp oryginalny plik PEM głównego urzędu certyfikacji z plikiem połączonego głównego urzędu certyfikacji i uruchom ponownie aplikację/klienta.
*   W przyszłości po wdrożeniu nowego certyfikatu po stronie serwera można zmienić plik PEM urzędu certyfikacji na DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Jaki może być wpływ nieaktualizowania certyfikatu?
Jeśli używasz Azure Database for MySQL wystawionego certyfikatu zgodnie z opisem w tym miejscu, dostępność aplikacji może zostać przerwana, ponieważ baza danych nie będzie osiągalna. W zależności od aplikacji może pojawić się wiele komunikatów o błędach, w tym między innymi:
*   Nieprawidłowy certyfikat/odwołany certyfikat
*   Przekroczono limit czasu połączenia

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Jeśli nie korzystam z protokołu SSL/TLS, czy nadal muszę zaktualizować główny urząd certyfikacji?
Jeśli nie używasz protokołu SSL/TLS, akcje nie są wymagane. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Jeśli używam protokołu SSL/TLS, należy ponownie uruchomić serwer bazy danych w celu zaktualizowania głównego urzędu certyfikacji?
Nie, nie trzeba ponownie uruchamiać serwera bazy danych, aby rozpocząć korzystanie z nowego certyfikatu. Ten certyfikat główny jest zmianą po stronie klienta, a połączenia klienta przychodzącego muszą używać nowego certyfikatu, aby upewnić się, że mogą łączyć się z serwerem bazy danych.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. co się stanie, jeśli nie zaktualizuję certyfikatu głównego przed 26 października 2020 (10/26/2020)?
Jeśli certyfikat główny nie zostanie zaktualizowany do 26 października 2020, aplikacje, które łączą się za pośrednictwem protokołu SSL/TLS i sprawdzają, czy certyfikat główny nie będą mogły komunikować się z serwerem bazy danych MySQL, a aplikacja będzie powodować problemy z łącznością z serwerem bazy danych MySQL.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. jaki jest wpływ w przypadku używania App Service z Azure Database for MySQL?
W przypadku usług Azure App Services łączących się z Azure Database for MySQL mogą istnieć dwa możliwe scenariusze, które są zależne od tego, jak korzystasz z protokołu SSL z aplikacją.
*   Ten nowy certyfikat został dodany do App Service na poziomie platformy. Jeśli używasz certyfikatów SSL zawartych na platformie App Service w aplikacji, nie jest wymagana żadna akcja.
*   Jeśli jawnie dołączasz ścieżkę do pliku certyfikatu protokołu SSL w kodzie, należy pobrać nowy certyfikat i zaktualizować kod, aby użyć nowego certyfikatu.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. jaki jest wpływ, jeśli korzystasz z usługi Azure Kubernetes Services (AKS) z Azure Database for MySQL?
Jeśli próbujesz nawiązać połączenie z Azure Database for MySQL przy użyciu usługi Azure Kubernetes Services (AKS), będzie ona podobna do dostępu z poziomu dedykowanego środowiska macierzystego klientów. Zapoznaj się z [tymi](../aks/ingress-own-tls.md)krokami.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. jaki jest wpływ, jeśli przy użyciu Azure Data Factory połączyć się z Azure Database for MySQL?
W przypadku łącznika korzystającego z Azure Integration Runtime łącznik używa certyfikatów w magazynie certyfikatów systemu Windows w środowisku hostowanym na platformie Azure. Te certyfikaty są już zgodne z nowo zastosowanymi certyfikatami, w związku z czym nie jest wymagana żadna akcja.

W przypadku łącznika korzystającego z samoobsługowego Integration Runtime, w którym jawnie dołączysz ścieżkę do pliku certyfikatu protokołu SSL w parametrach połączenia, musisz pobrać [nowy certyfikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i zaktualizować parametry połączenia, aby go użyć.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Czy muszę zaplanować przestoje związane z konserwacją serwera bazy danych dla tej zmiany?
Nie. Ze względu na to, że zmiana ta jest dostępna tylko po stronie klienta, aby nawiązać połączenie z serwerem bazy danych, dla tej zmiany nie jest wymagane przestoje związane z konserwacją.

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>8. co zrobić, jeśli nie mogę uzyskać zaplanowanego przestoju dla tej zmiany przed 26 października 2020 (10/26/2020)?
Ponieważ klienci używani do łączenia się z serwerem muszą zaktualizować informacje o certyfikacie zgodnie z opisem w sekcji poprawka [tutaj](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), w tym przypadku nie ma potrzeby przestoju serwera.

### <a name="9-if-i-create-a-new-server-after-october-26-2020-will-i-be-impacted"></a>9. Jeśli Tworzę nowy serwer po 26 października 2020, wpłynie to na to, co będzie miało wpływ?
W przypadku serwerów utworzonych po 26 października 2020 (10/26/2020) można użyć nowo wystawionego certyfikatu dla aplikacji w celu nawiązania połączenia przy użyciu protokołu SSL.

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. jak często firma Microsoft aktualizuje swoje certyfikaty lub jakie są zasady wygasania?
Te certyfikaty używane przez Azure Database for MySQL są udostępniane przez zaufane urzędy certyfikacji. W związku z tym obsługa tych certyfikatów w Azure Database for MySQL jest związana z obsługą tych certyfikatów przez urząd certyfikacji. Jednak podobnie jak w tym przypadku, w tych wstępnie zdefiniowanych certyfikatach mogą znajdować się nieprzewidziane usterki, które muszą zostać ustalone najwcześniej.

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-the-read-replicas"></a>11. Jeśli używam replik odczytu, czy muszę wykonać tę aktualizację tylko na serwerze głównym lub w replikach odczytu?
Ponieważ ta aktualizacja jest zmianą po stronie klienta, jeśli klient używany do odczytywania danych z serwera repliki, należy również zastosować zmiany dla tych klientów.

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Czy masz zapytanie po stronie serwera, aby sprawdzić, czy protokół SSL jest używany?
Aby sprawdzić, czy jest używane połączenie SSL do łączenia się z serwerem, odwołaj się do [weryfikacji protokołu SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="13-what-if-i-have-further-questions"></a>13. co zrobić, jeśli mam więcej pytań?
Jeśli masz pytania, uzyskaj odpowiedzi od ekspertów społeczności w [firmie Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej,  [skontaktuj się z nami](mailto:AzureDatabaseforMySQL@service.microsoft.com)