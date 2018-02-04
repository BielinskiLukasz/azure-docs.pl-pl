---
title: Azure AD UserPrincipalName populacji
description: "Następującym dokumencie opisano, jak atrybut UserPrincipalName jest wypełnione."
author: billmath
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 1fca41a8498cec506298748acd3511a5c5802d26
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD UserPrincipalName populacji

W tym artykule opisano, jak atrybut UserPrincipalName jest wypełniana w usłudze Azure Active Directory (Azure AD).
Wartość atrybutu UserPrincipalName jest nazwa użytkownika konta użytkowników usługi Azure AD.

## <a name="upn-terminology"></a>Terminologia nazwy UPN
Następującą terminologią jest używana w tym artykule:

|Termin|Opis|
|-----|-----|
|Domena początkowa|Domena domyślna (onmicrosoft.com) w dzierżawie usługi Azure AD. Na przykład: contoso.onmicrosoft.com.|
|Adres routingu E-mail Online firmy Microsoft (MOERA)|Usługi Azure AD oblicza MOERA z atrybutów usługi Azure AD MailNickName i domeny początkowej usługi Azure AD jako &lt;MailNickName&gt;&#64;&lt; domena początkowa&gt;.|
|Atrybut mailNickName lokalnej|Atrybut w usłudze Active Directory, którego wartość reprezentuje alias użytkownika w organizacji programu Exchange.|
|Atrybut poczty lokalnej|Atrybut w usłudze Active Directory, w których wartość reprezentuje adres e-mail użytkownika|
|Podstawowy adres SMTP|Adres e-mail podstawowego obiektu adresatów programu Exchange. Na przykład SMTP:user@contoso.com.|
|Alternatywnego Identyfikatora logowania|Atrybut lokalnymi innego niż UserPrincipalName, takich jak atrybut poczty, używane do logowania.|

