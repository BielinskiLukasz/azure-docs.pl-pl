---
title: Integracja kontroli w usłudze Azure Automation — starsza wersja źródła
description: W tym artykule opisano Integracja kontroli źródła z usługą GitHub w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8316e571e97fce65b3f8308709d3300bc585663f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434873"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integracja kontroli źródła w usłudze Azure Automation — starsza wersja

> [!NOTE]
> Istnieje nowe środowisko do kontroli źródła. Aby dowiedzieć się więcej na temat nowego środowiska, zobacz [kontroli źródła (wersja zapoznawcza)](source-control-integration.md).

Integracja kontroli źródła umożliwia kojarzenie elementów runbook na Twoim koncie usługi Automation do repozytorium kontroli źródła usługi GitHub. Kontrola źródła pozwala łatwo współpracować z zespołem, śledzenie zmian i wrócić do wcześniejszych wersji elementów runbook. Na przykład kontroli źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła do rozwoju, testów lub produkcji kont usługi Automation, co ułatwia promowanie kod, który został przetestowany w środowisku projektowym automatyzacji w środowisku produkcyjnym konto.

Kontrola źródła umożliwia wypychanie kodu z usługi Azure Automation do kontroli źródła lub pobierania elementów runbook z poziomu kontroli źródła do usługi Azure Automation. W tym artykule opisano sposób konfigurowania kontroli źródła w środowisku usługi Azure Automation. Rozpoczniemy pracę przez skonfigurowanie usługi Azure Automation, dostęp do repozytorium GitHub i opisano różne operacje, które mogą realizować za pomocą integracji kontroli źródła. 

