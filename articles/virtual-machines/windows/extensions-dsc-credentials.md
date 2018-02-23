---
title: "Przekazywania poświadczeń na platformie Azure przy użyciu konfiguracji żądanego stanu | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak bezpiecznie przekazywania poświadczeń do maszyn wirtualnych platformy Azure przy użyciu konfiguracji żądanego stanu środowiska PowerShell (DSC)."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-resource-manager
keywords: DSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/17/2018
ms.author: zachal,migreene
ms.openlocfilehash: a0a565c0bb7e17315c7b0475f3213b620a3e2d6c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Przekazywania poświadczeń do obsługi Azure DSCExtension
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

W tym artykule omówiono rozszerzenia konfiguracji żądanego stanu (DSC) dla platformy Azure. Omówienie obsługi rozszerzenia DSC, zobacz [wprowadzenie do obsługi rozszerzenia konfiguracji żądanego stanu Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="pass-in-credentials"></a>Podaj poświadczenia

W trakcie procesu konfiguracji może skonfigurować konta użytkowników, dostęp do usług, lub zainstaluj program w kontekście użytkownika. Aby wykonać te czynności, musisz podać poświadczenia.

DSC służy do ustawiania konfiguracji sparametryzowana. W konfiguracji sparametryzowane poświadczenia są przekazywane do konfiguracji i bezpiecznie przechowywane w plikach MOF. Obsługi rozszerzenie Azure ułatwia zarządzanie poświadczeniami, zapewniając automatyczne zarządzanie certyfikatami.

Poniższy skrypt konfiguracji DSC tworzy konto użytkownika lokalnego z określonym hasłem:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Należy uwzględnić **localhost węzła** jako część konfiguracji. W szczególności szuka obsługi rozszerzenia **localhost węzła** instrukcji. W przypadku braku tej instrukcji nie działają następujące kroki. Należy również uwzględnić typecast **[PsCredential]**. Określonego typu wyzwala rozszerzenia, aby zaszyfrować poświadczenia.

Aby opublikować ten skrypt do magazynu obiektów Blob platformy Azure:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Aby ustawić rozszerzenia usługi Konfiguracja DSC Azure i podaj poświadczenie:

```powershell
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```

## <a name="how-a-credential-is-secured"></a>Jak chronione są poświadczenia

Uruchomienie tego kodu wyświetla monit o podanie poświadczeń. Po podano poświadczenia, krótko jest przechowywany w pamięci. Jeśli jest publikowany poświadczeń przy użyciu **AzureVmDscExtension zestaw** polecenia cmdlet, poświadczenia są przesyłane za pośrednictwem protokołu HTTPS do maszyny Wirtualnej. Na maszynie wirtualnej Azure przechowuje poświadczenia szyfrowane na dysku przy użyciu lokalnego certyfikatu maszyny Wirtualnej. Poświadczenie krótko zostaną odszyfrowane w pamięci, a następnie ponownie jest szyfrowany do przekazania go do usługi Konfiguracja DSC.

Ten proces jest inna niż [za pomocą bezpiecznej konfiguracji bez obsługi rozszerzenia](https://msdn.microsoft.com/powershell/dsc/securemof). Środowisko Azure udostępnia sposób przekazują dane konfiguracji w bezpieczny sposób za pomocą certyfikatów. Korzystając z procedury obsługi rozszerzenia DSC, nie musisz podać **$CertificatePath** lub **$CertificateID**/ **$Thumbprint** wpis w **ConfigurationData**.

## <a name="next-steps"></a>Kolejne kroki

* Pobierz [wprowadzenie do obsługi rozszerzenia usługi Konfiguracja DSC Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Sprawdź [szablonu usługi Azure Resource Manager dla rozszerzenia DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby uzyskać więcej informacji o konfiguracji DSC środowiska PowerShell, przejdź do [Centrum dokumentacji programu PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
* Więcej funkcji, którą można zarządzać za pomocą usługi Konfiguracja DSC środowiska PowerShell, a więcej zasobów DSC, Przeglądaj [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
