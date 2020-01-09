---
title: Niestandardowe weryfikacje wiadomości e-mail
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować weryfikację wiadomości e-mail wysyłanych do klientów podczas rejestrowania się w celu korzystania z aplikacji obsługujących Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3d9316f42c8d0ac5b44cda2e484ca4c92110813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479152"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C

Użyj niestandardowych wiadomości e-mail w programie Azure Active Directory B2C (Azure AD B2C), aby wysyłać niestandardowe wiadomości e-mail do użytkowników, którzy logują się w celu korzystania z aplikacji. Korzystając z usługi [DisplayControls](display-controls.md) (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy, można użyć własnego szablonu wiadomości E-mail oraz *z adresu:* adres i temat, a także do obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Niestandardowa Weryfikacja poczty e-mail wymaga użycia dostawcy poczty e-mail innej firmy, takiego jak [SendGrid](https://sendgrid.com) lub [SparkPost](https://sparkpost.com), niestandardowy interfejs API REST lub dowolny dostawca poczty e-mail oparty na protokole HTTP (w tym własny). W tym artykule opisano konfigurowanie rozwiązania korzystającego z SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid

Jeśli jeszcze tego nie masz, Zacznij od skonfigurowania konta SendGrid (klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail miesięcznie). Instrukcje dotyczące instalacji znajdują się w sekcji [Tworzenie konta SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) w temacie [jak wysyłać wiadomości E-mail przy użyciu usługi SendGrid z platformą Azure](../sendgrid-dotnet-how-to-send-email.md).

Pamiętaj, aby ukończyć sekcję, w której [tworzysz klucz interfejsu API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Zapisz klucz interfejsu API do użycia w późniejszym kroku.

## <a name="create-azure-ad-b2c-policy-key"></a>Utwórz klucz zasad Azure AD B2C

Następnie Zapisz klucz interfejsu API SendGrid w kluczu zasad Azure AD B2C, aby uzyskać informacje dotyczące zasad.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz pozycję `Manual`.
1. Wprowadź **nazwę** klucza zasad. Na przykład `SendGridSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza**wybierz pozycję `Signature`.
1. Wybierz pozycję **Utwórz**.

## <a name="create-sendgrid-template"></a>Utwórz szablon SendGrid

Przy użyciu konta SendGrid i klucza interfejsu API SendGrid przechowywanego w kluczu zasad Azure AD B2C Utwórz [dynamiczny szablon transakcyjny](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. W witrynie SendGrid Otwórz stronę [Szablony transakcyjne](https://sendgrid.com/dynamic_templates) i wybierz pozycję **Utwórz szablon**.
1. Wprowadź unikatową nazwę szablonu, taką jak `Verification email`, a następnie wybierz pozycję **Zapisz**.
1. Aby rozpocząć edytowanie nowego szablonu, wybierz pozycję **Dodaj wersję**.
1. Wybierz **Edytor kodu** , a następnie **Kontynuuj**.
1. W edytorze HTML wklej następujący szablon HTML lub użyj własnych. Parametry `{{otp}}` i `{{email}}` zostaną automatycznie zamienione na wartość hasła jednorazowego i adres e-mail użytkownika.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Rozwiń pozycję **Ustawienia** po lewej stronie, a w polu **temat wiadomości e-mail**wprowadź `{{subject}}`.
1. Wybierz pozycję **Zapisz szablon**.
1. Wróć do strony **Szablony transakcyjne** , wybierając strzałkę wstecz.
1. Zapisz **Identyfikator** szablonu, który został utworzony do użycia w późniejszym kroku. Na przykład `d-989077fbba9746e89f3f6411f596fb96`. Ten identyfikator należy określić podczas [dodawania transformacji oświadczeń](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Dodawanie Azure AD B2C typów roszczeń

W zasadach Dodaj następujące typy roszczeń do elementu `<ClaimsSchema>` w `<BuildingBlocks>`.

Te typy oświadczeń są niezbędne do generowania i weryfikowania adresu e-mail przy użyciu kodu hasła jednorazowego (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Dodawanie transformacji oświadczeń

Następnie konieczne jest przekształcenie oświadczeń, aby uzyskać dane wyjściowe oświadczenia ciągu JSON, które będzie treścią żądania wysłanego do SendGrid.

Struktura obiektu JSON jest definiowana przez identyfikatory w notacji kropkowej obiektu InputParameters i TransformationClaimTypes InputClaims. Liczby w zapisie kropkowym oznaczają tablice. Wartości pochodzą z wartości InputClaims i właściwości InputParameters "". Aby uzyskać więcej informacji na temat transformacji oświadczeń JSON, zobacz [przekształcenia oświadczeń JSON](json-transformations.md).

Dodaj następującą transformację oświadczeń do elementu `<ClaimsTransformations>` w `<BuildingBlocks>`. Wprowadź następujące aktualizacje do przekształcenia XML oświadczeń:

* Zaktualizuj `template_id` wartość InputParameter z IDENTYFIKATORem szablonu transakcyjnego SendGrid utworzonego wcześniej w [szablonie tworzenie SendGrid](#create-sendgrid-template).
* Zaktualizuj wartość adresu `from.email`. Użyj prawidłowego adresu e-mail, aby pomóc w zapobieganiu oznaczania wiadomości e-mail weryfikacyjnej jako spamu.
* Zaktualizuj wartość parametru wejściowego `personalizations.0.dynamic_template_data.subject` wiersza tematu z wierszem tematu odpowiednim dla Twojej organizacji.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Dodaj definicję zawartości DataUri

Poniżej przekształceń oświadczeń w `<BuildingBlocks>`Dodaj następujący [ContentDefinition](contentdefinitions.md) , aby odwołać się do identyfikatora URI danych 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Tworzenie elementu DisplayControl

Kontrolka wyświetlania weryfikacji służy do weryfikowania adresu e-mail z kodem weryfikacyjnym, który jest wysyłany do użytkownika.

Ten przykładowy formant wyświetlania jest skonfigurowany do:

1. Zbierz typ `email`ego adresu z użytkownika.
1. Poczekaj, aż użytkownik poda `verificationCode` typ zgłoszenia przy użyciu kodu wysłanego do użytkownika.
1. Zwróć `email` z powrotem do profilu technicznego z własnym potwierdzeniem, który ma odwołanie do tego formantu ekranu.
1. Za pomocą akcji `SendCode` Wygeneruj kod OTP i Wyślij wiadomość e-mail z kodem OTP do użytkownika.

![Wyślij wiadomość e-mail z kodem weryfikacyjnym](media/custom-email/display-control-verification-email-action-01.png)

W obszarze definicje zawartości nadal w obszarze `<BuildingBlocks>`Dodaj do zasad następujący [formant DisplayControl](display-controls.md) typu [VerificationControl](display-control-verification.md) .

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Dodaj profile techniczne OTP

Profil techniczny `GenerateOtp` generuje kod dla adresu e-mail. Profil techniczny `VerifyOtp` weryfikuje kod skojarzony z adresem e-mail. Można zmienić konfigurację formatu i czas wygaśnięcia hasła jednorazowego. Więcej informacji o profilach technicznych OTP znajduje się w temacie [Definiowanie profilu technicznego hasła jednorazowego](one-time-password-technical-profile.md).

Dodaj następujące profile techniczne do elementu `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Dodawanie profilu technicznego interfejsu API REST

Profil techniczny interfejsu API REST generuje zawartość wiadomości e-mail (przy użyciu formatu SendGrid). Więcej informacji o profilach technicznych RESTful znajduje się w temacie [Definiowanie profilu technicznego RESTful](restful-technical-profile.md).

Podobnie jak w przypadku profilów technicznych OTP, Dodaj następujące profile techniczne do elementu `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Utwórz odwołanie do elementu DisplayControl

W ostatnim kroku Dodaj odwołanie do utworzonego elementu DisplayControl. Jeśli używasz wcześniejszej wersji zasad Azure AD B2C, Zastąp istniejący `LocalAccountSignUpWithLogonEmail` z własnym profilem technicznym. Ten profil techniczny używa `DisplayClaims` z odwołaniem do elementu DisplayControl.

Aby uzyskać więcej informacji, Zobacz Profil techniczny i [formant DisplayControl](display-controls.md)z [własnym potwierdzeniem](restful-technical-profile.md) .

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Następne kroki

Przykład niestandardowych zasad weryfikacji poczty e-mail można znaleźć w witrynie GitHub:

[Niestandardowa Weryfikacja poczty e-mail — DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Aby uzyskać informacje o korzystaniu z niestandardowego interfejsu API REST lub dowolnego dostawcy poczty e-mail opartego na protokole HTTP, zobacz [Definiowanie profilu technicznego RESTful w zasadach niestandardowych Azure AD B2C](restful-technical-profile.md).