## <a name="what-is-userprincipalname"></a>Co to jest UserPrincipalName?
UserPrincipalName jest atrybut, który jest Internet stylu nazwa logowania użytkownika na podstawie standardu Internet [RFC 822](http://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Format nazwy UPN
Nazwy UPN składa się z prefiksu nazwy UPN (nazwa konta użytkownika) i sufiks nazwy UPN (nazwa domeny DNS). Prefiks jest połączony z za pomocą sufiksu "@" symbolu. Na przykład "someone@example.com". Nazwy UPN musi być unikatowa wśród wszystkich obiektów podmiotów zabezpieczeń w ramach lasu katalogu. 

## <a name="upn-in-azure-ad"></a>Nazwy UPN w usłudze Azure AD 
Nazwy UPN jest używany przez usługę Azure AD, aby zezwolić użytkownikom na logowanie.  Nazwa UPN, które użytkownik może używać, zależy od tego, czy domena została zweryfikowana.  Jeśli domena została zweryfikowana, a następnie użytkownik z tego sufiksu będą mogli zalogować się do usługi Azure AD.  

Ten atrybut jest zsynchronizowany przy użyciu usługi Azure AD Connect.  Podczas instalacji można wyświetlić domeny, które zostały zweryfikowane oraz te, które nie.

   ![Niezweryfikowane domeny](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatywnego Identyfikatora logowania
W niektórych środowiskach, ze względu na zasady obowiązujące w firmie lub zależności aplikacji biznesowych z lokalnymi użytkownicy końcowi mogą być tylko znane swój adres e-mail, a nie ich nazwy UPN.

Alternatywnego Identyfikatora logowania umożliwia skonfigurowanie środowiska logowania z której użytkownicy mogą logować się przy użyciu atrybutu innego niż ich nazwy UPN, taki jak adres e-mail.

Aby włączyć alternatywnego Identyfikatora logowania z usługą Azure AD, żadne czynności dodatkowe konfiguracje są wymagane, gdy za pomocą usługi Azure AD Connect. Alternatywny identyfikator można skonfigurować bezpośrednio z poziomu kreatora. Zobacz temat usługi Azure AD logowania w konfiguracji dla użytkowników w sekcji synchronizacja. W obszarze **główna nazwa użytkownika** listy rozwijanej, wybierz atrybut alternatywnego identyfikatora.

![Niezweryfikowane domeny](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego Identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) i [Konfiguracja logowania w usłudze Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufiks głównej nazwy użytkownika nie została zweryfikowana
Jeśli sufiks identyfikator logowania lokalnego UserPrincipalName atrybutu/alternatywny weryfikacji dzierżawy Azure AD do MOERA jest ustawiona wartość atrybutu Azure AD UserPrincipalName. Usługi Azure AD oblicza MOERA z atrybutów usługi Azure AD MailNickName i domeny początkowej usługi Azure AD jako &lt;MailNickName&gt;&#64;&lt; domena początkowa&gt;.

## <a name="verified-upn-suffix"></a>Zweryfikowano sufiks nazwy UPN
Jeśli atrybut UserPrincipalName lokalnymi/alternatywny sufiks Identyfikatora logowania jest weryfikowany z dzierżawą usługi Azure AD, a następnie wartość atrybutu Azure AD UserPrincipalName ma być taka sama jak wartość Identyfikatora lokalnej UserPrincipalName atrybutu/alternatywny identyfikator logowania.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Obliczanie wartości atrybutu w usłudze Azure AD MailNickName
Ponieważ do MOERA można ustawić wartości atrybutu AD UserPrincipalName platformy Azure, należy zrozumieć, jak jest obliczana wartość atrybutu Azure AD MailNickName jest prefiksem MOERA.

Jeśli obiekt użytkownika jest synchronizowane dzierżawy usługi Azure AD po raz pierwszy, usługi Azure AD sprawdza następujące czynności w podanej kolejności i ustawia wartość atrybutu MailNickName pierwszy istniejący:

- Atrybut mailNickName lokalnej
- Prefiks atrybutu poczty lokalnej
- Prefiks podstawowego adresu SMTP
- Prefiks Identyfikatora logowania atrybutu/alternatywny userPrincipalName lokalnej

Podczas aktualizacji obiektu użytkownika są synchronizowane dzierżawy Azure AD, Azure AD aktualizuje wartość atrybutu MailNickName tylko w przypadku braku aktualizacji wartość atrybutu mailNickName lokalnymi.

>[!IMPORTANT]
>Ponownie oblicza wartości atrybutu UserPrincipalName usługi Azure AD, tylko w przypadku aktualizacji do wartości Identyfikatora logowania lokalnego UserPrincipalName atrybutu/alternatywny są zsynchronizowane dzierżawy Azure AD. 
>
>Zawsze, gdy usługi Azure AD ponownie oblicza atrybutu UserPrincipalName, oblicza również ponownie MOERA. 

## <a name="upn-scenarios"></a>Scenariusze nazwy UPN
Poniżej przedstawiono przykładowe scenariusze dotyczące sposobu nazwa UPN jest obliczany na podstawie danego scenariusza.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenariusz 1:-Zweryfikować sufiks nazwy UPN — synchronizacji początkowej

Obiekt użytkownika lokalnego:
- mailNickName: &lt;nieustawiona&gt;
- poczty:us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName: us3@contoso.com"

Zsynchronizowane obiekt użytkownika do dzierżawy Azure AD po raz pierwszy
- Atrybut Azure AD MailNickName do prefiksu atrybut poczty lokalnymi.
- Ustaw MOERA &lt;MailNickName&gt;&#64;&lt; domena początkowa&gt;.
- Atrybut Azure AD UserPrincipalName na MOERA.

Obiektu użytkownika w usłudze Azure AD dzierżawy:
- MailNickName: us1           
- UserPrincipalName:us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenariusz 2: Sufiks-zweryfikować nazwy UPN — zestaw lokalną atrybutu mailNickName

Obiekt użytkownika lokalnego:
- mailNickName: us4
- poczty:us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName:us3@contoso.com

Synchronizowanie aktualizacji w atrybucie mailNickName lokalnej do dzierżawy Azure AD
- Aktualizacja usługi Azure AD MailNickName atrybutu z atrybutem mailNickName lokalnymi.
- Ponieważ nie ma aktualizacji do atrybutu userPrincipalName lokalnymi, nie została zmieniona z atrybutem Azure AD UserPrincipalName.

Obiektu użytkownika w usłudze Azure AD dzierżawy:
- MailNickName: us4
- UserPrincipalName:us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenariusz 3: Sufiks-zweryfikować nazwy UPN — aktualizacja lokalną atrybutu userPrincipalName

Obiekt użytkownika lokalnego:
- mailNickName: us4
- poczty:us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName:us5@contoso.com

Synchronizowanie aktualizacji na atrybut userPrincipalName lokalnej do dzierżawy Azure AD
- Aktualizacja atrybutu userPrincipalName lokalnymi wyzwala ponowne obliczenie atrybutu MOERA i Azure AD UserPrincipalName.
- Ustaw MOERA &lt;MailNickName&gt;&#64;&lt; domena początkowa&gt;.
- Atrybut Azure AD UserPrincipalName na MOERA.

Obiektu użytkownika w usłudze Azure AD dzierżawy:
- MailNickName: us4
- UserPrincipalName:us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-on-premises-mail-attribute-and-primary-smtp-address"></a>Scenariusz 4: Sufiks-zweryfikować nazwy UPN — aktualizacja lokalne atrybut poczty i podstawowego adresu SMTP

Obiekt użytkownika lokalnego:
- mailNickName: us4
- poczty:us6@contoso.com
- proxyAddresses: {SMTP:us7@contoso.com}
- userPrincipalName:us5@contoso.com

Synchronizowanie aktualizacji na atrybut poczty lokalnymi i podstawowego adresu SMTP do dzierżawy Azure AD
- Po początkowej synchronizacji obiektu user aktualizacji do lokalnej poczty atrybutu i podstawowego adresu SMTP nie ma wpływu na usługi Azure AD MailNickName ani UserPrincipalName atrybutu.

Obiektu użytkownika w usłudze Azure AD dzierżawy:
- MailNickName: us4
- UserPrincipalName:us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenariusz 5: Sufiks nazwy UPN zweryfikowano — aktualizacja lokalnych sufiks domeny atrybutu userPrincipalName

Obiekt użytkownika lokalnego:
- mailNickName: us4
- poczty:us6@contoso.com
- proxyAddresses: {SMTP:us7@contoso.com}
- serPrincipalName:us5@verified.contoso.com

Synchronizowanie aktualizacji na atrybut userPrincipalName lokalnej dzierżawcy usługi Azure AD
- Zaktualizuj przy ponownych obliczeniach wyzwalaczy atrybut userPrincipalName lokalną atrybutu AD UserPrincipalName platformy Azure.
- Ustaw atrybut Azure AD UserPrincipalName do atrybutu userPrincipalName lokalnego jako sufiks nazwy UPN są weryfikowane z dzierżawą usługi Azure AD.

Obiektu użytkownika w usłudze Azure AD dzierżawy:
- MailNickName: us4     
- UserPrincipalName:us5@verified.contoso.com

## <a name="next-steps"></a>Następne kroki
- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
- [Niestandardowa instalacja programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
