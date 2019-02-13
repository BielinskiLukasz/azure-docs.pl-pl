---
title: Potencjalne przyczyny komunikat o błędzie "Nie można dostać się tam z tego miejsca" w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z potencjalnych przyczyn, które otrzymujesz komunikat o błędzie "Nie można dostać się tam z tego miejsca".
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd67a9a7027e8567e470acab006fd09748de54
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181715"
---
# <a name="potential-reasons-for-the-you-cant-get-there-from-here-error-message"></a>Potencjalne przyczyny komunikat o błędzie "Nie można dostać się tam z tego miejsca"
Podczas uzyskiwania dostępu do wewnętrznej sieci web aplikacji lub usług w organizacji, może uzyskać komunikat o błędzie informujący, że, **nie możesz tam przejść stąd**. Ten komunikat oznacza, że Twoja organizacja opracowała zasady, co uniemożliwia urządzeniu dostęp do zasobów organizacji. Gdy użytkownik może pozostać konieczności kontaktowania się z pomocą techniczną, aby rozwiązać ten problem, Oto kilka rzeczy, które możesz najpierw wypróbować.

## <a name="make-sure-youre-using-a-supported-browser"></a>Upewnij się, że używasz obsługiwanej przeglądarki
Jeśli otrzymasz **nie możesz tam przejść stąd** komunikat informujący o tym, że próbujesz uzyskać dostęp do witryn w organizacji z nieobsługiwanej przeglądarki, należy sprawdzić przeglądarki, z której korzystasz.

![Komunikat o błędzie obsługi przeglądarki](media/user-help-device-remediation/browser-version.png)

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwanej przeglądarki, w oparciu o system operacyjny. Jeśli używasz systemu Windows 10 obsługiwanych przeglądarek obejmują Microsoft Edge, Internet Explorer i Google Chrome. Jeśli używasz innego systemu operacyjnego, można sprawdzić pełną listę [obsługiwanych przeglądarek](../conditional-access/technical-reference.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Upewnij się, że używasz obsługiwanego systemu operacyjnego
Upewnij się, że używasz obsługiwanej wersji systemu operacyjnego, w tym:

- **Klient Windows.** Windows 7 lub nowszy.

- **Windows Server.** Windows Server 2008 R2 lub nowszym.

- **macOS.** System macOS X lub nowszy

- **Systemy android i iOS.** Najnowszą wersję systemu Android i iOS systemach operacyjnych urządzeń przenośnych

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwanym systemie operacyjnym.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Upewnij się, że urządzenie jest przyłączone do sieci
Jeśli otrzymasz **nie możesz tam przejść stąd** komunikat informujący o tym, że urządzenie jest poza zgodności z zasadami dostępu w organizacji, upewnij się, urządzenie zostało przyłączone do sieci organizacji.

![Komunikat o błędzie, które dotyczą tego, czy jesteś w sieci](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Aby sprawdzić, czy urządzenie jest przyłączone do sieci
1. Zaloguj się do Windows przy użyciu konta służbowego lub szkolnego. Na przykład alain@contoso.com.

2. Połącz się z siecią organizacji za pośrednictwem wirtualnej sieci prywatnej (VPN) lub funkcji DirectAccess.

3. Po nawiązaniu połączenia naciśnij **Windows logo + l.** zablokować urządzenie.

4. Odblokuj urządzenie przy użyciu lub konta służbowego, a następnie spróbuj uzyskać dostęp do problematycznych aplikacji lub usługi ponownie.

    Jeśli widzisz **nie możesz tam przejść stąd** komunikat o błędzie ponownie, wybierz opcję **szczegółowe** połączyć, a następnie skontaktuj się z pomocą techniczną ze szczegółami.

### <a name="to-join-your-device-to-your-network"></a>Aby dołączyć urządzenie do sieci
Jeśli urządzenie nie jest przyłączone do sieci organizacji, możesz wykonaj jedną z następujących czynności:

- **Dołącz swoje urządzenie pracy.** Dołączanie urządzenia należące do pracy systemu Windows 10 do sieci w organizacji, aby dostęp do zasobów z potencjalnie ograniczeniami. Aby uzyskać więcej informacji oraz instrukcje krok po kroku, zobacz [Dołączanie urządzenia pracy z siecią organizacji](user-help-join-device-on-network.md).

- **Zarejestruj urządzenie osobiste do pracy.** Zarejestruj urządzenia osobistego, zwykle telefon lub tablet w sieci organizacji. Po zarejestrowaniu urządzenia, system dostępu do zasobów z ograniczeniami w organizacji. Aby uzyskać więcej informacji oraz instrukcje krok po kroku, zobacz [zarejestrowania urządzenia osobistego w sieci organizacji](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Kolejne kroki
- [Co to jest MyApps portal?](active-directory-saas-access-panel-introduction.md)

- [Logowanie się za pomocą telefonu zamiast hasła](user-help-auth-app-sign-in.md)
