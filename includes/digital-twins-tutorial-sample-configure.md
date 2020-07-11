---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji platformy Azure — Konfigurowanie przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6edc6facc884fc6469b75b63216d6e5036321ea8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277865"
---
## <a name="configure-the-sample-project"></a>Konfigurowanie przykładowego projektu

Następnie skonfiguruj przykładową aplikację kliencką, która będzie współpracująca z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Jeśli nie pobrano jeszcze przykładowego projektu, Pobierz go teraz ze strony docelowej [próbek Digital bliźniaczych reprezentacji na platformie Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) , wybierając przycisk *Pobierz zip* poniżej tytułu.

Przejdź do pobranego pliku na maszynie i rozpakuj go.

Po umieszczeniu wewnątrz folderu rozpakowanego przejdź do _AdtSampleApp/_. Otwórz _**AdtE2ESample. sln**_ w programie Visual Studio 2019. 

W programie Visual Studio użyj okienka *Eksplorator rozwiązań* , aby utworzyć kopię _serviceConfig.js> SampleClientApp **na. Plik szablonu** _ (możesz użyć menu z prawym przyciskiem myszy w celu skopiowania i wklejenia). Zmień nazwę *serviceConfig.jskopii na*. Będzie to stanowić wstępnie ustawiony plik JSON z niezbędnymi zmiennymi konfiguracyjnymi do uruchomienia projektu.

Wybierz *serviceConfig.jsna* pliku, aby otworzyć go w oknie edycji. Zmień wartość `tenantId` na *Identyfikator katalogu*, `clientId` *Identyfikator aplikacji*i `instanceUrl` adres URL *nazwy hosta* usługi Azure Digital bliźniaczych reprezentacji (z *https://* przed nim, jak pokazano poniżej).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Zapisz i zamknij plik. 

Następnie skonfiguruj *serviceConfig.jsw* pliku, który ma zostać skopiowany do katalogu wyjściowego podczas kompilowania *SampleClientApp*. W tym celu kliknij prawym przyciskiem myszy *serviceConfig.jsw* pliku, a następnie wybierz polecenie *właściwości.* W Inspektorze *Właściwości* Zmień wartość właściwości *Kopiuj do katalogu wyjściowego* na *Kopiuj, jeśli nowszy*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Fragment z okna programu Visual Studio z okienkiem Eksplorator rozwiązań z wyróżnioną serviceConfig.js, a okienko właściwości z właściwością "Kopiuj do katalogu wyjściowego" ma wartość "Kopiuj, jeśli nowszy"" border="false":::

Kontynuuj otwieranie projektu _**AdtE2ESample**_ w programie Visual Studio, aby nadal korzystać z niego w samouczku.

