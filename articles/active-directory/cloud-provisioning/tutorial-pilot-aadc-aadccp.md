---
title: Samouczek — pilotaż Azure AD Connect aprowizacji w chmurze dla istniejącego synchronizowanego lasu usługi AD
description: Ręczny.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298823"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Pilotażowa aprowizacja w chmurze dla istniejącego zsynchronizowanego lasu usługi AD 

Ten samouczek przeprowadzi Cię przez pilotażowe Inicjowanie obsługi administracyjnej chmury dla Active Directory lasu testowego, która jest już synchronizowana przy użyciu usługi Azure Active Directory (Azure AD) Connect Sync.

![Tworzenie](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed podjęciem próby wykonania tego samouczka należy wziąć pod uwagę następujące elementy:
1. Upewnij się, że znasz już podstawy aprowizacji w chmurze. 
2. Upewnij się, że korzystasz z programu Azure AD Connect Sync w wersji 1.4.32.0 lub nowszej i skonfigurowano reguły synchronizacji zgodnie z opisem. Podczas pilotażu zostanie usunięta testowa jednostka organizacyjna lub grupa z zakresu synchronizacji Azure AD Connect. Przeniesienie obiektów poza zakresem prowadzi do usunięcia tych obiektów w usłudze Azure AD. W przypadku obiektów użytkownika obiekty w usłudze Azure AD są usuwane jako nietrwałe i można je przywrócić. W przypadku obiektów grupy obiekty w usłudze Azure AD są trwale usuwane i nie można ich przywrócić. Nowy typ łącza został wprowadzony w Azure AD Connect synchronizacji, co uniemożliwi usunięcie w przypadku scenariusza pilotażowego. 
3. Upewnij się, że obiekty w zakresie pilotażowym mają wartość MS-ds-consistencyGUID, aby zapewnić, że inicjowanie obsługi administracyjnej w chmurze jest zgodne z obiektami. 

   > [!NOTE]
   > Synchronizacja Azure AD Connect nie powoduje domyślnego wypełnienia *usługi MS-ds-consistencyGUID* dla obiektów Group.

4. Jest to zaawansowany scenariusz. Upewnij się, że wykonano kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne niezbędne do wykonania kroków tego samouczka
- Środowisko testowe z Azure AD Connect synchronizacji w wersji 1.4.32.0 lub nowszej
- Jednostka organizacyjna lub Grupa, która znajduje się w zakresie synchronizacji i może być używana przez pilotaż. Zalecamy rozpoczęcie od małego zestawu obiektów.
- Serwer z systemem Windows Server 2012 R2 lub nowszym, który będzie hostem agenta aprowizacji.  Nie może to być ten sam serwer, co serwer Azure AD Connect.
- Kotwicą źródłową dla synchronizacji programu AAD Connect powinna być wartość *objectGUID* lub *MS-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Azure AD Connect aktualizacji

Musisz mieć co najmniej [usługę Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Aby zaktualizować synchronizację Azure AD Connect, wykonaj czynności opisane w [Azure AD Connect: Uaktualnij do najnowszej wersji](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Zatrzymaj harmonogram
Synchronizacja Azure AD Connect synchronizuje zmiany występujące w katalogu lokalnym przy użyciu harmonogramu. W celu zmodyfikowania i dodania reguł niestandardowych należy wyłączyć harmonogram, aby synchronizacje nie były uruchamiane podczas pracy nad tym.  Wykonaj następujące czynności:

1.  Na serwerze z uruchomioną Azure AD Connect zsynchronizuj Otwórz program PowerShell z uprawnieniami administracyjnymi.
2.  Uruchom polecenie `Stop-ADSyncSyncCycle`.  Naciśnij klawisz ENTER.
3.  Uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Jeśli używasz własnego harmonogramu niestandardowego dla synchronizacji z usługą AAD Connect, wyłącz Harmonogram. 

## <a name="create-custom-user-inbound-rule"></a>Utwórz niestandardową regułę ruchu przychodzącego użytkownika

 1. Uruchom Edytor synchronizacji z menu aplikacji na pulpicie, jak pokazano poniżej:</br>
 ![menu edytora reguł synchronizacji](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Z listy rozwijanej wybierz pozycję **ruch przychodzący** , a następnie kliknij pozycję **Dodaj nową regułę**.
 ![](media/how-to-cloud-custom-user-rule/user1.png) regułę niestandardową</br>
 
 3. Na stronie **Opis** wprowadź następujące opcje i kliknij przycisk **dalej**:

    **Nazwa:** Nadaj regule nazwę zrozumiałą<br>
    **Opis:** Dodaj opis istotny<br>
    **Połączony system:** Wybierz łącznik usługi AD, dla którego chcesz napisać regułę synchronizacji niestandardowej<br>
    **Typ połączonego obiektu systemowego:** Użytkownicy<br>
    **Typ obiektu metaverse:** Sprzedawca<br>
    **Typ łącza:** Złącza<br>
    **Priorytet:** Podaj wartość, która jest unikatowa w systemie<br>
    **Tag:** Pozostaw to pole puste<br>
    ![](media/how-to-cloud-custom-user-rule/user2.png) regułę niestandardową</br>
 
 4. Na stronie **Filtr określania zakresu** wprowadź jednostkę organizacyjną lub grupę zabezpieczeń, dla której ma zostać wykorzystana pilotaż.  Aby filtrować według jednostki organizacyjnej, należy dodać część jednostki organizacyjnej nazwy wyróżniającej. Ta reguła zostanie zastosowana dla wszystkich użytkowników należących do tej jednostki organizacyjnej.  W związku z tym, jeśli DN zostanie zakończona z "OU = procesorami, DC = contoso, DC = com, należy dodać ten filtr.  Następnie kliknij przycisk **Next** (Dalej). 

    |Reguła|Atrybut|Operator|Wartość|
    |-----|----|----|-----|
    |Określanie zakresu jednostki organizacyjnej|NAZWA WYRÓŻNIAJĄCA|ENDSWITH|Nazwa wyróżniająca jednostki organizacyjnej.|
    |Grupa określania zakresu||ISMEMBEROF|Nazwa wyróżniająca grupy zabezpieczeń.|

    ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na stronie reguły **sprzężenia** kliknij przycisk **dalej**.
 6. Na stronie **przekształcenia** Dodaj stałą transformację: przepływaj do cloudNoFlow atrybutu. Kliknij pozycję **Add** (Dodaj).
 ![](media/how-to-cloud-custom-user-rule/user4.png) regułę niestandardową</br>

Należy wykonać te same czynności dla wszystkich typów obiektów (użytkownik, Grupa i kontakt). Powtórz kroki według skonfigurowanego łącznika usługi AD/dla każdego lasu usługi AD. 

## <a name="create-custom-user-outbound-rule"></a>Utwórz niestandardową regułę ruchu wychodzącego użytkownika

 1. Z listy rozwijanej wybierz pozycję ruch **wychodzący** , a następnie kliknij pozycję **Dodaj regułę**.
 ![](media/how-to-cloud-custom-user-rule/user5.png) regułę niestandardową</br>
 
 2. Na stronie **Opis** wprowadź następujące opcje i kliknij przycisk **dalej**:

    **Nazwa:** Nadaj regule nazwę zrozumiałą<br>
    **Opis:** Dodaj opis istotny<br>
    **Połączony system:** Wybierz łącznik usługi AAD, dla którego piszesz regułę synchronizacji niestandardowej<br>
    **Typ połączonego obiektu systemowego:** Użytkownicy<br>
    **Typ obiektu metaverse:** Sprzedawca<br>
    **Typ łącza:** JoinNoFlow<br>
    **Priorytet:** Podaj wartość, która jest unikatowa w systemie<br>
    **Tag:** Pozostaw to pole puste<br>
    
    ![Reguła niestandardowa](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na stronie **Filtr określania zakresu** wybierz pozycję **cloudNoFlow** równe **true**. Następnie kliknij przycisk **Next** (Dalej).
 ![](media/how-to-cloud-custom-user-rule/user7.png) regułę niestandardową</br>
 
 4. Na stronie reguły **sprzężenia** kliknij przycisk **dalej**.
 5. Na stronie **przekształcenia** kliknij pozycję **Dodaj**.

Należy wykonać te same czynności dla wszystkich typów obiektów (użytkownik, Grupa i kontakt).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Zainstaluj agenta aprowizacji Azure AD Connect
1. Zaloguj się na serwerze, który będzie używany z uprawnieniami administratora przedsiębiorstwa.  Jeśli używasz podstawowego samouczka [środowiska usług AD i Azure](tutorial-basic-ad-azure.md) , będzie on CP1.
2. Pobierz Azure AD Connect agenta aprowizacji w chmurze, wykonując kroki opisane [tutaj](how-to-install.md#install-the-agent).
3. Uruchamianie Azure AD Connect aprowizacji w chmurze (AADConnectProvisioningAgent. Installer)
3. Na ekranie powitalnym **Zaakceptuj** postanowienia licencyjne, a następnie kliknij przycisk **Instaluj**.</br>
![Ekran powitalny](media/how-to-install/install1.png)</br>

4. Po zakończeniu tej operacji zostanie uruchomiony Kreator konfiguracji.  Zaloguj się przy użyciu konta administratora globalnego usługi Azure AD.
5. Na ekranie **połącz Active Directory** kliknij pozycję **Dodaj katalog** , a następnie zaloguj się przy użyciu konta administratora Active Directory.  Ta operacja spowoduje dodanie katalogu lokalnego.  Kliknij przycisk **Dalej**.</br>
![Ekran powitalny](media/how-to-install/install3.png)</br>

6. Na ekranie **Konfiguracja ukończona** kliknij przycisk **Potwierdź**.  Ta operacja spowoduje zarejestrowanie i ponowne uruchomienie agenta.</br>
![Ekran powitalny](media/how-to-install/install4.png)</br>

7. Po zakończeniu tej operacji powinna zostać wyświetlona informacja o **tym, że została pomyślnie zweryfikowana.**  Możesz kliknąć przycisk **Zakończ**.</br>
![Ekran powitalny](media/how-to-install/install5.png)</br>
8. Jeśli nadal widzisz początkowy ekran powitalny, kliknij przycisk **Zamknij**.

## <a name="verify-agent-installation"></a>Weryfikuj instalację agenta
Weryfikacja agenta odbywa się w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal
Aby sprawdzić, czy Agent jest widziany przez platformę Azure, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** .</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** kliknij pozycję **Przejrzyj wszystkich agentów**.
![](media/how-to-install/install7.png) aprowizacji usługi Azure AD</br>
 
4. Na **ekranie agenci aprowizacji lokalnego** zostaną zainstalowani agenci.  Sprawdź, czy dany Agent jest tam i jest oznaczony jako **wyłączony**.  Agent jest domyślnie wyłączony ![agentów aprowizacji](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent działa, wykonaj następujące kroki:

1.  Zaloguj się na serwerze przy użyciu konta administratora
2.  Otwórz **usługi** , przechodząc do niej lub uruchamiając/Uruchom/Services. msc.
3.  W obszarze **usługi** upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam, a stan jest **uruchomiony**.
![Usługi](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurowanie aprowizacji Azure AD Connect chmury
Wykonaj następujące kroki, aby skonfigurować Inicjowanie obsługi:

 1. Zaloguj się do portalu usługi Azure AD.
 2. Kliknij **Azure Active Directory**
 3. Kliknij **Azure AD Connect**
 4. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** 
 ![](media/how-to-configure/manage1.png)</br>
 5.  Kliknij pozycję **nowa
 konfiguracji** ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Na ekranie Konfiguracja wprowadź **wiadomość e-mail z powiadomieniem**, Przenieś selektor, aby go **włączyć** , a następnie kliknij przycisk **Zapisz**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. W obszarze **Konfiguracja**wybierz pozycję **Wszyscy użytkownicy** , aby zmienić zakres reguły konfiguracji.
 ![](media/how-to-configure/scope2.png)</br>
 8. Po prawej stronie Zmień zakres tak, aby obejmował konkretną jednostkę organizacyjną, która została właśnie utworzona "OU = procesory CPU, DC = contoso, DC = com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Kliknij przycisk **gotowe** i **Zapisz**.
 10. Zakres powinien teraz być ustawiony na jedną jednostkę organizacyjną. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Weryfikowanie, czy użytkownicy są obsługiwani przez aprowizacji w chmurze
Teraz sprawdź, czy użytkownicy, którzy mieli dostęp do naszego katalogu lokalnego, zostali zsynchronizowani i teraz istnieją w dzierżawie usługi Azure AD.  Ukończenie tego procesu może potrwać kilka godzin.  Aby sprawdzić, czy użytkownicy są aprowizacji przy użyciu aprowizacji w chmurze, wykonaj następujące kroki:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta z subskrypcją platformy Azure.
2. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory**.
3. Kliknij **Azure AD Connect**
4. Kliknij pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**
5. Kliknij przycisk **dzienniki**
6. Wyszukaj nazwę użytkownika, aby upewnić się, że użytkownik jest zainicjowany przez obsługę chmury

Ponadto możesz sprawdzić, czy użytkownik i Grupa istnieją w usłudze Azure AD.

## <a name="start-the-scheduler"></a>Uruchom Harmonogram
Synchronizacja Azure AD Connect synchronizuje zmiany występujące w katalogu lokalnym przy użyciu harmonogramu. Teraz, po zmodyfikowaniu reguł, można ponownie uruchomić harmonogram.  Wykonaj następujące czynności:

1.  Na serwerze z uruchomioną Azure AD Connect synchronizacji Otwórz program PowerShell z uprawnieniami administracyjnymi
2.  Uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Uruchom polecenie `Start-ADSyncSyncCycle`.  Naciśnij klawisz ENTER.  

>[!NOTE] 
>Jeśli używasz własnego harmonogramu niestandardowego dla synchronizacji z usługą AAD Connect, Włącz harmonogram. 

## <a name="something-went-wrong"></a>Wystąpił problem
W przypadku, gdy pilotaż nie działa zgodnie z oczekiwaniami, możesz wrócić do konfiguracji synchronizacji Azure AD Connect, wykonując poniższe kroki:
1.  Wyłącz konfigurację aprowizacji w Azure Portal. 
2.  Wyłącz wszystkie niestandardowe reguły synchronizacji utworzone na potrzeby aprowizacji w chmurze za pomocą narzędzia Edytor reguł synchronizacji. Wyłączenie powinno spowodować pełną synchronizację wszystkich łączników.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurowanie synchronizacji Azure AD Connect w celu wykluczenia pilotażowej jednostki organizacyjnej
Po sprawdzeniu, czy użytkownicy z jednostki organizacyjnej pilotażowej są pomyślnie zarządzani przez aprowizacji w chmurze, można ponownie skonfigurować Azure AD Connect, aby wykluczyć utworzoną powyżej jednostkę organizacyjną pilotażową.  Agent aprowizacji w chmurze będzie obsługiwał synchronizację dla tych użytkowników do przodu.  Wykonaj następujące kroki, aby uzyskać zakres Azure AD Connect.

 1. Na serwerze, na którym działa Azure AD Connect, kliknij dwukrotnie ikonę Azure AD Connect.
 2. Kliknij pozycję **Konfiguruj**.
 3. Wybierz pozycję **Dostosuj opcje synchronizacji** , a następnie kliknij przycisk Dalej.
 4. Zaloguj się do usługi Azure AD, a następnie kliknij przycisk **dalej**.
 5. Na ekranie **łączenie katalogów** kliknij przycisk **dalej**.
 6. Na ekranie **filtrowanie domeny i jednostki organizacyjnej** wybierz opcję **Synchronizuj wybrane domeny i jednostki organizacyjne**.
 7. Rozwiń domenę i **Usuń zaznaczenie** jednostki organizacyjnej **procesorów CPU** .  Kliknij przycisk **Dalej**.
](media/tutorial-existing-forest/scope1.png) zakres ![</br>
 9. Na ekranie **funkcje opcjonalne** kliknij przycisk **dalej**.
 10. Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
 11. Po zakończeniu kliknij przycisk **Zakończ**. 

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

