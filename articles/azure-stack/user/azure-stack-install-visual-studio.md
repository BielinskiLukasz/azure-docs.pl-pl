---
title: Zainstaluj program Visual Studio i Połącz z usługą Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się kroki wymagane do instalacji programu Visual Studio i Połącz z usługą Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 6bb3215e63e2fc672cde5746e6a9867df899ee88
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016702"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Zainstaluj program Visual Studio i Połącz z usługą Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Visual Studio umożliwia pisanie i wdrażanie usługi Azure Resource Manager [szablony](azure-stack-arm-templates.md) do usługi Azure Stack. Kroki opisane w tym artykule opisano instalowanie programu Visual Studio na [usługi Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub komputerze zewnętrznego, jeśli planujesz używać usługi Azure Stack za pośrednictwem [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalacja programu Visual Studio

1. Pobierz i uruchom [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Otwórz **Instalatora platformy sieci Web firmy Microsoft**.

3. Wyszukaj **Visual Studio Community 2015 z zestawem SDK usługi Microsoft Azure — 2.9.6**. Kliknij przycisk **Dodaj**, i **zainstalować**.

4. Odinstaluj **Microsoft Azure PowerShell** który jest instalowany jako część zestawu Azure SDK.

    ![Kroki instalacji zrzut ekranu Instalatora WebPI](./media/azure-stack-install-visual-studio/image1.png) 

5. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)

6. Po zakończeniu instalacji, należy ponownie uruchomić system operacyjny.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Nawiązywanie połączenia usługi Azure Stack z usługą Azure AD

1. Uruchom program Visual Studio.

2. Z **widoku** menu, wybierz opcję **programu Cloud Explorer**.

3. W nowym okienku zaznacz **Dodaj konto** i zaloguj się przy użyciu poświadczeń usługi Azure Active Directory (Azure AD).  

    ![Zrzut ekranu Eksploratora chmury po zalogowaniu i podłączone do usługi Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Po zalogowaniu możesz [wdrażanie szablonów](azure-stack-deploy-template-visual-studio.md) lub przeglądania dostępnych typów zasobów i grup zasobów, aby tworzyć własne szablony.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Nawiązywanie połączenia usługi Azure Stack z usługami AD FS

1. Uruchom program Visual Studio.

2. Z **narzędzia**, wybierz opcję **opcje**.

3. Rozwiń **środowiska** w **okienka nawigacji** i wybierz **kont**.

4. Wybierz **Dodaj**, a następnie wprowadź punkt końcowy użytkownika usługi Azure Resource Manger.  
  W przypadku usługi Azure Stack Development Kit jest adres URL: `https://management.local.azurestack/external`.  
  W systemach zintegrowanych w usłudze Azure Stack jest adres URL: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Wybierz pozycję **Dodaj**.  

    Visual Studio wymaga usługi Azure Resource Manager i umożliwia odnalezienie punktów końcowych, łącznie z punktu końcowego uwierzytelniania dla usług federacyjnych Azure Directory (AD FS).

    ![Zrzut ekranu Eksploratora chmury po zalogowaniu i podłączone do usługi Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Wybierz **programu Cloud Explorer** z **widoku** menu.

1. Wybierz **Dodaj konto** i zaloguj się przy użyciu poświadczeń usług AD FS.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Eksplorator chmury kwerendy dostępnych subskrypcji. Możesz wybrać jeden dostępny subskrypcję, aby zarządzać.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Przeglądanie swoje istniejące zasoby, grupy zasobów lub wdrażania szablonów.

## <a name="next-steps"></a>Kolejne kroki

 - Dowiedz się więcej o programie Visual Studio [obok siebie](https://msdn.microsoft.com/library/ms246609.aspx) z innymi wersjami programu Visual Studio.
 - [Tworzenie szablonów usługi Azure Stack](azure-stack-develop-templates.md).