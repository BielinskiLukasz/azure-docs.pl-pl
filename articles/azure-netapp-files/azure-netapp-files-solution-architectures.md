---
title: Architektury rozwiązań używające Azure NetApp Files | Microsoft Docs
description: Zawiera informacje o najlepszych rozwiązaniach dotyczących architektur rozwiązań przy użyciu Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: b-juche
ms.openlocfilehash: 9d39a6d85f878b6d67ffbcc55f468ad6984eecab
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260812"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architektury rozwiązań używające usługi Azure NetApp Files
Ten artykuł zawiera informacje o najlepszych rozwiązaniach, które mogą pomóc w zrozumieniu architektur rozwiązań na potrzeby używania Azure NetApp Files.  

Na poniższym diagramie przedstawiono podsumowanie kategorii architektury rozwiązań, które Azure NetApp Files oferuje:

![Kategorie architektury rozwiązania](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplikacje OSS systemu Linux i rozwiązania baz danych

Ta sekcja zawiera odwołania do rozwiązań dla aplikacji OSS systemu Linux i baz danych. 

### <a name="oracle"></a>Oracle

* [Wskazówki dotyczące najlepszych rozwiązań firmy Oracle na platformie Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure: opcje konfiguracji magazynu udostępnionego](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Korzyści związane z korzystaniem z usługi Azure NetApp Files w bazie danych Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Aplikacje systemu Windows i rozwiązania SQL Server

Ta sekcja zawiera odwołania do aplikacji systemu Windows i rozwiązań SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Udostępnianie plików i globalne buforowanie plików

* [Wdrożenie Talon/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Wdrażanie SQL Server za pośrednictwem protokołu SMB z Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Wdróż zawsze włączone grupy dostępności przy użyciu Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Rozwiązania SAP na platformie Azure

Ta sekcja zawiera odwołania do rozwiązań SAP w systemie Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Ogólne SAP i SAP NetWeaver 

* [Aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux for SAP — Przewodnik dotyczący wiele identyfikatorów SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Społeczność techniczna SAP i wpisy w blogu 

* [Azure NetApp Files — SAP HANA kopii zapasowej w ciągu kilku sekund](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files — przywracanie bazy danych HANA z kopii zapasowej migawki](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files — SAP HANA odciążania kopii zapasowej za pomocą synchronizacji z chmurą](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Przyspiesz kopiowanie systemu SAP HANA przy użyciu Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Woluminy w chmurze ONTAP i Azure NetApp Files: łatwa migracja systemu SAP HANA](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Rozwiązania infrastruktury pulpitów wirtualnych

Ta sekcja zawiera odwołania do rozwiązań infrastruktury pulpitu wirtualnego.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Opcje magazynu dla kontenerów profilów FSLogix w programie Virtual Desktop systemu Windows](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

## <a name="hpc-solutions"></a>Rozwiązania HPC

Ta sekcja zawiera odwołania do rozwiązań obliczeniowych o wysokiej wydajności (HPC). 

### <a name="generic-hpc"></a>Ogólne HPC

* [Azure NetApp Files: maksymalne wykorzystanie magazynu w chmurze](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Uruchamianie obciążeń MPI z użyciem Azure Batch i Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud środowiska HPC na Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Olej i gaz

* [Obliczenia o wysokiej wydajności (HPC): ropa naftowa i gaz na platformie Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Uruchamianie oprogramowania do symulacji zbiorników na platformie Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatyzacja projektowania elektronicznego (EDA)

* [Zalety korzystania z usługi Azure NetApp Files na potrzeby automatyzacji projektowania elektroniki](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [CycleCloud Azure: EDA Lab z Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analiza

* [Azure NetApp Files: nowy współużytkowany system plików do użycia z siatką SAS na Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Rozwiązania usług platformy Azure

Ta sekcja zawiera rozwiązania dla usług platformy Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Usługi Azure Kubernetes Services i Kubernetes

* [Integracja Azure NetApp Files z usługą Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Nieaktualna Kubernetes wydajność na platformie Azure dzięki Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident-Storage Orchestrator for Containers](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Uruchamianie obciążeń MPI z użyciem Azure Batch i Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 