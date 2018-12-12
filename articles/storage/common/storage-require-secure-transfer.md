---
title: Wymaganie bezpiecznego transferu w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Więcej informacji na temat funkcji "Wymagany bezpieczny transfer" dla usługi Azure Storage i jak go włączyć.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.component: common
ms.openlocfilehash: 72e0937e91a7287d240bbdb25996865f934d432d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876506"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Wymaganie bezpiecznego transferu w usłudze Azure Storage

Opcja "Wymagany bezpieczny transfer" podnosi poziom bezpieczeństwa konta magazynu, zezwalając jedynie na żądania do konta z bezpiecznych połączeń. Na przykład w przypadku wywoływania interfejsów API REST dostępu do konta magazynu, musisz połączyć za pośrednictwem protokołu HTTPS. "Wymagany bezpieczny transfer" odrzuca żądania, które używają protokołu HTTP.

Korzystając z usługi pliki Azure, każde połączenie bez szyfrowania nie powiedzie się, gdy "Wymagany bezpieczny transfer" jest włączona. W tym scenariuszami korzystającymi z protokołu SMB 2.1, protokołu SMB 3.0 bez szyfrowania i niektóre wersje klienta SMB w systemie Linux. 

Domyślnie opcja "Wymagany bezpieczny transfer" jest wyłączona podczas tworzenia konta magazynu przy użyciu zestawu SDK. I jest włączona domyślnie po utworzeniu konta magazynu w witrynie Azure Portal.

> [!NOTE]
> Ponieważ usługi Azure Storage nie obsługuje protokołu HTTPS dla nazw domen niestandardowych, ta opcja nie została zastosowana podczas korzystania z niestandardowej nazwy domeny. I klasycznych kont magazynu nie są obsługiwane.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Włącz "Wymagany bezpieczny transfer" w witrynie Azure portal

Można włączyć "bezpieczny transfer wymagane" ustawienie podczas tworzenia konta magazynu w [witryny Azure portal](https://portal.azure.com). Można również włączyć dla istniejących kont magazynu.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Wymaganie bezpiecznego transferu dla nowego konta magazynu

1. Otwórz **Tworzenie konta magazynu** okienko w witrynie Azure portal.
1. W obszarze **Wymagany bezpieczny transfer**, wybierz opcję **włączone**.

  ![Tworzenie bloku konto magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Wymaganie bezpiecznego transferu dla istniejącego konta magazynu

1. Wybierz istniejące konto magazynu w witrynie Azure portal.
1. W magazynie konta menu okienku w obszarze **ustawienia**, wybierz opcję **konfiguracji**.
1. W obszarze **Wymagany bezpieczny transfer**, wybierz opcję **włączone**.

  ![Okienko menu konta magazynu](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Włącz "Wymagany bezpieczny transfer" programowe

Aby programowo Wymaganie bezpiecznego transferu, użyj ustawienia _supportsHttpsTrafficOnly_ we właściwościach konta magazynu przy użyciu interfejsu API REST, narzędzi i bibliotek:

* [Interfejs API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (wersja: 2016-12-01)
* [Program PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (wersja: 4.1.0)
* [Interfejs wiersza polecenia](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (wersja: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (wersja: 1.1.0)
* [Zestaw SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (wersja: wersji 6.3.0)
* [Zestaw SDK języka Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (wersja: 1.1.0)
* [Zestaw Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (wersja: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Włącz "Bezpieczny transfer wymagane" ustawienie przy użyciu programu PowerShell

Ten przykładowy skrypt wymaga modułu w wersji 4.1 lub nowszym programu Azure PowerShell. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenia z platformą Azure.

 Aby sprawdzić ustawienie, należy użyć następującego polecenia:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Aby włączyć ustawienie, należy użyć następującego polecenia:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Włącz "Bezpieczny transfer wymagane" Ustawianie za pomocą interfejsu wiersza polecenia

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Aby sprawdzić ustawienie, należy użyć następującego polecenia:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Aby włączyć ustawienie, należy użyć następującego polecenia:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Storage udostępnia rozbudowany zestaw funkcji zabezpieczeń, umożliwiających deweloperom tworzenie bezpiecznych aplikacji. Aby uzyskać więcej informacji, przejdź do [Przewodnik po zabezpieczeniach magazynu](storage-security-guide.md).
