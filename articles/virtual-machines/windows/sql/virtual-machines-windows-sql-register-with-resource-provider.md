---
title: Rejestrowanie przy użyciu dostawcy zasobów maszyny wirtualnej SQL
description: Zarejestruj maszynę wirtualną SQL Server platformy Azure za pomocą dostawcy zasobów maszyny wirtualnej SQL, aby włączyć funkcje dla SQL Server maszyn wirtualnych wdrożonych poza portalem Azure Marketplace, a także zapewnić zgodność i zwiększyć możliwości zarządzania.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b2430b5135a5d3f7ad1f9ef0bd17d9149bf48ee
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793457"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Rejestrowanie SQL Server maszyny wirtualnej na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL

W tym artykule opisano sposób rejestrowania maszyny wirtualnej SQL Server na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Rejestracja przy użyciu dostawcy zasobów powoduje utworzenie _zasobu_ **maszyny wirtualnej SQL** w ramach subskrypcji, czyli oddzielnego zasobu z zasobu maszyny wirtualnej. Wyrejestrowanie maszyny wirtualnej SQL Server od dostawcy zasobów spowoduje usunięcie _zasobu_ **maszyny wirtualnej SQL** , ale nie spowoduje porzucenia rzeczywistej maszyny wirtualnej. 

Wdrożenie SQL Server maszyny wirtualnej w portalu Azure Marketplace za pomocą Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server przy użyciu dostawcy zasobów. Jednak w przypadku wybrania opcji samodzielnego zainstalowania SQL Server na maszynie wirtualnej platformy Azure lub aprowizacji maszyny wirtualnej platformy Azure na podstawie niestandardowego wirtualnego dysku twardego należy zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów dla:

- **Zalety funkcji**: Rejestracja SQL SERVEREJ maszyny wirtualnej przy użyciu dostawcy zasobów odblokowuje [Automatyczne stosowanie poprawek](virtual-machines-windows-sql-automated-patching.md), [Automatyczne tworzenie kopii zapasowej](virtual-machines-windows-sql-automated-backup-v2.md), a także możliwości monitorowania i zarządzania. Umożliwia również odblokowanie [licencjonowania](virtual-machines-windows-sql-ahb.md) i [wersji](virtual-machines-windows-sql-change-edition.md) . Wcześniej te funkcje były dostępne tylko w przypadku SQL Server obrazów maszyn wirtualnych wdrożonych w portalu Azure Marketplace. 

- **Zgodność**: Rejestracja za pomocą dostawcy zasobów maszyny wirtualnej SQL oferuje uproszczoną metodę spełnienia wymagania dotyczącego powiadomienia firmy Microsoft o włączeniu korzyść użycia hybrydowego platformy Azure, zgodnie z opisem w postanowień dotyczących produktu. Ten proces wyklucza konieczność zarządzania formularzami rejestracji licencjonowania dla każdego zasobu.  

- **Bezpłatne zarządzanie**: Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL we wszystkich trzech trybach zarządzania jest całkowicie bezpłatna. Dostawca zasobów nie wiąże się z dodatkowymi kosztami ani z zmianami trybów zarządzania. 

- **Uproszczone zarządzanie licencjami**: Rejestracja za pomocą dostawcy zasobów maszyny wirtualnej SQL upraszcza zarządzanie licencjami SQL Server i umożliwia szybkie identyfikowanie SQL Server maszyn wirtualnych z korzyść użycia hybrydowego platformy Azure włączony przy użyciu [Azure Portal](virtual-machines-windows-sql-manage-portal.md), AZ CLI lub PowerShell: 

   # <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Aby można było korzystać z dostawcy zasobów maszyny wirtualnej SQL, należy najpierw [zarejestrować swoją subskrypcję u dostawcy zasobów](#register-subscription-with-rp), co daje dostawcy zasobów możliwość tworzenia zasobów w ramach tej konkretnej subskrypcji.

