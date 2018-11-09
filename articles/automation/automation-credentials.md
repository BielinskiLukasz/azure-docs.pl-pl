---
title: Zasoby poświadczeń w usłudze Azure Automation
description: Zasoby poświadczeń w usłudze Azure Automation zawierają poświadczenia zabezpieczeń, które może służyć do uwierzytelniania w zasobach uzyskiwał dostęp do elementu runbook lub konfiguracji DSC. W tym artykule opisano sposób tworzenia zasobów poświadczeń i używać ich w elementu runbook lub konfiguracji DSC.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 07452a97ba16d7745d48e210dd90ce077be64683
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233443"
---
# <a name="credential-assets-in-azure-automation"></a>Zasoby poświadczeń w usłudze Azure Automation

Zasób poświadczenia usługi Automation przechowuje obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Konfiguracje elementów Runbook i DSC może używać poleceń cmdlet, które akceptuje obiekt PSCredential uwierzytelniania lub może ich wyodrębnić nazwy użytkownika i hasła obiektu PSCredential, aby zapewnić do niektórych aplikacji lub usługi wymagającej uwierzytelniania. Właściwości dla poświadczenia są bezpiecznie przechowywane w usłudze Azure Automation i dostępne w elemencie runbook lub konfiguracji DSC przy użyciu [Get-AutomationPSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) działania.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Bezpiecznych zasobów w usłudze Azure Automation obejmują poświadczeń, certyfikatów, połączeń i szyfrowane zmienne. Te zasoby są zaszyfrowane i przechowywane w usłudze Azure Automation za pomocą Unikatowy klucz, który jest generowany dla każdego konta usługi automation. Ten klucz jest przechowywany w usłudze Key Vault. Przed zapisaniem zabezpieczonym zasobem, klucz jest ładowane z usługi Key Vault i następnie używany do szyfrowania elementu zawartości.

## <a name="azure-classic-powershell-cmdlets"></a>Polecenia cmdlet programu Azure PowerShell klasycznego

