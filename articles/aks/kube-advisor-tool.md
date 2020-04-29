---
title: Sprawdź wdrożenia pod kątem najlepszych rozwiązań
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak sprawdzić implementację najlepszych rozwiązań w Twoich wdrożeniach w usłudze Azure Kubernetes za pomocą usługi polecenia-Advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 17e21c142dc354de7b72bc17396b19366027c5cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668401"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Sprawdzanie najlepszych rozwiązań dotyczących platformy Kubernetes w klastrze

Istnieje kilka najlepszych rozwiązań, które należy wykonać w ramach wdrożeń usługi Kubernetes w celu zapewnienia najlepszej wydajności i odporności aplikacji. Aby wyszukać wdrożenia, które nie są następujące, można użyć narzędzia polecenia-Advisor.

## <a name="about-kube-advisor"></a>Informacje o polecenia-Advisor

[Narzędzie polecenia-Advisor][kube-advisor-github] to pojedynczy kontener przeznaczony do uruchamiania w klastrze. Wysyła zapytanie do serwera interfejsu API Kubernetes o informacje o wdrożeniach i zwraca zestaw sugerowanych ulepszeń.

Narzędzie polecenia-Advisor może raportować żądania zasobów i limitów braku w PodSpecs dla aplikacji systemu Windows, a także aplikacji z systemem Linux, ale narzędzie Advisor polecenia musi zostać zaplanowane w systemie Linux pod. Można zaplanować uruchomienie w puli węzłów z określonym systemem operacyjnym przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji pod.

> [!NOTE]
> Narzędzie polecenia-Advisor jest obsługiwane przez firmę Microsoft na podstawie najlepszego wysiłku. Problemy i sugestie należy zgłosić w serwisie GitHub.

## <a name="running-kube-advisor"></a>Uruchamianie polecenia-Advisor

Aby uruchomić narzędzie w klastrze skonfigurowanym na potrzeby [kontroli dostępu opartej na rolach (RBAC)](azure-ad-integration.md), użyj następujących poleceń. Pierwsze polecenie tworzy konto usługi Kubernetes. Drugie polecenie uruchamia narzędzie w składniku pod przy użyciu tego konta usługi i konfiguruje go pod kątem usuwania po zakończeniu. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Jeśli nie korzystasz z funkcji RBAC, możesz uruchomić polecenie w następujący sposób:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

W ciągu kilku sekund powinna zostać wyświetlona tabela opisująca potencjalne ulepszenia wdrożeń.

![Polecenia — dane wyjściowe usługi Advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Testy wykonane

Narzędzie sprawdza poprawność kilku najlepszych rozwiązań Kubernetes, z których każdy ma swoje proponowane korygowanie.

### <a name="resource-requests-and-limits"></a>Żądania i limity dotyczące zasobów

Kubernetes obsługuje definiowanie [żądań zasobów i limitów na temat specyfikacji pod][kube-cpumem]. Żądanie definiuje minimalny procesor i pamięć wymaganą do uruchomienia kontenera. Limit określa maksymalną liczbę procesorów i pamięci, które powinny być dozwolone.

Domyślnie żadne żądania ani limity nie są ustawione dla specyfikacji pod. Może to prowadzić do zaplanowanych węzłów i Starved kontenerów. Narzędzie polecenia-Advisor podświetla informacje o zbiorach bez żądań i limitów.

## <a name="cleaning-up"></a>Czyszczenie

Jeśli klaster ma włączoną funkcję RBAC, możesz wyczyścić działanie `ClusterRoleBinding` po uruchomieniu narzędzia przy użyciu następującego polecenia:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Jeśli uruchamiasz narzędzie w klastrze, który nie obsługuje kontroli RBAC, nie jest wymagane żadne czyszczenie.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors