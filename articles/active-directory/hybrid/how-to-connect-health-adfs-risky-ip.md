---
title: Usługa Azure AD Connect Health dla usług AD FS ryzykownych adresów IP raport | Dokumentacja firmy Microsoft
description: W tym artykule opisano raport Azure AD Connect Health usługi AD FS ryzykownych adresów IP.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b93cb7852692e4dad65fcbd72cd749db1b16fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350563"
---
# <a name="risky-ip-report-public-preview"></a>Ryzykowne raport adresów IP (publiczna wersja zapoznawcza)
Klienci usług AD FS mogą uwidaczniać w Internecie punkty końcowe uwierzytelniania za pomocą hasła w celu udostępniania użytkownikom końcowym usług uwierzytelniania umożliwiających dostęp do aplikacji SaaS, takich jak usługa Office 365. W takim przypadku możliwe są nieuprawnione próby logowania przy użyciu systemu AD FS w celu odgadnięcia hasła użytkownika końcowego i uzyskania dostępu do zasobów aplikacji. Od wersji 2012 R2 systemu Windows Server usługi AD FS udostępniają funkcję blokady konta na ekstranecie, która uniemożliwia przeprowadzenie takich ataków. Jeśli korzystasz ze starszej wersji, zdecydowanie zalecamy uaktualnienie systemu AD FS do wersji 2016 systemu Windows Server. <br />

Ponadto możliwe jest podejmowanie szeregu prób logowania z jednego adresu IP przy użyciu danych wielu użytkowników. W takich przypadkach liczba prób na użytkownika może być mniejsza od wartości progowej ochronnej blokady konta w usługach AD FS. Aktualnie program Azure AD Connect Health udostępnia raport ryzykownych adresów IP, który pozwala wykryć ten stan i powiadomić administratorów. Poniżej wymieniono najważniejsze zalety korzystania z tego raportu: 
- Wykrywanie adresów IP, w przypadku których przekroczono próg nieudanych prób logowania opartego na haśle
- Obsługa nieudanych prób logowania związanych z nieprawidłowym hasłem lub stanem blokady ekstranetu
- Możliwość natychmiastowego powiadamiania administratorów za pomocą konfigurowalnych wiadomości e-mail
- Możliwość konfiguracji ustawień progowych odpowiadających zasadom zabezpieczeń używanym w organizacji
- Możliwość pobierania raportów w celu ich analizy w trybie offline oraz integracji z innymi systemami za pomocą automatyzacji

