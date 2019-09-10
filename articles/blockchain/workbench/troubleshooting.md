---
title: Rozwiązywanie problemów z usługą Azure łańcucha bloków Workbench Preview
description: Jak rozwiązywać problemy z aplikacją Azure łańcucha bloków Workbench w wersji zapoznawczej.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844028"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure łańcucha bloków Workbench Preview

Skrypt programu PowerShell jest dostępny, aby pomóc w debugowaniu i obsłudze deweloperów. Skrypt generuje podsumowanie i zbiera szczegółowe dzienniki dotyczące rozwiązywania problemów. Zebrane dzienniki obejmują:

* Sieć łańcucha bloków, taka jak Ethereum
* Mikrousługi łańcucha bloków Workbench
* Application Insights
* Monitorowanie platformy Azure (dzienniki Azure Monitor)

Możesz użyć tych informacji do określenia następnych kroków i ustalenia głównej przyczyny problemów.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Skrypt rozwiązywania problemów

Skrypt rozwiązywania problemów z programem PowerShell jest dostępny w witrynie GitHub. [Pobierz plik ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Uruchamianie skryptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` Uruchom skrypt, aby zebrać dzienniki i utworzyć plik zip zawierający folder informacji o rozwiązywaniu problemów. Przykład:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skrypt akceptuje następujące parametry:

| Parametr  | Opis | Wymagane |
|---------|---------|----|
| Identyfikator | Utwórz identyfikator subskrypcji, aby utworzyć lub zlokalizować wszystkie zasoby. | Tak |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w której wdrożono łańcucha bloków Workbench. | Tak |
| OutputDirectory | Ścieżka do tworzenia danych wyjściowych. Plik ZIP. Jeśli nie zostanie określony, wartością domyślną jest bieżący katalog. | Nie |
| LookbackHours | Liczba godzin do użycia podczas ściągania danych telemetrycznych. Wartość domyślna to 24 godziny. Wartość maksymalna to 90 godzin | Nie |
| OmsSubscriptionId | Identyfikator subskrypcji, w której wdrożone są dzienniki Azure Monitor. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench.| Nie |
| OmsResourceGroup |Grupa zasobów, w której wdrożono dzienniki Azure Monitor. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench.| Nie |
| OmsWorkspaceName | Nazwa obszaru roboczego Log Analytics. Ten parametr należy przekazać tylko wtedy, gdy dzienniki Azure Monitor dla sieci łańcucha bloków są wdrożone poza grupą zasobów łańcucha bloków Workbench | Nie |

## <a name="what-is-collected"></a>Co jest zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Folder lub plik | Opis  |
|---------|---------|
| \Summary.txt | Podsumowanie systemu |
| \Metrics\blockchain | Metryki dotyczące łańcucha bloków |
| \Metrics\Workbench | Metryki dotyczące Workbench |
| \Details\Blockchain | Szczegółowe dzienniki dotyczące łańcucha bloków |
| \Details\Workbench | Szczegółowe dzienniki dotyczące Workbench |

Plik podsumowania zawiera migawkę ogólnego stanu aplikacji i kondycji aplikacji. Podsumowanie zawiera zalecane akcje, wyróżnia najważniejsze błędy i metadane dotyczące uruchomionych usług.

Folder **metryk** zawiera metryki różnych składników systemu w miarę upływu czasu. Na przykład plik `\Details\Workbench\apiMetrics.txt` wyjściowy zawiera podsumowanie różnych kodów odpowiedzi i czasów odpowiedzi w całym okresie zbierania. Folder **szczegóły** zawiera szczegółowe dzienniki umożliwiające rozwiązywanie określonych problemów z usługą Workbench lub podstawową siecią łańcucha bloków. Na przykład `\Details\Workbench\Exceptions.csv` zawiera listę najnowszych wyjątków, które wystąpiły w systemie, co jest przydatne do rozwiązywania problemów z inteligentnymi kontraktami lub interakcjami z łańcucha bloków. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przewodnik rozwiązywania problemów z usługą Azure łańcucha bloków Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
