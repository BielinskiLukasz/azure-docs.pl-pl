---
title: Łączenie Azure AD Identity Protection danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Azure AD Identity Protection dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: de8f31a02b62164a8a6b099a90297a207aefae80
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240798"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Łączenie danych z Azure AD Identity Protection



Można przesyłać strumieniowo dzienniki z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do usługi Azure wskaźnikowego, aby przesyłać strumieniowo alerty do usługi Azure wskaźnikowego, aby wyświetlać pulpity nawigacyjne, tworzyć niestandardowe alerty i ulepszać badanie. Azure Active Directory Identity Protection zapewnia skonsolidowany widok narażonych użytkowników, wykrywanie ryzyka i luki w zabezpieczeniach, z możliwością natychmiastowego korygowania ryzyka i ustawiania zasad automatycznego korygowania przyszłych zdarzeń. Usługa jest oparta na doświadczeniu firmy Microsoft chroniącym tożsamości klientów i uzyskuje ogromną dokładność od sygnału od ponad 13 000 000 000 logowań dziennie. 


## <a name="prerequisites"></a>Wymagania wstępne

- Wymagana jest licencja na [Azure Active Directory — wersja Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń


## <a name="connect-to-azure-ad-identity-protection"></a>Połącz z Azure AD Identity Protection

Jeśli masz już Azure AD Identity Protection, upewnij się, że jest [włączona w sieci](../active-directory/identity-protection/enable.md).
Jeśli Azure AD Identity Protection jest wdrożona i pobiera dane, dane alertów można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure AD Identity Protection** .

2. Kliknij przycisk **Połącz** , aby rozpocząć przesyłanie strumieniowe zdarzeń Azure AD Identity Protection do usługi Azure wskaźnikowej.


6. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure AD Identity Protection, Wyszukaj pozycję **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure AD Identity Protection z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
