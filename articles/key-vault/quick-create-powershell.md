---
title: 'Szybki start: ustawianie & pobierania klucza tajnego z magazynu kluczy za pomocą programu PowerShell'
description: W tym przewodniku Szybki start dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu programu Azure PowerShell.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 627d74f48c0f2b3da8665cd255102f36869477c2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79472762"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](key-vault-overview.md). W tym przewodniku Szybki start opisano tworzenie magazynu kluczy przy użyciu programu PowerShell, a następnie umieszczanie wpisu tajnego w nowo utworzonym magazynie.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Następnie utworzysz magazyn kluczy. Podczas wykonywania tego kroku potrzebne będą pewne informacje:

Mimo iż w tym przewodniku Szybki start używany nazwy „Contoso KeyVault2” jako nazwy usługi Key Vault, musisz użyć unikatowej nazwy.

- **Nazwa magazynu**: Contoso Vault2.
- **Nazwa grupy zasobów** ContosoResourceGroup.
- **Lokalizacja** Wschodnie stany USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu**: w tym przykładzie jest to **Contoso-Vault2**. Ta nazwa będzie używana do innych poleceń cmdlet usługi Key Vault.
* **Identyfikator URI magazynu**: w tym przykładzie jest to https://Contoso-Vault2.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Po utworzeniu magazynu Twoje konto platformy Azure będzie jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu wykonywania polecenia tworzenia magazynu kluczy](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wystarczy tylko wykonać kilka czynności. W tym przypadku dodaj hasło, którego będzie mogła używać aplikacja. Hasło ma nazwę **ExamplePassword** i przechowywana jest w nim wartość **hVFkk965BuUv**.

Najpierw przekonwertuj wartość **hVFkk965BuUv** na bezpieczny ciąg, wpisując polecenie:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Następnie wpisz poniższe polecenia programu PowerShell, aby utworzyć wpis tajny w usłudze Key Vault o nazwie **ExamplePassword** z wartością **hVFkk965BuUv**:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Aby wyświetlić wartość zawartą we wpisie tajnym jako zwykły tekst:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

Utworzono usługę Key Vault, umieszczono w niej wpis tajny i pobrano go.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z innych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów, usługa Key Vault i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy i zapis tajny. Aby dowiedzieć się więcej o programie Key Vault i integruj go z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj [omówienie usługi Azure Key Vault](key-vault-overview.md)
- Zobacz odwołanie do [poleceń cmdlet usługi Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](key-vault-best-practices.md)
