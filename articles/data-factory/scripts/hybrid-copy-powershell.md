---
title: 'Skrypt programu PowerShell: kopiowanie danych ze środowiska lokalnego do platformy Azure przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell kopiuje dane z lokalnej bazy danych programu SQL Server do innej usługi Azure Blob Storage.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: c3bc9f04777a9153fd8683b221e7d0aeef4609b3
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42062101"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Aby utworzyć potok usługi data factory do kopiowania danych ze środowiska lokalnego do platformy Azure przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy potok w usłudze Azure Data Factory kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- **SQL Server**. Korzystasz z bazy danych programu SQL Server w środowisku lokalnym jako **źródła** magazynu danych w tym przykładzie.
- **Konto usługi Azure Storage**. Możesz użyć usługi Azure blob storage jako **docelowego/ujścia** magazynu danych w tym przykładzie. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).
- **Może być samodzielnie hostowane środowisko IR**. Pobierz plik MSI z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) i uruchom go, aby zainstalować własnego środowiska integration runtime na maszynie.  

### <a name="create-sample-database-in-sql-server"></a>Tworzenie przykładowej bazy danych w programie SQL Server
1. W lokalnej bazie danych programu SQL Server, Utwórz tabelę o nazwie **emp** za pomocą poniższego skryptu SQL: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Wstaw przykładowe dane do tabeli:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki w formacie JSON, które definiują jednostek usługi Data Factory (połączone usługi, zestaw danych i potok) na dysku twardym w folderze c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowy skrypt służy następujące polecenie do usunięcia grupy zasobów i wszystkie skojarzone z nią zasoby:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Aby usunąć fabrykę danych z grupy zasobów, uruchom następujące polecenie: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Tworzenie fabryki danych. |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Szyfruje poświadczenia w połączonej usłudze, a następnie generuje nowych definicji usługi połączonej z zaszyfrowanych poświadczeń. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Tworzy połączonej usługi w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeniowych z fabryką danych. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Tworzy zestaw danych w usłudze data factory. Zestaw danych reprezentuje dane wejściowe i wyjściowe dla działania w potoku. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Tworzy potok w fabryce danych. Potok zawiera jedno lub więcej działań, które wykonuje określonej operacji. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Tworzy uruchomienia potoku. Innymi słowy uruchamia potok. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Pobiera szczegóły uruchomienia działania (działanie uruchamiania) w potoku. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell do fabryki danych platformy Azure można znaleźć w [przykładów programu Azure Data Factory PowerShell](../samples-powershell.md).