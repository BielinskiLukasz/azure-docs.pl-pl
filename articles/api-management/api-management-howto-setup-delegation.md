---
title: Jak delegować użytkownika rejestracji i subskrypcji produktów
description: Dowiedz się, jak delegować użytkownika rejestracji i subskrypcji produktów do innej usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: c15dc83929aeaf6811f4d19bfca462abfacf4014
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892459"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Jak delegować użytkownika rejestracji i subskrypcji produktów
Delegowanie umożliwia istniejącej witryny sieci Web do obsługi projektanta znak — w/rejestracją i subskrypcji produktów, a nie za pomocą wbudowanej funkcji w portalu dla deweloperów. Dzięki temu dane użytkownika i przeprowadzania weryfikacji z tych kroków w niestandardowy sposób witryny sieci Web.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegowanie dla deweloperów Zaloguj się, a rejestracji
Aby delegować dla deweloperów, logowania i Zarejestruj się w istniejącej witryny sieci Web, należy utworzyć punkt końcowy delegowania specjalne w witrynie, która działa jako punkt wejścia dla takich żądań inicjowanych z portalu dla deweloperów usługi API Management.

Końcowe przepływ pracy zostanie w następujący sposób:

1. Kliknięć dla deweloperów Link logowania lub tworzenia konta w portalu dla deweloperów usługi API Management
2. Przeglądarka jest przekierowywana do punktu końcowego delegowania
3. W zamian przekierowuje do punktu końcowego delegowania lub przedstawia interfejsu użytkownika monitem użytkownika do logowania w lub rejestracji
4. W przypadku powodzenia użytkownik jest przekierowany z powrotem do strony portalu dla deweloperów usługi API Management, uruchomienia z

Aby rozpocząć, możemy pierwszy konfiguracji usługa API Management do rozsyłania żądań za pośrednictwem punktu końcowego delegowania. W portalu wydawcy usługi API Management, kliknij pozycję **zabezpieczeń** a następnie kliknij przycisk **delegowania** kartę. Kliknij pole wyboru, aby włączyć "Delegowanie logowania i rejestracji".

![Strony delegowania][api-management-delegation-signin-up]

* Zdecyduj, co adres URL punktu końcowego delegowania specjalne zostanie i wprowadź go w **adres URL punktu końcowego delegowania** pola. 
* W polu klucza uwierzytelniania delegowania wprowadź klucz tajny, który będzie używany do obliczania podpisu, udostępnionych Ci w celu weryfikacji upewnić się, że żądanie rzeczywiście pochodzi z usługi Azure API Management. Możesz kliknąć pozycję **Generowanie** przycisk, aby mieć usługi API Management losowo Generuj klucz dla Ciebie.

Teraz musisz utworzyć **punktu końcowego delegowania**. Musi przeprowadzić szereg akcji:

1. Otrzyma żądanie w następującej postaci:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {adres URL strony źródłowej} & ziarna = {ciąg} & sig = {ciąg}*
   > 
   > 
   
    Parametry dla logowania zapytania / Zarejestruj przypadków:
   
   * **Operacja**: Określa, jakiego rodzaju żądania delegowania jest — może być tylko **SignIn** w tym przypadku
   * **returnUrl**: adres URL strony, gdy użytkownik kliknął element Link logowania lub rejestracji
   * **ziarna**: specjalny ciąg zaburzający używana do przetwarzania danych skrót zabezpieczeń
   * **SIG**: obliczanej zabezpieczeń wyznaczania wartości skrótu do użycia w porównaniu do własnych obliczony skrót
2. Sprawdź, czy żądanie, pochodzi z usługi Azure API Management (opcjonalne, lecz zdecydowanie zalecane dla zabezpieczeń)
   
   * Obliczenia z ciągu na podstawie skrótu HMAC SHA512 **returnUrl** i **ziarna** parametry zapytania ([przykładowego kodu podanego poniżej]):
     
     > HMAC (**ziarna** + '\n' + **returnUrl**)
     > 
     > 
   * Porównaj skrótu powyżej obliczona wartość **sig** parametr zapytania. Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie odrzucić żądanie.
3. Sprawdzić, czy są odbiera żądanie dotyczące logowania w/sign telefoniczny: **operacji** parametr zapytania zostanie ustawiony na "**SignIn**".
4. Przedstawia zalogowaniem się lub Utwórz konto użytkownika za pomocą interfejsu użytkownika
5. Jeśli użytkownik jest utworzeniu konta masz utworzyć odpowiednie konto w usłudze API Management. [Tworzenie użytkownika] za pomocą interfejsu API REST zarządzania interfejsu API. Po wykonaniu tej czynności upewnij się, Ustaw identyfikator użytkownika, do tej samej znajduje się w magazynie użytkownika lub identyfikator, który można śledzenie bieżącego.
6. Po pomyślnym uwierzytelnieniu użytkownika:
   
   * [żądania tokenu logowanie jednokrotne (SSO)] za pośrednictwem interfejsu API REST zarządzania interfejsu API
   * Dołącz returnUrl parametr zapytania do adresu URL logowania jednokrotnego otrzymane z wywołania interfejsu API powyżej:
     
     > Na przykład https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * przekieruje użytkownika na powyższy adres URL do produkcji

