---
title: Protokoły uwierzytelniania platformy tożsamości firmy Microsoft | Microsoft Docs
description: Omówienie protokołów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699295"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protokoły uwierzytelniania platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji. W tematach w tej sekcji opisano obsługiwane protokoły i ich implementacje na platformie Microsoft Identity platform. W tematach uwzględniono przegląd obsługiwanych typów zgłoszeń, wprowadzenie do korzystania z metadanych Federacji, szczegółowe informacje dotyczące protokołu OAuth 2,0. Dokumentacja dotycząca protokołu SAML 2,0 i sekcja rozwiązywania problemów.

## <a name="authentication-protocols-articles-and-reference"></a>Artykuły i Dokumentacja protokołów uwierzytelniania

* [Ważne informacje o przerzucaniu klucza podpisywania na platformie tożsamości firmy Microsoft](active-directory-signing-key-rollover.md) — informacje o przerzucaniu klucza podpisywania platformy tożsamości firmy Microsoft erze, zmiany można wprowadzić w celu automatycznej aktualizacji klucza oraz omówienie sposobu aktualizowania najpopularniejszych scenariuszy aplikacji.
* [Obsługiwane typy tokenów i oświadczeń](id-tokens.md) — informacje o oświadczeniach w tokenach, których dotyczą platformy tożsamości firmy Microsoft.
* [Uwierzytelnianie oauth 2,0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md) — informacje o implementacji protokołu OAuth 2,0 na platformie tożsamości firmy Microsoft.
* [OpenID Connect Connect 1,0](v2-protocols-oidc.md) — Dowiedz się, jak za pomocą uwierzytelniania OAuth 2,0, protokołu autoryzacji.
* [Wywołania usługi do obsługi przy użyciu poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) — informacje o sposobie przydzielenia przez usługę do wywołań usług przy użyciu poświadczeń klienta OAuth 2,0.
* [Wywołania usługi do obsługi w imieniu przepływu](v2-oauth2-on-behalf-of-flow.md) — informacje na temat używania protokołu OAuth 2,0 w imieniu usługi Flow do wywołań usługi do obsługi.
* [Odwołanie do protokołu SAML](active-directory-saml-protocol-reference.md) — informacje o profilach SAML logowania jednokrotnego i rejestracji jednokrotnej platformy tożsamości firmy Microsoft.

## <a name="see-also"></a>Zobacz także

* [Microsoft Identity platform — Omówienie](v2-overview.md)
* [Przykłady kodu Active Directory](sample-v2-code.md)