Aby uzyskać więcej informacji na temat korzyści z używania dostawcy zasobów maszyny wirtualnej SQL, Obejrzyj ten [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) wideo: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Korzystanie z dostawcy zasobów maszyny wirtualnej SQL podczas samoinstalowania SQL Server na platformie Azure — wideo w witrynie Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Wymagania wstępne

Aby zarejestrować SQL Server maszynę wirtualną przy użyciu dostawcy zasobów, musisz: 

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/).
- Model zasobów platformy Azure [SQL Server maszynę wirtualną](virtual-machines-windows-portal-sql-server-provision.md) wdrożoną w chmurze publicznej. 
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Tryby zarządzania

Jeśli [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) nie zostało jeszcze zainstalowane, Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL automatycznie zainstaluje rozszerzenie SQL Server IaaS w jednym z trzech trybów zarządzania określonych podczas procesu rejestracji. Nieokreślanie trybu zarządzania spowoduje zainstalowanie rozszerzenia SQL IaaS w trybie zarządzania pełnego.  

Jeśli rozszerzenie SQL IaaS zostało już zainstalowane ręcznie, to jest już w trybie zarządzania pełnego, a Rejestracja przy użyciu dostawcy zasobów w trybie pełnym nie spowoduje ponownego uruchomienia usługi SQL Server.

Trzy tryby zarządzania są następujące:

- Tryb **uproszczony** nie wymaga ponownego uruchomienia SQL Server, ale obsługuje tylko Zmienianie typu licencji i wydania SQL Server. Użyj tej opcji dla SQL Server maszyn wirtualnych z wieloma wystąpieniami lub biorąc udział w wystąpieniu klastra trybu failover (FCI). Nie ma to wpływu na pamięć ani procesor CPU podczas korzystania z trybu uproszczonego i nie ma skojarzonego kosztu. Zalecane jest, aby najpierw zarejestrować maszynę wirtualną SQL Server w trybie uproszczonym, a następnie przeprowadzić uaktualnienie do trybu pełnego w trakcie zaplanowanego okna obsługi.  

- Tryb **pełny** zapewnia wszystkie funkcje, ale wymaga ponownego uruchomienia uprawnień SQL Server i administratora systemu. Jest to opcja instalowana domyślnie podczas ręcznego instalowania rozszerzenia SQL IaaS. Służy do zarządzania maszyną wirtualną SQL Server przy użyciu jednego wystąpienia. Tryb pełny instaluje dwie usługi systemu Windows, które mają minimalny wpływ na pamięć i procesor CPU — można je monitorować za pomocą Menedżera zadań. Korzystanie z trybu pełnego zarządzania nie wiąże się z żadnymi kosztami. 

- Tryb **noagent** jest przeznaczony dla SQL Server 2008 i SQL Server 2008 R2 zainstalowanych w systemie Windows Server 2008. Użycie trybu noagent nie ma wpływu na pamięć ani procesor CPU. Nie jest dostępny żaden koszt związany z korzystaniem z trybu zarządzania bez agenta. 

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą programu PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Zarejestruj subskrypcję za pomocą RP

Aby zarejestrować maszynę wirtualną SQL Server przy użyciu dostawcy zasobów maszyny wirtualnej SQL, należy najpierw zarejestrować subskrypcję u dostawcy zasobów. Daje to dostawcy zasobów maszyny wirtualnej SQL możliwość tworzenia zasobów w ramach subskrypcji.  Można to zrobić za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Otwórz Azure Portal i przejdź do pozycji **wszystkie usługi**. 
1. Przejdź do pozycji **subskrypcje** i wybierz swoją subskrypcję.  
1. Na stronie **subskrypcje** przejdź do pozycji **dostawcy zasobów**. 
1. Wprowadź **SQL** w filtrze, aby wyświetlić dostawców zasobów związanych z programem SQL. 
1. Wybierz pozycję **zarejestruj**, **zarejestruj ponownie**lub **Wyrejestruj** dostawcę **Microsoft. SqlVirtualMachine** , w zależności od żądanej akcji. 

