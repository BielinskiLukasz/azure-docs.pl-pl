---
title: łącznik zarządzania usługami IT na platformie Azure Log Analytics | Microsoft Docs
description: Ten artykuł zawiera omówienie łącznik zarządzania usługami IT (ITSMC) i informacje na temat sposobu korzystania z tego rozwiązania w celu centralnego monitorowania i zarządzania elementami roboczymi narzędzia ITSM w usłudze Azure Log Analytics oraz szybkiego rozwiązywania problemów.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373339"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Łączenie platformy Azure z narzędziami narzędzia ITSM przy użyciu łącznik zarządzania usługami IT

![Symbol łącznik zarządzania usługami IT](media/itsmc-overview/itsmc-symbol.png)

Łącznik zarządzania usługami IT (ITSMC) umożliwia łączenie z platformą Azure i obsługiwanym produktem/usługą zarządzania usługami IT (narzędzia ITSM).

Usługi platformy Azure, takie jak Log Analytics i Azure Monitor, udostępniają narzędzia umożliwiające wykrywanie, analizowanie i rozwiązywanie problemów z zasobami platformy Azure i spoza niej. Jednak elementy robocze związane z problemem zwykle znajdują się w narzędzia ITSM produktu/usłudze. Łącznik narzędzia ITSM zapewnia dwukierunkową połączenie między narzędziami platformy Azure i narzędzia ITSM, które ułatwiają szybsze rozwiązywanie problemów.

ITSMC obsługuje połączenia z następującymi narzędziami narzędzia ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Za pomocą ITSMC można:

-  Utwórz elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure (alertów metryk, alertów dziennika aktywności i alertów Log Analytics).
-  Opcjonalnie możesz zsynchronizować zdarzenie i dane żądania zmiany z narzędzia Narzędzia ITSM w obszarze roboczym usługi Azure Log Analytics.

