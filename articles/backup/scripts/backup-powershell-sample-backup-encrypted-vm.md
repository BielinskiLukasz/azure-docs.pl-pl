---
title: Przykład skryptu programu PowerShell — wykonywanie kopii zapasowej maszyny Wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak użyć przykładu skryptu programu Azure PowerShell do utworzenia kopii zapasowej maszyny wirtualnej platformy Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: ad4d290961bd39659283795f6a58ccb815b639b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171802"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Tworzenie zaszyfrowanej kopii zapasowej maszyny wirtualnej platformy Azure za pomocą programu PowerShell

Ten skrypt tworzy magazyn usług odzyskiwania z magazynem geograficznie nadmiarowym (GRS) dla zaszyfrowanej maszyny wirtualnej platformy Azure. Względem magazynu są stosowane domyślne zasady ochrony. Te zasady powodują generowanie kopii zapasowej maszyny wirtualnej codziennie oraz przechowywanie każdej kopii zapasowej przez 30 dni. Skrypt wyzwala również początkowy punkt odzyskiwania dla maszyny wirtualnej i przechowuje ten punkt odzyskiwania przez 365 dni.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowy-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Tworzy magazyn usług Recovery Services w celu przechowywania kopii zapasowych. |
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Określa właściwości magazynu kopii zapasowych w magazynie usług Recovery Services. |
| [Nowa-AzRecoveryServicesBackupProtectionPolityka](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Tworzy zasady ochrony, używając zasad planowania i zasad przechowywania, w magazynie usług Recovery Services. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Umożliwia utworzenie kopii zapasowej elementu za pomocą określonych zasad ochrony kopii zapasowych. |
| [Set-AzRecoveryServicesBackupProtectionPolityka](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modyfikuje istniejące zasady ochrony kopii zapasowych. |
| [Kopia zapasowa-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Uruchamia tworzenie kopii zapasowej chronionego elementu usługi Azure Backup, który nie jest związany z harmonogramem tworzenia kopii zapasowych. |
| [Wait-AzRecoveryServicesBackupOb](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Czeka na zakończenie zadania usługi Azure Backup. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
