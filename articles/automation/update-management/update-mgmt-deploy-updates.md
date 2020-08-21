---
title: Jak utworzyć wdrożenia aktualizacji dla Azure Automation Update Management
description: W tym artykule opisano sposób planowania wdrożeń aktualizacji i przeglądania ich stanu.
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717697"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Jak wdrażać aktualizacje i przeglądać wyniki

W tym artykule opisano sposób planowania wdrożenia aktualizacji i przeglądania procesu po zakończeniu wdrażania.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Planowanie wdrożenia aktualizacji powoduje utworzenie zasobu [harmonogramu](../shared-resources/schedules.md) połączonego z elementem Runbook **MicrosoftOMSComputers poprawek** , który obsługuje wdrożenie aktualizacji na komputerach docelowych. Aby zainstalować aktualizacje, należy zaplanować wdrożenie zgodne z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Jeśli usuniesz zasób harmonogramu z Azure Portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, to usunięcie spowoduje przerwanie zaplanowanego wdrożenia aktualizacji i wyświetli błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z poziomu portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji:

1. Na koncie usługi Automation przejdź do pozycji **Update Management** w obszarze **Update Management**, a następnie wybierz pozycję **Planowanie wdrożenia aktualizacji**.

2. W obszarze **nowe wdrożenie aktualizacji**w polu **Nazwa** wprowadź unikatową nazwę wdrożenia.

3. Wybierz system operacyjny, który ma być przeznaczony do wdrożenia aktualizacji.

4. W regionie **grupy do zaktualizowania (wersja zapoznawcza)** Zdefiniuj zapytanie łączące subskrypcję, grupy zasobów, lokalizacje i Tagi, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. Aby dowiedzieć się więcej, zobacz [Używanie grup dynamicznych z Update Management](update-mgmt-groups.md).

5. W obszarze **maszyny do zaktualizowania** Wybierz zapisane wyszukiwanie, zaimportowaną grupę lub wybierz **maszyny** z menu rozwijanego i wybierz opcję poszczególne maszyny. Po wybraniu tej opcji można zobaczyć gotowość Log Analytics agenta dla każdej maszyny. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach Azure Monitor, zobacz [grupy komputerów w dziennikach Azure monitor](../../azure-monitor/platform/computer-groups.md).

