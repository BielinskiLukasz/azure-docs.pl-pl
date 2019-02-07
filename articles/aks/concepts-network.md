---
title: Pojęcia — sieć w usługach Azure Kubernetes (AKS)
description: Informacje dotyczące sieci w usłudze Azure Kubernetes Service (AKS), m.in. wtyczki kubenet i wtyczki Azure CNI sieci kontrolery transferu danych przychodzących, moduły równoważenia obciążenia i statycznych adresów IP.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: b2fc4b518ee0857014c59b84b89a0102b86f687a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820134"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Koncepcji sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)

W ramach podejścia do tworzenia aplikacji opartych na kontenerach mikrousług składniki aplikacji muszą współpracować do przetwarzania swoich zadań. Usługa Kubernetes zapewnia różnych zasobów, które umożliwiają komunikację z aplikacjami. Można nawiązać połączenie i udostępnianie aplikacji wewnętrznie lub zewnętrznie. Aby tworzyć aplikacje o wysokiej dostępności, można załadować saldo aplikacji. Bardziej złożone aplikacje mogą wymagać konfiguracji ruch przychodzący na kończenie żądań SSL/TLS lub routingu z wielu składników. Ze względów bezpieczeństwa może być również konieczne ograniczanie przepływu ruchu sieciowego do lub między zasobników i węzłów.

W tym artykule przedstawiono podstawowe pojęcia, które zapewniają sieci do aplikacji w usłudze AKS:

