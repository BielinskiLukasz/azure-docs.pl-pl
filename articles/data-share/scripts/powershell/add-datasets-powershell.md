---
title: 'Skrypt programu PowerShell: dodawanie zestawu danych obiektów blob do udziału danych platformy Azure | Dokumenty firmy Microsoft'
description: Ten skrypt programu PowerShell dodaje zestaw danych obiektu blob do istniejącego udziału.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307301"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Tworzenie udziału danych na platformie Azure za pomocą programu PowerShell

Ten skrypt programu PowerShell dodaje zestaw danych obiektu blob do istniejącego udziału danych.

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Zestaw danych new-AzDataShareData](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | Dodaje zestaw danych do udziału danych. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu PowerShell udostępniania danych platformy Azure można znaleźć w [przykładach programu PowerShell udostępniania danych platformy Azure.](../../samples-powershell.md)
