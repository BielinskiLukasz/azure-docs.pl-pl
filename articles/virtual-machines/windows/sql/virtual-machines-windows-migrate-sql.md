---
title: Migrowanie bazy danych SQL Server do SQL Server na maszynie wirtualnej | Microsoft Docs
description: Dowiedz się więcej o tym, jak migrować lokalną bazę danych użytkownika do SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646867"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrowanie bazy danych programu SQL Server do programu SQL Server na maszynie wirtualnej platformy Azure

Istnieje wiele metod migrowania lokalnej bazy danych użytkownika SQL Server do SQL Server na maszynie wirtualnej platformy Azure. Ten artykuł zawiera krótkie omówienie różnych metod i zaleca najlepszą metodę dla różnych scenariuszy.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 i SQL Server 2008 R2 zbliżają się do [końca cyklu życia pomocy technicznej](https://www.microsoft.com/sql-server/sql-server-2008) dla wystąpień lokalnych. Aby rozszerzyć obsługę, można przeprowadzić migrację wystąpienia SQL Server na maszynę wirtualną platformy Azure lub zakupić rozszerzone aktualizacje zabezpieczeń, aby zachować je w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz sekcję [dotyczącą rozszerzeń dla SQL Server 2008 i 2008 R2 z platformą Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Jakie są podstawowe metody migracji?
Podstawowe metody migracji są następujące:

* Wykonaj lokalną kopię zapasową przy użyciu kompresji i ręcznie Skopiuj plik kopii zapasowej do maszyny wirtualnej platformy Azure
* Wykonaj kopię zapasową do adresu URL i przywróć ją na maszynie wirtualnej platformy Azure przy użyciu adresu URL
* Odłączanie i kopiowanie plików danych i dzienników do usługi Azure Blob Storage, a następnie dołączanie do SQL Server na maszynie wirtualnej platformy Azure z adresu URL
* Przekształć lokalną maszynę fizyczną na dysk VHD funkcji Hyper-V, Przekaż do usługi Azure Blob Storage, a następnie wdróż ją jako nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego
* Wyślij dysk twardy przy użyciu usługi Windows Import/Export
* Jeśli masz lokalne wdrożenie grupy dostępności, użyj [Kreatora dodawania repliki platformy Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) , aby utworzyć replikę na platformie Azure, a następnie przełączenia w tryb failover użytkowników do wystąpienia usługi Azure Database
* Użyj SQL Server [replikację transakcyjną](https://msdn.microsoft.com/library/ms151176.aspx) , aby skonfigurować wystąpienie usługi Azure SQL Server jako subskrybenta, a następnie wyłączyć replikację, a następnie pozycję Użytkownicy wskazujący wystąpienie usługi Azure Database

> [!TIP]
> Możesz również użyć tych samych technik do przenoszenia baz danych między maszynami wirtualnymi SQL Server na platformie Azure. Na przykład nie ma żadnego obsługiwanego sposobu uaktualnienia SQL Server Galeria obrazów z jednej wersji/wydania do innej. W takim przypadku należy utworzyć nową maszynę wirtualną SQL Server z nową wersją/wersją, a następnie użyć jednej z technik migracji w tym artykule, aby przenieść bazy danych. 

## <a name="choosing-your-migration-method"></a>Wybieranie metody migracji
Aby uzyskać optymalną wydajność transferu danych, dokonaj migracji plików bazy danych na maszynę wirtualną platformy Azure przy użyciu skompresowanego pliku kopii zapasowej.

Aby zminimalizować przestoje podczas procesu migracji bazy danych, należy użyć opcji AlwaysOn lub opcji replikacja transakcyjna.

Jeśli nie można użyć powyższych metod, należy ręcznie przeprowadzić migrację bazy danych. Korzystając z tej metody, zwykle zaczynasz od kopii zapasowej bazy danych, a następnie kopii zapasowej bazy danych na platformie Azure, a następnie przeprowadzasz przywracanie bazy danych. Możesz również skopiować pliki bazy danych na platformę Azure, a następnie dołączyć je. Istnieje kilka metod, za pomocą których można wykonać ten ręczny proces migrowania bazy danych na maszynę wirtualną platformy Azure.

> [!NOTE]
> Po uaktualnieniu do SQL Server 2014 lub SQL Server 2016 ze starszych wersji SQL Server należy wziąć pod uwagę, czy zmiany są potrzebne. Zaleca się, aby zająć się wszystkimi zależnościami od funkcji nieobsługiwanych przez nową wersję SQL Server w ramach projektu migracji. Aby uzyskać więcej informacji na temat obsługiwanych wersji i scenariuszy, zobacz [uaktualnianie do SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

W poniższej tabeli wymieniono wszystkie podstawowe metody migracji i omówiono, kiedy użycie każdej metody jest najbardziej odpowiednie.

| Metoda | Wersja źródłowej bazy danych | Wersja docelowej bazy danych | Ograniczenie rozmiaru kopii zapasowej źródłowej bazy danych | Uwagi |
| --- | --- | --- | --- | --- |
| [Wykonaj lokalną kopię zapasową przy użyciu kompresji i ręcznie Skopiuj plik kopii zapasowej do maszyny wirtualnej platformy Azure](#backup-and-restore) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Jest to bardzo prosta i dobrze sprawdzona technika służąca do przemieszczania baz danych między maszynami. |
| [Wykonaj kopię zapasową do adresu URL i przywróć ją na maszynie wirtualnej platformy Azure przy użyciu adresu URL](#backup-to-url-and-restore) |SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 lub nowszym |SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 lub nowszym |< 12,8 TB dla SQL Server 2016, w przeciwnym razie < 1 TB | Ta metoda jest już inna metodą przenoszenia pliku kopii zapasowej na maszynę wirtualną przy użyciu usługi Azure Storage. |
| [Odłącz i skopiuj pliki danych i dziennika do usługi Azure Blob Storage, a następnie Dołącz do SQL Server na maszynie wirtualnej platformy Azure z adresu URL](#detach-and-attach-from-url) |SQL Server 2005 lub więcej |SQL Server 2014 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Ta metoda służy do [przechowywania tych plików przy użyciu usługi Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx) i dołączania ich do SQL Server działających na maszynie wirtualnej platformy Azure, szczególnie w przypadku bardzo dużych baz danych |
| [Przekonwertuj maszynę lokalną na wirtualne dyski twarde funkcji Hyper-V, Przekaż do usługi Azure Blob Storage, a następnie wdróż nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Użyj podczas korzystania z [własnej licencji SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)podczas migrowania bazy danych, która będzie uruchamiana w starszej wersji SQL Server, lub podczas migrowania baz danych systemu i użytkownika w ramach migracji bazy danych zależnej od innych baz danych użytkownika i/lub systemowych baz danych. |
| [Wyślij dysk twardy przy użyciu usługi Windows Import/Export](#ship-hard-drive) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Korzystanie z [usługi Import/Export systemu Windows](../../../storage/common/storage-import-export-service.md) , gdy ręczna metoda kopiowania jest zbyt wolna, na przykład z bardzo dużymi bazami danych |
| [Korzystanie z Kreatora dodawania repliki platformy Azure](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 lub więcej |SQL Server 2012 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimalizuje czas przestoju, należy używać w przypadku, gdy istnieje zawsze lokalne wdrożenie |
| [Użyj SQL Server replikacji transakcyjnej](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 lub więcej |SQL Server 2005 lub więcej |[Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Użyj, gdy zachodzi potrzeba zminimalizowania przestoju i nie ma możliwości ciągłego wdrażania. |

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie
Wykonaj kopię zapasową bazy danych z kompresją, skopiuj kopię zapasową do maszyny wirtualnej, a następnie Przywróć bazę danych. Jeśli plik kopii zapasowej jest większy niż 1 TB, należy go rozdzielić, ponieważ maksymalny rozmiar dysku maszyny wirtualnej wynosi 1 TB. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację bazy danych użytkownika przy użyciu tej metody ręcznej:

1. Wykonaj pełną kopię zapasową bazy danych w lokalizacji lokalnej.
2. Utwórz lub Przekaż maszynę wirtualną z odpowiednią wersją SQL Server.
3. Skonfiguruj łączność na podstawie Twoich wymagań. Zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (Menedżer zasobów)](virtual-machines-windows-sql-connect.md).
4. Skopiuj pliki kopii zapasowej na maszynę wirtualną za pomocą pulpitu zdalnego, Eksploratora Windows lub polecenia copy z wiersza polecenia.

## <a name="backup-to-url-and-restore"></a>Utwórz kopię zapasową do adresu URL i Przywróć
Zamiast tworzyć kopię zapasową w pliku lokalnym, można użyć [kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx) , a następnie przywrócić z adresu URL do maszyny wirtualnej. W przypadku SQL Server 2016 obsługiwane są rozłożone zestawy kopii zapasowych, zalecane do wydajności i wymagane do przekroczenia limitów rozmiaru na obiekt BLOB. W przypadku bardzo dużych baz danych zalecane jest korzystanie z [usługi Import/Export systemu Windows](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-url"></a>Odłączanie i dołączanie adresu URL
Odłącz bazę danych i pliki dziennika, a następnie prześlij je do [usługi Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx). Następnie Dołącz bazę danych z adresu URL na maszynie wirtualnej platformy Azure. Użyj tego, jeśli chcesz, aby pliki fizycznych baz danych znajdowały się w magazynie obiektów BLOB. Może to być przydatne w przypadku bardzo dużych baz danych. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację bazy danych użytkownika przy użyciu tej metody ręcznej:

1. Odłączanie plików bazy danych od lokalnego wystąpienia bazy danych.
2. Skopiuj odłączone pliki bazy danych do usługi Azure Blob Storage za pomocą [narzędzia wiersza polecenia AzCopy](../../../storage/common/storage-use-azcopy.md).
3. Dołącz pliki bazy danych z adresu URL platformy Azure do wystąpienia SQL Server na maszynie wirtualnej platformy Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Przeprowadź konwersję na maszynę wirtualną, przekaż ją do adresu URL i wdróż ją jako nową maszynę wirtualną
Ta metoda służy do migrowania wszystkich baz danych systemu i użytkownika w lokalnym wystąpieniu SQL Server do maszyny wirtualnej platformy Azure. Wykonaj następujące ogólne kroki, aby przeprowadzić migrację całego wystąpienia SQL Server przy użyciu tej metody ręcznej:

1. Konwertowanie maszyn fizycznych lub wirtualnych na wirtualne dyski twarde funkcji Hyper-V.
2. Przekaż pliki VHD do usługi Azure Storage za pomocą [polecenia cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Wdróż nową maszynę wirtualną przy użyciu przekazanego wirtualnego dysku twardego.

> [!NOTE]
> Aby przeprowadzić migrację całej aplikacji, należy rozważyć użycie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Wyślij dysk twardy
Korzystając z [metody usługi Import/Export systemu Windows](../../../storage/common/storage-import-export-service.md) , można transferować duże ilości danych plików do usługi Azure Blob Storage w sytuacjach, gdy przekazywanie przez sieć jest niezwykle kosztowne lub niemożliwe. Za pomocą tej usługi wysyłasz co najmniej jeden dysk twardy zawierający te dane do centrum danych platformy Azure, w którym dane zostaną przekazane do konta magazynu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz [SQL Server na platformie Virtual Machines Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej SQL Server platformy Azure z przechwyconego obrazu, zobacz [porady & wskazówki dotyczące "klonowania" maszyn wirtualnych usługi Azure SQL z przechwyconych obrazów](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) w blogu SQL Server inżynierów CSS.

