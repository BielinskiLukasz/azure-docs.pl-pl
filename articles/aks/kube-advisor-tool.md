---
title: Sprawdzanie wdrożenia rozwiązania Kubernetes na platformie Azure dla implementacji najlepszych rozwiązań
description: Dowiedz się, jak sprawdzić, czy stosowania najlepszych rozwiązań dotyczących wdrożenia w usłudze Azure Kubernetes Service przy użyciu klastra kubernetes w usłudze advisor
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 01095ac4ed8e362f1a89a53b10b5da6a547feb57
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218642"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Sprawdzanie, czy są najlepsze rozwiązania Kubernetes w klastrze

Istnieje kilka najlepszych rozwiązań, które należy wykonać na swoich wdrożeń Kubernetes, aby zapewnić najlepszą wydajność i odporność aplikacji. Narzędzie klastra kubernetes w usłudze advisor do wyszukania wdrożenia, które nie są następujące tych propozycji.

## <a name="about-kube-advisor"></a>Informacji o usłudze advisor rozwiązania kubernetes

[Klastra kubernetes w usłudze advisor narzędzie] [ kube-advisor-github] jest jeden kontener przeznaczony do uruchamiania w klastrze. On wysyła do serwera interfejsu API rozwiązania Kubernetes, uzyskać informacji na temat wdrożeń i zwraca zestaw elementów sugerowane ulepszenia.

> [!NOTE]
> Narzędzia klastra kubernetes w usłudze advisor jest obsługiwana przez firmę Microsoft na zasadzie największej staranności. Problemy i sugestie powinny być zgłaszane w witrynie GitHub.

## <a name="running-kube-advisor"></a>Uruchamianie klastra kubernetes w usłudze advisor

Aby uruchomić narzędzie w klastrze, który jest skonfigurowany dla [kontroli dostępu opartej na rolach (RBAC)](aad-integration.md), używając następujących poleceń. Pierwsze polecenie tworzy konto usługi Kubernetes. Drugie polecenie uruchamia narzędzie w zasobniku przy użyciu tego konta usługi i konfiguruje zasobnika do usunięcia, po jej zakończenia. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Jeśli nie używasz funkcji RBAC, można w następujący sposób Uruchom polecenie:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

W ciągu kilku sekund powinna być widoczna tabelę opisującą potencjalne ulepszenia do wdrożenia.

![Dane wyjściowe klastra kubernetes w usłudze advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Sprawdza, czy wykonano

Narzędzie sprawdza poprawność kilka Kubernetes najlepszych rozwiązań, każdy z własnych sugerowane rozwiązanie problemu.

### <a name="resource-requests-and-limits"></a>Żądania zasobów i limity

Rozwiązanie Kubernetes obsługuje definiowanie [zasobów żądania i ogranicza na specyfikacji zasobnika][kube-cpumem]. Żądanie definiuje minimalny Procesora i pamięci wymaganej do uruchamiania kontenera. Limit definiuje maksymalną Procesora i pamięci, które mają być dozwolone.

Domyślnie nie żądania lub granice są ustawione na specyfikacje zasobników. Może to prowadzić do węzłów, które są overscheduled i kontenery są przetrzymywany. Narzędzia klastra kubernetes w usłudze advisor wyróżnia zasobników bez żądania i ogranicza zestaw.

## <a name="cleaning-up"></a>Czyszczenie

Jeśli klaster ma włączone RBAC, można oczyścić `ClusterRoleBinding` po uruchomieniu narzędzia, za pomocą następującego polecenia:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Jeśli używasz narzędzia względem klastra, który nie jest włączone RBAC, żadne oczyszczanie jest wymagana.

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z usługą Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor