---
title: Azure AD Connect Rozwiązywanie problemów z obsługą chmury
description: W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić w przypadku agenta aprowizacji w chmurze.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: fa7292d423d8b716ffd75a1a20431fb5a79bbf96
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237344"
---
# <a name="cloud-provisioning-troubleshooting"></a>Rozwiązywanie problemów z obsługą chmury

Obsługa administracyjna w chmurze styka się z wieloma różnymi elementami i ma wiele różnych zależności. Ten rozległy zakres może prowadzić do różnych problemów. Ten artykuł pomaga w rozwiązywaniu tych problemów. Przedstawiono w nim typowe obszary, w których można skoncentrować się na tym, jak zbierać dodatkowe informacje i różne techniki, których można użyć do śledzenia problemów.


## <a name="common-troubleshooting-areas"></a>Typowe obszary rozwiązywania problemów

|Nazwa|Opis|
|-----|-----|
|[Problemy z agentem](#agent-problems)|Sprawdź, czy Agent został zainstalowany prawidłowo i czy komunikuje się z Azure Active Directory (Azure AD).|
|[Problemy z synchronizacją obiektów](#object-synchronization-problems)|Dzienniki aprowizacji umożliwiają rozwiązywanie problemów z synchronizacją obiektów.|
|[Inicjowanie obsługi administracyjnej problemów z kwarantanną](#provisioning-quarantined-problems)|Informacje o aprowizacji problemów z kwarantanną i sposobach ich naprawy.|


## <a name="agent-problems"></a>Problemy z agentem
Poniżej wymieniono niektóre z nich, które mają być zweryfikowane przez agenta:

-  Czy jest zainstalowana?
-  Czy agent działa lokalnie?
-  Czy Agent jest w portalu?
-  Czy Agent jest oznaczony jako w dobrej kondycji?

Te elementy można zweryfikować w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal

Aby sprawdzić, czy Agent jest widziany przez platformę Azure i jest w dobrej kondycji, wykonaj następujące kroki.

1. Zaloguj się w witrynie Azure Portal.
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**. W centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.
1. Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** wybierz pozycję **Przejrzyj wszystkich agentów**.

   ![Przejrzyj wszystkich agentów](media/how-to-install/install-7.png)</br>
 
1. Na ekranie **na lokalnym agencie aprowizacji** można zobaczyć zainstalowanych agentów. Sprawdź, czy dany Agent jest tam i jest oznaczony jako w *dobrej kondycji*.

   ![Ekran lokalnych agentów aprowizacji](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu

Sprawdź, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować. 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure przez port 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym zainstalowano agenta.

![Weryfikacja osiągalności portów](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym

Aby sprawdzić, czy agent działa, wykonaj następujące kroki.

1. Na serwerze z zainstalowanym agentem Otwórz pozycję **usługi** , przechodząc do niej lub klikając **polecenie Uruchom program**  >  **Run**  >  **Services. msc**.
1. W obszarze **usługi** upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam i ich stan jest *uruchomiony*.

   ![Ekran usług](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Typowe problemy z instalacją agenta

W poniższych sekcjach opisano niektóre typowe problemy z instalacją agenta i typowe rozwiązania.

#### <a name="agent-failed-to-start"></a>Uruchomienie agenta nie powiodło się

Może zostać wyświetlony komunikat o błędzie informujący o tym, że:

**Nie można uruchomić usługi "Microsoft Azure AD połączyć się z agentem aprowizacji. Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych.** 

Ten problem jest zwykle spowodowany przez zasady grupy, które uniemożliwiły zastosowanie uprawnień do lokalnego konta logowania usługi NT, utworzonego przez Instalatora (NT SERVICE\AADConnectProvisioningAgent). Te uprawnienia są wymagane do uruchomienia usługi.

Aby rozwiązać ten problem, wykonaj następujące kroki.

1. Zaloguj się na serwerze przy użyciu konta administratora.
1. Otwórz **usługi** , przechodząc do niej lub naciskając **pozycję Uruchom**  >  **Run**  >  **Services. msc**.
1. W obszarze **usługi** kliknij dwukrotnie pozycję **Microsoft Azure AD Połącz agenta aprowizacji**.
1. Na karcie **Logowanie** Zmień **to konto** na administratora domeny. Następnie uruchom ponownie usługę. 

   ![Karta logowanie](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Limit czasu agenta lub nieprawidłowy certyfikat

Podczas próby zarejestrowania agenta może zostać wyświetlony następujący komunikat o błędzie.

![Komunikat o błędzie limitu czasu](media/how-to-troubleshoot/troubleshoot4.png)

Ten problem zwykle występuje, ponieważ agent nie może nawiązać połączenia z usługą zarządzania tożsamościami hybrydowymi i wymaga skonfigurowania serwera proxy HTTP. Aby rozwiązać ten problem, skonfiguruj serwer proxy ruchu wychodzącego. 

Agent aprowizacji obsługuje korzystanie z wychodzącego serwera proxy. Można ją skonfigurować, edytując plik konfiguracji agenta *C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config*. Dodaj do niego następujące wiersze, w kierunku końca pliku tuż przed tagiem zamykającym `</configuration>` .
Zastąp zmienne `[proxy-server]` i `[proxy-port]` wartościami nazw i portów serwera proxy.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Rejestracja agenta kończy się niepowodzeniem z powodu błędu zabezpieczeń

Podczas instalowania agenta aprowizacji w chmurze może zostać wyświetlony komunikat o błędzie.

Ten problem jest zwykle spowodowany tym, że Agent nie może wykonać skryptów rejestracji programu PowerShell z powodu lokalnych zasad wykonywania programu PowerShell.

Aby rozwiązać ten problem, Zmień zasady wykonywania programu PowerShell na serwerze. Zasady komputera i użytkownika muszą mieć ustawioną wartość *undefined* lub *RemoteSigned*. Jeśli są ustawione jako *nieograniczone*, zobaczysz ten błąd. Aby uzyskać więcej informacji, zobacz [zasady wykonywania programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Pliki dziennika

Domyślnie agent generuje minimalną liczbę komunikatów o błędach i ilość informacji śledzenia stosu. Te dzienniki śledzenia można znaleźć w folderze **C:\ProgramData\Microsoft\Azure usługi AD Connect Provisioning Agent\Trace**.

Aby zebrać dodatkowe szczegóły dotyczące rozwiązywania problemów związanych z agentem, wykonaj następujące czynności.

1.  Zainstaluj moduł AADCloudSyncTools PowerShell zgodnie z opisem w [tym miejscu](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. Użyj `Export-AADCloudSyncToolsLogs` polecenia cmdlet programu PowerShell, aby przechwycić informacje.  Aby dostosować zbieranie danych, można użyć następujących przełączników.
      - SkipVerboseTrace tylko na eksportowanie bieżących dzienników bez przechwytywania pełnych dzienników (wartość domyślna = false)
      - TracingDurationMins, aby określić inny czas trwania przechwytywania (wartość domyślna = 3 minuty)
      - OutputPath aby określić inną ścieżkę wyjściową (domyślnie = dokumenty użytkownika)


## <a name="object-synchronization-problems"></a>Problemy z synchronizacją obiektów

Poniższa sekcja zawiera informacje dotyczące rozwiązywania problemów z synchronizacją obiektów.

### <a name="provisioning-logs"></a>Dzienniki aprowizowania

W Azure Portal dzienniki aprowizacji mogą pomóc w śledzeniu i rozwiązywaniu problemów z synchronizacją obiektów. Aby wyświetlić dzienniki, wybierz pozycję **dzienniki**.

![Przycisk dzienniki](media/how-to-troubleshoot/log1.png)

Dzienniki aprowizacji zapewniają mnóstwo informacji na temat stanu obiektów synchronizowanych między środowiskiem lokalnym Active Directory i platformą Azure.

![Ekran dzienniki aprowizacji](media/how-to-troubleshoot/log2.png)

Możesz użyć pól rozwijanych w górnej części strony, aby filtrować widok do zera w przypadku określonych problemów, takich jak daty. Kliknij dwukrotnie pojedyncze zdarzenie, aby wyświetlić dodatkowe informacje.

![Informacje o polu listy rozwijanej dzienniki aprowizacji](media/how-to-troubleshoot/log3.png)

Te informacje zawierają szczegółowe instrukcje i miejsce, w którym występuje problem z synchronizacją. W ten sposób można zlokalizować dokładne miejsce problemu.


## <a name="provisioning-quarantined-problems"></a>Inicjowanie obsługi administracyjnej problemów z kwarantanną

Inicjowanie obsługi administracyjnej służy do monitorowania kondycji konfiguracji i umieszczania w stanie kwarantanny obiektów w złej kondycji. Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie aprowizacji zostanie oznaczone jako objęte kwarantanną.

![Stan kwarantanny](media/how-to-troubleshoot/quarantine1.png)

Po wybraniu stanu można wyświetlić dodatkowe informacje o kwarantannie. Możesz również uzyskać kod błędu i komunikat.

![Informacje o stanie kwarantanny](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Rozwiązywanie kwarantanny

- Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie Konfiguracja agenta wybierz pozycję **Uruchom ponownie Inicjowanie obsługi administracyjnej**.

  ![Ponowne uruchamianie aprowizacji](media/how-to-troubleshoot/quarantine3.png)

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wyczyścić:
  - Usługa Escrow umożliwia ponowne uruchomienie licznika Escrow, który naliczy się na stan kwarantanny.
  - Kwarantanna, aby usunąć aplikację z kwarantanny.
  - Znaki wodne. 
  
  Użyj następującego żądania:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Naprawianie konta usługi synchronizacji w chmurze
Jeśli musisz naprawić konto usługi synchronizacji w chmurze, możesz użyć `Repair-AADCloudSyncToolsAccount` .  


   1.  Wykonaj kroki instalacji opisane [tutaj](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) , aby rozpocząć pracę, a następnie kontynuuj pozostałe kroki.
   2.  W sesji programu Windows PowerShell z uprawnieniami administracyjnymi wpisz lub skopiuj i wklej następujące elementy: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Wprowadź swoje poświadczenia administratora globalnego usługi Azure AD
   4. Wpisz lub skopiuj i wklej następujące elementy: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Po zakończeniu tego procesu należy się zastanowić, że konto zostało naprawione pomyślnie.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
