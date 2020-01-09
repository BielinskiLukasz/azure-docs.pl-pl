---
title: Wyświetl Azure Monitor dla wdrożeń kontenerów (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano widok Kubernetes wdrożeń w czasie rzeczywistym bez użycia polecenia kubectl w Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404776"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Jak wyświetlać wdrożenia (wersja zapoznawcza) w czasie rzeczywistym

Za pomocą Azure Monitor dla kontenerów Funkcja Wyświetl wdrożenia (wersja zapoznawcza) emuluje bezpośredni dostęp do obiektów wdrożenia Kubernetes w czasie rzeczywistym przez udostępnienie poleceń `kubeclt get deployments` i `kubectl describe deployment {your deployment}`. 

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) są nieobsługiwane w przypadku tej funkcji. Ta funkcja wykorzystuje bezpośrednio dostęp do interfejsu API Kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieci w celu blokowania interfejsu API Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w tym na platformie Azure (Chiny). Nie jest ona obecnie dostępna w systemie Azure dla instytucji rządowych USA.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją Kubernetes dotyczącą [wdrożeń](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Zasady działania

Funkcja dane dynamiczne (wersja zapoznawcza) bezpośrednio uzyskuje dostęp do interfejsu API Kubernetes. dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funkcja wdrożenia (wersja zapoznawcza) wykonuje jednorazowe (odświeżane) obciążenie dla punktu końcowego wdrożeń `/apis/apps/v1/deployments`. Umożliwia wybranie danego wdrożenia i załadowanie szczegółowych informacji o tym konkretnym wdrożeniu do punktu końcowego wdrożenia `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`. 

Wybranie opcji **Odśwież** w lewym górnym rogu strony spowoduje odświeżenie listy wdrożenia. To symuluje ponowne uruchomienie polecenia `kubectl`. 

>[!IMPORTANT]
>Podczas wykonywania tej funkcji żadne dane nie są trwale przechowywane. Wszystkie informacje przechwycone podczas sesji są usuwane, gdy zamkniesz przeglądarkę lub opuścisz ją.  

>[!NOTE]
>Nie można przypiąć danych na żywo (wersja zapoznawcza) z konsoli programu do pulpitu nawigacyjnego platformy Azure.

## <a name="deployments-describe"></a>Opis wdrożeń

Aby wyświetlić szczegółowe informacje dotyczące wdrożenia, które jest równoważne `kubectl describe deployment`, wykonaj następujące czynności.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Na pulpicie nawigacyjnym klastra AKS, w obszarze **monitorowanie** po lewej stronie, wybierz pozycję **szczegółowe informacje**. 

3. Wybierz kartę **wdrożenia (wersja zapoznawcza)** .

    ![Widok wdrożeń w Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Widok przedstawia listę wszystkich uruchomionych wdrożeń wraz z obszarem nazw i innymi szczegółowymi informacjami, które emulują wykonywanie polecenia `kubectl get deployments –all-namespaces`. Wyniki można sortować, wybierając jedną z kolumn. 

![Szczegóły okienka właściwości wdrożenia](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Po wybraniu wdrożenia z listy okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. Zawiera informacje dotyczące wybranego wdrożenia, które można wyświetlić, jeśli uruchomiono polecenie `kubectl describe deployment {deploymentName}`. Być może zauważono, że w opisie informacji brakuje pewnych szczegółów. W szczególności brakuje **szablonu** . Wybranie zakładki **RAW** pozwala przejść do szczegółów nieanalizowanych opisów.  

![Szczegóły szczegółów okienka właściwości wdrożenia](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Podczas przeglądania szczegółów wdrożenia można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Wybierz pozycję **Pokaż konsolę na żywo** i konsolę dane dynamiczne (wersja zapoznawcza) pojawi się poniżej siatki wdrożenia, w której można wyświetlić dane dziennika na żywo w strumieniu ciągłym. Jeśli wskaźnik stanu pobierania zawiera zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli programu.

Można również filtrować według przestrzeni nazw lub zdarzeń na poziomie klastra. Aby dowiedzieć się więcej na temat wyświetlania danych w czasie rzeczywistym w konsoli programu, zobacz [Wyświetlanie danych na żywo (wersja zapoznawcza) za pomocą Azure monitor dla kontenerów](container-insights-livedata-overview.md). 

![Wdrożenia wyświetlanie danych na żywo w konsoli](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do tworzenia alertów, wizualizacji lub przeprowadzenia dalszej analizy klastrów.
