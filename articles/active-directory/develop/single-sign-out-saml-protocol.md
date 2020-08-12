---
title: Protokół SAML logowania jednokrotnego na platformie Azure
description: W tym artykule opisano protokół SAML logowania jednokrotnego w Azure Active Directory
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 1d09355993af96e9e0cd334c57174cdaa771b388
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118267"
---
# <a name="single-sign-out-saml-protocol"></a>Protokół SAML wylogowania jednokrotnego

Azure Active Directory (Azure AD) obsługuje profil rejestracji jednokrotnej w przeglądarce internetowej protokołu SAML 2,0. Aby logowanie jednokrotne działało prawidłowo, **LogoutURL** aplikacji musi być jawnie zarejestrowana w usłudze Azure AD podczas rejestracji aplikacji. Usługa Azure AD korzysta z LogoutURL, aby przekierować użytkowników po ich wylogowaniu.

Na poniższym diagramie przedstawiono przepływ pracy procesu rejestracji jednokrotnej usługi Azure AD.

![Przepływ pracy rejestracji jednokrotnej usługi Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Usługa w chmurze wysyła `LogoutRequest` komunikat do usługi Azure AD w celu wskazania, że sesja została przerwana. Poniższy fragment przedstawia przykładowy `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest`Element wysłany do usługi Azure AD wymaga następujących atrybutów:

* `ID`— Identyfikuje żądanie wylogowania. Wartość `ID` nie może rozpoczynać się od cyfry. Typowym sposobem jest dołączenie **identyfikatora** do ciągu REPREZENTUJĄCEGO identyfikator GUID.
* `Version`-Ustaw wartość tego elementu na **2,0**. Ta wartość jest wymagana.
* `IssueInstant`— Jest to `DateTime` ciąg z wartością uniwersalnego czasu koordynowanego (UTC) i [formatem rundy ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Usługa Azure AD oczekuje wartości tego typu, ale nie wymusza jej.

### <a name="issuer"></a>Wystawca
`Issuer`Element w a `LogoutRequest` musi dokładnie odpowiadać jednemu z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle jest to **Identyfikator URI aplikacji** określony podczas rejestracji aplikacji.

### <a name="nameid"></a>NameID
Wartość `NameID` elementu musi być dokładnie zgodna z `NameID` wylogowanym użytkownikiem.

## <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD wysyła `LogoutResponse` odpowiedź w odpowiedzi do `LogoutRequest` elementu. Poniższy fragment przedstawia przykład `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Usługa Azure AD ustawia `ID` `Version` wartości i `IssueInstant` w `LogoutResponse` elemencie. Ustawia również `InResponseTo` element na wartość `ID` atrybutu `LogoutRequest` , który nadaje odpowiedź.

### <a name="issuer"></a>Wystawca
Usługa Azure AD ustawia tę wartość na `https://login.microsoftonline.com/<TenantIdGUID>/` gdzie \<TenantIdGUID> jest identyfikator dzierżawy dzierżawy usługi Azure AD.

Aby oszacować wartość `Issuer` elementu, użyj wartości **identyfikatora URI aplikacji** podanej podczas rejestracji aplikacji.

### <a name="status"></a>Stan
Usługa Azure AD używa `StatusCode` elementu w `Status` elemencie, aby wskazać powodzenie lub niepowodzenie wylogowywania. Gdy próba wylogowania nie powiedzie się, `StatusCode` element może również zawierać niestandardowe komunikaty o błędach.