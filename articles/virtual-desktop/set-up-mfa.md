---
title: Konfigurowanie usługi Azure Multi-Factor Authentication dla pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować usługę Azure Multi-Factor Authentication w celu zwiększenia bezpieczeństwa na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 47b1a3a44c494560dde9ffdab004ea576f434ffe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091304"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Włączanie usługi Azure Multi-Factor Authentication na potrzeby usługi Windows Virtual Desktop

>[!IMPORTANT]
> Jeśli odwiedzasz Tę stronę z dokumentacji usługi 2019, pamiętaj, aby [wrócić 2019 do dokumentacji dotyczącej](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) programu.

Klient systemu Windows dla pulpitu wirtualnego systemu Windows jest doskonałym rozwiązaniem do integrowania pulpitu wirtualnego systemu Windows z maszyną lokalną. Jednak podczas konfigurowania konta pulpitu wirtualnego systemu Windows na kliencie systemu Windows istnieją pewne miary, które należy podjąć, aby zapewnić sobie bezpieczeństwo i użytkowników.

Po pierwszym zalogowaniu klient monituje o podanie nazwy użytkownika, hasła i usługi Azure MFA. Po tym czasie przy następnym logowaniu klient będzie pamiętał token z aplikacji Azure Active Directory (AD) dla przedsiębiorstw. Po wybraniu opcji **Zapamiętaj mnie**użytkownicy mogą się zalogować po ponownym uruchomieniu klienta bez konieczności ponownego wprowadzania poświadczeń.

Zapamiętanie poświadczeń jest wygodne, ale może również sprawić, że wdrożenia w scenariuszach korporacyjnych lub na urządzeniach osobistych są mniej bezpieczne. Aby zapewnić ochronę użytkowników, należy się upewnić, że klient będzie monitować o poświadczenia usługi Azure Multi-Factor Authentication (MFA). W tym artykule opisano sposób konfigurowania zasad dostępu warunkowego dla pulpitu wirtualnego systemu Windows w celu włączenia tego ustawienia.

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić:

- Przypisz użytkownikom licencję obejmującą Azure Active Directory — wersja Premium P1 lub P2.
- Grupa Azure Active Directory z użytkownikami przypisanymi jako członkowie grupy.
- Włącz usługę Azure MFA dla wszystkich użytkowników. Aby uzyskać więcej informacji o tym, jak to zrobić, zobacz [temat jak wymagać weryfikacji dwuetapowej dla użytkownika](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Poniższe ustawienie dotyczy również [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniżej przedstawiono sposób tworzenia zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego podczas nawiązywania połączenia z pulpitem wirtualnym systemu Windows:

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
2. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
3. Wybierz pozycję **nowe zasady**.
4. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
5. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
6. W obszarze **dołączanie**wybierz pozycję **Wybierz użytkowników i grupy**  >  **Użytkownicy i grupy** > wybierz grupę utworzoną na etapie [wymagania wstępne](#prerequisites) .
7. Kliknij **Gotowe**.
8. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz**wybierz pozycję **Wybierz aplikacje**.
9. Wybierz jedną z następujących grup aplikacji w zależności od używanej wersji pulpitu wirtualnego systemu Windows.
   - Jeśli korzystasz z wersji 2019, wybierz te dwie aplikacje:
       - **Pulpit wirtualny systemu Windows** (Identyfikator aplikacji 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Klient pulpitu wirtualnego systemu Windows** (Identyfikator aplikacji fa4345a4-a730-4230-84a8-7d9651b86739)
   - Jeśli używasz sprężynowego wydania 2020, wybierz te dwie aplikacje:
       -  **Pulpit wirtualny systemu Windows** (Identyfikator aplikacji 9cdead84-a844-4324-93f2-b2e6bb768d07)
       -  **Klient pulpitu wirtualnego systemu Windows** (Identyfikator aplikacji a85cf173-4192-42f8-81fa-777a763e6e2c)

   >[!IMPORTANT]
   > Aplikacje klienckie pulpitu wirtualnego systemu Windows są używane dla klienta sieci Web. Nie należy jednak wybierać aplikacji o nazwie Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Ta aplikacja jest używana tylko do pobierania źródła danych użytkownika i nie powinna mieć usługi MFA.
  
1. Po wybraniu aplikacji wybierz pozycję **Wybierz**, a następnie wybierz pozycję **gotowe**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu strony aplikacji w chmurze lub akcji. Pulpity wirtualne systemu Windows i aplikacje klienckie pulpitu wirtualnego systemu Windows są wyróżnione kolorem czerwonym.](media/cloud-apps-enterprise.png)

   >[!NOTE]
   >Aby znaleźć identyfikator aplikacji dla aplikacji, którą chcesz wybrać, przejdź do pozycji **aplikacje dla przedsiębiorstw** i wybierz pozycję **aplikacje firmy Microsoft** z menu rozwijanego Typ aplikacji.

10. W obszarze **Kontrola dostępu**  >  **przyznawanie**wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie **Wybierz opcję**.
11. W obszarze sesja **kontroli dostępu**  >  **Session**wybierz **pozycję częstotliwość logowania**, ustaw wartość **1** i jednostkę na **godziny**, a następnie wybierz pozycję **Wybierz**.
12. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
13. Wybierz pozycję **Utwórz** , aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o zasadach dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Dowiedz się więcej o częstotliwości logowania użytkownika](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
