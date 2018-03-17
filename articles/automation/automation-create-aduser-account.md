---
title: "Tworzenie konta użytkownika usługi Azure AD"
description: "W tym artykule opisano sposób tworzenia poświadczeń dla konta użytkownika programu Azure AD dla elementów runbook w automatyzacji Azure do uwierzytelniania na platformie Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "konto użytkownika usługi Azure Active Directory, azure service management, konto użytkownika usługi Azure AD"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: cd9e3ee5900c3928573fbac6809c107b5ac331b5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Uwierzytelnianie elementów Runbook za pomocą klasycznego wdrożenia platformy Azure i usługi Resource Manager
W tym artykule opisano kroki, które należy wykonać, aby skonfigurować konto użytkownika usługi Azure AD dla elementów Runbook usługi Azure Automation działających w odniesieniu do klasycznego modelu wdrażania platformy Azure lub zasobów usługi Azure Resource Manager. Gdy to nadal być tożsamością obsługiwanych uwierzytelniania dla usługi Azure Resource Manager, na podstawie elementy runbook, zalecaną metodą jest użycie konta Uruchom jako platformy Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Tworzenie nowego użytkownika usługi Azure Active Directory
1. Zaloguj się do portalu Azure jako administrator usługi dla subskrypcji platformy Azure, którą chcesz zarządzać.
2. Wybierz **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy** > **nowego użytkownika**.
3. Wprowadź szczegóły użytkownika, takie jak **nazwa** i **nazwy użytkownika**.  
4. Zapamiętaj pełną nazwę użytkownika i hasło tymczasowe.
5. Wybierz **roli katalogu**.
6. Przypisz rolę globalny lub Administrator ograniczone.
7. Wyloguj się z platformy Azure, a następnie zaloguj się ponownie przy użyciu konta, na którym został utworzony. Monit o zmianę hasła użytkownika.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Tworzenie konta usługi Automation w witrynie Azure Portal
W tej części należy wykonać poniższe kroki, aby utworzyć konto usługi Azure Automation w witrynie Azure Portal. Będzie ono używane z elementami Runbook zarządzającymi zasobami w trybie usługi Azure Resource Manager.  

1. Zaloguj się do portalu Azure jako administrator usługi dla subskrypcji platformy Azure, którą chcesz zarządzać.
2. Wybierz opcję **Konta automatyzacji**.
3. Wybierz pozycję **Dodaj**.<br><br>![Dodawanie konta usługi Automation](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. W bloku **Dodaj konto automatyzacji** w polu **Nazwa** wpisz nazwę nowego konta usługi Automation.
5. Jeśli masz więcej niż jedną subskrypcję, jedną z nich określ dla nowego konta, podaj także nową lub istniejącą **grupę zasobów** i **lokalizację** centrum danych Azure.
6. Wybierz wartość **Tak** dla opcji **Utwórz konto Uruchom jako platformy Azure**, a następnie kliknij przycisk **Utwórz**.  
   
    > [!NOTE]
    > Jeśli zdecydujesz się nie utworzyć konta Uruchom jako, wybierając opcję **nr**, otrzymasz komunikat ostrzegawczy w **Dodaj konto automatyzacji** bloku. Gdy konto jest tworzone i przypisywane do **współautora** roli w ramach subskrypcji, nie ma odpowiedniego tożsamość uwierzytelniania w usłudze katalogowej subskrypcji i w związku z tym brak dostępu do zasobów w sieci Subskrypcja. Dzięki temu wszystkie elementy runbook odwołuje się do tego konta mogą uwierzytelniać i wykonywać zadania względem zasobów usługi Azure Resource Manager.
    > 
    >

    <br>![Ostrzeżenie podczas dodawania konta usługi Automation](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. W trakcie tworzenia konta usługi Automation na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

Po zakończeniu tworzenia poświadczenia musisz utworzyć zasób poświadczenia, aby skojarzyć konto usługi Automation z utworzonym wcześniej kontem użytkownika usługi AD. Pamiętaj, że tylko utworzyć konto usługi Automatyzacja i nie jest skojarzony z tożsamością uwierzytelniania. Wykonaj czynności opisane w artykule [Credential assets in Azure Automation](automation-credentials.md#creating-a-new-credential-asset) (Zasoby poświadczeń w usłudze Azure Automation) i wprowadź wartość dla zmiennej **username** w formacie **domena\użytkownik**.

## <a name="use-the-credential-in-a-runbook"></a>Użycie poświadczeń w elemencie Runbook
Można pobrać poświadczenie w elemencie Runbook za pomocą działania [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx), a następnie można go użyć za pomocą działania [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) w celu nawiązania połączenia z subskrypcją platformy Azure. Jeśli poświadczenie odnosi się do administratora wielu subskrypcji Azure, należy też użyć polecenia [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx), aby wskazać odpowiednią subskrypcję. Przedstawiono to w poniższym przykładzie programu PowerShell, zwykle wyświetlany u góry większość elementów runbook automatyzacji Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Powtórz te wiersze po dowolnym [punktów kontrolnych](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) w elemencie runbook. Jeśli element runbook został wstrzymany, a następnie kontynuowanie na innego procesu roboczego, następnie należy ponownie uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
* Informacje o różnych typach elementów Runbook i krokach prowadzących do tworzenia własnych elementów Runbook można znaleźć w artykule [Azure Automation runbook types](automation-runbook-types.md) (Typy elementów Runbook usługi Azure Automation).

