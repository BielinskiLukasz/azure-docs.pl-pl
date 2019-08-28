---
title: Przywróć aplikację — Azure App Service
description: Dowiedz się, jak przywrócić aplikację z kopii zapasowej.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 519cf5388b095c7ca6e0ae7d978608f0824dc3a2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066516"
---
# <a name="restore-an-app-in-azure"></a>Przywracanie aplikacji na platformie Azure
W tym artykule pokazano, jak przywrócić aplikację w [Azure App Service](../app-service/overview.md) , której kopię zapasową utworzono wcześniej (zobacz [Tworzenie kopii zapasowej aplikacji na platformie Azure](manage-backup.md)). Możesz przywrócić swoją aplikację ze swoimi połączonymi bazami danych na żądanie do poprzedniego stanu lub utworzyć nową aplikację na podstawie kopii zapasowych oryginalnej aplikacji. Azure App Service obsługuje następujące bazy danych na potrzeby tworzenia kopii zapasowych i przywracania:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL w aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Przywracanie z kopii zapasowych jest dostępne dla aplikacji działających w warstwie **standardowa** i **Premium** . Aby uzyskać informacje na temat skalowania aplikacji w górę, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md). Warstwa **Premium** umożliwia wykonywanie większej liczby codziennych kopii zapasowych niż w warstwie **standardowa** .

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Przywracanie aplikacji z istniejącej kopii zapasowej
1. Na stronie **Ustawienia** aplikacji w Azure Portal kliknij pozycję **kopie zapasowe** , aby wyświetlić stronę **kopie zapasowe** . Następnie kliknij przycisk **Przywróć**.
   
    ![Wybierz pozycję Przywróć teraz][ChooseRestoreNow]
2. Na stronie **przywracanie** najpierw wybierz źródło kopii zapasowej.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Opcja **kopia zapasowa aplikacji** zawiera wszystkie istniejące kopie zapasowe bieżącej aplikacji. można je łatwo wybrać.
    Opcja **magazynu** umożliwia wybranie dowolnego pliku zip kopii zapasowej z dowolnego istniejącego konta usługi Azure Storage i kontenera w ramach subskrypcji.
    Jeśli próbujesz przywrócić kopię zapasową innej aplikacji, użyj opcji **magazynu** .
3. Następnie określ miejsce docelowe przywracania aplikacji w **miejscu docelowym przywracania**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > W przypadku wybrania opcji **Zastąp**wszystkie istniejące dane w bieżącej aplikacji zostaną wymazane i zastąpione. Przed kliknięciem przycisku **OK**upewnij się, że jest dokładnie to, co chcesz zrobić.
   > 
   > 
   
   > [!WARNING]
   > Jeśli App Service zapisuje dane do bazy danych podczas przywracania, może to spowodować powstanie objawów takich jak naruszenie klucza podstawowego i utraty danych. Zalecane jest zatrzymanie App Service przed rozpoczęciem przywracania bazy danych.
   > 
   > 
   
    Możesz wybrać **istniejącą aplikację** , aby przywrócić kopię zapasową aplikacji do innej aplikacji w tej samej grupie zasobów. Przed użyciem tej opcji należy wcześniej utworzyć inną aplikację w grupie zasobów z konfiguracją bazy danych dublowania do tej, która została zdefiniowana w kopii zapasowej aplikacji. Możesz również utworzyć **nową** aplikację, aby przywrócić zawartość do programu.

4. Kliknij przycisk **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Pobieranie lub usuwanie kopii zapasowej z konta magazynu
1. Na stronie głównej **przeglądania** Azure Portal wybierz pozycję **konta magazynu**. Zostanie wyświetlona lista istniejących kont magazynu.
2. Wybierz konto magazynu zawierające kopię zapasową, którą chcesz pobrać lub usunąć. Zostanie wyświetlona strona dla konta magazynu.
3. Na stronie konto magazynu wybierz żądany kontener
   
    ![Wyświetl kontenery][ViewContainers]
4. Wybierz plik kopii zapasowej, który chcesz pobrać lub usunąć.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. W zależności od tego, co chcesz zrobić, kliknij pozycję **Pobierz** lub **Usuń** .  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorowanie operacji przywracania
Aby wyświetlić szczegółowe informacje o powodzeniu lub niepowodzeniu operacji przywracania aplikacji, przejdź do strony **dziennika aktywności** w Azure Portal.  
 

Przewiń w dół, aby znaleźć żądaną operację przywracania, a następnie kliknij, aby ją zaznaczyć.

Na stronie Szczegóły są wyświetlane dostępne informacje związane z operacją przywracania.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie tworzeniem kopii zapasowych za pomocą skryptów można zautomatyzować przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/overview).

Aby zapoznać się z przykładami, zobacz:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady dla programu Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
