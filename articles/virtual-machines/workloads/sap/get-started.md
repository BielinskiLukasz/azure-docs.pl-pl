---
title: Wprowadzenie do rozwiązania SAP na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozwiązaniach SAP uruchamianych na maszynach wirtualnych (VM) w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fbb09d41535bf811a42b204f4e3d38b87c2846c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287952"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Korzystanie z platformy Azure do hostowania i uruchamiania scenariusze obciążeń SAP

Dzięki systemowi Azure firmy Microsoft jako partnerem chmurze SAP, jesteś w stanie uruchamiać scenariuszy i obciążeń o znaczeniu krytycznym SAP na platformie skalowalne, zgodne i sprawdzonym działaniu w przedsiębiorstwach.  Korzystaj ze skalowalności, elastyczności i oszczędności platformy Azure. Dzięki rozszerzonemu partnerstwu między firmami Microsoft i SAP umożliwia uruchamianie aplikacji SAP w środowiskach deweloperskich lub testowych oraz produkcyjnych na platformie Azure — i być w pełni obsługiwane. Od oprogramowania SAP NetWeaver SAP S4/HANA, SAP BI, systemu Linux Windows, SQL oprogramowania SAP HANA mamy potrzebne.

Oprócz hostowania oprogramowania SAP NetWeaver scenariuszy obejmujących różne systemy DBMS na platformie Azure, możesz hostować różne inne scenariusze obciążeń SAP, takich jak SAP BI na platformie Azure. Dokumentacja dotyczące wdrożeń oprogramowania SAP NetWeaver na maszynach wirtualnych Azure natywnych znajduje się w sekcji "Oprogramowanie SAP NetWeaver na maszynach wirtualnych platformy Azure".

Unikatowość platformy Azure dla oprogramowania SAP HANA jest unikatowy oferta, która ustawia Azure oprócz konkurencji. Aby włączyć hostowanie więcej pamięci i zasobów procesora CPU w wymagających scenariuszach SAP obejmujących platformy SAP HANA, platforma Azure oferuje użycie klienta dedykowanego sprzętu bez systemu operacyjnego na potrzeby uruchamiania wdrożenia oprogramowania SAP HANA, które wymagają do 20 TB (60 TB skalowalnego w poziomie) pamięci dla S/4HANA lub innych obciążeń SAP HANA. To unikatowe rozwiązanie platformy Azure środowiska SAP Hana na platformie Azure (duże wystąpienia) umożliwia uruchamianie oprogramowania SAP HANA na dedykowanym sprzęcie bez systemu operacyjnego w warstwie aplikacji SAP lub warstwy pośredniczącym obciążenia hostowane natywnych maszynach wirtualnych platformy Azure. To rozwiązanie jest udokumentowany w różnych dokumentach w sekcji "Platformy SAP HANA na platformie Azure (duże wystąpienia)."   

Wymagania dotyczące integracji tożsamości i logowanie jednokrotne przy użyciu usługi Azure Active Directory do różnych składników SAP i SAP SaaS również hostingu scenariusze obciążeń SAP na platformie Azure można utworzyć lub PaaS oferuje. Listę takich integracji i scenariusze logowanie jednokrotne z jednostkami usługi Azure Active Directory (AAD) i SAP jest opisane i opisano w sekcji "Integracja tożsamości usługi AAD SAP oraz logowanie jednokrotne."

## <a name="latest-changes"></a>Najnowsze zmiany

Wyjaśnienia dotyczące użycia i konfigurację [wirtualne urządzenia sieciowe Azure](https://azure.microsoft.com/solutions/network-appliances/) w ścieżkach komunikacji krytycznej opóźnienie systemów SAP na:

- [Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)

Dokumentacja wokół SAP HANA dynamiczna obsługa warstw na maszynach wirtualnych platformy Azure

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#sap-hana-dynamic-tiering-20-for-azure-virtual-machines)

Dokumentacja wokół oprogramowania SAP HANA skalowalnego w poziomie na M128s maszyny Wirtualnej platformy Azure masz dodane do:

- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)
- [SAP HANA dostępności w obrębie jednego regionu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Oprogramowanie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia)

