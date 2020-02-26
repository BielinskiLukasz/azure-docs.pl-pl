---
title: Dołączanie do Azure Security Center za pomocą programu PowerShell
description: Ten dokument przeprowadzi Cię przez proces dołączania Azure Security Center przy użyciu poleceń cmdlet programu PowerShell.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603692"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatyzowanie dołączania Azure Security Center przy użyciu programu PowerShell

Obciążenia platformy Azure można zabezpieczyć programowo przy użyciu modułu Azure Security Center PowerShell.
Za pomocą programu PowerShell można zautomatyzować zadania i uniknąć błędu ludzkiego związanego z ręcznymi zadaniami. Jest to szczególnie przydatne w przypadku wdrożeń na dużą skalę, które obejmują dziesiątki subskrypcji z setkami i tysiącami zasobów — wszystkie te, które muszą być zabezpieczone od początku.

Azure Security Center dołączania przy użyciu programu PowerShell umożliwiają programowe Automatyzowanie dołączania i zarządzania zasobami platformy Azure oraz dodawanie niezbędnych kontroli zabezpieczeń.

Ten artykuł zawiera przykładowy skrypt programu PowerShell, który można zmodyfikować i użyć w środowisku w celu wdrożenia Security Center w ramach subskrypcji. 

W tym przykładzie zostanie włączone Security Center w ramach subskrypcji o IDENTYFIKATORze: d07c0080-170c-4c24-861d-9c817742786c i zastosowaniu zalecanych ustawień, które zapewniają wysoki poziom ochrony, implementując standardową warstwę Security Center, która zapewnia Zaawansowane funkcje ochrony przed zagrożeniami i wykrywania:

1. Ustaw [Security Center standardowy poziom ochrony](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Ustaw obszar roboczy Log Analytics, do którego Microsoft Monitoring Agent wyśle dane zbierane na maszynach wirtualnych skojarzonych z subskrypcją — w tym przykładzie istniejący zdefiniowany przez użytkownika obszar roboczy (mój obszar roboczy).

3. Aktywuj automatyczne Inicjowanie obsługi agenta Security Center, które [wdraża Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Ustaw [CISO organizacji jako kontakt z zabezpieczeniami dla alertów Security Center i istotnych zdarzeń](security-center-provide-security-contact-details.md).

6. Przypisz [domyślne zasady zabezpieczeń](tutorial-security-policy.md)Security Center.

## <a name="prerequisites"></a>Wymagania wstępne

Te kroki należy wykonać przed uruchomieniem Security Center poleceń cmdlet:

1.  Uruchom program PowerShell jako administrator.
2.  Uruchom następujące polecenia w programie PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Dołączanie Security Center przy użyciu programu PowerShell

1.  Zarejestruj subskrypcje dla dostawcy zasobów Security Center:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Opcjonalne: Ustaw poziom pokrycia (warstwa cenowa) subskrypcji (jeśli nie jest zdefiniowany, warstwa cenowa jest ustawiona na wartość bezpłatna):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Skonfiguruj obszar roboczy Log Analytics, do którego będą zgłaszane agenci. Musisz mieć już utworzony obszar roboczy Log Analytics, do którego będą raportowane maszyny wirtualne subskrypcji. Można zdefiniować wiele subskrypcji do raportowania w tym samym obszarze roboczym. Jeśli nie zostanie zdefiniowany, zostanie użyty domyślny obszar roboczy.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Zainicjuj obsługę administracyjną Microsoft Monitoring Agent na maszynach wirtualnych platformy Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Zaleca się włączenie automatycznej obsługi administracyjnej, aby upewnić się, że maszyny wirtualne platformy Azure są automatycznie chronione przez Azure Security Center.
    >

5.  Opcjonalne: zdecydowanie zaleca się zdefiniowanie szczegółowych informacji o kontakcie z zabezpieczeniami dla subskrypcji, które zostaną użyte jako adresaci alertów i powiadomień generowanych przez Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Przypisz domyślną inicjatywę zasad Security Center:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Pomyślnie dołączono Azure Security Center za pomocą programu PowerShell!

Teraz można używać tych poleceń cmdlet programu PowerShell ze skryptami automatyzacji do programistycznego iteracji między subskrypcjami i zasobami. Pozwala to zaoszczędzić czas i zmniejsza prawdopodobieństwo wystąpienia błędu przez człowieka. Tego [przykładowego skryptu](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) można użyć jako odwołania.






## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat sposobu automatyzowania dołączania do Security Center za pomocą programu PowerShell, zobacz następujący artykuł:

* [AZ. Security](https://docs.microsoft.com/powershell/module/az.security).

Aby dowiedzieć się więcej na temat Security Center, zobacz następujący artykuł:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.