---
title: Punkty przywracania zdefiniowane przez użytkownika
description: Jak utworzyć punkt przywracania dla puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 18d341db8538af12dbbabab4797459779a553e4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85205003"
---
# <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

W tym artykule opisano tworzenie nowego punktu przywracania zdefiniowanego przez użytkownika dla puli SQL w usłudze Azure Synapse Analytics przy użyciu programu PowerShell i Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Tworzenie punktów przywracania zdefiniowanych przez użytkownika za poorednictwem programu PowerShell

Aby utworzyć punkt przywracania zdefiniowany przez użytkownika, należy użyć polecenia cmdlet [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) programu PowerShell.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.
5. Utwórz punkt przywracania natychmiastowej kopii magazynu danych.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Zapoznaj się z listą wszystkich istniejących punktów przywracania.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Utwórz punkty przywracania zdefiniowane przez użytkownika za pomocą Azure Portal

Punkty przywracania zdefiniowane przez użytkownika mogą być również tworzone za poorednictwem Azure Portal.

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .

2. Przejdź do puli SQL, dla której chcesz utworzyć punkt przywracania.

3. Wybierz pozycję **Przegląd** w okienku po lewej stronie, a następnie wybierz pozycję **+ nowy punkt przywracania**. Jeśli przycisk Nowy punkt przywracania nie jest włączony, upewnij się, że Pula SQL nie jest wstrzymana.

    ![Nowy punkt przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Określ nazwę punktu przywracania zdefiniowanego przez użytkownika, a następnie kliknij przycisk **Zastosuj**. Punkty przywracania zdefiniowane przez użytkownika mają domyślny okres przechowywania wynoszący siedem dni.

    ![Nazwa punktu przywracania](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej puli SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Przywracanie z puli SQL geograficznej kopii zapasowej](sql-data-warehouse-restore-from-geo-backup.md)

