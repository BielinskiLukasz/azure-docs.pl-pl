---
title: Bezpieczny dostęp do konta Azure Cosmos DB przy użyciu punktu końcowego usługi Azure Virtual Network
description: W tym dokumencie opisano kontrolę dostępu do sieci wirtualnej i podsieci dla konta usługi Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 791821fbfe5854c27b7e3e6927a56a66ac1f1dc2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819087"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Dostęp do Azure Cosmos DB z sieci wirtualnych (VNet)

Konto usługi Azure Cosmos można skonfigurować tak, aby zezwalało na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Przez włączenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w celu uzyskania dostępu do Azure Cosmos DB w podsieci w sieci wirtualnej, ruch z tej podsieci jest wysyłany do Azure Cosmos dB przy użyciu tożsamości podsieci i Virtual Network. Po włączeniu punktu końcowego usługi Azure Cosmos DB można ograniczyć dostęp do podsieci, dodając ją do konta usługi Azure Cosmos.

Domyślnie konto usługi Azure Cosmos jest dostępne z dowolnego źródła, jeśli do żądania dołączono prawidłowy token autoryzacji. Po dodaniu co najmniej jednej podsieci w programie sieci wirtualnych tylko żądania pochodzące z tych podsieci będą mieć prawidłową odpowiedź. Żądania pochodzące z dowolnego innego źródła otrzymają odpowiedź 403 (dostęp zabroniony). 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniżej przedstawiono kilka często zadawanych pytań dotyczących konfigurowania dostępu z sieci wirtualnych:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Czy można określić zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu IP na koncie usługi Azure Cosmos? 

Można włączyć zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu IP (Zapora) na koncie usługi Azure Cosmos. Te dwie funkcje uzupełniają się i wspólnie zapewniają izolację i bezpieczeństwo konta usługi Azure Cosmos. Użycie zapory IP gwarantuje, że statyczne adresy IP będą mogły uzyskiwać dostęp do Twojego konta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak mogę ograniczyć dostęp do podsieci w ramach sieci wirtualnej? 

Istnieją dwa kroki, które należy wykonać, aby ograniczyć dostęp do konta usługi Azure Cosmos z podsieci. Najpierw można zezwolić na ruch z podsieci, aby mógł przenieść swoją podsieć i tożsamość sieci wirtualnej do Azure Cosmos DB. W tym celu należy włączyć punkt końcowy usługi dla Azure Cosmos DB w podsieci. Następnie Dodaj regułę do konta usługi Azure Cosmos, określając tę podsieć jako źródło, z którego można uzyskać dostęp do konta.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Czy listy ACL sieci wirtualnych i zapory IP odrzucają żądania lub połączenia? 

Gdy są dodawane reguły dostępu zapory IP lub sieci wirtualnej, tylko żądania od dozwolonych źródeł uzyskują prawidłowe odpowiedzi. Inne żądania są odrzucane z 403 (niedozwolone). Ważne jest, aby rozróżnić zaporę konta usługi Azure Cosmos na podstawie zapory na poziomie połączenia. Źródło może nadal łączyć się z usługą, a połączenia same nie są odrzucane.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje żądania rozpoczęły się, gdy włączono punkt końcowy usługi do Azure Cosmos DB w podsieci. Co się stało?

Po włączeniu punktu końcowego usługi dla Azure Cosmos DB w podsieci źródło ruchu docierające do konta jest przełączane z publicznego adresu IP do sieci wirtualnej i podsieci. Jeśli konto usługi Azure Cosmos ma zaporę opartą na protokole IP, ruch z podsieci z obsługą usługi nie będzie już zgodny z regułami zapory IP i dlatego zostanie odrzucony. Wykonaj kroki, aby bezproblemowo migrować z zapory opartej na protokole IP do kontroli dostępu opartej na sieci wirtualnej.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Czy równorzędne sieci wirtualne mają również dostęp do konta usługi Azure Cosmos? 
Tylko sieć wirtualna i ich podsieci dodane do konta usługi Azure Cosmos mają dostęp. Ich Komunikacja równorzędna sieci wirtualnych nie może uzyskać dostępu do konta do momentu dodania do konta podsieci w ramach równorzędnych sieci wirtualnych.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaka jest maksymalna liczba podsieci, które mogą uzyskać dostęp do jednego konta Cosmos? 
Obecnie możesz mieć maksymalnie 64 podsieci dozwolonych dla konta usługi Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Czy mogę włączyć dostęp z sieci VPN i Express Route? 
Aby uzyskać dostęp do konta Azure Cosmos za pośrednictwem usługi Express Route z lokalnego, należy włączyć komunikację równorzędną firmy Microsoft. Po wprowadzeniu zapory IP lub reguł dostępu do sieci wirtualnej można dodać publiczne adresy IP używane do komunikacji równorzędnej firmy Microsoft na zaporze IP konta usługi Azure Cosmos, aby zezwolić na dostęp usług lokalnych do konta usługi Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Czy muszę zaktualizować reguły grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń)? 
Reguły sieciowej grupy zabezpieczeń są używane do ograniczania łączności do i z podsieci przy użyciu sieci wirtualnej. Gdy dodasz punkt końcowy usługi dla Azure Cosmos DB do podsieci, nie ma potrzeby otwierania łączności wychodzącej w sieciowej grupy zabezpieczeń dla konta usługi Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Czy punkty końcowe usługi są dostępne dla wszystkich sieci wirtualnych?
Nie, tylko Azure Resource Manager sieci wirtualnych może mieć włączony punkt końcowy usługi. Klasyczne sieci wirtualne nie obsługują punktów końcowych usługi.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Czy mogę "akceptować połączenia z poziomu publicznych centrów danych platformy Azure", gdy dostęp do punktu końcowego usługi jest włączony dla Azure Cosmos DB?  
Jest to wymagane tylko wtedy, gdy chcesz, aby konto Azure Cosmos DB było dostępne przez inne usługi platformy Azure, takie jak Azure Data Factory, Azure Wyszukiwanie poznawcze lub dowolna usługa wdrożona w danym regionie świadczenia usługi Azure.


## <a name="next-steps"></a>Następne kroki

* [Jak ograniczyć dostęp do kont usługi Azure Cosmos do podsieci w ramach sieci wirtualnych](how-to-configure-vnet-service-endpoint.md)
* [Jak skonfigurować zaporę IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)