Przeczytaj więcej na temat [postanowień prawnych i zasad zachowania poufności informacji](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Możesz rozpocząć korzystanie z łącznik ITSM, wykonując następujące czynności:

1.  [Dodaj rozwiązanie łącznik ITSM](#adding-the-it-service-management-connector-solution)
2.  Utwórz połączenie narzędzia ITSM
3.  [Korzystanie z połączenia](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Dodawanie rozwiązania łącznik zarządzania usługami IT

Aby można było utworzyć połączenie, należy dodać rozwiązanie łącznik ITSM.

1. W Azure Portal kliknij pozycję **+ Nowy** ikonę.

   ![Nowy zasób platformy Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **Łącznik zarządzania usługami IT** w portalu Marketplace, a następnie kliknij przycisk **Utwórz**.

   ![Dodaj rozwiązanie ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. W sekcji **obszar roboczy pakietu OMS** wybierz obszar roboczy usługi Azure log Analytics, w którym chcesz zainstalować rozwiązanie.
   >[!NOTE]
   > * W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz określane jako Log Analytics obszary robocze.
   > * Łącznik ITSM można zainstalować tylko w obszarze roboczym Log Analytics w następujących regionach: Wschodnie stany USA, zachodnie stany USA, Południowo-środkowe stany USA, Europa Zachodnia, Kanada środkowa, Południowo-Wschodnia, Południowo-Wschodnia, wschodnie Zjednoczone Królestwo, Indie Środkowe, Japonia Południowo-Wschodnia.

4. W sekcji **Ustawienia obszaru roboczego pakietu OMS** wybierz pozycję zasoby, w której chcesz utworzyć zasób rozwiązania.

   ![Obszar roboczy ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz określane jako Log Analytics obszary robocze.

5. Kliknij przycisk **Utwórz**.

Gdy zasób rozwiązania zostanie wdrożony, w prawym górnym rogu okna pojawi się powiadomienie.


## <a name="creating-an-itsm--connection"></a>Tworzenie połączenia narzędzia ITSM

Po zainstalowaniu rozwiązania można utworzyć połączenie.

Aby można było utworzyć połączenie, konieczne będzie przygotowanie narzędzia Narzędzia ITSM w celu zezwolenia na połączenie z rozwiązania łącznik ITSM.  

W zależności od produktu narzędzia ITSM, z którym nawiązujesz połączenie, wykonaj następujące czynności:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Po jego narzędzi Narzędzia ITSM wykonaj następujące kroki, aby utworzyć połączenie:

1. Przejdź do pozycji **wszystkie zasoby**i Znajdź pozycję **Servicedesk (YourWorkspaceName)** .
2. W obszarze **źródła danych obszaru roboczego** w lewym okienku kliknij pozycję **połączenia narzędzia ITSM**.
   ![połączenia narzędzia ITSM](media/itsmc-overview/itsm-connections.png)

   Na tej stronie zostanie wyświetlona lista połączeń.
3. Kliknij pozycję **Dodaj połączenie**.

   ![Dodaj połączenie narzędzia ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Określ ustawienia połączenia zgodnie z opisem w [artykule Konfigurowanie połączenia usługi ITSMC z artykułem produkty/usługi narzędzia ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Domyślnie program ITSMC odświeża dane konfiguracji połączenia raz w ciągu 24 godzin. Aby natychmiast odświeżyć dane połączenia w celu dokonania edycji lub aktualizacji szablonów, kliknij przycisk **Synchronizuj** w bloku połączenie.

   ![Odświeżanie połączenia](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Użycie rozwiązania
   Korzystając z rozwiązania łącznik ITSM, można tworzyć elementy robocze na podstawie alertów platformy Azure, alertów Log Analytics i rekordów dziennika Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure

Po utworzeniu połączenia z usługą narzędzia ITSM można tworzyć elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure za pomocą **akcji narzędzia ITSM** w **grupach akcji**.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w programie Azure Portal.

Postępuj zgodnie z następującą procedurą:

1. W Azure Portal kliknij pozycję **Monitoruj**.
2. W lewym okienku kliknij pozycję **grupy akcji**. Zostanie wyświetlone okno **Dodaj grupę akcji** .

    ![Grupy akcji](media/itsmc-overview/action-groups.png)

3. Podaj **nazwę** i **ShortName** dla grupy akcji. Wybierz **grupę zasobów** i **subskrypcję** , w której chcesz utworzyć grupę akcji.

    ![Szczegóły grup akcji](media/itsmc-overview/action-groups-details.png)

4. Na liście Akcje wybierz pozycję **Narzędzia ITSM** z menu rozwijanego dla **typu akcji**. Podaj **nazwę** akcji, a następnie kliknij pozycję **Edytuj szczegóły**.
5. Wybierz **subskrypcję** , w której znajduje się obszar roboczy log Analytics. Wybierz nazwę **połączenia** (nazwę łącznik ITSM), a następnie nazwę obszaru roboczego. Na przykład "MyITSMMConnector (mój obszar roboczy)".

    ![Szczegóły akcji narzędzia ITSM](media/itsmc-overview/itsm-action-details.png)

6. Z menu rozwijanego wybierz pozycję typ **elementu pracy** .
   Wybierz opcję użycia istniejącego szablonu lub wypełnij pola wymagane przez produkt narzędzia ITSM.
7. Kliknij przycisk **OK**.

Podczas tworzenia/edytowania reguły alertu platformy Azure Użyj grupy akcji, która ma akcję narzędzia ITSM. Gdy wyzwalacze alertów, element roboczy jest tworzony/aktualizowany w narzędziu narzędzia ITSM.

> [!NOTE]
>
> Aby uzyskać informacje na temat cen akcji narzędzia ITSM, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/) dla grup akcji.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i analizuj dane zdarzenia i żądania zmiany

Na podstawie konfiguracji podczas konfigurowania połączenia Łącznik narzędzia ITSM może synchronizować do 120 dni i dane żądania zmiany. Schemat rekordów dziennika dla tych danych znajduje się w [następnej sekcji](#additional-information).

Dane zdarzenia i żądania zmiany można wizualizować przy użyciu pulpitu nawigacyjnego łącznik ITSM w rozwiązaniu.

![Ekran Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje o stanie łącznika, który może służyć jako punkt wyjścia do analizowania wszelkich problemów z połączeniami.

Możesz również wizualizować zdarzenia zsynchronizowane z komputerami, na których ma to wpływ, w ramach rozwiązania Service Map.

Service Map automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Pozwala to na wyświetlanie Twoich serwerów w miarę ich działania — jako połączone systemy, które dostarczają usługi krytyczne. Service Map przedstawia połączenia między serwerami, procesami i portami w ramach dowolnej architektury połączonej z protokołem TCP bez konieczności konfiguracji innej niż Instalacja agenta. [Dowiedz się więcej](../../azure-monitor/insights/service-map.md).

Jeśli używasz rozwiązania Service Map, możesz wyświetlić elementy pomocy technicznej utworzone w rozwiązaniach narzędzia ITSM, jak pokazano w następującym przykładzie:

![Ekran Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Więcej informacji: [Service map](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Dodatkowe informacje

### <a name="data-synced-from-itsm-product"></a>Dane zsynchronizowane z produktu narzędzia ITSM
Zdarzenia i żądania zmiany są synchronizowane z poziomu produktu narzędzia ITSM do obszaru roboczego Log Analytics w oparciu o konfigurację połączenia.

Poniższe informacje przedstawiają przykłady danych zebranych przez ITSMC:

> [!NOTE]
>
> W zależności od typu elementu pracy zaimportowanego do Log Analytics, **ServiceDesk_CL** zawiera następujące pola:

**Element roboczy:** **zdarzenia**  
ServiceDeskWorkItemType_s="Incident"

**Pola**

- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Stan
- Pilność
- Wpływ
- Priorytet
- Eskalacja
- Utworzony przez
- Rozwiązany przez
- Zamknięte przez
- Element źródłowy
- Przypisane do
- Kategoria
- Tytuł
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer


**Element roboczy:** **żądania zmiany**

ServiceDeskWorkItemType_s="ChangeRequest"

**Pola**
- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Utworzony przez
- Zamknięte przez
- Element źródłowy
- Przypisane do
- Tytuł
- Typ
- Kategoria
- Stan
- Eskalacja
- Stan konfliktu
- Pilność
- Priorytet
- Ryzyko
- Wpływ
- Przypisane do
- Data utworzenia
- Data zamknięcia
- Data ostatniej modyfikacji
- Żądana Data
- Planowana data rozpoczęcia
- Planowana data zakończenia
- Data rozpoczęcia pracy
- Data zakończenia pracy
- Opis
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla zdarzenia usługi ServiceNow

| Pole Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilność |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarte przez |
| ResolvedBy_s | Rozwiązane przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Typ kontaktu |
| AssignedTo_s | Przypisane do  |
| Category_s | Kategoria |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otworzyć |
| ClosedDate_t| Zamknięte|
| ResolvedDate_t|Klienta|
| Computer  | Element konfiguracji |

## <a name="output-data-for-a-servicenow-change-request"></a>Dane wyjściowe żądania zmiany usługi ServiceNow

| Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Żądane przez |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisane do  |
| Title_s|  Krótki opis |
| Type_s|  Typ |
| Category_s|  Kategoria |
| CRState_s|  Stan|
| Urgency_s|  Pilność |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Żądane w dniu |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  |     Planowana data rozpoczęcia |
| PlannedEndDate_t  |   Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer  | Element konfiguracji |


## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami narzędzia ITSM
1. Jeśli połączenie nie powiedzie się z interfejsu użytkownika połączonego źródła z **błędem zapisu komunikatu połączenia** , wykonaj następujące czynności:
   - W przypadku połączeń usługi ServiceNow, Cherwell i Provance,  
   - Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
   - Sprawdź, czy masz wystarczające uprawnienia do odpowiedniego produktu narzędzia ITSM, aby nawiązać połączenie.  
   - Dla połączeń Service Manager,  
   - Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, a połączenie hybrydowe zostało utworzone. Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane z lokalną maszyną Service Manager, przejdź do adresu URL aplikacji sieci Web, zgodnie z opisem w dokumentacji dotyczącej tworzenia [połączenia hybrydowego](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Jeśli dane z usługi ServiceNow nie są synchronizowane do Log Analytics, upewnij się, że wystąpienie usługi ServiceNow nie jest w stanie uśpienia. Wystąpienia dev usługi ServiceNow czasami przechodzą w stan uśpienia, gdy jest on bezczynny przez długi czas. W przeciwnym razie Zgłoś problem.
3. Jeśli alerty Log Analytics wyzwalane, ale elementy robocze nie są tworzone w produkcie narzędzia ITSM lub elementy konfiguracji nie są tworzone/połączone z elementami roboczymi lub innymi informacjami ogólnymi, należy poszukać w następujących miejscach:
   -  ITSMC: rozwiązanie pokazuje podsumowanie połączeń/elementów roboczych/komputerów itp. Kliknij kafelek ze **stanem łącznika**, który przeprowadzi Cię przez **Wyszukiwanie** przy użyciu odpowiedniego zapytania. Aby uzyskać więcej informacji, sprawdź rekordy dziennika z LogType_S jako błąd.
   - Strona **przeszukiwania dzienników** : Wyświetl błędy/powiązane informacje bezpośrednio przy użyciu `*`kwerendy ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrażaniem aplikacji sieci Web Service Manager
1.  W przypadku problemów z wdrażaniem aplikacji sieci Web upewnij się, że masz wystarczające uprawnienia w ramach subskrypcji wymienionej do tworzenia/wdrażania zasobów.
2.  Jeśli podczas uruchamiania [skryptu](itsmc-service-manager-script.md)zostanie wyświetlony komunikat o błędzie **"odwołanie do obiektu nie jest ustawione na wystąpienie obiektu"** , upewnij się, że wprowadzono prawidłowe wartości w sekcji **Konfiguracja użytkownika** .
3.  Jeśli nie można utworzyć przestrzeni nazw usługi Service Bus Relay, upewnij się, że wymagany dostawca zasobów jest zarejestrowany w subskrypcji. Jeśli nie jest zarejestrowany, ręcznie Utwórz przestrzeń nazw usługi Service Bus Relay z Azure Portal. Można go również utworzyć podczas [tworzenia połączenia hybrydowego](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) z Azure Portal.


## <a name="contact-us"></a>Skontaktuj się z nami

W przypadku dowolnych zapytań lub informacji zwrotnych dotyczących łącznik zarządzania usługami IT skontaktuj się z nami na [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Następne kroki
[Dodaj narzędzia ITSM produkty/usługi do łącznik zarządzania usługami IT](../../azure-monitor/platform/itsmc-connections.md).
