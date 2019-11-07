---
title: Zarządzanie administratorami serwera w Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano sposób zarządzania administratorami serwera Azure Analysis Servicesm przy użyciu interfejsów API Azure Portal, PowerShell lub REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572620"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów

Administratorzy serwera muszą być prawidłowymi użytkownikami lub grupami zabezpieczeń w Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. Do zarządzania administratorami serwera można używać administratorów **Analysis Services** serwera w Azure Portal, właściwości serwera w programie SSMS, PowerShell lub interfejsie API REST. 

**Grupy zabezpieczeń** muszą mieć [włączoną obsługę poczty](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) z właściwością `MailEnabled` ustawioną na `True`. Podczas określania grupy przy użyciu adresu e-mail `obj:groupid@tenantid`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorów serwera przy użyciu Azure Portal

1. W portalu dla serwera kliknij pozycję **administratorzy Analysis Services**.
2. W **\<nazwa_serwera >-Analysis Services Administratorzy**kliknij przycisk **Dodaj**.
3. W obszarze **Dodaj administratorów serwera**wybierz pozycję konta użytkowników z usługi Azure AD lub Zaproś użytkowników zewnętrznych według adresu e-mail.

    ![Administratorzy serwera w Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorów serwera przy użyciu programu SSMS

1. Kliknij prawym przyciskiem myszy serwer > **Właściwości**.
2. W oknie **właściwości Analysis Server**kliknij pozycję **zabezpieczenia**.
3. Kliknij przycisk **Dodaj**, a następnie wprowadź adres e-mail użytkownika lub grupy w usłudze Azure AD.
   
    ![Dodawanie administratorów serwera w programie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj polecenia cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) , aby określić parametr administratora podczas tworzenia nowego serwera. <br>
Użyj polecenia cmdlet [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) , aby zmodyfikować parametr administratora dla istniejącego serwera.

## <a name="rest-api"></a>Interfejs API REST

Użyj [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) , aby określić właściwość asAdministrator podczas tworzenia nowego serwera. <br>
Użyj [aktualizacji](https://docs.microsoft.com/rest/api/analysisservices/servers/update) , aby określić właściwość asAdministrator podczas modyfikowania istniejącego serwera. <br>



## <a name="next-steps"></a>Następne kroki 

[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  

