---
title: 'Skrypt programu PowerShell: Dodawanie użytkownika zewnętrznego do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 0acff1eb4cee441187205b11a7e07cc718072cbf
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248193"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Aby dodać użytkownika zewnętrznego do laboratorium Azure DevTest Labs przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell dodaje użytkownika zewnętrznego do laboratorium Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Ponawia próbę obiektu użytkownika z usługi Azure active directory. |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Przypisuje określoną rolę do określonego podmiotu zabezpieczeń w podanym zakresie. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell usługi laboratorium Azure można znaleźć w [przykładów programu Azure Lab Services PowerShell](../samples-powershell.md).
