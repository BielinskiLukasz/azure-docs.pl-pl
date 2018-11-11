---
title: Przykładowy skrypt programu Azure PowerShell — migrowanie obiektów blob między kontami magazynu przy użyciu narzędzia AzCopy w systemie Windows | Microsoft Docs
description: Kopiowanie zawartości obiektów blob z jednego konta usługi Azure Storage na inne przy użyciu narzędzia AzCopy.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 58e2275398216f29764d54d1a230959e86eda0b6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253265"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrowanie obiektów blob między kontami magazynu przy użyciu narzędzia AzCopy w systemie Windows

Ten przykładowy skrypt kopiuje wszystkie obiekty blob ze wskazanego przez użytkownika źródłowego konta magazynu na wskazane przez użytkownika docelowe konto magazynu. 

W tym celu jest używane polecenie `Get-AzureStorageContainer`, zwracające listę wszystkich kontenerów na koncie magazynu. Następnie przykładowy skrypt uruchamia polecenia narzędzia AzCopy, aby skopiować poszczególne kontenery ze źródłowego konta magazynu na konto docelowe. W przypadku wystąpienia błędów przykładowy skrypt ponawia próbę zgodnie z wartością $retryTimes (domyślnie 3 razy, można zmienić to ustawienie przy użyciu parametru `-RetryTimes`). Jeśli błąd wystąpi podczas każdej z ponownych prób, użytkownik może uruchomić skrypt przykładowy ponownie, określając ostatni pomyślnie skopiowany kontener przy użyciu parametru `-LastSuccessContainerName`. W takim przypadku przykładowy skrypt będzie kontynuował kopiowanie kontenerów od tego miejsca.

Ten przykładowy skrypt wymaga modułu programu PowerShell dla usługi Azure Storage w wersji **4.0.2** lub nowszej. Można sprawdzić, jaka wersja jest zainstalowana, przy użyciu polecenia `Get-Module -ListAvailable Azure.storage`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Ten przykładowy skrypt wymaga też najnowszej wersji narzędzia [AzCopy w systemie Windows](https://aka.ms/downloadazcopy). Domyślny katalog instalacji to `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Przykładowy skrypt wymaga wprowadzenia nazwy i klucza źródłowego konta magazynu, nazwy i klucza docelowego konta magazynu oraz pełnej ścieżki pliku AzCopy.exe (jeśli to narzędzie nie jest zainstalowane w katalogu domyślnym).

Poniżej przedstawiono przykładowe dane wejściowe dla tego skryptu przykładowego:

Jeśli narzędzie AzCopy jest zainstalowane w katalogu domyślnym:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Jeśli narzędzie AzCopy nie jest zainstalowane w katalogu domyślnym:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Jeśli wystąpił błąd i jest wymagane ponowne uruchomienie skryptu od określonego kontenera: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do skopiowania danych między kontami magazynu. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Zwraca kontenery skojarzone z kontem magazynu. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Tworzy kontekst usługi Azure Storage. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi Storage można znaleźć na stronie [PowerShell samples for Azure Blob storage (Przykładowe skrypty programu PowerShell dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-powershell.md).
