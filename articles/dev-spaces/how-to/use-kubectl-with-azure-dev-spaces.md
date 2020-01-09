---
title: Jak używać polecenia kubectl z Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Dowiedz się, jak używać poleceń polecenia kubectl w obszarze dev w klastrze usługi Azure Kubernetes z włączonym Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438360"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Korzystanie z polecenia kubectl z przestrzenią dev platformy Azure

Możesz uzyskać dostęp do klastra Kubernetes w przestrzeni usługi Azure dev i korzystać z istniejących narzędzi Kubernetes, takich jak `kubectl`.

Uruchomienie `az aks use-dev-spaces` polecenie spowoduje automatyczne dodanie kontekstu konfiguracji `kubectl`, więc polecenia kubectl powinny być już połączone z klastrem Azure Dev Spaces Kubernetes. Przykłady:
- Potwierdź bieżący kontekst: `kubectl config current-context`
- Wyświetl wszystkie dostępne konteksty: `kubectl config get-contexts`. 
- Zmień kontekst: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny Kubernetes: Uruchom `kubectl proxy`, a następnie otwórz przeglądarkę na adres, który to polecenie emituje (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Wyświetl listę uruchomionych usług w domyślnym obszarze Azure Dev Spaces nazwa *Domyślna*: `kubectl get services --namespace=default`

