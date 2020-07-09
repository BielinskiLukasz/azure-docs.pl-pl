---
title: Automatyzacja przepływu pracy w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak tworzyć i automatyzować przepływy pracy w Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c97dafa80adedd64d45666eb98ef6b1e69850719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629396"
---
# <a name="workflow-automation"></a>Automatyzacja przepływu pracy

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie. Pozwala również zwiększyć bezpieczeństwo, zapewniając, że kroki procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

W tym artykule opisano funkcję automatyzacji przepływu pracy Azure Security Center. Ta funkcja może wyzwolić Logic Apps na temat alertów zabezpieczeń i zaleceń. Na przykład możesz chcieć, aby Security Center e-mail określonego użytkownika w przypadku wystąpienia alertu. Dowiesz się również, jak utworzyć Logic Apps przy użyciu [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Jeśli wcześniej był używany widok elementy PlayBook (wersja zapoznawcza) na pasku bocznym, znajdziesz te same funkcje wraz z rozwiniętymi funkcjami na nowej stronie automatyzacji przepływu pracy.



## <a name="availability"></a>Dostępność

- Stan wydania: **ogólnie dostępny**
- Wymagane role i uprawnienia:
    - **Czytelnik** w subskrypcji zawierającej konfigurację eksportu
    - **Rola administratora zabezpieczeń** w grupie zasobów (lub **właściciela**)
    - Musi mieć również uprawnienia do zapisu dla zasobu docelowego
    - Ponadto w celu pracy z przepływami pracy Azure Logic Apps należy mieć następujące role/uprawnienia Logic Apps:

        * Wymagane są uprawnienia [operatora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) lub dostęp do odczytu/wyzwalacza aplikacji logiki (Ta rola nie może tworzyć ani edytować aplikacji logiki; można *uruchamiać* tylko istniejące)

        * Uprawnienia [współautora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) są wymagane do utworzenia i modyfikacji aplikacji logiki

        * Jeśli chcesz używać łączników aplikacji logiki, możesz potrzebować dodatkowych poświadczeń, aby zalogować się do odpowiednich usług (na przykład wystąpień programu Outlook/zespołów/zapasowych).
- Połączeń 
    - ✔ Chmury komercyjne
    - ✔ US Gov
    - ✘ Chiny gov, inne gov


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Tworzenie aplikacji logiki i Definiowanie jej automatycznego uruchamiania 

1. Na pasku bocznym Security Center wybierz pozycję **Automatyzacja przepływu pracy**.

    [![Lista automatyzacji przepływu pracy](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Na tej stronie można tworzyć nowe reguły automatyzacji, a także włączać, wyłączać lub usuwać istniejące.  
1. Aby zdefiniować nowy przepływ pracy, kliknij przycisk **Dodaj automatyzację przepływu pracy**. 

    Zostanie wyświetlone okienko z opcjami nowej automatyzacji. W tym miejscu możesz wprowadzić:
    1. Nazwa i Opis automatyzacji.
    1. Wyzwalacze, które będą inicjować ten automatyczny przepływ pracy. Na przykład może być konieczne uruchomienie aplikacji logiki po wygenerowaniu alertu zabezpieczeń zawierającego "SQL".
    1. Aplikacja logiki, która będzie uruchamiana po spełnieniu warunków wyzwalacza. 

        [![Lista automatyzacji przepływu pracy](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. W sekcji Actions (akcje) kliknij pozycję **Utwórz nową** , aby rozpocząć proces tworzenia aplikacji logiki.

    Nastąpi przekierowanie do Azure Logic Apps.

    [![Tworzenie nowej aplikacji logiki](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Wprowadź nazwę, grupę zasobów i lokalizację, a następnie kliknij przycisk **Utwórz**.

1. W nowej aplikacji logiki można wybrać opcję z wbudowanych, wstępnie zdefiniowanych szablonów z kategorii zabezpieczenia. Można też zdefiniować niestandardowy przepływ zdarzeń, które mają być wykonywane w momencie wyzwolenia tego procesu.

    W Projektancie aplikacji logiki są obsługiwane następujące wyzwalacze z łączników Security Center:

    * **Po utworzeniu lub wyzwoleniu rekomendacji Azure Security Center**
    * **Po utworzeniu lub wyzwoleniu alertu Azure Security Center** 
    
    > [!TIP]
    > Możesz dostosować wyzwalacz, aby odnosił się tylko do alertów z poziomami ważności, które Cię interesują.
    
    > [!NOTE]
    > Jeśli używasz starszego wyzwalacza "gdy zostanie wyzwolona odpowiedź na Azure Security Center alertu", Logic Apps nie zostanie uruchomiona przez funkcję automatyzacji przepływu pracy. Zamiast tego należy użyć dowolnego wyzwalacza wymienionego powyżej. 

    [![Przykładowa aplikacja logiki](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po zdefiniowaniu aplikacji logiki Wróć do okienka definicji automatyzacji przepływu pracy ("Dodaj automatyzację przepływu pracy"). Kliknij przycisk **Odśwież** , aby upewnić się, że nowa aplikacja logiki jest dostępna do wybrania.

    ![Odśwież](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wybierz aplikację logiki i Zapisz automatyzację. Należy pamiętać, że lista rozwijana aplikacji logiki zawiera tylko Logic Apps z obsługą łączników Security Center wymienionych powyżej.


## <a name="manually-trigger-a-logic-app"></a>Ręczne wyzwalanie aplikacji logiki

Możesz również uruchomić Logic Apps ręcznie podczas wyświetlania alertu zabezpieczeń lub wszelkich zaleceń, które oferują [szybkie rozwiązywanie problemów](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Aby ręcznie uruchomić aplikację logiki, Otwórz alert lub zalecenie, które obsługuje szybkie rozwiązywanie problemów i kliknij kolejno pozycje **Wyzwól aplikację logiki**:

[![Ręczne wyzwalanie aplikacji logiki](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schematy typów danych

Aby wyświetlić nieprzetworzone schematy zdarzeń alertów zabezpieczeń lub zaleceń przesyłanych do wystąpienia aplikacji logiki, odwiedź [schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas). Może to być przydatne w przypadkach, gdy nie Security Center korzystasz z wbudowanych łączników aplikacji logiki, które zostały wymienione powyżej, ale zamiast tego używamy ogólnego łącznika HTTP aplikacji logiki — możesz użyć schematu JSON zdarzenia, aby ręcznie przeanalizować ją zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje na temat tworzenia Logic Apps, automatyzowania ich wykonywania w Security Center i uruchamiania ich ręcznie. 

W przypadku innych powiązanych materiałów Zobacz: 

- [Moduł Microsoft Learn dotyczący używania automatyzacji przepływu pracy do automatyzowania odpowiedzi zabezpieczeń](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md)
- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-alerts-overview.md)
- [Azure Logic Apps — informacje](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Łączniki usługi Logic Apps](https://docs.microsoft.com/connectors/)
- [Schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas)
