---
title: Powiadomienia Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak powiadomienia obsługują Twoje działania dochodzeniowe.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81452f4d1f77c07222bbff05093a7e8d5d0a1bee
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887548"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Powiadomienia Azure Active Directory Identity Protection

Azure AD Identity Protection wysyła dwa typy zautomatyzowanych wiadomości e-mail z powiadomieniami, które ułatwiają zarządzanie ryzykiem użytkownika i wykrywaniem ryzyka:

- Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail
- Tygodniowy adres e-mail w postaci skróconej

Ten artykuł zawiera omówienie obu wiadomości e-mail z powiadomieniami.

## <a name="users-at-risk-detected-email"></a>Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail

W odpowiedzi na wykryte konto zagrożone Azure AD Identity Protection generuje alert e-mail z **użytkownikami narażonymi na ryzyko wykryte** jako temat. Wiadomość e-mail zawiera link do raportu **[Użytkownicy oflagowani w związku z ryzykiem](../reports-monitoring/concept-user-at-risk.md)** . Najlepszym rozwiązaniem jest natychmiastowe badanie narażonych użytkowników.

Konfiguracja tego alertu umożliwia określenie poziomu ryzyka użytkownika, który ma zostać wygenerowany przez alert. Wiadomość e-mail zostanie wygenerowana, gdy poziom ryzyka użytkownika osiągnie określone elementy. jednak nie otrzymasz nowych użytkowników na ryzyko wykrycia alertów e-mail dla tego użytkownika po przejściu na ten poziom ryzyka użytkownika. Jeśli na przykład ustawisz zasady, aby otrzymywać alerty na średnim ryzyku użytkownika, a użytkownik Jan przejdzie do średniego ryzyka, otrzymasz wiadomość e-mail z informacją o ryzyku wykrytym przez Jan. Jednak nie otrzymasz alertu drugiego użytkownika o zagrożeniu, jeśli Jan następnie przejdzie do wysokiego ryzyka lub wykryje dodatkowe ryzyko.

![Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurowanie alertów wykrytych dla użytkowników

Jako administrator możesz ustawić:

- **Poziom ryzyka użytkownika wyzwalający generowanie tej wiadomości e-mail** — domyślnie poziom ryzyka jest ustawiany na "wysoki".
- Adresaci **tej wiadomości e-mail** — domyślnie odbiorcy obejmują wszystkich administratorów globalnych. Administratorzy globalni mogą również dodawać innych administratorów globalnych, administratorów zabezpieczeń, czytelników zabezpieczeń jako odbiorców.
   - Opcjonalnie możesz **dodać dodatkowe wiadomości e-mail w celu otrzymywania powiadomień o alertach** , ponieważ ta funkcja jest dostępna w wersji zapoznawczej, a zdefiniowane przez użytkowników muszą mieć odpowiednie uprawnienia do wyświetlania raportów połączonych w Azure Portal.

Należy skonfigurować użytkowników pod kątem ryzykownych wiadomości e-mail w **Azure Portal** w obszarze **Azure Active Directory** > **zabezpieczenia** > j **ochrony tożsamości** > **Użytkownicy na wykryciu zagrożeń**.

## <a name="weekly-digest-email"></a>Tygodniowy adres e-mail w postaci skróconej

Cotygodniowa wiadomość e-mail z podsumowaniem zawiera streszczenie nowych wykrytych zagrożeń.  
Obejmuje:

- Narażeni użytkownicy
- Podejrzane działania
- Wykryte luki w zabezpieczeniach
- Linki do pokrewnych raportów w usłudze Identity Protection

![Tygodniowy adres e-mail w postaci skróconej](./media/howto-identity-protection-configure-notifications/400.png)

Domyślnie odbiorcy obejmują wszystkich administratorów globalnych. Administratorzy globalni mogą również dodawać innych administratorów globalnych, administratorów zabezpieczeń, czytelników zabezpieczeń jako odbiorców.

### <a name="configure-weekly-digest-email"></a>Konfigurowanie tygodniowego adresu e-mail w postaci skróconej

Jako administrator możesz przełączać wysyłanie cotygodniowej wiadomości e-mail z podsumowaniem lub wyłączyć ją i wybrać użytkowników przypisanych do odbierania wiadomości e-mail.

Skonfiguruj cotygodniowe wiadomości e-mail z podsumowaniem w **Azure Portal** w obszarze **Azure Active Directory** > **zabezpieczenia** > **Identity Protection** > **cotygodniowe podsumowanie**.

## <a name="see-also"></a>Zobacz także

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
