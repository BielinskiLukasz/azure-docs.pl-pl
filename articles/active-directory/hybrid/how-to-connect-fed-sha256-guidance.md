---
title: Zmień algorytm wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej Office 365 — Azure
description: Ta strona zawiera wskazówki dotyczące zmiany algorytmu SHA dla relacji zaufania federacji z pakietem Office 365
keywords: SHA1, SHA256, O365, Federacja, aadconnect, ADFS, AD FS, zmiana SHA, zaufanie federacji, zaufanie jednostki uzależnionej
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897337"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Zmień algorytm wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej pakietu Office 365
## <a name="overview"></a>Przegląd
Active Directory Federation Services (AD FS) podpisuje tokeny, aby Microsoft Azure Active Directory zapewnić, że nie mogą zostać naruszone. Sygnatura może opierać się na algorytm SHA1 lub SHA256. Azure Active Directory obsługuje teraz tokeny podpisane przy użyciu algorytmu SHA256 i zalecamy ustawienie algorytmu podpisywania tokenu na SHA256 w celu uzyskania wyższego poziomu zabezpieczeń. W tym artykule opisano kroki niezbędne do ustawienia algorytmu podpisywania tokenu na bardziej bezpieczny poziom SHA256.

>[!NOTE]
>Firma Microsoft zaleca użycie SHA256 jako algorytm podpisywania tokenów, ponieważ jest bardziej bezpieczny niż SHA1, ale algorytm SHA1 nadal pozostaje obsługiwaną opcją.

## <a name="change-the-token-signing-algorithm"></a>Zmień algorytm podpisywania tokenu
Po ustawieniu algorytmu podpisu przy użyciu jednego z dwóch poniższych procesów AD FS podpisuje tokeny zaufania jednostki uzależnionej pakietu Office 365 z SHA256. Nie musisz wprowadzać żadnych dodatkowych zmian konfiguracji i ta zmiana nie ma wpływu na możliwość dostępu do pakietu Office 365 lub innych aplikacji usługi Azure AD.

### <a name="ad-fs-management-console"></a>Konsola zarządzania AD FS
1. Otwórz konsolę zarządzania AD FS na serwerze podstawowym AD FS.
2. Rozwiń węzeł AD FS i kliknij pozycję **relacje zaufania jednostek uzależnionych**.
3. Kliknij prawym przyciskiem myszy relację zaufania jednostki uzależnionej usługi Office 365/Azure i wybierz pozycję **Właściwości**.
4. Wybierz kartę **Zaawansowane** i wybierz algorytm Secure Hash SHA256.
5. Kliknij przycisk **OK**.

![Algorytm podpisywania SHA256--MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS polecenia cmdlet programu PowerShell
1. Na dowolnym serwerze AD FS Otwórz program PowerShell w obszarze uprawnienia administratora.
2. Ustaw algorytm Secure Hash przy użyciu polecenia cmdlet **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Przeczytaj również
* [Napraw relację zaufania Office 365 z Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

