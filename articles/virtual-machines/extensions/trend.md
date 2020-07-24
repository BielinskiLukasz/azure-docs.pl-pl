---
title: Instalowanie Trend Micro głębokich zabezpieczeń na maszynie wirtualnej
description: W tym artykule opisano sposób instalowania i konfigurowania Trend Micro Security na maszynie wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: c4a4f641283a9a4771a236ba844cfe296b31c28c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082682"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak zainstalować i skonfigurować Trend Micro głębokiego zabezpieczenia jako usługi na maszynie wirtualnej z systemem Windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
W tym artykule opisano sposób instalowania i konfigurowania Trend Micro głębokiego zabezpieczenia jako usługi na nowej lub istniejącej maszynie wirtualnej z systemem Windows Server. Głębokie zabezpieczenia jako usługa obejmują ochronę przed złośliwym oprogramowaniem, zaporę, system zapobiegania włamaniom i monitorowanie integralności.

Klient jest instalowany jako rozszerzenie zabezpieczeń za pośrednictwem agenta maszyny wirtualnej. Na nowej maszynie wirtualnej jest instalowany Agent zabezpieczeń głębokiej, ponieważ agent maszyny wirtualnej jest tworzony automatycznie przez Azure Portal.

Istniejąca maszyna wirtualna utworzona przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell może nie mieć agenta maszyny wirtualnej. W przypadku istniejącej maszyny wirtualnej, która nie ma agenta maszyny wirtualnej, należy najpierw ją pobrać i zainstalować. W tym artykule opisano obie sytuacje.

Jeśli masz bieżącą subskrypcję z usługi Trend Micro dla rozwiązania lokalnego, możesz użyć jej do ochrony maszyn wirtualnych platformy Azure. Jeśli nie jesteś jeszcze klientem, możesz zarejestrować się w celu uzyskania subskrypcji wersji próbnej. Aby uzyskać więcej informacji na temat tego rozwiązania, zobacz informacje o usłudze Trend Micro w blogu [Microsoft Azure rozszerzenia agenta maszyny wirtualnej na potrzeby głębokiego zabezpieczenia](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Zainstaluj agenta zabezpieczeń szczegółowych na nowej maszynie wirtualnej

[Azure Portal](https://portal.azure.com) pozwala zainstalować rozszerzenie Trend Micro Security w przypadku tworzenia maszyny wirtualnej przy użyciu obrazu z **portalu Marketplace** . Jeśli tworzysz pojedynczą maszynę wirtualną, korzystanie z portalu jest łatwym sposobem na dodanie ochrony z Trend Micro.

Przy użyciu wpisu z **portalu Marketplace** zostanie otwarty Kreator, który pomoże Ci skonfigurować maszynę wirtualną. Aby zainstalować rozszerzenie Trend Micro Security, należy użyć bloku **Ustawienia** , trzeciego panelu Kreatora.  Aby uzyskać ogólne instrukcje, zobacz temat [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../windows/quick-create-portal.md).

Po przepoczęciu pracy z blokiem **Ustawienia** kreatora wykonaj następujące czynności:

1. Kliknij pozycję **rozszerzenia**, a następnie kliknij pozycję **Dodaj rozszerzenie** w następnym okienku.

   ![Rozpocznij dodawanie rozszerzenia][1]

2. Wybierz pozycję **Szczegółowa ochrona** w okienku **nowy zasób** . W okienku Agent zabezpieczeń głębokiej kliknij pozycję **Utwórz**.

   ![Identyfikowanie agenta zabezpieczeń szczegółowych][2]

3. Wprowadź **Identyfikator dzierżawy** i **hasło aktywacji dzierżawy** dla rozszerzenia. Opcjonalnie możesz wprowadzić **Identyfikator zasad zabezpieczeń**. Następnie kliknij przycisk **OK** , aby dodać klienta.

   ![Podaj szczegóły rozszerzenia][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalowanie agenta zabezpieczeń głębokiej na istniejącej maszynie wirtualnej
Aby zainstalować agenta na istniejącej maszynie wirtualnej, potrzebne są następujące elementy:

* Moduł Azure PowerShell w wersji 0.8.2 lub nowszej zainstalowany na komputerze lokalnym. Zainstalowaną wersję Azure PowerShell można sprawdzić za pomocą polecenia **Get-module platformy Azure | format-Table Version** . Instrukcje i link do najnowszej wersji znajdują się w temacie [How to Install and configure Azure PowerShell](/powershell/azure/). Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Add-AzureAccount` .
* Agent maszyny wirtualnej zainstalowany na docelowej maszynie wirtualnej.

Najpierw sprawdź, czy Agent maszyny wirtualnej jest już zainstalowany. Wypełnij pola Nazwa usługi w chmurze i nazwa maszyny wirtualnej, a następnie uruchom następujące polecenia w wierszu polecenia Azure PowerShell na poziomie administratora. Zamień wszystkie elementy w cudzysłowie, w tym znaki < i >.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Jeśli nie znasz usługi w chmurze i nazwy maszyny wirtualnej, uruchom polecenie **Get-AzureVM** , aby wyświetlić te informacje dla wszystkich maszyn wirtualnych w bieżącej subskrypcji.

Jeśli polecenie **write-host** zwróci **wartość true**, Agent maszyny wirtualnej jest zainstalowany. Jeśli zwraca **wartość false**, zapoznaj się z instrukcjami i linkiem do pobrania w blogu usługi Azure post [VM Agent i Extensions-część 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Jeśli Agent maszyny wirtualnej jest zainstalowany, Uruchom te polecenia.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Następne kroki
Uruchomienie agenta po jego zainstalowaniu może potrwać kilka minut. Po tym celu należy aktywować głębokie zabezpieczenia na maszynie wirtualnej, aby można było nimi zarządzać za pomocą głębokiego Menedżera zabezpieczeń. Dodatkowe instrukcje można znaleźć w następujących artykułach:

* Artykuł dotyczący trendu dotyczącego tego rozwiązania, [natychmiastowego zabezpieczenia w chmurze dla Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* [Przykładowy skrypt programu Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) służący do konfigurowania maszyny wirtualnej
* [Instrukcje](https://go.microsoft.com/fwlink/?LinkId=404099) dotyczące przykładu

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]

[Rozszerzenia i funkcje maszyny wirtualnej platformy Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak zalogować się do maszyny wirtualnej z systemem Windows Server]:../windows/classic/connect-logon.md
[Rozszerzenia i funkcje maszyny wirtualnej platformy Azure]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