- [Usługi](#services)
- [Sieci wirtualne platformy Azure](#azure-virtual-networks)
- [Ruch przychodzący kontrolerów](#ingress-controllers)
- Zasady sieciowe

## <a name="kubernetes-basics"></a>Podstawy platformy Kubernetes

Aby zezwolić na dostęp do aplikacji lub dla składników aplikacji do komunikowania się ze sobą, usługa Kubernetes zapewnia warstwę abstrakcji sieci wirtualnej. Węzły rozwiązania Kubernetes są podłączone do sieci wirtualnej i może zapewnić łączność ruchu przychodzącego i wychodzącego do zasobników. *Serwera proxy klastra kubernetes w usłudze* składnika, który jest uruchamiany w każdym węźle, aby zapewnić, te funkcje sieciowego.

W usłudze Kubernetes *usług* pogrupowania zasobników, aby zezwolić na potrzeby bezpośredniego dostępu za pośrednictwem adresu IP lub nazwę DNS i na określonym porcie. Możesz też dystrybuować ruch przy użyciu *moduł równoważenia obciążenia*. Bardziej złożonym routingiem ruchu aplikacji, również można osiągnąć za pomocą *kontrolerów ruch przychodzący*. Zabezpieczenia i filtrowanie ruchu sieciowego do zasobników jest możliwe przy użyciu rozwiązania Kubernetes *zasad sieciowych*.

Platforma Azure pomaga uprościć sieci wirtualnych w przypadku klastrów usługi AKS. Podczas tworzenia modułu równoważenia obciążenia rozwiązania Kubernetes bazowego zasobu modułu równoważenia obciążenia platformy Azure zostało utworzone i skonfigurowane. Podczas otwierania portów sieciowych w celu zasobników skonfigurowano odpowiednie zasady grupy zabezpieczeń sieci platformy Azure. Routing aplikacji protokołu HTTP, aby uzyskać Azure można również skonfigurować *zewnętrznego serwera DNS* jako nowy ruch przychodzący trasy są skonfigurowane.

## <a name="services"></a>Usługi

Aby uprościć konfigurację sieci w przypadku obciążeń aplikacji, korzysta z rozwiązania Kubernetes *usług* logicznie pogrupować zestaw zasobników i zapewniają łączność sieciową. Dostępne są następujące typy usługi:

- **Adres IP klastra** — tworzy wewnętrznego adresu IP do użycia w klastrze AKS. Dobre dla wewnętrznych aplikacji, które obsługują innych obciążeń w klastrze.

    ![Diagram przedstawiający przepływ ruchu IP klastra w klastrze AKS][aks-clusterip]

- **NodePort** — tworzy mapowanie portów na podstawowym węźle, który umożliwia aplikacji są dostępne bezpośrednio z adresu IP węzła i portu.

    ![Diagram przedstawiający przepływ ruchu NodePort w klastrze AKS][aks-nodeport]

- **Moduł równoważenia obciążenia** — tworzy zasób modułu równoważenia obciążenia platformy Azure, konfiguruje zewnętrzny adres IP, a żądane zasobników nawiązanie połączenia z puli zaplecza modułu równoważenia obciążenia. Aby zezwolić na ruch klientów zasięg aplikacji, reguły równoważenia obciążenia są tworzone na żądany ruch na portach. 

    ![Diagram przedstawiający przepływ ruchu modułu równoważenia obciążenia w klastrze AKS][aks-loadbalancer]

    Dodatkową kontrolę i routingu ruchu przychodzącego, możesz zamiast tego użyć [kontrolera danych przychodzących](#ingress-controllers).

- **ExternalName** — tworzy określonego wpisu DNS w celu ułatwienia dostępu w aplikacji.

Adres IP dla usługi równoważenia obciążenia i usługi mogą być przypisywane dynamicznie, lub można określić istniejący statyczny adres IP do użycia. Wewnętrzne i zewnętrzne statyczne adresy IP można przypisać. Ten istniejący statyczny adres IP jest często powiązane wpis DNS.

Zarówno *wewnętrzny* i *zewnętrznych* modułów równoważenia obciążenia mogą być tworzone. Wewnętrzne moduły równoważenia obciążenia są przypisywane tylko prywatny adres IP, dzięki czemu nie jest dostępny z Internetu.

## <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

W usłudze AKS można wdrożyć klaster, który korzysta z jednego z następujących modeli dwóch sieci:

- *Wtyczki Kubenet* sieci - sieci zasobów są zazwyczaj tworzone i konfigurowane jako klaster AKS jest wdrażany.
- *Wtyczki Azure Container Networking interfejsu (CNI)* sieci — klaster AKS jest podłączony do istniejących zasobów sieci wirtualnej i konfiguracji.

### <a name="kubenet-basic-networking"></a>Sieć z wtyczki Kubenet (basic)

*Wtyczki kubenet* sieci — opcja jest domyślnie skonfigurowany do tworzenia klastra AKS. Za pomocą *wtyczki kubenet*, węzły Uzyskaj adres IP z podsieci sieci wirtualnej platformy Azure. Zasobników otrzymują adres IP z logicznie różnymi przestrzeniami adresowymi w podsieci sieci wirtualnej platformy Azure z węzłów. Translator adresów sieciowych (NAT) jest następnie konfigurowana, tak aby zasobników może dotrzeć do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu jest translatora adresów Sieciowych do podstawowego adresu IP węzła zajmie się.

Węzły używają [wtyczki kubenet] [ kubenet] wtyczka platformy Kubernetes. Można pozwolić, aby utworzyć i konfigurowanie sieci wirtualnych dla Ciebie lub wybrać wdrożenie klastra usługi AKS na istniejącą podsieć sieci wirtualnej platformy Azure. Ponownie tylko węzły odbierać routingowi adresu IP i zasobników umożliwia komunikację z innymi zasobami poza klastrem AKS translatora adresów Sieciowych. To podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeń sieci dla zasobników do użycia.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci dla klastra usługi AKS wtyczki kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sieć Azure CNI (zaawansowane)

Za pomocą wtyczki Azure CNI pod każdym pobiera adres IP z podsieci i są dostępne bezpośrednio. Te adresy IP musi być unikatowa w przestrzeń sieci i muszą być planowane z góry. Każdy węzeł ma parametr konfiguracji maksymalna liczba zasobników, które obsługuje. Równoważną liczbę adresów IP w każdym węźle następnie są zarezerwowane na początku dla tego węzła. Takie podejście wymaga więcej planowania i często prowadzi do wyczerpania adresu IP lub jest potrzebna ponowna kompilacja klastrów w większej podsieci, w miarę wzrostu wymagań aplikacji.

Węzły używają [wtyczki Azure Container Networking interfejsu (CNI)] [ cni-networking] wtyczka platformy Kubernetes.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure][advanced-networking-diagram]

Wtyczki Azure CNI udostępnia następujące funkcje w porównaniu z wtyczki kubenet sieci:

- Każdy zasobnik w klastrze jest przypisany adres IP w sieci wirtualnej. Zasobników mogą bezpośrednio komunikować się z innymi zasobników w klastrze i innych węzłów w sieci wirtualnej.
- Zasobników w podsieci, które mają włączone punkty końcowe usługi można bezpiecznie łączyć się z usługami platformy Azure, takich jak Azure Storage i bazą danych SQL.
- Można utworzyć trasy zdefiniowane przez użytkownika (UDR) do kierowania ruchu z zasobników do wirtualnego urządzenia sieciowego.

Aby uzyskać więcej informacji, zobacz [wtyczki Azure CNI skonfigurować dla klastra usługi AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Ruch przychodzący kontrolerów

Podczas tworzenia usługi typu usługi równoważenia obciążenia jest tworzony bazowego zasobu modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na danym porcie. Moduł równoważenia obciążenia działa tylko w warstwie 4 - usługa nie rozpoznaje rzeczywiste aplikacje i nie może wprowadzać żadnych dodatkowych kwestii dotyczących routingu.

*Ruch przychodzący kontrolerów* działa w warstwie 7, a następnie użyć bardziej inteligentne i reguł w celu dystrybucji ruchu aplikacji. Zazwyczaj kontrolera danych przychodzących jest używane do kierowania ruchu HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

W usłudze AKS możesz utworzyć zasób usługi transferu danych przychodzących przy użyciu polecenia podobnego NGINX lub użyć funkcji routing aplikacji protokołu HTTP w usłudze AKS. Po włączeniu routing aplikacji protokołu HTTP dla klastra usługi AKS, platforma Azure tworzy kontroler danych przychodzących i *DNS zewnętrznego* kontrolera. Podczas tworzenia nowych zasobów ruch przychodzący w usłudze Kubernetes wymagane rekordy A systemu DNS są tworzone w strefie DNS specyficzne dla klastra. Aby uzyskać więcej informacji, zobacz [wdrożenia, routing aplikacji protokołu HTTP][aks-http-routing].

Kolejną funkcją typowych danych przychodzących jest kończenia żądań SSL/TLS. Dla aplikacji sieci web dużych dostępne za pośrednictwem protokołu HTTPS kończenie żądań protokołu TLS mogą być obsługiwane przez zasób danych przychodzących, a nie w samej aplikacji. Aby zapewnić automatyczne generowanie certyfikacji TLS i konfiguracji, można skonfigurować na korzystanie z dostawców takich jak umożliwia szyfrowanie zasobu transferu danych przychodzących. Aby uzyskać więcej informacji na temat konfigurowania kontrolera danych przychodzących NGINX za pomocą umożliwia szyfrowanie, zobacz [ruch przychodzący i protokół TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Zabezpieczenia grupy Filtr ruchu sieciowego dla maszyn wirtualnych, takich jak węzłów AKS. Podczas tworzenia usług, takich jak usługi równoważenia obciążenia, platforma Azure automatycznie skonfiguruje wszystkie reguły sieciowej grupy zabezpieczeń, które są potrzebne. Nie należy ręcznie konfigurować reguły sieciowej grupy zabezpieczeń do filtrowania ruchu do zasobników w klastrze AKS. Zdefiniuj wszystkie wymagane porty i przekazywanie jako część Twojego manifestów usługi Kubernetes i pozwolić platformie Azure, Utwórz lub zaktualizuj odpowiednie zasady.

Domyślną sieciową grupę zabezpieczeń, które istnieją reguły dla ruchu, takich jak SSH. Te reguły domyślne mają zastosowanie do zarządzania klastrem i rozwiązywaniu problemów z dostępem. Usunięcie tych reguł domyślnych może spowodować problemy z zarządzaniem AKS i przerywa cel poziomu usług (SLO).

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą AKS, sieć, tworzenie i konfigurowanie klastra usługi AKS przy użyciu własnych zakresów adresów IP przy użyciu [wtyczki kubenet] [ aks-configure-kubenet-networking] lub [wtyczki Azure CNI] [ aks-configure-advanced-networking].

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes dostępu w usłudze AKS i zarządzania tożsamościami][aks-concepts-identity]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
