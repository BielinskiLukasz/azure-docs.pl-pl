---
title: Przełączanie między wyświetlaniem i Edytuj tryb dla raportów w kolekcji obszarów roboczych usługi Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączać się między widoku, a i raportów w ramach kolekcji obszarów roboczych usługi Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 19a576440742684849ffc74092162be7008621ba
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045022"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Przełączanie między wyświetlaniem i Edytuj tryb dla raportów w kolekcji obszarów roboczych usługi Power BI

Dowiedz się, jak przełączać się między widoku, a i raportów w ramach kolekcji obszarów roboczych usługi Power BI.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Tworzenie tokenu dostępu

Musisz utworzyć token dostępu, który daje możliwość wyświetlania i edytowania raportu. Aby edytować i zapisać raport, musisz mieć **Report.ReadWrite** token uprawnień. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie i autoryzowanie w kolekcji obszarów roboczych usługi Power BI](app-token-flow.md).

> [!NOTE]
> Dzięki temu można zapisać zmian do istniejącego raportu. Jeśli chcesz również funkcję obsługi **Zapisz jako**, musisz podać dodatkowe uprawnienia. Aby uzyskać więcej informacji, zobacz [zakresy](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Osadzanie konfiguracji

Musisz podać uprawnienia i viewMode, aby można było wyświetlić zapisywania przycisku w trybie edycji. Aby uzyskać więcej informacji, zobacz [osadzić szczegóły konfiguracji](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Na przykład w języku JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Oznacza to, aby osadzić raport w trybie widoku na podstawie **viewMode** zostanie ustawiona **modeli. ViewMode.View**.

## <a name="view-mode"></a>Tryb wyświetlania

Następujący kod JavaScript umożliwia przełączanie do trybu wyświetlania, jeśli jesteś w trybie edycji.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Tryb edycji

Następujący kod JavaScript służy do przełączania do trybu edycji, jeśli jesteś w trybie.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repozytorium Git języka CSharp usługi Power BI](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git w węźle usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
