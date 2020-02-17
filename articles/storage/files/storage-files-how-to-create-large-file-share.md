---
title: Włączanie i tworzenie dużych udziałów plików — Azure Files
description: W tym artykule dowiesz się, jak włączyć i utworzyć duże udziały plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94a376c01229de20e6a1264da3f29532becefa8a
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368663"
---
# <a name="enable-and-create-large-file-shares"></a>Włączanie i tworzenie dużych udziałów plików

Po włączeniu dużych udziałów plików na koncie magazynu udziały plików można skalować do 100 TiB. Skalowanie można włączyć na istniejących kontach magazynu dla istniejących udziałów plików.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Ograniczenia

Na razie można używać tylko magazynu lokalnie nadmiarowego (LRS) lub magazynu Strefowo nadmiarowego (ZRS) na kontach z włączonym dużym udziałem plików. Nie można używać magazynu geograficznie nadmiarowego (GZRS), magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS).
Włączenie dużych udziałów plików na koncie jest procesem nieodwracalnym. Po włączeniu tej operacji nie będzie możliwe przekonwertowanie konta na GZRS, GRS lub RA-GRS.

## <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu

### <a name="portal"></a>Portal

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. 
1. Na liście zasobów wprowadź **konto magazynu**. Podczas wpisywania lista jest filtrowana na podstawie danych wejściowych. Wybierz pozycję **Konta usługi Storage**.
1. W wyświetlonym oknie **konta magazynu** wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, która będzie używana do tworzenia konta magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów.

    ![Zrzut ekranu przedstawiający sposób tworzenia grupy zasobów w portalu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Następnie wprowadź nazwę konta magazynu. Nazwa musi być unikatowa na platformie Azure. Nazwa musi również mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację dla konta magazynu i upewnij się, że jest to [jedna z obsługiwanych replikacji dla dużych udziałów plików](storage-files-planning.md#regional-availability).
1. Ustaw replikację na magazyn **lokalnie nadmiarowy** lub magazynu **Strefowo nadmiarowego**.
1. Pozostaw wartości domyślne tych pól:

   |Pole  |Wartość  |
   |---------|---------|
   |Model wdrażania     |Resource Manager         |
   |Wydajność     |Standard         |
   |Rodzaj konta     |StorageV2 (ogólnego przeznaczenia wersja 2)         |
   |Warstwa dostępu     |Gorąca         |

1. Wybierz pozycję **Zaawansowane**, a następnie wybierz przycisk opcji **włączone** po prawej stronie **dużych udziałów plików**.
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.

    ![Zrzut ekranu z przyciskiem opcji "Enabled" na nowym koncie magazynu w Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Wybierz pozycję **Utwórz**.

### <a name="cli"></a>Interfejs wiersza polecenia

Najpierw [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby umożliwić obsługę dużych udziałów plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` informacjami.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>Program PowerShell

Najpierw [Zainstaluj najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) , aby można było włączyć duże udziały plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` informacjami.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Włącz udziały dużych plików na istniejącym koncie

Możesz również włączyć duże udziały plików na istniejących kontach. Jeśli włączysz duże udziały plików, nie będzie możliwe przekonwertowanie na GZRS, GRS lub RA-GRS. Włączenie dużych udziałów plików jest nieodwracalne na tym koncie magazynu.

### <a name="portal"></a>Portal

1. Otwórz [Azure Portal](https://portal.azure.com)i przejdź do konta magazynu, w którym chcesz włączyć duże udziały plików.
1. Otwórz konto magazynu i wybierz pozycję **Konfiguracja**.
1. Wybierz pozycję **włączone** w przypadku **dużych udziałów plików**, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przegląd** , a następnie wybierz pozycję **Odśwież**.

![Wybieranie przycisku opcji włączone na istniejącym koncie magazynu w Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Na koncie magazynu są teraz włączone duże udziały plików. Następnie należy zaktualizować limit przydziału istniejącego udziału, aby korzystać z większej pojemności i skalowalności.

Jeśli zostanie wyświetlony komunikat o błędzie "duże udziały plików nie są jeszcze dostępne dla konta", region może być w trakcie wykonywania jego wdrożenia. Skontaktuj się z pomocą techniczną, jeśli masz pilną potrzebę w przypadku dużych udziałów plików.

### <a name="cli"></a>Interfejs wiersza polecenia

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` informacjami.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>Program PowerShell

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` informacjami.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Tworzenie dużego udziału plików

Po włączeniu dużych udziałów plików na koncie magazynu można utworzyć udziały plików na tym koncie z wyższymi przydziałami. 

### <a name="portal"></a>Portal

Tworzenie dużego udziału plików jest niemal identyczne z tworzeniem standardowego udziału plików. Główną różnicą jest to, że można ustawić limit przydziału do 100 TiB.

1. Na koncie magazynu wybierz pozycję **udziały plików**.
1. Wybierz pozycję **+ Udział plików**.
1. Wprowadź nazwę udziału plików. Możesz również ustawić żądany rozmiar przydziału, do 100 TiB. Następnie wybierz przycisk **Utwórz**. 

![Interfejs użytkownika Azure Portal przedstawiający pola Nazwa i limit przydziału](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>` informacjami.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>Program PowerShell

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>` informacjami.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Rozwiń istniejące udziały plików

Po włączeniu dużych udziałów plików na koncie magazynu można również rozszerzyć istniejące udziały plików na tym koncie do wyższego przydziału. 

### <a name="portal"></a>Portal

1. Na koncie magazynu wybierz pozycję **udziały plików**.
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz pozycję **przydział**.
1. Wprowadź nowy rozmiar, a następnie wybierz przycisk **OK**.

![Interfejs użytkownika Azure Portal z przydziałem istniejących udziałów plików](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Aby ustawić limit przydziału na maksymalny rozmiar, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>` informacjami.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>Program PowerShell

Aby ustawić limit przydziału na maksymalny rozmiar, użyj następującego polecenia. Zastąp `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>` informacjami.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Następne kroki

* [Łączenie i Instalowanie udziału plików w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i Instalowanie udziału plików w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i Instalowanie udziału plików w systemie macOS](storage-how-to-use-files-mac.md)