Oprócz **SignIn** operacji, można również wykonać Zarządzanie kontami, zgodnie z poprzednich kroków i przy użyciu jednej z następujących czynności:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Należy przekazać następujące parametry zapytania dla operacji zarządzania kontem.

* **Operacja**: Określa, jakiego rodzaju żądania delegowania jest (ChangePassword, ChangeProfile lub CloseAccount)
* **Identyfikator userId**: identyfikator użytkownika, konto do zarządzania
* **ziarna**: specjalny ciąg zaburzający używana do przetwarzania danych skrót zabezpieczeń
* **SIG**: obliczanej zabezpieczeń wyznaczania wartości skrótu do użycia w porównaniu do własnych obliczony skrót

## <a name="delegate-product-subscription"> </a>Delegowanie subskrypcję produktu
Delegowanie subskrypcję produktu działa podobnie do delegowania użytkownika logowania/w górę. Końcowe przepływ pracy będzie następujący:

1. Deweloper wybiera produktu w portalu dla deweloperów usługi API Management i kliknie przycisk subskrypcji
2. Przeglądarka jest przekierowywana do punktu końcowego delegowania
3. Punktu końcowego delegowania wykonuje kroki subskrypcji produktu wymagane — to zależy od użytkownika i może pociągać za sobą przekierowania do innej strony, aby zażądać informacje rozliczeniowe pytaniem dodatkowe pytania, lub po prostu przechowywanie informacji i nie wymaga interwencji ze strony użytkownika

Aby włączyć funkcje, na **delegowania** kliknij **Deleguj subskrypcję produktu**.

Następnie upewnij się, że punkt końcowy delegowania wykonuje następujące czynności:

1. Otrzyma żądanie w następującej postaci:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operacji operation} & productId = {produktu do subskrybowania} & userId = {użytkownika zgłaszającego żądanie} & ziarna = {ciąg} & sig = {ciąg}*
   > 
   > 
   
    Parametry zapytania w przypadku subskrypcji produktu:
   
   * **Operacja**: Określa, jakiego rodzaju żądania delegowania jest. Subskrypcja produktu są żądań prawidłowe opcje:
     * "Subskrypcja": żądanie, aby dodać subskrypcji użytkownika do danego produktu za pomocą podany identyfikator (patrz poniżej)
     * "Anuluj subskrypcję": żądanie, aby anulować subskrypcji użytkownika z produktu
     * "Odnowić": żądanie odnowienia subskrypcji (na przykład, który może być wygasa)
   * **productId**: identyfikator produktu, o których użytkownik zażądał do subskrybowania
   * **Identyfikator userId**: identyfikator użytkownika, dla którego żądanie jest wysyłane
   * **ziarna**: specjalny ciąg zaburzający używana do przetwarzania danych skrót zabezpieczeń
   * **SIG**: obliczanej zabezpieczeń wyznaczania wartości skrótu do użycia w porównaniu do własnych obliczony skrót
2. Sprawdź, czy żądanie, pochodzi z usługi Azure API Management (opcjonalne, lecz zdecydowanie zalecane dla zabezpieczeń)
   
   * Obliczenia SHA512 HMAC ciągu na podstawie **productId**, **userId**, i **ziarna** parametry zapytania:
     
     > HMAC (**ziarna** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Porównaj skrótu powyżej obliczona wartość **sig** parametr zapytania. Jeśli dwa skróty są zgodne, przejdź do następnego kroku, w przeciwnym razie odrzucić żądanie.
3. Wykonywać żadnego przetwarzania subskrypcji produktu, na podstawie typu operacji żądanej w **operacji** — na przykład, rozliczenia, w razie dalszych pytań itp.
4. Na ten pomyślnie użytkownikowi produktu po swojej stronie, subskrypcja użytkownika do produktu API Management przez [wywołanie interfejsu API REST dla subskrypcji produktu].

## <a name="delegate-example-code"> </a> Przykładowy kod
Te przykłady kodu przedstawiają sposób zająć *klucz walidacji delegowania*, która jest ustawiona na ekranie delegowania w portalu wydawców na tworzenie kluczem HMAC, który jest następnie używany do weryfikacji podpisu, potwierdzające poprawność przekazanych returnUrl. Ten sam kod działa w przypadku productId i identyfikator użytkownika z niewielkich modyfikacji.

**C#Kod można wygenerować skrót returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Kod NodeJS, aby wygenerować skrót returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat delegowania zobacz poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[żądania tokenu logowanie jednokrotne (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Tworzenie użytkownika]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[wywołanie interfejsu API REST dla subskrypcji produktu]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[przykładowego kodu podanego poniżej]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