![Modyfikowanie dostawcy](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Wiersz polecenia

Zarejestruj dostawcę zasobów maszyny wirtualnej SQL w ramach subskrypcji platformy Azure przy użyciu polecenia AZ CLI lub PowerShell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Rejestrowanie maszyny wirtualnej SQL z usługą RP 

### <a name="lightweight-management-mode"></a>Uproszczony tryb zarządzania

Jeśli na maszynie wirtualnej nie zainstalowano [rozszerzenia agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) , zaleca się zarejestrowanie się w trybie uproszczonym dostawcy zasobów maszyny wirtualnej SQL. Spowoduje to zainstalowanie rozszerzenia SQL IaaS w [trybie uproszczonym](#management-modes) i uniemożliwienie ponownego uruchamiania usługi SQL Server. Następnie można przeprowadzić uaktualnienie do trybu pełnego w dowolnym momencie, ale spowoduje to ponowne uruchomienie usługi SQL Server, dlatego zaleca się zaczekanie do czasu zaplanowanego okna obsługi. Musisz podać typ SQL Server licencji jako płatność zgodnie z rzeczywistym użyciem (`PAYG`) do płacenia za użycie lub Korzyść użycia hybrydowego platformy Azure (`AHUB`), aby korzystać z własnej licencji.

Wystąpienia klastra trybu failover i wdrożenia z obsługą wiele wystąpień mogą być rejestrowane tylko w trybie uproszczonym dostawcy zasobów maszyny wirtualnej SQL. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Zarejestruj SQL Server maszynę wirtualną w trybie uproszczonym za pomocą polecenia AZ CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

Rejestrowanie SQL Server maszyny wirtualnej w trybie uproszczonym przy użyciu programu PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Tryb pełnego zarządzania


Jeśli rozszerzenie programu SQL IaaS zostało już zainstalowane na maszynie wirtualnej ręcznie, można zarejestrować maszynę wirtualną SQL Server w trybie pełnym bez ponownego uruchamiania usługi SQL Server. **Jeśli jednak rozszerzenie SQL IaaS nie zostało zainstalowane, zarejestrowanie w trybie pełnym spowoduje zainstalowanie rozszerzenia SQL IaaS w trybie pełnym i ponowne uruchomienie usługi SQL Server. Należy zachować ostrożność.**

Poniżej znajduje się fragment kodu do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie pełnym. Aby zarejestrować się w trybie pełnego zarządzania, użyj następującego polecenia programu PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```


### <a name="noagent-management-mode"></a>Tryb zarządzania bez agenta 

SQL Server 2008 i 2008 R2 zainstalowane w systemie Windows Server 2008 można zarejestrować za pomocą dostawcy zasobów maszyny wirtualnej SQL w [trybie noagent](#management-modes). Ta opcja zapewnia zgodność i umożliwia monitorowanie SQL Server maszyny wirtualnej w Azure Portal z ograniczoną funkcjonalnością.

Określ `AHUB` lub `PAYG` jako **Sqllicensetype**, a `SQL2008-WS2008` lub `SQL2008R2-WS2008` jako **sqlImageOffer**. 

Aby zarejestrować wystąpienie SQL Server 2008 lub 2008 R2 w wystąpieniu systemu Windows Server 2008, użyj następującego polecenia AZ CLI i PowerShell Code fragment: 


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Zarejestruj SQL Server maszynę wirtualną w trybie noagent przy użyciu polecenia AZ CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

Zarejestruj SQL Server maszynę wirtualną w trybie noagent przy użyciu programu PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Uaktualnianie do trybu pełnego zarządzania 

SQL Server maszyny wirtualne, na których zainstalowano *uproszczone* rozszerzenie IaaS, mogą uaktualnić tryb do _pełnego_ przy użyciu Azure Portal, AZ CLI lub PowerShell. SQL Server maszyny wirtualne w trybie _noagent_ można uaktualnić do wersji _pełnej_ po uaktualnieniu systemu operacyjnego do wersji Windows 2008 R2 lub nowszej. Nie jest możliwe przeprowadzenie obniżenia — w tym celu należy [wyrejestrować](#unregister-vm-from-rp) SQL Server maszynę wirtualną z dostawcy zasobów maszyny wirtualnej SQL. Spowoduje to usunięcie _zasobu_ **maszyny wirtualnej SQL** , ale nie spowoduje usunięcia rzeczywistej maszyny wirtualnej. 

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą programu PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Aby uaktualnić tryb agenta do pełnej: 


### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do zasobu [maszyny wirtualnej SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Wybierz swoją SQL Server maszynę wirtualną, a następnie wybierz pozycję **Przegląd**. 
1. W przypadku SQL Server maszyn wirtualnych z trybem noagent lub Lightweight IaaS wybierz opcję **jedyne typy licencji i aktualizacje wersji są dostępne w komunikacie rozszerzenia SQL IaaS** .

   ![Wybory dotyczące zmiany trybu z portalu](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Zaznacz pole wyboru **Zgadzam się na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej** , a następnie wybierz pozycję **Potwierdź** , aby uaktualnić tryb IaaS do pełnego. 

    ![Pole wyboru, aby wyrazić zgodę na ponowne uruchomienie usługi SQL Server na maszynie wirtualnej](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Wiersz polecenia

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Uruchom następujący fragment kodu polecenia AZ CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

Uruchom następujący fragment kodu programu PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Update to full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
     -LicenseType PAYG -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Sprawdź stan rejestracji
Możesz sprawdzić, czy maszyna wirtualna SQL Server została już zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
1. Przejdź do [SQL Server maszyn wirtualnych](virtual-machines-windows-sql-manage-portal.md).
1. Z listy wybierz maszynę wirtualną z SQL Server. Jeśli maszyna wirtualna w SQL Server nie jest wymieniona w tym miejscu, prawdopodobnie nie została zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 
1. Wyświetl wartość w obszarze **stan**. Jeśli **stan** ma wartość **powodzenie**, maszyna wirtualna SQL Server została pomyślnie zarejestrowana w dostawcy zasobów maszyny wirtualnej SQL. 

![Weryfikowanie stanu przy użyciu rejestracji jednostki UZALEŻNIONej SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Wiersz polecenia

Sprawdź bieżące SQL Server stanu rejestracji maszyny wirtualnej za pomocą polecenia AZ CLI lub PowerShell. Jeśli rejestracja zakończyła się pomyślnie, `ProvisioningState` będzie wyświetlana `Succeeded`. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Błąd oznacza, że maszyna wirtualna SQL Server nie została zarejestrowana u dostawcy zasobów. 


## <a name="unregister-vm-from-rp"></a>Wyrejestruj maszynę wirtualną z jednostki UZALEŻNIONej

Aby wyrejestrować maszynę wirtualną SQL Server za pomocą dostawcy zasobów maszyny wirtualnej SQL, Usuń *zasób* maszyny wirtualnej SQL przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure. Usunięcie *zasobu* maszyny wirtualnej SQL nie powoduje usunięcia maszyny wirtualnej SQL Server. Należy jednak ostrożnie i postępować zgodnie z krokami, ponieważ można przypadkowo usunąć maszynę wirtualną podczas próby usunięcia *zasobu*. 

Wyrejestrowanie maszyny wirtualnej SQL przy użyciu dostawcy zasobów maszyny wirtualnej SQL jest niezbędne do obniżenia poziomu trybu zarządzania z pełnego. 

### <a name="azure-portal"></a>Azure Portal

Aby wyrejestrować SQL Server maszynę wirtualną za pomocą dostawcy zasobów przy użyciu Azure Portal, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Przejdź do SQL Server zasobu maszyny wirtualnej. 
  
   ![Zasób maszyn wirtualnych SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Wybierz pozycję **Usuń**. 

   ![Usuń dostawcę zasobów maszyny wirtualnej SQL](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Wpisz nazwę maszyny wirtualnej SQL i **Usuń zaznaczenie pola wyboru obok maszyny wirtualnej**.

   ![Usuń dostawcę zasobów maszyny wirtualnej SQL](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nie można wyczyścić pola wyboru obok nazwy maszyny wirtualnej. całkowicie *usunie* maszynę wirtualną. Wyczyść pole wyboru, aby wyrejestrować SQL Server MASZYNę wirtualną z dostawcy zasobów, ale *nie usunąć rzeczywistej maszyny wirtualnej*. 

1. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie *zasobu*maszyny wirtualnej SQL, a nie maszyny wirtualnej SQL Server. 

### <a name="command-line"></a>Wiersz polecenia

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby wyrejestrować SQL Serverą maszynę wirtualną z dostawcy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Spowoduje to usunięcie SQL Server *zasobu* maszyny wirtualnej, ale nie spowoduje usunięcia maszyny wirtualnej. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyrejestrować SQL Serverą maszynę wirtualną z dostawcy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm). Spowoduje to usunięcie SQL Server *zasobu* maszyny wirtualnej, ale nie spowoduje usunięcia maszyny wirtualnej. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Ograniczenia

Dostawca zasobów maszyny wirtualnej SQL obsługuje tylko:
- SQL Server maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. SQL Server maszyny wirtualne wdrożone za pośrednictwem modelu klasycznego nie są obsługiwane. 
- SQL Server maszyny wirtualne wdrożone w chmurze publicznej. Wdrożenia w chmurze prywatnej lub publicznej nie są obsługiwane. 


## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**Czy należy zarejestrować moją SQL Serverą maszynę wirtualną z poziomu obrazu SQL Server w witrynie Azure Marketplace?**

Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne obsługiwane przez obrazy SQL Server w portalu Azure Marketplace. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL jest wymagana tylko wtedy, gdy maszyna wirtualna *nie* została zainicjowana z poziomu obrazów SQL Server w portalu Azure Marketplace i SQL Server została zainstalowana samoobsługowo.

**Czy dostawca zasobów maszyny wirtualnej SQL jest dostępny dla wszystkich klientów?** 

Tak. Klienci powinni rejestrować SQL Server maszyny wirtualne przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli nie korzystają z obrazu SQL Server z witryny Azure Marketplace, a zamiast tego są instalowane samodzielnie SQL Server lub do własnego wirtualnego dysku twardego. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, Umowa Enterprise i dostawca rozwiązań w chmurze) mogą być rejestrowane przez dostawcę zasobów maszyny wirtualnej SQL.

**Czy należy zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, jeśli SQL Server na maszynie wirtualnej SQL Server jest już zainstalowane rozszerzenie IaaS?**

Jeśli maszyna wirtualna SQL Server jest samodzielna i nie została zainicjowana z poziomu obrazów SQL Server w portalu Azure Marketplace, należy zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, nawet jeśli zainstalowano rozszerzenie SQL Server IaaS. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL tworzy nowy zasób typu Microsoft. SqlVirtualMachines. Zainstalowanie rozszerzenia IaaS SQL Server nie powoduje utworzenia tego zasobu.

**Jaki jest domyślny tryb zarządzania podczas rejestrowania się w dostawcy zasobów maszyny wirtualnej SQL?**

Domyślny tryb zarządzania podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL jest *pełny*. Jeśli właściwość Zarządzanie SQL Server nie jest ustawiona podczas rejestrowania u dostawcy zasobów maszyny wirtualnej SQL, tryb zostanie ustawiony jako pełna łatwość zarządzania i usługa SQL Server zostanie uruchomiona ponownie. Zaleca się, aby najpierw zarejestrować się w trybie uproszczonym przy użyciu dostawcy zasobów maszyny wirtualnej SQL, a następnie przeprowadzić uaktualnienie do pełnej wersji w oknie obsługi. 

**Jakie wymagania wstępne należy zarejestrować u dostawcy zasobów maszyny wirtualnej SQL?**

Nie ma wymagań wstępnych do zarejestrowania z dostawcą zasobów maszyny wirtualnej SQL w trybie uproszczonym lub bez agenta. Wymagania wstępne dotyczące rejestrowania się w ramach dostawcy zasobów maszyny wirtualnej SQL w trybie pełnym mają zainstalowane SQL Server rozszerzenie IaaS na maszynie wirtualnej, ponieważ w przeciwnym razie usługa SQL Server zostanie uruchomiona ponownie. 

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia SQL Server IaaS?**

Tak, możesz zarejestrować się w trybie uproszczonego zarządzania przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli na maszynie wirtualnej nie zainstalowano rozszerzenia IaaS SQL Server. W trybie uproszczonym dostawca zasobów maszyny wirtualnej SQL będzie używać aplikacji konsolowej do weryfikowania wersji i wydania wystąpienia SQL Server. 

Domyślny tryb zarządzania SQL podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL jest _pełny_. Jeśli właściwość SQL Management nie jest ustawiona podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL, tryb zostanie ustawiony jako pełny zarządzanie. Zaleca się, aby najpierw zarejestrować się w trybie uproszczonym przy użyciu dostawcy zasobów maszyny wirtualnej SQL, a następnie przeprowadzić uaktualnienie do pełnej wersji w oknie obsługi. 

**Czy w ramach dostawcy zasobów maszyny wirtualnej SQL zostanie zainstalowany agent na mojej maszynie wirtualnej?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL spowoduje utworzenie nowego zasobu metadanych. Nie zainstaluje agenta na maszynie wirtualnej.

SQL Server rozszerzenia IaaS jest wymagany tylko w celu umożliwienia pełnego zarządzania. Uaktualnianie trybu zarządzania z wersji uproszczonej do pełnej spowoduje zainstalowanie rozszerzenia SQL Server IaaS i ponowne uruchomienie SQL Server.

**Czy zarejestrowano SQL Server ponownego uruchomienia dostawcy zasobów maszyny wirtualnej SQL Server na mojej maszynie wirtualnej?**

Jest to zależne od trybu określonego podczas rejestracji. W przypadku określenia trybu uproszczonego lub noagent usługa SQL Server nie zostanie uruchomiona ponownie. Jednak określenie trybu zarządzania jako pełny lub pozostawienie pustego trybu zarządzania spowoduje zainstalowanie rozszerzenia SQL IaaS w trybie pełnego zarządzania, co spowoduje ponowne uruchomienie usługi SQL Server. 

**Jaka jest różnica między trybami zarządzania uproszczonego i bez agenta podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL?** 

Tryb zarządzania bez agenta jest dostępny tylko dla SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. Jest to jedyny dostępny tryb zarządzania dla tych wersji. W przypadku wszystkich innych wersji SQL Server dwa dostępne tryby zarządzania są lekkie i pełne. 

Tryb No-Agent wymaga, aby właściwości SQL Server wersji i wydania zostały ustawione przez klienta. Tryb uproszczony wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i wydania wystąpienia SQL Server.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL bez określenia typu licencji SQL Server?**

Nie. Typ licencji SQL Server nie jest właściwością opcjonalną podczas rejestrowania z dostawcą zasobów maszyny wirtualnej SQL. Musisz ustawić typ licencji SQL Server jako płatność zgodnie z rzeczywistym użyciem lub Korzyść użycia hybrydowego platformy Azure podczas rejestrowania się u dostawcy zasobów maszyny wirtualnej SQL we wszystkich trybach zarządzania (bez agenta, lekkich i pełnych).

**Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu bez agenta do trybu pełnego?**

Nie. Uaktualnianie trybu zarządzania do wersji pełnej lub lekkiej nie jest dostępne w trybie bez agenta. Jest to ograniczenie techniczne systemu Windows Server 2008. Musisz najpierw uaktualnić system operacyjny do wersji Windows Server 2008 R2 lub nowszej, a następnie można przeprowadzić uaktualnienie do trybu pełnego zarządzania. 

**Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu uproszczonego do trybu pełnego?**

Tak. Uaktualnianie trybu zarządzania z lekkich do pełnych jest obsługiwane za pośrednictwem programu PowerShell lub Azure Portal. Wymaga ponownego uruchomienia usługi SQL Server.

**Czy mogę obniżyć SQL Server rozszerzenie IaaS z trybu pełnego do poziomu bez agenta lub Lightweight Management Mode?**

Nie. Obniżenie poziomu trybu zarządzania rozszerzeniami SQL Server IaaS nie jest obsługiwane. Trybu zarządzania nie można zmienić z trybu pełnego na lekki lub bez agenta i nie można go zmienić z trybu uproszczonego na tryb bez agenta. 

Aby zmienić tryb zarządzania z pełnego zarządzania, [Wyrejestruj](#unregister-vm-from-rp) maszynę wirtualną SQL Server z poziomu dostawcy zasobów SQL Server, usuwając *zasób* SQL Server i ponownie zarejestruj ponownie SQL Server maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL w innym trybie zarządzania.

**Czy można zarejestrować się u dostawcy zasobów maszyny wirtualnej SQL z Azure Portal?**

Nie. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL nie jest dostępna w Azure Portal. Rejestracja przy użyciu dostawcy zasobów maszyny wirtualnej SQL jest obsługiwana tylko przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

**Czy można zarejestrować maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL przed zainstalowaniem SQL Server?**

Nie. Maszyna wirtualna powinna mieć co najmniej jedno wystąpienie SQL Server, aby można było zarejestrować się w dostawcy zasobów maszyny wirtualnej SQL. Jeśli na maszynie wirtualnej nie ma SQL Server wystąpienia, nowy zasób Microsoft. SqlVirtualMachine będzie w stanie niepowodzenia.

**Czy można zarejestrować maszynę wirtualną przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli istnieje wiele wystąpień SQL Server?**

Tak. Dostawca zasobów maszyny wirtualnej SQL będzie rejestrował tylko jedno wystąpienie SQL Server. Dostawca zasobów maszyny wirtualnej SQL zarejestruje domyślne wystąpienie SQL Server w przypadku wielu wystąpień. Jeśli nie ma wystąpienia domyślnego, obsługiwane jest tylko rejestrowanie w trybie uproszczonym. Aby można było uaktualnić tryb uproszczony do pełnego zarządzania, należy podać domyślne wystąpienie SQL Server lub maszyna wirtualna powinna mieć tylko jedno nazwane wystąpienie SQL Server.

**Czy można zarejestrować SQL Server wystąpienie klastra trybu failover z dostawcą zasobów maszyny wirtualnej SQL?**

Tak. SQL Server wystąpienia klastra trybu failover na maszynie wirtualnej platformy Azure można zarejestrować w trybie uproszczonym przy użyciu dostawcy zasobów maszyny wirtualnej SQL. Niemniej jednak SQL Server wystąpienia klastra trybu failover nie można uaktualnić do trybu pełnego zarządzania.

**Czy można zarejestrować moją maszynę wirtualną u dostawcy zasobów maszyny wirtualnej SQL, jeśli jest skonfigurowana zawsze włączona Grupa dostępności?**

Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia SQL Server na maszynie wirtualnej platformy Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL, jeśli bierzesz udział w konfiguracji grupy dostępności zawsze włączone.

**Jaki jest koszt rejestrowania w ramach dostawcy zasobów maszyny wirtualnej SQL, czy z uaktualnieniem do trybu pełnego zarządzania?**
Brak. Nie jest naliczana opłata związana z rejestracją u dostawcy zasobów maszyny wirtualnej SQL lub z użyciem jednego z trzech trybów zarządzania. Zarządzanie maszyną wirtualną SQL Server przy użyciu dostawcy zasobów jest całkowicie bezpłatne. 

**Jaki jest wpływ na wydajność korzystania z różnych trybów zarządzania?**
Nie ma to wpływu na użycie trybu *noagent* i *uproszczonego* zarządzania. W przypadku korzystania z trybu *pełnego* zarządzania z dwóch usług, które są zainstalowane w systemie operacyjnym, ma minimalny wpływ. Mogą one być monitorowane za pośrednictwem Menedżera zadań i widoczne w wbudowanej konsoli usług systemu Windows. 

Nazwy następujących usług:
- `SqlIaaSExtensionQuery` (nazwa wyświetlana `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (nazwa wyświetlana `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