6. Region **klasyfikacje aktualizacji** umożliwia określenie [klasyfikacji aktualizacji](update-mgmt-view-update-assessments.md#work-with-update-classifications) produktów. Dla każdego produktu odznacz wszystkie obsługiwane klasyfikacje aktualizacji, ale te, które mają zostać uwzględnione we wdrożeniu aktualizacji.

    Jeśli wdrożenie ma zastosowanie tylko do wybranego zestawu aktualizacji, należy usunąć zaznaczenie wszystkich wstępnie wybranych klasyfikacji aktualizacji podczas konfigurowania opcji **Dołącz/Wyklucz aktualizacje** zgodnie z opisem w następnym kroku. Gwarantuje to, że tylko aktualizacje określone do *uwzględnienia* w tym wdrożeniu zostaną zainstalowane na komputerach docelowych.

7. Za pomocą regionu **Dołącz/Wyklucz aktualizacje** można dodawać lub wykluczać wybrane aktualizacje z wdrożenia. Na stronie **dołączanie/wykluczanie** wprowadzasz numery identyfikacyjne artykułu w bazie wiedzy do dołączenia lub wykluczenia.

   > [!IMPORTANT]
   > Należy pamiętać, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*` , Update Management wyklucza wszystkie poprawki lub pakiety z instalacji. Wykluczone poprawki nadal są wyświetlane jako brakujące z maszyn. W przypadku komputerów z systemem Linux, jeśli zostanie uwzględniony pakiet z wyłączonym pakietem zależnym, program Update Management nie zainstaluje głównego pakietu.

   > [!NOTE]
   > Nie można określić aktualizacji, które zostały zastąpione w celu uwzględnienia w wdrożeniu aktualizacji.

8. Wybierz pozycję **ustawienia harmonogramu**. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

9. Użyj pola **cykl** , aby określić, czy wdrożenie występuje raz, czy ma cykliczny harmonogram, a następnie wybierz **przycisk OK**.

10. W regionie **pre-scripts + post-Scripts (wersja zapoznawcza)** wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz temat Zarządzanie skryptami [wstępnymi i po skryptach](update-mgmt-pre-post-scripts.md).
    
11. Za pomocą pola **okna obsługi (minuty)** określ ilość czasu dozwoloną dla aktualizacji do zainstalowania. Podczas określania okna obsługi należy wziąć pod uwagę następujące informacje:

    * Okna obsługi kontrolują liczbę zainstalowanych aktualizacji.
    * Update Management nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna obsługi.
    * W przypadku przekroczenia okna obsługi Update Management nie przerywa aktualizacji w toku.
    * W przypadku przekroczenia okna obsługi w systemie Windows często trwa Instalowanie aktualizacji dodatku Service Pack.

    > [!NOTE]
    > Aby uniknąć stosowania aktualizacji poza oknem obsługi w programie Ubuntu, należy ponownie skonfigurować pakiet, `Unattended-Upgrade` Aby wyłączyć aktualizacje automatyczne. Informacje o sposobie konfigurowania pakietu znajdują się [w temacie Aktualizacje automatyczne w podręczniku serwera Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Użyj pola **Opcje ponownego rozruchu** , aby określić sposób obsługi ponownych uruchomień podczas wdrażania. Dostępne są następujące opcje: 
    * Uruchom ponownie w razie potrzeby (ustawienie domyślne)
    * Zawsze uruchamiaj ponownie
    * Nigdy nie uruchamiaj ponownie
    * Tylko Uruchom ponownie; Ta opcja nie powoduje instalacji aktualizacji

    > [!NOTE]
    > Klucze rejestru wymienione w obszarze [klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować ponowne uruchomienie zdarzenia w przypadku, gdy **opcja ponownego** uruchamiania jest ustawiona na **nigdy nie uruchamiaj**ponownie.

13. Po zakończeniu konfigurowania harmonogramu wdrażania wybierz pozycję **Utwórz**.

    ![Okienko ustawień harmonogramu aktualizacji](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz pozycję **zaplanowane wdrożenia aktualizacji** , aby wyświetlić utworzony harmonogram wdrożenia.

## <a name="schedule-an-update-deployment-programmatically"></a>Programowe Planowanie wdrożenia aktualizacji

Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create).

Aby utworzyć cotygodniowe wdrożenie aktualizacji, można użyć przykładowego elementu Runbook. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Sprawdź stan wdrożenia

Po rozpoczęciu zaplanowanego wdrożenia można zobaczyć jego stan na karcie **wdrożenia aktualizacji** w obszarze **Update Management**. Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrożenia stan zmieni się na **powodzenie**. Jeśli w danym wdrożeniu wystąpią błędy, stan jest **częściowo niepowodzenie**.

## <a name="view-results-of-a-completed-update-deployment"></a>Wyświetl wyniki ukończonego wdrożenia aktualizacji

Po zakończeniu wdrażania możesz wybrać je, aby wyświetlić jego wyniki.

[![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

W obszarze **Aktualizuj wyniki**podsumowanie zawiera łączną liczbę aktualizacji i wyników wdrożenia na docelowych maszynach wirtualnych. Tabela po prawej stronie zawiera szczegółowy podział aktualizacji oraz wyniki instalacji dla każdego z nich.

Dostępne są następujące wartości:

* **Nie podjęto próby — nie** zainstalowano aktualizacji z powodu niewystarczającego czasu dostępności na podstawie zdefiniowanego czasu trwania okna obsługi.
* **Nie wybrano** — aktualizacja nie została wybrana do wdrożenia.
* **Powodzenie** — aktualizacja powiodła się.
* **Niepowodzenie** — aktualizacja nie powiodła się.

Wybierz pozycję **wszystkie dzienniki** , aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz pozycję **dane wyjściowe** , aby wyświetlić strumień zadań elementu Runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowych maszynach wirtualnych.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak utworzyć alerty w celu powiadomienia o wynikach wdrożenia aktualizacji, zobacz [tworzenie alertów dla Update Management](update-mgmt-configure-alerts.md).