> [!NOTE]
> Aby móc korzystać z tego raportu, należy się upewnić, że inspekcja usług AD FS jest włączona. Aby uzyskać więcej informacji, zobacz [Włączanie inspekcji dla usług AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> W celu uzyskania dostępu do wersji zapoznawczej wymagane są uprawnienia administratora globalnego lub [czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

## <a name="what-is-in-the-report"></a>Co to jest w raporcie?
Logowanie nie powiodło się adresów IP klientów aktywności są zagregowane za pośrednictwem serwerów Proxy aplikacji sieci Web. Poszczególne elementy raportu ryzykownych adresów IP zawierają zagregowane informacje o nieudanych operacjach logowania za pomocą usług AD FS, przekraczających wyznaczoną wartość progową. Raport zawiera następujące informacje: ![Portal programu Azure AD Connect Health](./media/how-to-connect-health-adfs/report4a.png)

| Element raportu | Opis |
| ------- | ----------- |
| Sygnatura czasowa | Zawiera sygnaturę czasową początku okna detekcji, opartą na czasie lokalnym witryny Azure Portal.<br /> Wszystkie zdarzenia dzienne są generowane o północy czasu UTC. <br />Sygnatura czasowa zdarzeń godzinowych jest zaokrąglona do początku godziny. Informację o godzinie rozpoczęcia pierwszego działania zawiera element „firstAuditTimestamp” w wyeksportowanym pliku. |
| Typ wyzwalacza | Zawiera typ przedziału czasu wykrywania. Wyzwalacze agregacji mogą być godzinne lub dziennie. Ten element ułatwia odróżnienie ataków siłowych o dużej częstotliwości od powolnych ataków, w przypadku których próby uzyskania dostępu są rozłożone na cały dzień. |
| Adres IP | Pojedynczy ryzykowny adres IP, powiązany z blokadą ekstranetu lub próbą logowania przy użyciu nieprawidłowego hasła. Może to być adresu IPv4 lub IPv6. |
| Liczba błędów dotyczących nieprawidłowego hasła | Liczba błędów dotyczących nieprawidłowego hasła związanych z tym adresem IP, które wystąpiły w danym przedziale czasu wykrywania. W przypadku niektórych użytkowników błędy dotyczące użycia nieprawidłowego hasła mogą występować wielokrotnie. Zwróć uwagę, że nie obejmują one prób logowania, które nie powiodły się z powodu nieaktualnego hasła. |
| Liczba błędów dotyczących blokady ekstranetu | Liczba błędów dotyczących blokady ekstranetu związanych z tym adresem IP, które wystąpiły w danym przedziale czasu wykrywania. W przypadku niektórych użytkowników błędy dotyczące blokady ekstranetu mogą występować wielokrotnie. Będą one widoczne tylko wtedy, gdy skonfigurowano blokadę ekstranetu w usługach AD FS (w wersji 2012 R2 lub nowszej). <b>Uwaga</b> Zdecydowanie zalecamy włączenie tej funkcji w przypadku zezwolenia na logowanie z ekstranetu przy użyciu hasła. |
| Unikatowi użytkownicy, dla których podjęto próbę | Liczba kont unikatowych użytkowników związanych z tym adresem IP, przy użyciu których podjęto próbę w danym przedziale czasu wykrywania. Umożliwia ona odróżnienie wzorca ataku przy użyciu jednego konta użytkownika od wzorca ataku przy użyciu wielu kont użytkowników.  |

Na przykład zgodnie z poniższym elementem raportu w dniu 28.02.2018 w godzinach 18:00–19:00 z adresu IP <i>104.2XX.2XX.9</i> zainicjowano próby uzyskania dostępu, które spowodowały wystąpienie 284 błędów blokady ekstranetu. Nie wystąpiły błędy związane z nieprawidłowym hasłem. W ramach kryteriów zdarzenia obejmowały 14 unikatowych użytkowników. Działania przekroczyły wartość progową określoną w raporcie. 

![Portal programu Azure AD Connect Health](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Raport zawiera tylko działania przekraczające wyznaczoną wartość progową. 
> - Raport może obejmować zdarzenia sprzed maksymalnie 30 dni.
> - Raport z alertem nie zawiera adresów IP programu Exchange ani prywatnych adresów IP. Jednak można je znaleźć na wyeksportowanej liście. 
>

![Portal programu Azure AD Connect Health](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Obciążenia równoważenia adresy IP na liście
Moduł równoważenia obciążenia agreguje nieudane działania związane z logowaniem i osiąga próg alertu. Jeśli adresy IP modułu równoważenia obciążenia są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych. Skonfiguruj prawidłowo moduł równoważenia obciążenia, aby przekazać adres IP klienta na potrzeby przekazywania dalej. 

## <a name="download-risky-ip-report"></a>Pobierz raport ryzykownych adresów IP 
Przy użyciu funkcji **pobierania** całą listę ryzykownych adresów IP z ostatnich 30 dni można wyeksportować z portalu programu Connect Health. Wyeksportowane dane obejmują wszystkie nieudane próby logowania przy użyciu usług AD FS w poszczególnych przedziałach czasu wykrywania. Do wyeksportowanych informacji można zastosować własne filtry. Oprócz agregacji wyróżnionych w portalu wyeksportowane dane zawierają również dodatkowe szczegóły nieudanych prób logowania związanych z konkretnym adresem IP:

|  Element raportu  |  Opis  | 
| ------- | ----------- | 
| firstAuditTimestamp | Zawiera sygnaturę czasową pierwszej nieudanej próby w przedziale czasu wykrywania.  | 
| lastAuditTimestamp | Zawiera sygnaturę czasową ostatniej nieudanej próby w przedziale czasu wykrywania.  | 
| attemptCountThresholdIsExceeded | Flaga ustawiana, jeśli bieżące działania przekraczają próg alertu.  | 
| isWhitelistedIpAddress | Flaga ustawiana, jeśli adres IP został odfiltrowany z alertów i raportów. Prywatne adresy IP (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) i adresy IP programu Exchange są odfiltrowane i oznaczone wartością True. Jeśli zakresy prywatnych adresów IP są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych.  | 

## <a name="configure-notification-settings"></a>Konfigurowanie ustawień powiadomień
Zawarte w raporcie dane kontaktowe administratorów można zaktualizować za pośrednictwem **ustawień powiadomień**. Domyślnie powiadomienia e-mail z alertem o ryzykownych adresach IP są wyłączone. Można je włączyć za pomocą przełącznika w obszarze „Otrzymuj powiadomienia e-mail dotyczące raportu adresów IP przekraczających próg nieudanych działań”. Podobnie jak w przypadku ogólnych ustawień powiadomień z alertami w programie Connect Health, można tu dostosować listę adresatów powiadomień o raporcie o ryzykownych adresach IP. Wprowadzając zmiany, można również powiadomić wszystkich administratorów globalnych. 

## <a name="configure-threshold-settings"></a>Konfigurowanie ustawień progowych
Próg alertu można zaktualizować za pomocą ustawień progowych. W systemie jest ustawiony domyślny próg. Ustawienia progowe raportu o ryzykownych adresach IP zawierają cztery kategorie:

![Portal programu Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Element progu | Opis |
| --- | --- |
| Nieprawidłowy użytkownik/hasło + blokada ekstranetu — dzień  | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z podaniem nieprawidłowego hasła oraz blokadą ekstranetu na **dzień**. |
| Nieprawidłowy użytkownik/hasło + blokada ekstranetu — godzina | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z podaniem nieprawidłowego hasła oraz blokadą ekstranetu na **godzinę**. |
| Blokada ekstranetu — dzień | Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z blokadą ekstranetu na **dzień**. |
| Blokada ekstranetu — godzina| Ustawienie progowe sterujące raportowaniem działania i wyzwoleniem powiadomienia o alercie w przypadku przekroczenia liczby zdarzeń związanych z blokadą ekstranetu na **godzinę**. |

> [!NOTE]
> - Zmiana progu raportu zostanie zastosowana po godzinie od zmiany ustawień. 
> - Nie wpłynie ona na istniejące elementy raportu. 
> - Zaleca się dostosowanie progu na podstawie liczby zdarzeń występujących w danym środowisku. 
>
>

## <a name="faq"></a>Często zadawane pytania
**Dlaczego widzę zakresy prywatnych adresów IP w raporcie?**  <br />
Prywatne adresy IP (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) i adresy IP programu Exchange są odfiltrowane i oznaczone wartością True na liście dozwolonych adresów IP. Jeśli zakresy prywatnych adresów IP są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych.

**Dlaczego widzę adresy IP modułu równoważenia w raporcie?**  <br />
Jeśli adresy IP modułu równoważenia obciążenia są widoczne, prawdopodobnie zewnętrzny moduł równoważenia obciążenia nie wysyła adresu IP klienta podczas przekazywania żądania do serwera proxy aplikacji internetowych. Skonfiguruj prawidłowo moduł równoważenia obciążenia, aby przekazać adres IP klienta na potrzeby przekazywania dalej. 

**Co należy zrobić, aby zablokować adres IP?**  <br />
Złośliwy adres IP należy dodać do zapory lub blokady w programie Exchange.   <br />

**Dlaczego nie widzę żadnych elementów w tym raporcie?** <br />
- Nieudane działania związane z logowaniem nie przekraczają ustawień progowych.
- Upewnij się, że lista serwerów AD FS nie zawiera aktywnego alertu „Dane usługi kondycji są nieaktualne”.  Dowiedz się więcej o tym, jak [rozwiązywać problemy z tym alertem](how-to-connect-health-data-freshness.md).
- Na farmach AD FS nie włączono inspekcji.

**Dlaczego widzę żadnego dostępu do raportu?**  <br />
Wymagane są uprawnienia administratora globalnego lub [czytelnika zabezpieczeń](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Skontaktuj się z administratorem globalnym, aby uzyskać dostęp.


## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalowanie agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md)
