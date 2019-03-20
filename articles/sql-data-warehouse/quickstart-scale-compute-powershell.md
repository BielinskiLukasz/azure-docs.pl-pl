---
title: 'Szybki start: skalowanie w poziomie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse — PowerShell | Microsoft Docs'
description: Skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse za pomocą programu PowerShell. Skalowanie zasobów obliczeniowych w poziomie zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: bd137b71cab4a345afce835effd2ecb0c03df312
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882985"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Szybki start: skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse za pomocą programu PowerShell

Skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse za pomocą programu PowerShell. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku Szybki start założono, że użytkownik ma już magazyn danych SQL, który może skalować. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do swojej subskrypcji platformy Azure za pomocą [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom [Get AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom [AzContext zestaw](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Wyszukiwanie informacji w magazynie danych

Znajdź nazwę bazy danych, nazwę serwera oraz grupę zasobów magazynu danych, który chcesz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla magazynu danych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Magazyny danych SQL** po lewej stronie witryny Azure Portal.
3. Wybierz pozycję **mySampleDataWarehouse** ze strony **Magazyny danych SQL**. Spowoduje to otworzenie magazynu danych.

    ![Nazwa serwera i grupa zasobów](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Zanotuj nazwę magazynu danych, która będzie używana jako nazwa bazy danych. Pamiętaj, że magazyn danych jest jednym z typów bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów. Będziesz używać tych nazw w poleceniach wstrzymywania i wznawiania.
5. Jeśli serwer to foo.database.windows.net, użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na poprzedniej ilustracji pełną nazwą serwera jest newserver-20171113.database.windows.net. W poleceniach cmdlet programu PowerShell używamy ciągu **newserver-20180430** jako nazwy serwera.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

W usłudze SQL Data Warehouse można zwiększyć lub zmniejszyć ilość zasobów obliczeniowych przez odpowiednie dostosowanie jednostek magazynu danych. Postępując według czynności opisanych w artykule [Tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzono bazę danych **mySampleDataWarehouse** z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych, użyj [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) polecenia cmdlet programu PowerShell. Poniższy przykład ustawia jednostki magazynu danych na DW300 dla bazy danych **mySampleDataWarehouse**, która jest hostowana w grupie zasobów **myResourceGroup** na serwerze **mynewserver-20180430**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Sprawdzanie stanu magazynu danych

Aby wyświetlić bieżący stan magazynu danych, użyj [Get AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) polecenia cmdlet programu PowerShell. Polecenie spowoduje pobranie stanu bazy danych **mySampleDataWarehouse** w grupie zasobów **myResourceGroup** na serwerze **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Wynik operacji będzie podobny do poniższego:

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

**Stan** bazy danych jest widoczny w danych wyjściowych. W tym przypadku możesz zobaczyć, że baza danych jest w trybie online.  Po uruchomieniu tego polecenia użytkownik powinien otrzymać wartość stanu Online, Wstrzymywanie, Wznawianie, Skalowanie lub Wstrzymana.

Aby wyświetlić stan samodzielnie, użyj następującego polecenia:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Kolejne kroki
Teraz już wiesz, jak skalować zasoby obliczeniowe na potrzeby magazynu danych. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do magazynu danych SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
