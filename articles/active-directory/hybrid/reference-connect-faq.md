---
title: Często zadawane pytania Azure Active Directory Connect | Microsoft Docs
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0442b987c502533bfbd55990f201a5c02b119804
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011294"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect — często zadawane pytania

## <a name="general-installation"></a>Instalacja ogólna

**Pyt.: Jak zabezpieczyć serwer Azure AD Connect, aby zmniejszyć obszar ataków na zabezpieczenia?**

Firma Microsoft zaleca, aby zabezpieczyć serwer Azure AD Connect, aby zmniejszyć obszar ataków zabezpieczeń dla tego krytycznego składnika środowiska IT.  Poniższe zalecenia spowodują zmniejszenie zagrożeń bezpieczeństwa organizacji.

* Wdrażanie Azure AD Connect na serwerze przyłączonym do domeny i ograniczanie dostępu administracyjnego do administratorów domeny lub innych chronionych grup zabezpieczeń

Aby dowiedzieć się więcej, zobacz: 

* [Zabezpieczanie grup administratorów](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpieczanie wbudowanego konta administratora](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Poprawa zabezpieczeń i utrzymywanie poprzez zmniejszenie powierzchni ataków](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmniejszanie obszaru ataków Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**Pyt.: Czy instalacja będzie działała, jeśli administrator globalny usługi Azure Active Directory (Azure AD) ma włączone uwierzytelnianie dwuskładnikowe (funkcji 2FA)?**  
Zgodnie z kompilacjami 2016 w lutym, ten scenariusz jest obsługiwany.

**Pyt.: Czy istnieje sposób instalacji Azure AD Connect nienadzorowanej?**  
Instalacja Azure AD Connect jest obsługiwana tylko w przypadku korzystania z Kreatora instalacji programu. Instalacja nienadzorowana nie jest obsługiwana.

**Pyt.: Mam Las, w którym nie można skontaktować się z jedną domeną. Jak mogę zainstalować Azure AD Connect?**  
Zgodnie z kompilacjami 2016 w lutym, ten scenariusz jest obsługiwany.

**Pyt.: Czy Agent kondycji Azure Active Directory Domain Services (Azure AD DS) działa w trybie Server Core?**  
Tak. Po zainstalowaniu agenta można ukończyć proces rejestracji, używając następującego polecenia cmdlet programu PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Pyt.: Czy Azure AD Connect obsługuje synchronizowanie z dwóch domen do usługi Azure AD?**  
Tak, ten scenariusz jest obsługiwany. Zapoznaj się z [wieloma domenami](how-to-connect-install-multiple-domains.md).
 
**Pyt.: Czy istnieje wiele łączników dla tej samej domeny Active Directory w Azure AD Connect?**  
Nie, wiele łączników dla tej samej domeny usługi AD nie jest obsługiwanych. 

**Pyt.: Czy można przenieść bazę danych Azure AD Connect z lokalnej bazy danych do zdalnego wystąpienia SQL Server?**    
Tak, następujące kroki zawierają ogólne wskazówki dotyczące tego, jak to zrobić. Obecnie pracujemy nad bardziej szczegółowym dokumentem.
1. Utwórz kopię zapasową bazy danych LocalDB ADSync.
Najprostszym sposobem wykonania tej czynności jest użycie SQL Server Management Studio zainstalowanych na tym samym komputerze co Azure AD Connect. Połącz się z *(LocalDB) .\ADSync*, a następnie wykonaj kopię zapasową bazy danych ADSync.

2. Przywróć bazę danych ADSync do wystąpienia zdalnego SQL Server.

3. Zainstaluj Azure AD Connect w istniejącej [zdalnej bazie danych SQL](how-to-connect-install-existing-database.md).
   W tym artykule pokazano, jak przeprowadzić migrację do programu przy użyciu lokalnej bazy danych SQL. W przypadku migrowania do korzystania ze zdalnej bazy danych SQL w kroku 5 procesu należy również wprowadzić istniejące konto usługi, na którym będzie uruchamiana usługa synchronizacji systemu Windows. To konto usługi aparatu synchronizacji zostało opisane tutaj:
   
      **Użyj istniejącego konta usługi**: Domyślnie Azure AD Connect korzysta z konta usługi wirtualnej do użycia przez usługi synchronizacji. Jeśli używasz zdalnego wystąpienia SQL Server lub używasz serwera proxy wymagającego uwierzytelniania, użyj zarządzanego konta usługi lub konta usługi w domenie i Sprawdź hasło. W takich przypadkach wprowadź konto do użycia. Upewnij się, że użytkownicy, którzy uruchamiali instalację, są administratorami systemu w programie SQL, aby można było utworzyć poświadczenia logowania dla konta usługi. Aby uzyskać więcej informacji, zobacz [Azure AD Connect kont i uprawnień](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Od ostatniej kompilacji aprowizowanie bazy danych może wykonać poza pasmem administrator usługi SQL, a jej instalację może następnie przeprowadzić administrator programu Azure AD Connect z uprawnieniami właściciela bazy danych. Aby uzyskać więcej informacji, zobacz [instalowanie Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

Aby zachować prostotę, zalecamy, aby użytkownicy instalujący Azure AD Connect mogą być administratorami systemu w programie SQL Server. Jednak dzięki najnowszym kompilacjom można teraz używać delegowanych administratorów SQL, zgodnie z opisem w artykule [instalowanie Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md).

**Pyt.: Jakie są niektóre najlepsze rozwiązania z tego pola?**  

Poniżej znajduje się dokument informacyjny, który przedstawia niektóre z najlepszych rozwiązań, które inżynierowie, pomoc techniczną i naszych konsultantów rozwinęły się w ciągu lat.  Jest to przedstawione na liście punktowanej, która może być szybko wywoływana.  Mimo że ta lista próbuje być kompleksowa, mogą istnieć dodatkowe najlepsze rozwiązania, które nie zostały jeszcze utworzone na liście.

- W przypadku korzystania z pełnego języka SQL powinna pozostać lokalna a zdalna.
    - Mniej przeskoków
    - Łatwiejsze rozwiązywanie problemów
    - Mniejsza złożoność
    - Należy wyznaczyć zasoby na SQL i zezwolić na narzuty dla Azure AD Connect i systemu operacyjnego
- Obejście serwera proxy jeśli jest to możliwe, jeśli nie można ominąć serwera proxy, należy się upewnić, że wartość limitu czasu jest większa niż 5 minut.
- Jeśli wymagany jest serwer proxy, należy dodać serwer proxy do pliku Machine. config
- Zapoznaj się z lokalnymi zadaniami SQL i konserwacją oraz wpływem Azure AD Connect na ponowne indeksowanie
- Upewnij się, że system DNS może być rozpoznawany zewnętrznie
- Upewnij się, że [specyfikacje serwera](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) są zgodnie z zaleceniem niezależnie od tego, czy są używane serwery fizyczne czy wirtualne.
- Upewnij się, że w przypadku korzystania z serwera wirtualnego wymagane zasoby są dedykowane
- Upewnij się, że konfiguracja dysku i dysku spełnia najlepsze rozwiązania dotyczące SQL Server
- Instalowanie i Konfigurowanie Azure AD Connect Health do monitorowania
- Użyj progu usuwania, który jest wbudowany w Azure AD Connect.
- Uważnie Przejrzyj aktualizacje wydania, aby przygotować się do wszystkich zmian i nowych atrybutów, które mogą zostać dodane
- Wykonaj kopię zapasową wszystkiego
    - Klucze kopii zapasowych
    - Reguły synchronizacji kopii zapasowych
    - Konfiguracja serwera kopii zapasowej
    - SQL Database kopii zapasowej
- Upewnij się, że nie ma żadnych agentów kopii zapasowych innych firm, których kopie zapasowe są wykonywane bez składnika zapisywania usługi VSS programu SQL Server (typowe w przypadku serwerów wirtualnych z migawkami innych firm)
- Ogranicz liczbę niestandardowych reguł synchronizacji, które są używane podczas dodawania złożoności
- Traktuj serwery Azure AD Connect jako serwery warstwy 0
- Leery Modyfikowanie reguł synchronizacji chmury bez znajomości wpływu i właściwych sterowników firmy
- Upewnij się, że porty i adresy URL są otwarte do obsługi Azure AD Connect i Azure AD Connect Health
- Korzystanie z odfiltrowanego atrybutu Cloud w celu rozwiązywania problemów i zapobiegania powstawaniu obiektów fantomów
- Na serwerze tymczasowym upewnij się, że używasz dokumentu konfiguracji Azure AD Connect do spójności między serwerami
- Serwery przejściowe powinny znajdować się w różnych centrach danych (lokalizacjach fizycznych
- Serwery przejściowe nie powinny być rozwiązaniem o wysokiej dostępności, ale można mieć wiele serwerów przejściowych
- Wprowadzenie "zwłok" serwery przejściowe mogą wyeliminować potencjalne przestoje w przypadku wystąpienia błędu
- Najpierw Przetestuj i Weryfikuj wszystkie uaktualnienia na serwerze tymczasowym
- Zawsze Weryfikuj eksporty przed przełączeniem do przemieszczania serverLeverage serwer tymczasowy na potrzeby pełnego importu i pełnej synchronizacji, aby zmniejszyć wpływ na działalność
- Zachowanie spójności wersji między serwerami Azure AD Connect jak największej ilości 

**Pyt.: Czy mogę zezwolić Azure AD Connect na tworzenie konta łącznika usługi Azure AD na komputerze grupy roboczej?**
Nie.  Aby umożliwić Azure AD Connect Autotworzenie konta łącznika usługi Azure AD, komputer musi być przyłączony do domeny.  

## <a name="network"></a>Sieć
**Pyt.: Mam zaporę, urządzenie sieciowe lub coś innego, które ogranicza czas, w którym połączenia mogą pozostać otwarte w sieci. Jakie wartości progu limitu czasu po stronie klienta należy stosować Azure AD Connect?**  
Wszystkie oprogramowanie sieciowe, urządzenia fizyczne lub inne inne, które ograniczają czas, w którym połączenia mogą pozostać otwarte, powinny używać progu co najmniej pięciu minut (300 sekund) do łączności między serwerem, na którym zainstalowano klienta Azure AD Connect i Azure Active Directory. To zalecenie dotyczy również wszystkich wcześniej wydanych narzędzi Synchronizacja tożsamości firmy Microsoft.

**Pyt.: Czy są obsługiwane domeny o pojedynczej etykiecie (SLDs)?**  
Zdecydowanie zalecamy użycie tej konfiguracji sieci ([Zobacz artykuł](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), ponieważ jest obsługiwana usługa Azure AD Connect Sync z jedną domeną etykiet, pod warunkiem, że konfiguracja sieci dla domeny na jednym poziomie działa poprawnie.

**Pyt.: Czy są obsługiwane lasy z przyłączonymi domenami usługi AD?**  
Nie, Azure AD Connect nie obsługuje lasów lokalnych, które zawierają rozłączone przestrzenie nazw.

**Pyt.: Czy są obsługiwane nazwy NetBIOS "kropkowane"?**  
Nie, Azure AD Connect nie obsługuje lasów lokalnych ani domen, w których nazwa NetBIOS zawiera kropkę (.).

**Pyt.: Czy obsługiwane jest czyste środowisko IPv6?**  
Nie, Azure AD Connect nie obsługuje czystego środowiska IPv6.

**Q:I posiadanie środowiska z obsługą kilku lasów i sieci między dwoma lasami korzysta z translatora adresów sieciowych (NAT). Czy są używane Azure AD Connect między tymi dwoma obsługiwanymi lasami?**</br>
Nie, używanie Azure AD Connect przez translatora adresów sieciowych nie jest obsługiwane. 

## <a name="federation"></a>Federacja
**Pyt.: Co zrobić, jeśli otrzymam wiadomość e-mail z prośbą o odnowienie certyfikatu pakietu Office 365?**  
Aby uzyskać wskazówki dotyczące odnawiania certyfikatu, zobacz [Renew Certificates](how-to-connect-fed-o365-certs.md).

**Pyt.: Mam wartość "automatycznie Aktualizuj jednostkę uzależnioną" dla jednostki uzależnionej pakietu Office 365. Czy muszę podejmować jakieś działania, gdy token certyfikatu podpisywania jest automatycznie przenoszone?**  
Skorzystaj z wskazówek przedstawionych w artykule [Odnów certyfikaty](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Środowisko
**Pyt.: Czy można zmienić nazwę serwera po zainstalowaniu Azure AD Connectu?**  
Nie. Zmiana nazwy serwera renderuje aparat synchronizacji nie może nawiązać połączenia z wystąpieniem bazy danych SQL i nie można uruchomić usługi.

**Pyt.: Czy reguły synchronizacji usług kryptograficznych (NGC) są obsługiwane przez maszynę z obsługą standardu FIPS?**  
Nie.  Nie są one obsługiwane.

**PYTANIA I ODPOWIEDZI. Jeśli wyłączono urządzenie zsynchronizowane (na przykład: HAADJ) w Azure Portal, dlaczego jest ona ponowna włączona?**<br>
Zsynchronizowane urządzenia mogą być utworzone lub być tworzone w środowisku lokalnym. Jeśli zsynchronizowane urządzenie jest włączone lokalnie, można je ponownie włączyć w Azure Portal, nawet jeśli zostało wcześniej wyłączone przez administratora. Aby wyłączyć zsynchronizowane urządzenie, użyj Active Directory lokalnej, aby wyłączyć konto komputera.

**PYTANIA I ODPOWIEDZI. Jeśli zablokujesz Logowanie użytkownika w portalu pakietu Office 365 lub usługi Azure AD dla synchronizowanych użytkowników, dlaczego jest odblokowane po ponownym zalogowaniu?**<br>
Zsynchronizowani użytkownicy mogą być autorami lub być zarządzani w środowisku lokalnym. Jeśli konto jest włączone w środowisku lokalnym, może odblokować blok logowania umieszczony przez administratora.

## <a name="identity-data"></a>Dane tożsamości
**Pyt.: Dlaczego Atrybut userPrincipalName (UPN) w usłudze Azure AD nie jest zgodny z lokalną nazwą UPN?**  
Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Nazwy użytkowników w pakietach Office 365, Azure lub Intune nie są zgodne z lokalną nazwą UPN lub alternatywnym IDENTYFIKATORem logowania](https://support.microsoft.com/kb/2523192)
* [Zmiany nie są synchronizowane przez narzędzie Azure Active Directory Sync po zmianie nazwy UPN konta użytkownika w celu użycia innej domeny federacyjnej](https://support.microsoft.com/kb/2669550)

Możesz również skonfigurować usługę Azure AD, aby umożliwić aparatowi synchronizacji aktualizowanie nazwy UPN, zgodnie z opisem w temacie [funkcje usługi synchronizacji Azure AD Connect](how-to-connect-syncservice-features.md).

**Pyt.: Czy jest on obsługiwany przez niezależną, niezależną grupę usługi Azure AD lub obiekt Contact z istniejącym obiektem Contact lub grupą usługi Azure AD?**  
Tak, to niemiękkie dopasowanie jest oparte na proxyAddress. Dopasowanie miękkie nie jest obsługiwane w przypadku grup, które nie obsługują poczty e-mail.

**Pyt.: Czy jest obsługiwane ręczne ustawianie atrybutu ImmutableId w istniejącej grupie usługi Azure AD lub w obiekcie Contact, aby można było go na stałe dopasować do lokalnej grupy usługi Azure AD lub obiektu Contact?**  
Nie, ręczne ustawianie atrybutu ImmutableId w istniejącej grupie usługi Azure AD lub obiekcie Contact na potrzeby twardej dopasowania nie jest obecnie obsługiwane.

## <a name="custom-configuration"></a>Konfiguracja niestandardowa
**Pyt.: Gdzie są polecenia cmdlet programu PowerShell dla Azure AD Connect udokumentowane?**  
Z wyjątkiem poleceń cmdlet, które są udokumentowane w tej lokacji, inne polecenia cmdlet programu PowerShell Znalezione w Azure AD Connect nie są obsługiwane przez klienta.

**Pyt.: Czy mogę użyć opcji "Eksport serwera/import serwera", która znajduje się w Synchronization Service Manager, aby przenieść konfigurację między serwerami?**  
Nie. Ta opcja nie powoduje pobrania wszystkich ustawień konfiguracji i nie powinna być używana. Zamiast tego należy użyć kreatora, aby utworzyć konfigurację podstawową na drugim serwerze i użyć edytora reguł synchronizacji do generowania skryptów programu PowerShell do przenoszenia dowolnej reguły niestandardowej między serwerami. Aby uzyskać więcej informacji, zobacz [zasięg migracji](how-to-upgrade-previous-version.md#swing-migration).

**Pyt.: Czy hasła mogą być buforowane na stronie logowania platformy Azure, a buforowanie może być blokowane, ponieważ zawiera element wejściowy hasła z atrybutem *Autouzupełnianie = "false"* ?**  
Obecnie modyfikowanie atrybutów HTML pola **hasła** , w tym znacznika autouzupełniania, nie jest obsługiwane. Obecnie pracujemy nad funkcją, która umożliwia korzystanie z niestandardowego języka JavaScript, który umożliwia dodanie dowolnego atrybutu do pola **hasła** .

**Pyt.: Na stronie logowania platformy Azure są wyświetlane nazwy użytkowników, którzy wcześniej zarejestrowali się pomyślnie. Czy to zachowanie można wyłączyć?**  
Obecnie modyfikowanie atrybutów HTML pola wprowadzania **hasła** , w tym znacznika autouzupełniania, nie jest obsługiwane. Obecnie pracujemy nad funkcją, która umożliwia korzystanie z niestandardowego języka JavaScript, który umożliwia dodanie dowolnego atrybutu do pola **hasła** .

**Pyt.: Czy istnieje sposób, aby uniemożliwić sesje współbieżne?**  
Nie.

## <a name="auto-upgrade"></a>Autouaktualnianie

**Pyt.: Jakie są zalety i konsekwencje korzystania z funkcji autouaktualniania?**  
Zaleca się, aby wszyscy klienci mogli włączyć autouaktualnienie do Azure AD Connect instalacji. Korzyść polega na tym, że zawsze otrzymujesz najnowsze poprawki, w tym aktualizacje zabezpieczeń dla luk w zabezpieczeniach, które zostały znalezione w Azure AD Connect. Proces uaktualniania jest bezproblemowego i odbywa się automatycznie zaraz po udostępnieniu nowej wersji. Wielu tysięcy klientów Azure AD Connect korzystających z aktualizacji autoupgrade z każdą nową wersją.

Proces autouaktualniania zawsze określa, czy instalacja kwalifikuje się do autouaktualnienia. Jeśli jest uprawniony, uaktualnienie jest wykonywane i testowane. Proces ten obejmuje również wyszukiwanie niestandardowych zmian w regułach i określonych czynnikach środowiskowych. Jeśli testy pokazują, że uaktualnienie nie powiedzie się, zostanie automatycznie przywrócona Poprzednia wersja.

W zależności od rozmiaru środowiska proces może potrwać kilka godzin. W trakcie uaktualniania nie jest wykonywana synchronizacja między systemem Windows Server Active Directory i usługą Azure AD.

**Pyt.: Otrzymuję wiadomość e-mail z informacją o tym, że Autoaktualizacja nie działa, i chcę zainstalować nową wersję. Dlaczego należy to zrobić?**  
W zeszłym roku wydano wersję Azure AD Connect, która w pewnych okolicznościach może wyłączyć funkcję autouaktualniania na serwerze. Rozwiązano problem w Azure AD Connect wersja 1.1.750.0. Jeśli problem został naruszony, można go ograniczyć przez uruchomienie skryptu programu PowerShell w celu jego naprawy lub ręczne uaktualnienie do najnowszej wersji Azure AD Connect. 

Aby uruchomić skrypt programu PowerShell, [Pobierz skrypt](https://aka.ms/repairaadconnect) i uruchom go na serwerze Azure AD Connect w oknie administracyjnym programu PowerShell. Aby dowiedzieć się, jak uruchomić skrypt, [Obejrzyj ten krótki film wideo](https://aka.ms/repairaadcau).

Aby ręcznie uaktualnić, należy pobrać i uruchomić najnowszą wersję pliku AADConnect. msi.
 
-  Jeśli bieżąca wersja jest starsza niż 1.1.750.0, [Pobierz ją i przeprowadź uaktualnienie do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).
- Jeśli Azure AD Connect wersja to 1.1.750.0 lub nowsza, nie są wymagane żadne dalsze działania. Używasz już wersji, która zawiera poprawkę automatycznej aktualizacji. 

**Pyt.: Po otrzymaniu wiadomości e-mail z informacją o przeprowadź uaktualnienie do najnowszej wersji, aby ponownie włączyć automatyczną aktualizację. Używam wersji 1.1.654.0. Czy muszę uaktualnić?**  
Tak, musisz uaktualnić do wersji 1.1.750.0 lub nowszej, aby ponownie włączyć automatyczną aktualizację. [Pobierz i Uaktualnij do najnowszej wersji](https://www.microsoft.com/download/details.aspx?id=47594).

**Pyt.: Po otrzymaniu wiadomości e-mail z informacją o przeprowadź uaktualnienie do najnowszej wersji, aby ponownie włączyć automatyczną aktualizację. Czy jeśli program PowerShell został użyty do włączenia autouaktualniania, czy nadal muszę zainstalować najnowszą wersję?**  
Tak, nadal trzeba uaktualnić program do wersji 1.1.750.0 lub nowszej. Włączenie usługi autoupgrade przy użyciu programu PowerShell nie pozwala wyeliminować problemu z autouaktualnianiem znalezionym w wersjach przed 1.1.750.0.

**Pyt.: Chcę przeprowadzić uaktualnienie do nowszej wersji, ale nie mam pewności, kto ją zainstalowano Azure AD Connect i nie mamy nazwy użytkownika i hasła. Potrzebujesz?**
Nie musisz znać nazwy użytkownika i hasła, które zostały początkowo użyte do uaktualnienia Azure AD Connect. Użyj dowolnego konta usługi Azure AD z rolą administratora globalnego.

**Pyt.: Jak mogę sprawdzić, której wersji Azure AD Connect używam?**  
Aby sprawdzić, która wersja Azure AD Connect jest zainstalowana na serwerze, przejdź do panelu sterowania i sprawdź zainstalowaną wersję programu Microsoft Azure AD Connect, wybierając pozycję **programy** > **programy i funkcje**, jak pokazano poniżej:

![Wersja Azure AD Connect w panelu sterowania](./media/reference-connect-faq/faq1.png)

**Pyt.: Jak mogę uaktualnić do najnowszej wersji Azure AD Connect?**  
Aby dowiedzieć się, jak przeprowadzić uaktualnienie do najnowszej wersji [, zobacz Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](how-to-upgrade-previous-version.md). 

**Pyt.: Uaktualniono już najnowszą wersję Azure AD Connect ostatniego roku. Czy należy ponownie uaktualnić?**  
Zespół Azure AD Connect wykonuje częste aktualizacje usługi. Aby skorzystać z poprawek błędów i aktualizacji zabezpieczeń, a także nowych funkcji, ważne jest, aby zapewnić aktualność serwera w najnowszej wersji. W przypadku włączenia automatycznego uaktualniania wersja oprogramowania zostanie automatycznie zaktualizowana. Aby znaleźć historię wersji Azure AD Connect, zobacz [Azure AD Connect: Historia wersji](reference-connect-version-history.md).

**Pyt.: Jak długo trwa uaktualnianie i jaki jest wpływ na użytkowników?**  
Czas wymagany do uaktualnienia zależy od rozmiaru dzierżawy. W przypadku większych organizacji najlepszym rozwiązaniem może być przeprowadzenie uaktualnienia w wieczór lub weekend. Podczas uaktualniania nie są wykonywane żadne działania synchronizacji.

**Pyt.: Uważam, że uaktualniono do Azure AD Connect, ale Portal Office nadal zawiera informacje o narzędziu DirSync. Dlaczego to jest?**  
Zespół biurowy pracuje w celu uzyskania aktualizacji portalu Office w celu odzwierciedlenia bieżącej nazwy produktu. Nie odzwierciedla narzędzia do synchronizacji, którego używasz.

**Pyt.: Stan autouaktualnienia to "zawieszone". Dlaczego została wstrzymana? Czy należy ją włączyć?**  
Usterka została wprowadzona w poprzedniej wersji, która w pewnych okolicznościach pozostawia stan autouaktualnienia ustawiony na "zawieszone". Ręczne włączenie tej funkcji jest technicznie możliwe, ale będzie wymagało wykonania kilku złożonych kroków. Najlepszym rozwiązaniem jest zainstalowanie najnowszej wersji Azure AD Connect.

**Pyt.: Moja firma ma rygorystyczne wymagania dotyczące zarządzania zmianami i chcę kontrolować czas wypychania. Czy mogę kontrolować moment uruchomienia autouaktualniania?**  
Nie. obecnie nie ma tej funkcji. Funkcja jest szacowana dla przyszłej wersji.

**Pyt.: Czy w przypadku niepowodzenia autouaktualnienia otrzymasz wiadomość e-mail? Jak wiadomo, że zakończyło się pomyślnie?**  
Nie otrzymasz powiadomienia o wyniku uaktualnienia. Funkcja jest szacowana dla przyszłej wersji.

**Pyt.: Czy jest publikowana oś czasu dla sytuacji, gdy planujesz wypchnięcie autouaktualnień?**  
Autoupgrade to pierwszy krok w procesie wydania nowszej wersji. Zawsze, gdy jest dostępna nowa wersja, uaktualnienia są wypychane automatycznie. Nowsze wersje Azure AD Connect są wstępnie ogłoszone w [planie usługi Azure AD](../fundamentals/whats-new.md).

**Pyt.: Czy uaktualnienie jest również Azure AD Connect Health uaktualniane?**  
Tak, funkcja autouaktualniania uaktualnia również Azure AD Connect Health.

**Pyt.: Czy chcesz również uaktualnić Azure AD Connect serwery w trybie przejściowym?**  
Tak, możesz uaktualnić serwer Azure AD Connect, który jest w trybie przejściowym.

**Pyt.: Jeśli autouaktualnienie nie powiedzie się, a serwer Azure AD Connect nie zostanie uruchomiony, co mam zrobić?**  
W rzadkich przypadkach usługa Azure AD Connect nie zostanie uruchomiona po przeprowadzeniu uaktualnienia. W takich przypadkach ponowne uruchomienie serwera zwykle rozwiązuje ten problem. Jeśli usługa Azure AD Connect nadal nie zostanie uruchomiona, Otwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Tworzenie żądania obsługi w celu skontaktowania się z pomocą techniczną pakietu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**Pyt.: Nie mam pewności, jakie jest ryzyko związane z uaktualnieniem do nowszej wersji Azure AD Connect. Czy można zadzwonić do mnie, aby pomóc Ci w uaktualnieniu?**  
Jeśli potrzebujesz pomocy w uaktualnieniu do nowszej wersji Azure AD Connect, Otwórz bilet pomocy technicznej na temat [tworzenia żądania usługi, aby skontaktować się z pomocą techniczną pakietu Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
**Pyt.: Jak uzyskać pomoc dotyczącą Azure AD Connect?**

[Przeszukiwanie bazy wiedzy Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Zapoznaj się z artykułami z artykułu KB, aby poznać typowe problemy związane z rozwiązywaniem problemów dotyczących obsługi Azure AD Connect.

[Fora Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Wyszukaj pytania techniczne i odpowiedzi lub zadawaj swoje pytania, przechodząc do [społeczności usługi Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Uzyskaj pomoc techniczną dotyczącą usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**Pyt.: Dlaczego widzę zdarzenia 6311 i 6401 występują po błędach kroków synchronizacji?**

Zdarzenia 6311- **Serwer napotkał nieoczekiwany błąd podczas wykonywania wywołania zwrotnego** i 6401- **kontroler agenta zarządzania napotkał nieoczekiwany błąd** — są zawsze rejestrowane po błędzie kroku synchronizacji. Aby rozwiązać te błędy, należy wyczyścić błędy kroków synchronizacji.  Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów podczas synchronizacji](tshoot-connect-sync-errors.md) i [Rozwiązywanie problemów z synchronizacją obiektów przy użyciu synchronizacji Azure AD Connect](/tshoot-connect-objectsync.md)
