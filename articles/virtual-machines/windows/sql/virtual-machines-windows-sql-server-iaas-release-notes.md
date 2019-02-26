---
title: Program SQL Server na maszynie Wirtualnej platformy Azure — informacje o wersji | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat nowych funkcji i ulepszeń programu SQL Server na Maszynie wirtualnej platformy Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: d5c003b2588813f8e8a1dfc2923b9d4c8d8c6cc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820437"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Program SQL Server na maszynie wirtualnej platformy Azure — informacje o wersji

Platforma Azure umożliwia wdrażanie maszyny wirtualnej za pomocą obrazu programu SQL Server, wbudowane. Ten artykuł zawiera podsumowanie nowych funkcji i ulepszeń w najnowszych wersjach [programu SQL Server na maszynach wirtualnych Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Artykuł zawiera także listę znaczące zmiany zawartości, które nie są bezpośrednio do wersji mimo opublikowane w tym samym przedziale czasu. Ulepszenia do innych usług platformy Azure, można zobaczyć [usługi aktualizacji](https://azure.microsoft.com/updates)


## <a name="february-2019"></a>Lutego 2019 r

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
| **Ulepszenia w portalu** | Teraz jest możliwa zmiana modelu licencjonowania maszyny wirtualnej serwera SQL z rzeczywistym użyciem za pomocą bring-your-own-license [witryny Azure portal](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Uproszczenie wdrażania grupy dostępności przy użyciu interfejsu wiersza polecenia maszyny Wirtualnej SQL Azure** | Jest teraz łatwiejsze niż kiedykolwiek Aby wdrożyć do grupy dostępności na maszynę Wirtualną programu SQL Server na platformie Azure. Azure CLI maszyny Wirtualnej programu SQL umożliwia utworzenie odbiornika usługi WSFC, wewnętrznego modułu równoważenia obciążenia i grupy dostępności, wszystko z poziomu wiersza polecenia, a także w czasie rekordów! Aby uzyskać więcej informacji, zobacz [Użyj SQL VM wiersza polecenia platformy Azure na konfigurowanie zawsze włączonej grupy dostępności programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Udoskonalenia dokumentacji | Szczegóły |
| --- | --- |
|brak | |
| | |

## <a name="december-2018"></a>Grudzień 2018 r.

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
| **Nowego dostawcę zasobów grupy klastra SQL** | Nowego dostawcę zasobów (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definiujący metadane klastra pracy awaryjnej Windows. Przyłączanie maszyny Wirtualnej z programu SQL Server do *SqlVirtualMachineGroups* usługę klastra pracy awaryjnej Windows używa do ładowania i dołączania maszyny Wirtualnej do klastra.  |
|**Automatyzowanie konfigurowania wdrożenia grupy dostępności przy użyciu szablonów szybkiego startu platformy Azure** |Teraz istnieje możliwość tworzenia klastra pracy awaryjnej Windows, przyłączyć się do niej maszyny wirtualne SQL Server, utwórz odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia przy użyciu dwa szablony szybkiego startu platformy Azure. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure Użyj Konfigurowanie zawsze włączonej grupy dostępności dla programu SQL Server na Maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Rejestracja dostawcy zasobów automatyczne maszyny Wirtualnej SQL** | Maszyny wirtualne SQL Server wdrożony po miesiącem są automatycznie rejestrowane za pomocą nowego dostawcę zasobów programu SQL Server. Maszyny wirtualne SQL Server wdrożony przed miesiącem nadal trzeba zarejestrować ręcznie. Aby uzyskać więcej informacji, zobacz [zarejestrować istniejącej maszyny Wirtualnej SQL z dostawcą zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Listopada 2018 r.

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
| **Nowego dostawcę zasobów maszyny Wirtualnej SQL** |  Nowego dostawcę zasobów dla maszyny wirtualne SQL Server (Microsoft.SqlVirtualMachine) zapewniającej lepsze zarządzanie maszyny wirtualnej programu SQL Server. Aby uzyskać więcej informacji na temat rejestrowania maszyny Wirtualnej, zobacz [zarejestrować istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Przełącz model licencjonowania** |Możesz teraz przełączać się między model płatności za użycie i bring your own licencji dla maszyny Wirtualnej SQL przy użyciu wiersza polecenia platformy Azure lub programu Powershell. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Dodatkowe zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprowizowanie Windows serwer SQL maszyny Wirtualnej](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprowizowanie maszyny wirtualnej programu SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (system Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)