> [!NOTE]
> Obsługuje kontroli źródła, ściągając i wypychając [elementami runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) także [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks). [Graficzne elementy runbook](automation-runbook-types.md#graphical-runbooks) nie są jeszcze obsługiwane.<br><br>
> 
> 

Istnieją dwa proste kroki wymagane do skonfigurowania kontroli źródła dla Twojego konta usługi Automation i tylko jeden, jeśli masz już konto w usłudze GitHub. Oto one:

## <a name="step-1--create-a-github-repository"></a>Krok 1. Tworzenie repozytorium usługi GitHub
Jeśli masz już konto w usłudze GitHub i repozytorium, którego chcesz utworzyć łącze do usługi Azure Automation, następnie zaloguj się do istniejącego konta i rozpocząć od kroku 2 poniżej. W przeciwnym razie przejdź do [GitHub](https://github.com/), Utwórz nowe konto i [Utwórz nowe repozytorium](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 — Konfigurowanie kontroli źródła w usłudze Azure Automation
1. Z konta usługi Automation strony w witrynie Azure portal w obszarze **ustawienia konta**, kliknij przycisk **kontroli źródła.** 
   
1. **Kontroli źródła** stronie zostanie otwarty, w którym można skonfigurować szczegółowe informacje o koncie usługi GitHub. Poniżej znajduje się lista parametrów do skonfigurowania:  
   
   | **Parametr** | **Opis** |
   |:--- |:--- |
   | Wybieranie źródła |Wybierz źródło. Obecnie tylko **GitHub** jest obsługiwana. |
   | Autoryzacja |Kliknij przycisk **Autoryzuj** przycisk, aby udzielić dostępu usługi Azure Automation do repozytorium GitHub. Jeśli użytkownik jest już zalogowany do konta usługi GitHub w innym oknie, są używane poświadczenia tego konta. Po pomyślnym autoryzacji strony wyświetli swoją nazwę użytkownika usługi GitHub, w obszarze **właściwości autoryzacji**. |
   | Wybierz repozytorium |Wybierz repozytorium GitHub z listy dostępnych repozytoriów. |
   | Wybierz rozgałęzienie |Wybierz gałąź z listy dostępnych gałęzi. Tylko **wzorca** gałąź jest wyświetlana, jeśli nie utworzono żadnych gałęzi. |
   | Ścieżka folderu elementów Runbook |Ścieżka folderu elementów runbook ścieżka w repozytorium GitHub, z którego chcesz wypchnąć ani ściągnąć kod. Należy wprowadzić w formacie **/nazwa folderu/nazwa podfolderu**. Tylko elementy runbook w ścieżce folderu elementów runbook będą synchronizowane z kontem usługi Automation. Elementy Runbook w podfolderach ścieżka folderu elementów runbook będą **nie** zostanie on zsynchronizowany. Użyj **/** na zsynchronizowanie wszystkich elementów runbook w ramach repozytorium. |
3. Na przykład, jeśli masz repozytorium o nazwie **PowerShellScripts** zawiera folder o nazwie **RootFolder**, który zawiera folder o nazwie **podfolder**. Można zsynchronizować każdy poziom folderu, można użyć następujących ciągów:
   
   1. Synchronizacja elementów runbook, z **repozytorium**, jest ścieżka folderu elementów runbook */*
   2. Synchronizacja elementów runbook, z **RootFolder**, ścieżka folderu elementów runbook jest */RootFolder*
   3. Synchronizacja elementów runbook, z **podfolder**, ścieżka folderu elementów runbook jest */RootFolder/podfolder*.
4. Po skonfigurowaniu parametrów, są one wyświetlane na **ustawić kontroli źródła** strony.  
   
    ![Konfigurowanie strony kontroli źródła](media/automation-source-control-integration-legacy/automation_02_SourceControlConfigure.png)
5. Po kliknięciu **OK**, Integracja kontroli źródła został skonfigurowany dla konta usługi Automation i powinien zostać zaktualizowany z informacjami o usłudze GitHub. Teraz kliknięcie tej części, aby wyświetlić wszystkie historię zadania synchronizacji kontroli źródła.  
   
    ![Wartości repozytorium](media/automation-source-control-integration-legacy/automation_03_RepoValues.png)
6. Po skonfigurowaniu kontroli źródła w następujących zasobach usługi Automation zostanie utworzona na Twoim koncie usługi Automation:  
   Dwa [zmiennych elementów zawartości](automation-variables.md) są tworzone.  
   
   * Zmienna **Microsoft.Azure.Automation.SourceControl.Connection** zawiera wartości parametrów połączenia, jak pokazano poniżej.  
     
     | **Parametr** | **Wartość** |
     |:--- |:--- |
     | Name (Nazwa) |Microsoft.Azure.Automation.SourceControl.Connection |
     | Typ |Ciąg |
     | Wartość |{"Gałąź":\<*swoją nazwę gałęzi*>, "RunbookFolderPath":\<*ścieżka folderu elementów Runbook*>, "Typ dostawcy":\<*ma wartość 1 dla GitHub*>, "Repozytorium":\<*nazwę repozytorium*>, "Username":\<*nazwę użytkownika usługi GitHub*>} |

    * Zmienna **Microsoft.Azure.Automation.SourceControl.OAuthToken**, zawiera wartość zaszyfrowanych bezpiecznego swoje OAuthToken.  

    |**Parametr**            |**Wartość** |
    |:---|:---|
    | Name (Nazwa)  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Typ | Unknown(Encrypted) |
    | Wartość | <*Zaszyfrowane OAuthToken*> |  

    ![Zmienne](media/automation-source-control-integration-legacy/automation_04_Variables.png)  

    * **Kontrola źródła automatyzacji** jest dodawany jako autoryzowaną aplikację do konta usługi GitHub. Aby wyświetlić aplikację: Ze strony głównej usługi GitHub przejdź do swojej **profilu** > **ustawienia** > **aplikacji**. Ta aplikacja umożliwia usłudze Azure Automation zsynchronizować swoje repozytorium GitHub z kontem usługi Automation.  

    ![Aplikacja usługi Git](media/automation-source-control-integration-legacy/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Przy użyciu kontroli źródła w usłudze Automation
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Zaewidencjonuj element runbook usługi Azure Automation do kontroli źródła
Zaewidencjonuj element Runbook umożliwia wypychanie zmiany wprowadzone do elementu runbook w usłudze Azure Automation do repozytorium kontroli źródła. Poniżej przedstawiono kroki, aby zaewidencjonować elementu runbook:

1. Na koncie usługi Automation [Utwórz nowy element runbook tekstową](automation-first-runbook-textual.md), lub [Edytuj istniejący, tekstowy element runbook](automation-edit-textual-runbook.md). Ten element runbook może być w przepływie pracy programu PowerShell lub elementu runbook programu PowerShell script.  
2. Po zakończeniu edycji elementu runbook, zapisz go i kliknij przycisk **ewidencjonowania** z **Edytuj** strony.  
   
    ![Przycisk zaewidencjonowania](media/automation-source-control-integration-legacy/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Warunkowo w usłudze Azure Automation zastępuje kod, który znajduje się w kontroli źródła. Jest równoważne instrukcji wiersza polecenia usługi Git do zaewidencjonowania **git Dodaj + wypychanie zatwierdzeń usługi git i git**  

1. Po kliknięciu **ewidencjonowania**, zostanie wyświetlony monit z potwierdzeniem, kliknij przycisk **tak** aby kontynuować.  
   
    ![Komunikat dotyczący zaewidencjonowania](media/automation-source-control-integration-legacy/automation_07_CheckinMessage.png)
2. Warunkowo uruchamia element runbook z kontroli źródła: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Ten element runbook nawiązanie połączenia z usługi GitHub i wypychania zmian z usługi Azure Automation do repozytorium. Aby wyświetlić zaznaczonego w historii zadań, wróć do obszaru **Integracja kontroli źródła** kartę i kliknij, aby otworzyć stronę synchronizacja repozytorium. Ta strona zawiera wszystkich zadań kontroli źródła.  Zaznacz zadanie, które chcesz wyświetlić i kliknij, aby wyświetlić szczegóły.  
   
    ![Zaewidencjonowanie elementu Runbook](media/automation-source-control-integration-legacy/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Elementy runbook kontroli źródła są specjalne elementy runbook usługi Automation, które nie można wyświetlić ani edytować. Podczas ich nie są wyświetlane na liście elementów runbook, zobaczysz zadania synchronizacji wyświetlane na liście zadań.
   > 
   > 
3. Nazwa zmodyfikowanego elementu runbook są wysyłane jako parametr wejściowy elementu runbook została zaewidencjonowana. Możesz [wyświetlić szczegóły zadania](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) , rozwijając element runbook w programie **synchronizacja repozytorium** strony.  
   
    ![Ewidencjonowanie danych wejściowych](media/automation-source-control-integration-legacy/automation_09_CheckinInput.png)
4. Po zakończeniu zadania, aby wyświetlić zmiany, należy odświeżyć repozytorium GitHub.  Powinien istnieć zatwierdzenia w repozytorium, komunikat zatwierdzenia: **Zaktualizowano *nazwa elementu Runbook* w usłudze Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizacja elementów runbook z poziomu kontroli źródła do usługi Azure Automation
Przycisk synchronizacji na stronie synchronizacja repozytorium umożliwia ściągać wszystkie elementy runbook ścieżka folderu elementów runbook Twojego repozytorium do konta usługi Automation. Mogą być synchronizowane z tym samym repozytorium do więcej niż jedno konto usługi Automation. Poniżej przedstawiono kroki, aby zsynchronizować elementem runbook:

1. Korzystając z konta usługi Automation, w którym konfiguruje się kontroli źródła, otwórz **synchronizacja repozytorium/Integracja kontroli źródła** strony, a następnie kliknij przycisk **synchronizacji**.  Zostanie wyświetlony monit z potwierdzeniem, kliknij przycisk **tak** aby kontynuować.  
   
    ![Przycisk synchronizacji](media/automation-source-control-integration-legacy/automation_10_SyncButtonwithMessage.png)
2. Synchronizacja uruchomi element runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Ten element runbook nawiązanie połączenia z usługi GitHub i pobiera zmiany z repozytorium do usługi Azure Automation. Na powinno zostać wyświetlone nowe zadanie **synchronizacja repozytorium** strony dla tej akcji. Aby wyświetlić szczegóły dotyczące zadania synchronizacji, kliknij, aby otworzyć stronę szczegółów zadania.  
   
    ![Synchronizacja elementu Runbook](media/automation-source-control-integration-legacy/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Synchronizacja z kontroli źródła zastępuje wersję roboczą elementy runbook, które obecnie istnieje na koncie usługi Automation, aby uzyskać **wszystkich** elementów runbook, które są obecnie w kontroli źródła. Jest równoważne instrukcji wiersza polecenia Git można zsynchronizować **ściągnięcia usługi git**


## <a name="troubleshooting-source-control-problems"></a>Rozwiązywanie problemów kontroli źródła
Jeśli występują błędy z ewidencjonowanie lub zadanie synchronizacji, powinny być zawieszone, stan zadania i więcej szczegółów na temat błędu można wyświetlić na stronie zadań.  **Wszystkie dzienniki** część pokazuje wszystkie strumienie PowerShell skojarzonego z tym zadaniem. Zawiera szczegółowe informacje potrzebne do rozwiązywania wszelkich problemów z ewidencjonowanie lub synchronizacji. Pokazano także, sekwencji akcji, które wystąpiły podczas synchronizacji oraz sprawdzania w elemencie runbook.  

![Obraz AllLogs](media/automation-source-control-integration-legacy/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Trwa rozłączanie kontroli źródła
Aby odłączyć od Twojego konta usługi GitHub, otwórz stronę synchronizacja repozytorium, a następnie kliknij przycisk **rozłączenia**. Po odłączeniu kontroli źródła, elementy runbook, które zostały wcześniej zsynchronizowane wciąż znajdują się na koncie usługi Automation, ale nie zostanie włączona na stronie synchronizacja repozytorium.  

  ![Przycisk Rozłącz](media/automation-source-control-integration-legacy/automation_12_Disconnect.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat integracji kontroli źródła zobacz następujące zasoby:  

* [Usługa Azure Automation: Integracja kontroli źródła w usłudze Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Zagłosuj na system kontroli źródła z ulubionych](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Usługa Azure Automation: Integracja kontroli źródła elementu Runbook za pomocą DevOps platformy Azure](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  


