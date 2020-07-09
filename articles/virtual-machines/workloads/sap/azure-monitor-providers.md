---
title: Azure Monitor dla dostawców rozwiązań SAP | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure monitor dla rozwiązań SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a7e44046de3eccab83e8315e6adea150a146e660
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964196"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Usługa Azure monitor dla dostawców rozwiązań SAP (wersja zapoznawcza)

## <a name="overview"></a>Omówienie  

W kontekście Azure Monitor dla rozwiązań SAP *Typ dostawcy* odnosi się do określonego *dostawcy*. Na przykład *SAP HANA*, który jest skonfigurowany dla określonego składnika w oprogramowaniu SAP, takich jak SAP HANA Database. Dostawca zawiera informacje o połączeniu dla odpowiedniego składnika i pomaga zbierać dane telemetryczne z tego składnika. Jeden Azure Monitor dla zasobu rozwiązań SAP (znany również jako zasób monitora SAP) można skonfigurować przy użyciu wielu dostawców tego samego typu dostawcy lub wielu dostawców z wieloma typami dostawcy.
   
Klienci mogą konfigurować różne typy dostawców, aby umożliwić zbieranie danych z odpowiedniego składnika w systemie SAP. Na przykład klienci mogą skonfigurować jednego dostawcę dla typu dostawcy SAP HANA, innego dostawcę dla typu dostawcy klastrów o wysokiej dostępności i tak dalej.  

Klienci mogą również skonfigurować wielu dostawców określonego typu dostawcy, aby ponownie używać tego samego zasobu monitora SAP i skojarzonej grupy zarządzanej. Więcej informacji na temat zarządzanej grupy zasobów. W publicznej wersji zapoznawczej obsługiwane są następujące typy dostawców:   
- SAP HANA
- Klaster o wysokiej dostępności
- Microsoft SQL Server

![Azure Monitor dla dostawców rozwiązań SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Klienci są zalecani do skonfigurowania co najmniej jednego dostawcy spośród dostępnych typów dostawców w momencie wdrażania zasobu monitorowania SAP. Konfigurując dostawcę, klienci inicjują zbieranie danych z odpowiedniego składnika, dla którego skonfigurowano dostawcę.   

Jeśli klienci nie skonfigurują żadnych dostawców w czasie wdrażania zasobu monitora SAP, mimo że zasób monitora SAP zostanie pomyślnie wdrożony, nie będą zbierane żadne dane telemetryczne. Klienci mają możliwość dodawania dostawców po wdrożeniu za pomocą zasobu monitorowania SAP w ramach Azure Portal. Klienci mogą dodawać lub usuwać dostawców z zasobu monitorowania SAP w dowolnym momencie.

> [!Tip]
> Jeśli chcesz, aby firma Microsoft zaimplementował określonego dostawcę, wystaw opinię za pomocą linku na końcu tego dokumentu lub skontaktuj się z zespołem ds. klientów.  

## <a name="provider-type-sap-hana"></a>Typ dostawcy SAP HANA

Klienci mogą skonfigurować jednego lub więcej dostawców typu dostawcy *SAP HANA* , aby umożliwić zbieranie danych z bazy danych SAP HANA Database. Dostawca SAP HANA nawiązuje połączenie z bazą danych SAP HANA za pośrednictwem portu SQL, pobiera dane telemetryczne z bazy danych i wypycha je do obszaru roboczego Log Analytics w ramach subskrypcji klienta. Dostawca SAP HANA zbiera dane co 1 minutę z bazy danych SAP HANA.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że następujące dane są dostępne dla SAP HANA dostawcy: podstawowego wykorzystania infrastruktury, SAP HANA stanu hosta, SAP HANA replikacji systemu i SAP HANA kopii zapasowych danych telemetrycznych. Aby skonfigurować dostawcę SAP HANA, adres IP hosta, numer portu SQL HANA i SYSTEMDB nazwę użytkownika i hasło są wymagane. Klienci są zalecani do konfigurowania dostawcy SAP HANA w SYSTEMDB, ale można skonfigurować dodatkowych dostawców dla innych dzierżawców bazy danych.

![Azure Monitor dla dostawców rozwiązań SAP — SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Klaster o wysokiej dostępności typu dostawcy
Klienci mogą skonfigurować co najmniej jednego dostawcę typu dostawcy *o wysokiej dostępności* , aby umożliwić zbieranie danych z klastra Pacemaker w poziomie SAP. Dostawca klastrów o wysokiej dostępności nawiązuje połączenie z usługą Pacemaker przy użyciu punktu końcowego [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) , ściąga dane telemetryczne z bazy danych i wypycha je do log Analytics obszaru roboczego w ramach subskrypcji klienta. Dostawca klastrów o wysokiej dostępności zbiera dane co 60 sekund od Pacemaker.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że zobaczysz następujące dane z dostawcą klastrów o wysokiej dostępności:   
 - Stan klastra reprezentowany jako pakiet zbiorczy stanu węzła i zasobu 
 - [Notes](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor dla dostawców rozwiązań SAP — klaster o wysokiej dostępności](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Aby skonfigurować dostawcę klastrów o wysokiej dostępności, należy wykonać dwa podstawowe kroki: 
1. Zainstaluj [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) w *każdym* węźle w klastrze Pacemaker 
    - Klienci mogą używać skryptów Azure Automation do wdrożenia klastra o wysokiej dostępności. Skrypty zainstalują [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) na każdym węźle klastra.  
    - Klienci mogą przeprowadzić instalację ręczną, wykonując czynności opisane na [tej stronie](https://github.com/ClusterLabs/ha_cluster_exporter) 
2. Konfigurowanie dostawcy klastrów o wysokiej dostępności w *każdym* węźle w klastrze Pacemaker  
  Aby można było skonfigurować dostawcę klastrów o wysokiej dostępności, wymagany jest adres URL Prometheus, nazwa klastra, nazwa hosta i identyfikator systemu.   
  Klienci są zalecani do skonfigurowania jednego dostawcy dla każdego węzła klastra.   

## <a name="provider-type-microsoft-sql-server"></a>Typ dostawcy programu Microsoft SQL Server

Klienci mogą skonfigurować jednego lub więcej dostawców typu dostawcy *Microsoft SQL Server* , aby umożliwić zbieranie danych z [program SQL Server w usłudze Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Dostawca SQL Server nawiązuje połączenie z Microsoft SQL Server za pośrednictwem portu SQL, pobiera dane telemetryczne z bazy danych i wypycha je do obszaru roboczego Log Analytics w ramach subskrypcji klienta. Należy utworzyć SQL Server na potrzeby uwierzytelniania SQL i logowania SQL Server z użyciem usługi SAP DB jako domyślnej bazy danych dla dostawcy. Dostawca SQL Server zbiera dane od co 60 sekund do co godzinę od programu SQL Server.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że następujące dane są dostępne w ramach dostawcy SQL Server: bazowego wykorzystania infrastruktury, najważniejszych instrukcji SQL, najwyższej największej tabeli, problemów zarejestrowanych w dziennikach błędów SQL Server, blokowania procesów i innych.  

Aby skonfigurować dostawcę Microsoft SQL Server, wymagany jest identyfikator systemu SAP, adres IP hosta, SQL Server numer portu, a także SQL Server nazwę logowania i hasło.

![Azure Monitor dla dostawców rozwiązań SAP — SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy Azure Monitor dla zasobu rozwiązań SAP.
- Masz pytania dotyczące Azure Monitor dla rozwiązań SAP? Zapoznaj się z sekcją [często zadawane pytania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/azure-monitor-faq)
