---
title: Definiowanie profilu technicznego SAML w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny SAML w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a3df4d3a955926381a664ab872e03ae987f0839
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197976"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny SAML w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości SAML 2,0. W tym artykule opisano szczegóły dotyczące profilu technicznego dotyczącego współpracy z dostawcą oświadczeń obsługującym ten standardowy protokół. Profil techniczny SAML można sfederować za pomocą dostawcy tożsamości opartego na protokole SAML, takiego jak [ADFS](identity-provider-adfs2016-custom.md) i [Salesforce](identity-provider-salesforce-custom.md). Ta Federacja umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="metadata-exchange"></a>Wymiana metadanych

Metadane są informacje używane w protokole SAML do ujawnienia konfiguracji strony SAML, takiej jak dostawca usług lub dostawca tożsamości. Metadane określają lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania itd. Dostawca tożsamości używa metadanych, aby dowiedzieć się, jak komunikować się z Azure AD B2C. Metadane są konfigurowane w formacie XML i mogą być podpisane za pomocą podpisu cyfrowego, aby druga strona mogła sprawdzić integralność metadanych. Gdy Azure AD B2C tworzy federację z dostawcą tożsamości SAML, działa jako dostawca usług inicjując żądanie SAML i oczekując na odpowiedź SAML. A w niektórych przypadkach akceptuje nieżądane uwierzytelnianie SAML, które jest również znane jako dostawca tożsamości.

Metadane można skonfigurować w obu stronach jako "metadane statyczne" lub "metadane dynamiczne". W trybie statycznym skopiujesz całe metadane z jednej strony i ustawisz ją na drugiej stronie. W trybie dynamicznym należy ustawić adres URL metadanych, podczas gdy druga strona odczytuje konfigurację dynamicznie. Zasady są takie same, więc należy ustawić metadane profilu technicznego Azure AD B2C w dostawcy tożsamości i ustawić metadane dostawcy tożsamości w Azure AD B2C.

Każdy dostawca tożsamości SAML ma różne kroki, aby udostępnić i ustawić dostawcę usług, w tym przypadku Azure AD B2C i ustawić metadane Azure AD B2C w dostawcy tożsamości. Zapoznaj się z dokumentacją dostawcy tożsamości w celu uzyskania wskazówek na temat tego, jak to zrobić.

Poniższy przykład przedstawia adres URL metadanych SAML Azure AD B2C profilu technicznego:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **Nazwa** dzierżawy użytkownika, na przykład Fabrikam.b2clogin.com.
- **Twoje zasady** z nazwą zasad. Użyj zasad, w których konfigurujesz profil techniczny dostawcy SAML lub zasad, które są dziedziczone z tych zasad.
- **Twój profil techniczny** z nazwą profilu technicznego dostawcy tożsamości SAML.

## <a name="digital-signing-certificates-exchange"></a>Wymiana certyfikatów podpisywania cyfrowego

Aby utworzyć relację zaufania między Azure AD B2C i dostawcą tożsamości SAML, należy podać prawidłowy certyfikat x509 z kluczem prywatnym. Certyfikat z kluczem prywatnym (plik PFX) można przekazać do magazynu kluczy zasad Azure AD B2C. Azure AD B2C cyfrowo podpisać żądanie logowania SAML przy użyciu podania certyfikatu.

Certyfikat jest używany w następujący sposób:

- Azure AD B2C generuje i podpisuje żądanie SAML przy użyciu klucza prywatnego Azure AD B2C certyfikatu. Żądanie SAML jest wysyłane do dostawcy tożsamości, który sprawdza poprawność żądania przy użyciu Azure AD B2C klucza publicznego certyfikatu. Certyfikat publiczny Azure AD B2C jest dostępny za pomocą metadanych profilu technicznego. Alternatywnie można ręcznie przekazać plik CER do dostawcy tożsamości SAML.
- Dostawca tożsamości podpisuje dane wysyłane do Azure AD B2C przy użyciu klucza prywatnego dostawcy tożsamości. Azure AD B2C sprawdza poprawność danych przy użyciu publicznego certyfikatu dostawcy tożsamości. Każdy dostawca tożsamości ma inne czynności konfiguracyjne, zapoznaj się z dokumentacją dostawcy tożsamości w celu uzyskania wskazówek dotyczących tego, jak to zrobić. W Azure AD B2C zasady wymagają dostępu do klucza publicznego certyfikatu przy użyciu metadanych dostawcy tożsamości.