Polecenia cmdlet w poniższej tabeli służą do tworzenia i obsługi zasobów poświadczeń usługi automation przy użyciu programu Windows PowerShell.  Są dostarczane jako część systemu [modułu Azure PowerShell](/powershell/azure/overview) który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Pobiera informacje o zasób poświadczeń. Tylko można pobrać poświadczeń, sama z **Get-AutomationPSCredential** działania. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Tworzy nowe poświadczenie automatyzacji. |
| [Remove - AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Usuwa poświadczenie automatyzacji. |
| [Set - AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Ustawia właściwości istniejącego poświadczenia usługi Automation. |

## <a name="azurerm-powershell-cmdlets"></a>Polecenia cmdlet usługi AzureRM PowerShell

Dla usługi AzureRM poleceń cmdlet w poniższej tabeli służą do tworzenia i obsługi zasobów poświadczeń usługi automation przy użyciu programu Windows PowerShell.  Są dostarczane jako część systemu [modułu z wersjami AzureRM.Automation](/powershell/azure/overview) który jest dostępny do użycia w elementach runbook automatyzacji i konfiguracji DSC.

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Pobiera informacje o zasób poświadczeń.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Tworzy nowe poświadczenie automatyzacji. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Usuwa poświadczenie automatyzacji. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Ustawia właściwości istniejącego poświadczenia usługi Automation. |

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do dostępu do poświadczeń w elemencie runbook i konfiguracjach DSC.

| Działania | Opis |
|:--- |:--- |
| Get-AutomationPSCredential |Pobiera poświadczenia do użycia w elemencie runbook lub konfiguracji DSC. Zwraca [System.Management.Automation.PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential) obiektu. |

> [!NOTE]
> Należy unikać używania zmiennych w parametrze Name Get-AutomationPSCredential, ponieważ może to skomplikować wykrywanie zależności między elementami runbook lub konfiguracji DSC i poświadczeń zasobów w czasie projektowania.

## <a name="python2-functions"></a>Funkcje Python2

Funkcja w poniższej tabeli umożliwia dostęp do poświadczeń w element runbook programu Python2.

| Funkcja | Opis |
|:---|:---|
| automationassets.get_automation_credential | Pobiera informacje o zasób poświadczeń. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł "automationassets" w górnej części elementu runbook języka Python.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego elementu zawartości poświadczenia

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Aby utworzyć nowy zasób poświadczeń przy użyciu witryny Azure portal

1. Na koncie usługi automation kliknij **zasoby** , aby otworzyć **zasoby** bloku.
2. Kliknij przycisk **poświadczenia** , aby otworzyć **poświadczenia** bloku.
3. Kliknij przycisk **Dodawanie poświadczeń** w górnej części bloku.
4. Wypełnij formularz, a następnie kliknij przycisk **Utwórz** można zapisać nowe poświadczenie.

> [!NOTE]
> Konta użytkowników, które korzystają z uwierzytelniania Multi-Factor Authentication nie są obsługiwane do użytku w usłudze Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Aby utworzyć nowy zasób poświadczeń przy użyciu programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak utworzyć nowe poświadczenie automatyzacji. Obiekt PSCredential jest najpierw utworzone za pomocą nazwy i hasła i następnie użyty do utworzenia zasobu poświadczeń. Alternatywnie można użyć **Get-Credential** polecenia cmdlet, aby wyświetlić monit o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Używanie poświadczenia programu PowerShell

Pobierz zasób poświadczeń w elemencie runbook lub konfiguracji DSC przy użyciu **Get-AutomationPSCredential** działania. Spowoduje to zwrócenie [obiektu PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) , za pomocą działania lub polecenia cmdlet, które wymaga, aby parametr PSCredential. Możesz również pobrać właściwości obiektu poświadczeń można użyć pojedynczo. Obiekt ma właściwość dla nazwy użytkownika i hasła bezpiecznego lub użyć **GetNetworkCredential** metodę, aby zwrócić [NetworkCredential](https://msdn.microsoft.com/library/system.net.networkcredential.aspx) obiekt, który zapewni niezabezpieczonej wersji hasło.

### <a name="textual-runbook-sample"></a>Przykład tekstowy element runbook

Następujące przykładowe polecenia pokazują, jak używać poświadczenia programu PowerShell w elemencie runbook. W tym przykładzie poświadczenie jest pobierany i jego nazwę użytkownika i hasło przypisane do zmiennych.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Przykładowy graficzny element runbook

Możesz dodać **Get-AutomationPSCredential** działanie graficzny element runbook, klikając prawym przyciskiem myszy na poświadczeń w okienku Biblioteka edytor graficzny i wybierając polecenie **Dodaj do kanwy**.

![Dodawanie poświadczeń do kanwy](media/automation-credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład przy użyciu poświadczeń w graficznego elementu runbook.  W tym przypadku jest używany do uwierzytelniania elementów runbook w zasobach platformy Azure zgodnie z opisem w [uwierzytelniać elementy Runbook przy użyciu konta użytkownika usługi Azure AD](automation-create-aduser-account.md).  Pierwsze działanie pobiera poświadczenia, które ma dostęp do subskrypcji platformy Azure.  **Add-AzureAccount** działanie następnie używa tego poświadczenia do uwierzytelniania żadnych działań, które po nim.  A [linku potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest już dostępny, ponieważ **Get-AutomationPSCredential** oczekuje pojedynczego obiektu.  

![Dodawanie poświadczeń do kanwy](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Używanie poświadczenia programu PowerShell w DSC

Podczas konfiguracji DSC w usłudze Azure Automation mogą odwoływać się do zasobów poświadczeń przy użyciu **Get-AutomationPSCredential**, zasobów poświadczeń mogą również być przekazane za pośrednictwem parametrów, w razie potrzeby. Aby uzyskać więcej informacji, zobacz [kompilowanie konfiguracji w usłudze Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Przy użyciu poświadczeń w Python2

Poniższy przykład pokazuje przykład uzyskiwania dostępu do poświadczeń w elementami runbook programu Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o łączach w tworzenia elementów graficznych, zobacz [łącza w tworzenie graficzne](automation-graphical-authoring-intro.md#links-and-workflow)
* Aby poznać różnych metod uwierzytelniania za pomocą usługi Automation, zobacz [zabezpieczeń usługi Azure Automation](automation-security-overview.md)
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz artykuł [My first graphical runbook](automation-first-runbook-graphical.md) (Mój pierwszy graficzny element Runbook).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell). 
* Aby rozpocząć pracę z elementami runbook programu Python2, zobacz [Mój pierwszy element runbook programu Python2](automation-first-runbook-textual-python2.md) 