Seria dokumentacji prowadzi za pośrednictwem platformy SAP HANA na platformie Azure (duże wystąpienia) lub w krótkich dużych wystąpień HANA. Dokumenty przekazywać uwzględnione na liście obszarów dużych wystąpień HANA:

- [Omówienie środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura i łączność do platformy SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalowanie platformy SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Wysoka dostępność i odzyskiwanie po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Rozwiązywanie problemów i monitorowania środowiska SAP Hana na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Następne kroki:

- Odczyt [omówienie i architektura oprogramowania SAP HANA na platformie Azure (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Oprogramowanie SAP HANA na maszynach wirtualnych platformy Azure
Ten rozdział dokumentacji opisano różne aspekty platformy SAP HANA. Jako warunek wstępny należy zapoznać się z nazwy głównej usługi platformy Azure, świadczące podstawowe usługi w usłudze IaaS platformy Azure, więc przede wszystkim wiedzę na temat obliczeń platformy Azure, magazynu i sieci. Wiele z tych tematów są obsługiwane w oprogramowanie SAP NetWeaver powiązane [Azure Planning Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Tej listy artykułów i ich subarticles składa się z dokumentacją specyficzną dla platformy HANA na platformie Azure:

- [Szybki Start: Instalacja ręczna jednego wystąpienia środowiska SAP Hana na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA dostępności w obrębie jednego regionu platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostępność platformy SAP HANA w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Wysoka dostępność środowiska SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA usługi Azure Backup na poziomie plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Oprogramowanie SAP NetWeaver na maszynach wirtualnych Azure wdrożone
W tej sekcji możesz znaleźć dokumentację planowania i wdrażania oprogramowania SAP NetWeaver i Business One na platformie Azure. Dokumentacja w tym rozdziale koncentruje się głównie wokół z podstawowymi informacjami i użycie baz danych bez HANA obciążenia SAP na platformie Azure. Dokumenty i artykuły dotyczące wysokiej dostępności są podstawą dla platformy HANA wysoką dostępność na platformie Azure oraz. Lista artykułów, takich jak:

- [SAP Business One na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Wdrażanie oprogramowania SAP IDES EHP7 SP3 dla oprogramowania SAP ERP 6.0 na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Uruchamianie środowiska SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines, planowania i implementacji środowiska SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Wdrażania maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrona wdrożenia obejmujące wiele warstw aplikacji SAP NetWeaver przy użyciu usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Łącznik SAP LaMa dla platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Dotyczące bazy danych bez HANA obciążenia SAP na platformie azure, takich jak listy dokumentów:

- [Zagadnienia dotyczące wdrażania systemu DBMS na maszynach wirtualnych platformy Azure w przypadku obciążeń SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Wdrożenie programu SQL Server Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM DB2 dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, liveCache i serwer zawartości wdrożenia na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Baz danych SAP HANA na platformie Azure można znaleźć w sekcji SAP HANA na maszynach wirtualnych platformy Azure.

Aby zapewnić wysoką dostępność obciążeń SAP na platformie Azure dokument wejścia jest:

- [Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Dokument wpis wskazuje różnych innych dokumentów architektury i scenariusza. W scenariuszu kolejnych dokumentów znajdują się linki do szczegółowych dokumentów technicznych wyjaśniające, wdrażania i konfigurowania metody różnych wysokiej dostępności. Różne dokumenty z tworzeniem i skonfigurowanie wysokiej dostępności dla obciążeń SAP NetWeaver są obejmujące systemu Linux, a także Windows, systemów operacyjnych.


Aby uzyskać integrację usługi Azure Active Directory (AAD) i usług SAP i logowanie jednokrotne, wyświetlanie listy dokumentów, takich jak:

- [Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SAP Cloud klienta](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory za pomocą uwierzytelniania tożsamości platformy SAP w chmurze](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z platformy SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Samouczek: Integracja usługi Azure Active Directory z platformą SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Środowiska oprogramowania S/4HANA — Fiori Launchpad SAML logowania jednokrotnego z usługą Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Do integracji usług platformy Azure na składniki SAP listy dokumentów wygląda następująco:

- [Używanie oprogramowania SAP HANA w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [Tryb DirectQuery i oprogramowanie SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Używanie łącznika SAP BW Connector w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Usługa Azure Data Factory oferuje integrację danych oprogramowania SAP HANA i Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