Certyfikat z podpisem własnym jest akceptowalny dla większości scenariuszy. W przypadku środowisk produkcyjnych zaleca się użycie certyfikatu x509 wystawionego przez urząd certyfikacji. Ponadto, zgodnie z opisem w dalszej części tego dokumentu, dla środowiska nieprodukcyjnego można wyłączyć podpisywanie SAML po obu stronach.

Na poniższym diagramie przedstawiono metadane i wymianę certyfikatów:

![Wymiana metadanych i certyfikatów](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Szyfrowanie cyfrowe

Aby można było zaszyfrować potwierdzenie odpowiedzi SAML, dostawca tożsamości zawsze używa klucza publicznego certyfikatu szyfrowania w profilu technicznym Azure AD B2C. Gdy Azure AD B2C musi odszyfrować dane, używa prywatnej części certyfikatu szyfrowania.

Aby zaszyfrować potwierdzenie odpowiedzi SAML:

1. Przekaż prawidłowy certyfikat x509 z kluczem prywatnym (plik PFX) do magazynu kluczy zasad Azure AD B2C.
2. Dodaj element **CryptographicKey** o identyfikatorze `SamlAssertionDecryption` do kolekcji **CryptographicKeys** profilu technicznego. Ustaw **identyfikatorze storagereferenceid** na nazwę klucza zasad utworzonego w kroku 1.
3. Ustaw **WantsEncryptedAssertions** metadanych profilu technicznego na `true`.
4. Zaktualizuj dostawcę tożsamości za pomocą nowych Azure AD B2C metadanych profilu technicznego. Powinien zostać wyświetlony **deskryptor** klucza z właściwością **use** ustawioną na `encryption` zawierającym klucz publiczny certyfikatu.

W poniższym przykładzie przedstawiono sekcję Azure AD B2C szyfrowania profilu technicznego metadanych:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protokół

Atrybut **name** elementu Protocol musi mieć wartość `SAML2`.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwróconych przez dostawcę tożsamości SAML w sekcji `AttributeStatement`. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Możesz również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, dopóki ustawisz atrybut `DefaultValue`.

Aby odczytać **NamedId** potwierdzenia SAML w **oparciu** o znormalizowaną rolę, ustaw dla **PartnerClaimType** roszczeń wartość `assertionSubjectName`. Upewnij się, że **NameID** jest pierwszą wartością w pliku XML potwierdzenia. Podczas definiowania więcej niż jednego potwierdzenia, Azure AD B2C wybiera wartość podmiotu z ostatniego potwierdzenia.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości w usłudze Facebook:

- **IssuerUserId** jest mapowany na zastrzeżenie **assertionSubjectName** .
- **First_name** jest mapowany na **daną** wartość.
- **Last_name** jest mapowany do żądania **nazwisko** .
- Nazwa **wyświetlana** bez mapowania nazwy.
- Bez mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| PartnerEntity | Yes | Adres URL metadanych dostawcy tożsamości SAML. Skopiuj metadane dostawcy tożsamości i Dodaj je wewnątrz elementu CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nie | Wskazuje, czy profil techniczny wymaga podpisania wszystkich wychodzących żądań uwierzytelniania. Możliwe wartości: `true` lub `false`. Wartością domyślną jest `true`. Gdy wartość jest ustawiona na `true`, należy określić klucz kryptograficzny **SamlMessageSigning** i wszystkie żądania uwierzytelniania wychodzącego są podpisane. Jeśli wartość jest ustawiona na `false`, parametry **SigAlg** i **Signature** (ciąg zapytania lub parametr post) są pomijane w żądaniu. Te metadane również kontrolują atrybut **AuthnRequestsSigned** metadanych, który jest wyprowadzany w metadanych profilu technicznego Azure AD B2C, który jest udostępniony dostawcy tożsamości. Azure AD B2C nie podpisać żądania, jeśli wartość **WantsSignedRequests** w metadanych profilu technicznego jest ustawiona na `false`, a **WantAuthnRequestsSigned** metadanych dostawcy tożsamości jest ustawiona na `false` lub nie określono. |
| XmlSignatureAlgorithm | Nie | Metoda, która Azure AD B2C używa do podpisywania żądania SAML. To metadane określa wartość parametru **SigAlg** (ciąg zapytania lub parametr post) w żądaniu SAML. Możliwe wartości: `Sha256`, `Sha384`, `Sha512`lub `Sha1`. Upewnij się, że algorytm podpisu został skonfigurowany na obu stronach z tą samą wartością. Używaj tylko algorytmu obsługiwanego przez certyfikat. |
| WantsSignedAssertions | Nie | Wskazuje, czy profil techniczny wymaga podpisania wszystkich zatwierdzeń przychodzących. Możliwe wartości: `true` lub `false`. Wartością domyślną jest `true`. Jeśli wartość jest ustawiona na `true`, wszystkie sekcje potwierdzenia `saml:Assertion` wysyłane przez dostawcę tożsamości do Azure AD B2C muszą być podpisane. Jeśli wartość jest ustawiona na `false`, dostawca tożsamości nie powinien podpisać potwierdzeń, ale nawet wtedy, gdy jest to możliwe, Azure AD B2C nie może zweryfikować podpisu. Te metadane również sterują flagą metadanych **WantsAssertionsSigned**, która jest wyprowadzana w metadanych profilu technicznego Azure AD B2C, który jest udostępniony dostawcy tożsamości. Jeśli wyłączysz weryfikację potwierdzeń, możesz również wyłączyć weryfikację podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| ResponsesSigned | Nie | Możliwe wartości: `true` lub `false`. Wartością domyślną jest `true`. Jeśli wartość jest ustawiona na `false`, dostawca tożsamości nie powinien podpisać odpowiedzi SAML, ale nawet jeśli tak, Azure AD B2C nie będzie sprawdzać podpisu. Jeśli wartość jest ustawiona na `true`, odpowiedź SAML wysłana przez dostawcę tożsamości do Azure AD B2C jest podpisana i należy sprawdzić poprawność. Jeśli wyłączysz weryfikację odpowiedzi SAML, możesz również wyłączyć weryfikację podpisu potwierdzenia (Aby uzyskać więcej informacji, zobacz **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Nie | Wskazuje, czy profil techniczny wymaga szyfrowania wszystkich potwierdzeń przychodzących. Możliwe wartości: `true` lub `false`. Wartością domyślną jest `false`. Jeśli wartość jest ustawiona na `true`, potwierdzenia wysyłane przez dostawcę tożsamości do Azure AD B2C muszą być podpisane i należy określić klucz kryptograficzny **SamlAssertionDecryption** . Jeśli wartość jest ustawiona na `true`, metadane Azure AD B2C profilu technicznego obejmują sekcję **szyfrowanie** . Dostawca tożsamości odczytuje metadane i szyfruje potwierdzenie odpowiedzi SAML przy użyciu klucza publicznego dostarczonego w metadanych profilu technicznego Azure AD B2C. W przypadku włączenia szyfrowania potwierdzeń może być również konieczne wyłączenie walidacji podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Nie | Wskazuje, czy profil sesji logowania jednokrotnego jest włączony, który został zainicjowany przez profil dostawcy tożsamości SAML. Możliwe wartości: `true` lub `false`. Wartość domyślna to `false`. W przepływie zainicjowanym przez dostawcę tożsamości użytkownik jest uwierzytelniany zewnętrznie i do Azure AD B2C jest wysyłana niezamówiona odpowiedź, która następnie wykorzystuje token, wykonuje kroki aranżacji, a następnie wysyła odpowiedź do aplikacji jednostki uzależnionej. |
| NameIdPolicyFormat | Nie | Określa ograniczenia dotyczące identyfikatora nazwy, który będzie używany do reprezentowania żądanego tematu. W przypadku pominięcia można użyć dowolnego typu identyfikatora obsługiwanego przez dostawcę tożsamości dla żądanego tematu. Przykład: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** można używać z **NameIdPolicyAllowCreate**. Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki dotyczące tego, które zasady identyfikatorów nazw są obsługiwane. |
| NameIdPolicyAllowCreate | Nie | Korzystając z **NameIdPolicyFormat**, można również określić właściwość `AllowCreate` **NameIDPolicy**. Wartość tych metadanych jest `true` lub `false`, aby wskazać, czy dostawca tożsamości może utworzyć nowe konto w ramach przepływu logowania. Zapoznaj się z dokumentacją dostawcy tożsamości w celu uzyskania wskazówek na temat tego, jak to zrobić. |
| AuthenticationRequestExtensions | Nie | Opcjonalne elementy rozszerzeń komunikatów protokołu, które są uzgadniane między usługą Azure AD BC a dostawcą tożsamości. Rozszerzenie jest prezentowane w formacie XML. Należy dodać dane XML wewnątrz elementu CDATA `<![CDATA[Your IDP metadata]]>`. Sprawdź dokumentację dostawcy tożsamości, aby sprawdzić, czy element rozszerzeń jest obsługiwany. |
| IncludeAuthnContextClassReferences | Nie | Określa co najmniej jeden identyfikator URI identyfikujący klasy kontekstu uwierzytelniania. Aby na przykład zezwolić użytkownikowi na logowanie się tylko przy użyciu nazwy użytkownika i hasła, ustaw wartość na `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Aby zezwolić na logowanie za pomocą nazwy użytkownika i hasła w chronionej sesji (SSL/TLS), określ `PasswordProtectedTransport`. Zapoznaj się z dokumentacją dostawcy tożsamości w celu uzyskania wskazówek dotyczących obsługiwanych identyfikatorów URI **AuthnContextClassRef** . Określ wiele identyfikatorów URI jako listę rozdzielaną przecinkami. |
| IncludeKeyInfo | Nie | Wskazuje, czy żądanie uwierzytelnienia SAML zawiera klucz publiczny certyfikatu, gdy powiązanie jest ustawione na `HTTP-POST`. Możliwe wartości: `true` lub `false`. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true`lub `false` (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw tę opcję na `true`. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujące atrybuty:

| Atrybut |Wymagany | Opis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Yes | Certyfikat x509 (zestaw kluczy RSA) służący do podpisywania komunikatów SAML. Azure AD B2C używa tego klucza do podpisywania żądań i wysyłania ich do dostawcy tożsamości. |
| SamlAssertionDecryption |Yes | Certyfikat x509 (zestaw kluczy RSA) używany do odszyfrowywania komunikatów SAML. Ten certyfikat powinien być dostarczany przez dostawcę tożsamości. Azure AD B2C używa tego certyfikatu do odszyfrowania danych wysyłanych przez dostawcę tożsamości. |
| MetadataSigning |Nie | Certyfikat x509 (zestaw kluczy RSA) służący do podpisywania metadanych protokołu SAML. Azure AD B2C używa tego klucza do podpisywania metadanych.  |

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami pracy z dostawcami tożsamości SAML w Azure AD B2C:

- [Dodawanie usług AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych](identity-provider-adfs2016-custom.md)
- [Logowanie przy użyciu kont usługi Salesforce za pośrednictwem protokołu SAML](identity-provider-salesforce-custom.md)
