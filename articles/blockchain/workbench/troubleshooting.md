---
title: Rozwiązywanie problemów z platformy Azure Blockchain Workbench
description: Jak rozwiązywać problemy z aplikacją Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e205fce8b718e68200face33447e37cd3317298f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405488"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Rozwiązywanie problemów z platformy Azure Blockchain Workbench

Skrypt programu PowerShell jest dostępny dla uzyskanymi debugowania dla deweloperów lub pomocy technicznej. Skrypt generuje podsumowanie i zbiera dzienniki do rozwiązywania problemów. Zebrane dzienniki obejmują:

* Sieć łańcucha bloków, takich jak Ethereum
* Mikrousługi aplikacji Blockchain Workbench
* Application Insights
* (Usługa Log Analytics) do monitorowania platformy Azure

Aby określić następne kroki i ustalania głównej przyczyny problemów, można użyć informacji. 

## <a name="troubleshooting-script"></a>Rozwiązywanie problemów z skryptu

Rozwiązywanie problemów z skrypt programu PowerShell jest dostępny w witrynie GitHub. [Pobierz plik ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) lub sklonuj przykład z usługi GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Uruchamianie skryptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Uruchom `collectBlockchainWorkbenchTroubleshooting.ps1` skrypt, aby zbieranie dzienników i Utwórz plik ZIP zawierający folder informacje dotyczące rozwiązywania problemów. Na przykład:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Skrypt przyjmuje następujące parametry:

| Parametr  | Opis | Wymagane |
|---------|---------|----|
| Identyfikator subskrypcji | Identyfikator subskrypcji można utworzyć lub Znajdź wszystkie zasoby. | Yes |
| ResourceGroupName | Nazwa grupy zasobów platformy Azure, w którym została wdrożona Blockchain Workbench. | Yes |
| OutputDirectory | Ścieżka do utworzenia danych wyjściowych. Plik ZIP. Jeśli nie zostanie określony, domyślnie w bieżącym katalogu. | Nie |
| LookbackHours | Liczba godzin do użycia podczas ściągania danych telemetrycznych. Wartość domyślna to 24 godziny. Maksymalna wartość to 90 godzin | Nie |
| OmsSubscriptionId | Identyfikator subskrypcji, w której wdrażana jest usługa Log Analytics. Ten parametr należy przekazać tylko w przypadku wdrażania usługi Log Analytics dla sieciowych łańcucha bloków poza grupę zasobów aplikacji Blockchain Workbench.| Nie |
| OmsResourceGroup |Grupa zasobów, w której wdrażana jest usługa Log Analytics. Ten parametr należy przekazać tylko w przypadku wdrażania usługi Log Analytics dla sieciowych łańcucha bloków poza grupę zasobów aplikacji Blockchain Workbench.| Nie |
| OmsWorkspaceName | Nazwa obszaru roboczego usługi Log Analytics. Ten parametr należy przekazać tylko, jeśli usługa Log Analytics dla sieciowych łańcuch bloków jest wdrożony poza grupę zasobów aplikacji Blockchain Workbench | Nie |

## <a name="what-is-collected"></a>Jakie informacje są zbierane?

Wyjściowy plik ZIP zawiera następującą strukturę folderów:

| Plik lub folder | Opis  |
|---------|---------|
| \Summary.txt | Podsumowanie systemu |
| \Metrics\blockchain | Metryk dotyczących łańcucha bloków |
| \Metrics\Workbench | Metryki dotyczące aplikacji workbench |
| \Details\Blockchain | Szczegółowe dzienniki o łańcucha bloków |
| \Details\Workbench | Szczegółowe dzienniki o aplikacji workbench |

Plik podsumowania tworzy migawkę ogólny stan aplikacji i kondycji aplikacji. Podsumowanie proponują zalecane akcje, wymieniono najważniejsze błędy i metadane dotyczące uruchamiania usługi.

**Metryki** folder zawiera metryki różnych składników systemu wraz z upływem czasu. Na przykład plik wyjściowy `\Details\Workbench\apiMetrics.txt` zawiera podsumowanie kodów odpowiedzi różne i najlepsze czasy odpowiedzi przez cały okres zbierania. **Szczegóły** folder zawiera szczegółowe dzienniki określonych problemów z aplikacji Workbench lub odnośnej sieci łańcucha bloków. Na przykład `\Details\Workbench\Exceptions.csv` zawiera listę najnowsze wyjątki, które miały miejsce w systemie, który jest przydatne podczas rozwiązywania problemów z kontraktów inteligentnych lub interakcji z łańcucha bloków. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Architektura usługi Azure Blockchain Workbench](architecture.md)
