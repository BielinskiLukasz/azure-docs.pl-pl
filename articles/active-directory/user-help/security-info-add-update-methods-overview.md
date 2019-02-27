---
title: Omówienie modyfikowania metod i danych informacji zabezpieczających (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Omówienie dodawania, aktualizowania i usuwania metod informacji zabezpieczających na potrzeby weryfikacji dwuskładnikowej i samodzielnego resetowania hasła.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: olhaun
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 115354985284b090fef890068ade6ddaf5867f04
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457807"
---
# <a name="overview-about-changing-your-security-info-preview-methods-and-information"></a>Omówienie modyfikowania metod i danych informacji zabezpieczających (wersja zapoznawcza)
Domyślą metodę informacji zabezpieczających można dodać, edytować i usunąć na stronach **Informacje zabezpieczające**. Aby uzyskać więcej informacji na temat każdej metody i sposobu modyfikowania informacji, zobacz pokrewne artykuły dotyczące metod.

## <a name="two-factor-verification-versus-password-reset-authentication"></a>Weryfikacja dwuskładnikowa a uwierzytelnianie za pomocą resetowania hasła
Metody informacji zabezpieczających są używane zarówno do weryfikacji dwuskładnikowej, jak i do resetowania haseł. Jednak nie wszystkie metody mogą służyć do obu celów.

|Metoda|Używana do|
|------|--------|
|Aplikacja Authenticator|Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła.|
|Wiadomości SMS|Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła.|
|Rozmowy telefoniczne|Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła.|
|Konto e-mail|Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej.|
|Pytania zabezpieczające|Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej.|

## <a name="method-based-how-to-articles"></a>Artykuły z instrukcjami dotyczącymi metod
Ta lista zawiera artykuły dotyczące metod. Każdy artykuł zawiera szczegółowe informacje o tym, jak dodawać, edytować, usuwać lub modyfikować informacje zabezpieczające za pomocą określonej metody.

|Artykuł |Opis |
|------|------------|
|[Konfigurowanie informacji zabezpieczających dla aplikacji Authenticator](security-info-setup-auth-app.md)|Opisuje sposób modyfikowania metody informacji zabezpieczających dla aplikacji Microsoft Authenticator.|
|[Konfigurowanie informacji zabezpieczających pod kątem wiadomości SMS](security-info-setup-text-msg.md)|Opisuje sposób modyfikowania metody informacji zabezpieczających dla wiadomości SMS.|
|[Set up security info to use phone calls](security-info-setup-phone-number.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z połączeń telefonicznych)|Opisuje sposób modyfikowania metody informacji zabezpieczających dla rozmów telefonicznych.|
|[Set up security info to use email](security-info-setup-email.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z poczty e-mail)|Opisuje sposób modyfikowania metody informacji zabezpieczających pod kątem użycia adresu e-mail.|
|[Set up security info to use pre-defined security questions](security-info-setup-questions.md) (Konfigurowanie informacji zabezpieczających w celu korzystania ze wstępnie zdefiniowanych pytań zabezpieczających)|Opisuje sposób modyfikowania metody informacji zabezpieczających pod kątem użycia pytań zabezpieczających.|