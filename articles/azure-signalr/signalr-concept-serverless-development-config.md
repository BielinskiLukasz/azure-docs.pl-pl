---
title: Opracowywanie & konfigurowania Azure Functions aplikacji — usługa Azure Signal
description: Szczegółowe informacje na temat sposobu tworzenia i konfigurowania aplikacji bezserwerowych w czasie rzeczywistym przy użyciu Azure Functions i usługi Azure Signal Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523174"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions opracowywanie i Konfigurowanie za pomocą usługi Azure Signal Service

Aplikacje Azure Functions mogą korzystać z [powiązań usługi Azure sygnalizujących](../azure-functions/functions-bindings-signalr-service.md) w celu dodawania funkcji w czasie rzeczywistym. Aplikacje klienckie korzystają z zestawów SDK klienta dostępnych w kilku językach, aby nawiązać połączenie z usługą Azure Signal Service i odbierać komunikaty w czasie rzeczywistym.

W tym artykule opisano koncepcje dotyczące tworzenia i konfigurowania aplikacji funkcji platformy Azure, która jest zintegrowana z usługą sygnalizującej.

## <a name="signalr-service-configuration"></a>Konfiguracja usługi sygnalizującego

Usługę sygnałów platformy Azure można skonfigurować w różnych trybach. W przypadku używania z Azure Functions należy skonfigurować usługę w trybie *Bezserwerowym* .

W Azure Portal odszukaj stronę *Ustawienia* zasobu usługi sygnalizującego. Ustaw *tryb usługi* na *bezserwerowy*.

![Tryb usługi sygnalizującej](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Opracowywanie usługi Azure Functions

Aplikacja bezserwerowa działająca w czasie rzeczywistym utworzona przy użyciu usług Azure Functions i Azure SignalR Service zwykle wymaga dwóch funkcji Azure Functions:

* Funkcja „negotiate” (negocjacja) wywoływana przez klienta w celu uzyskania prawidłowego tokenu dostępu do usługi SignalR i adresu URL punktu końcowego usługi
* Co najmniej jedna funkcja, która wysyła komunikaty lub zarządza członkostwem w grupie

### <a name="negotiate-function"></a>Funkcja Negotiate

Aplikacja kliencka wymaga ważnego tokenu dostępu, aby połączyć się z usługą Azure Signal. Token dostępu może być anonimowy lub uwierzytelniony dla danego identyfikatora użytkownika. Aplikacje usługi sygnalizacji bezserwerowej wymagają punktu końcowego HTTP o nazwie "Negotiate", aby uzyskać token i inne informacje o połączeniu, takie jak adres URL punktu końcowego usługi sygnalizującego.

Użyj wyzwalanej przez protokół HTTP funkcji platformy Azure i powiązania danych wejściowych *SignalRConnectionInfo* w celu wygenerowania obiektu informacji o połączeniu. Funkcja musi mieć trasę HTTP kończącą się na `/negotiate`.

Aby uzyskać więcej informacji na temat tworzenia funkcji Negotiate, zobacz odwołanie do [powiązania danych wejściowych *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Aby dowiedzieć się więcej o sposobie tworzenia uwierzytelnionego tokenu, zapoznaj się z tematem [Korzystanie z uwierzytelniania App Service](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Wysyłanie komunikatów i zarządzanie członkostwem w grupie

Użyj powiązania danych wyjściowych *sygnalizującego* , aby wysyłać komunikaty do klientów podłączonych do usługi Azure signaler. Komunikaty można rozgłaszać do wszystkich klientów lub wysyłać je do podzbioru klientów, którzy są uwierzytelniani przy użyciu określonego identyfikatora użytkownika lub zostali dodani do określonej grupy.

Użytkowników można dodawać do co najmniej jednej grupy. Można również użyć powiązania danych wyjściowych *sygnalizującego* , aby dodać lub usunąć użytkowników do/z grup.

Aby uzyskać więcej informacji, zobacz [Informacje o powiązaniach wyjściowych *sygnalizującego* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Centra sygnałów

Sygnalizujący ma koncepcję "centrów". Każde połączenie z klientem i każdy komunikat wysyłany z Azure Functions jest objęty zakresem określonego centrum. Koncentratorów można używać jako sposobu rozdzielania połączeń i komunikatów w logiczne przestrzenie nazw.

## <a name="client-development"></a>Opracowywanie klienta

Aplikacje klienckie sygnalizujące mogą korzystać z zestawu SDK klienta sygnalizującego w jednym z kilku języków, aby łatwo łączyć się z usługą Azure sygnalizującą i odbierać komunikaty z nich.

### <a name="configuring-a-client-connection"></a>Konfigurowanie połączenia klienta

Aby nawiązać połączenie z usługą sygnalizującego, klient musi zakończyć pomyślne negocjowanie połączenia, które składa się z następujących kroków:

1. Prześlij żądanie do punktu końcowego *negocjowania* protokołu HTTP omówionego powyżej, aby uzyskać prawidłowe informacje o połączeniu
1. Połącz z usługą sygnalizującą przy użyciu adresu URL punktu końcowego usługi i tokenu dostępu uzyskanego z punktu końcowego *negocjowania*

Zestawy SDK klienta sygnalizujące już zawierają logikę wymaganą do wykonania uzgadniania negocjacji. Przekaż adres URL punktu końcowego negocjowania, który zostanie pomniejszony o segment `negotiate`, na `HubConnectionBuilder`zestawu SDK. Oto przykład kodu JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Zgodnie z Konwencją zestaw SDK automatycznie dołącza `/negotiate` do adresu URL i używa go do rozpoczęcia negocjacji.

> [!NOTE]
> Jeśli używasz skryptu JavaScript/TypeScript SDK w przeglądarce, musisz [włączyć udostępnianie zasobów między źródłami (CORS)](#enabling-cors) na aplikacja funkcji.

Aby uzyskać więcej informacji na temat korzystania z zestawu SDK klienta sygnalizującego, zapoznaj się z dokumentacją języka:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Wysyłanie komunikatów z klienta do usługi

Mimo że zestaw SDK sygnalizujący umożliwia aplikacjom klienckim wywoływanie logiki zaplecza w centrum sygnałów, ta funkcja nie jest jeszcze obsługiwana w przypadku korzystania z usługi sygnalizującej z Azure Functions. Użyj żądań HTTP do wywołania Azure Functions.

## <a name="azure-functions-configuration"></a>Konfiguracja Azure Functions

Aplikacje funkcji platformy Azure, które integrują się z usługą Azure Signal Service, można wdrożyć podobnie jak w przypadku dowolnej typowej aplikacji funkcji platformy Azure, korzystając z takich technik jak [ciągłe wdrażanie](../azure-functions/functions-continuous-deployment.md), [wdrażanie zip](../azure-functions/deployment-zip-push.md)i [Uruchamianie z pakietu](../azure-functions/run-functions-from-deployment-package.md).

Istnieje jednak kilka specjalnych zagadnień dotyczących aplikacji, które używają powiązań usługi sygnalizującego. Jeśli klient jest uruchomiony w przeglądarce, należy włączyć funkcję CORS. A jeśli aplikacja wymaga uwierzytelniania, można zintegrować punkt końcowy negocjowany z uwierzytelnianiem App Service.

### <a name="enabling-cors"></a>Włączanie mechanizmu CORS

Klient JavaScript/TypeScript wysyła żądania HTTP do funkcji Negotiate, aby zainicjować negocjowanie połączenia. Gdy aplikacja kliencka jest hostowana w innej domenie niż aplikacja funkcji platformy Azure, należy włączyć udostępnianie zasobów między źródłami (CORS) w aplikacji funkcji lub przeglądarka zablokuje żądania.

#### <a name="localhost"></a>Lokalnym

Podczas uruchamiania aplikacji funkcji na komputerze lokalnym można dodać sekcję `Host` do pliku *Local. Settings. JSON* w celu włączenia mechanizmu CORS. W sekcji `Host` Dodaj dwie właściwości:

* `CORS` — wprowadź podstawowy adres URL, który jest źródłem aplikacji klienckiej.
* `CORSCredentials` — ustaw ją na `true`, aby zezwolić na żądania "withCredentials"

Przykład:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Azure Functions mechanizmu CORS w chmurze

Aby włączyć funkcję CORS w aplikacji funkcji platformy Azure, przejdź do ekranu konfiguracja mechanizmu CORS na karcie *funkcje platformy* w aplikacji funkcji w Azure Portal.

> [!NOTE]
> Konfiguracja mechanizmu CORS nie jest jeszcze dostępna w planie zużycia w systemie Azure Functions Linux. Aby włączyć mechanizm CORS, użyj [usługi Azure API Management](#cloud---azure-api-management) .

Mechanizm CORS z dostępem-Control-Allow-Credentials musi być włączony dla klienta sygnalizującego, aby wywoływać funkcję Negotiate. Zaznacz pole wyboru, aby je włączyć.

W sekcji *dozwolone źródła* Dodaj wpis o podstawowym adresie URL aplikacji sieci Web.

![Konfigurowanie mechanizmu CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Chmura — API Management platformy Azure

Usługa Azure API Management udostępnia bramę interfejsu API, która dodaje możliwości do istniejących usług zaplecza. Można go użyć do dodania mechanizmu CORS do aplikacji funkcji. Oferuje on warstwę zużycia z cenami płatnymi za akcję i miesięczną bezpłatną dotacji.

Zapoznaj się z dokumentacją API Management, aby uzyskać informacje na temat [importowania aplikacji funkcji platformy Azure](../api-management/import-function-app-as-api.md). Po zaimportowaniu można dodać zasady dla ruchu przychodzącego, aby włączyć funkcję CORS z obsługą funkcji Kontrola dostępu — Zezwalaj na poświadczenia.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Skonfiguruj klientów sygnalizujących, aby używały adresu URL API Management.

### <a name="using-app-service-authentication"></a>Korzystanie z uwierzytelniania App Service

Azure Functions ma wbudowane uwierzytelnianie obsługujące popularnych dostawców, takich jak Facebook, Twitter, konto Microsoft, Google i Azure Active Directory. Tę funkcję można zintegrować z powiązaniem *SignalRConnectionInfo* w celu utworzenia połączeń z usługą Azure signaler, która została UWIERZYTELNIONA jako identyfikator użytkownika. Aplikacja może wysyłać wiadomości za pomocą powiązania danych wyjściowych *sygnalizującego* , które są przeznaczone dla tego identyfikatora użytkownika.

W Azure Portal na karcie *funkcje platformy* aplikacji funkcji Otwórz okno Ustawienia *uwierzytelniania/autoryzacji* . Postępuj zgodnie z dokumentacją [App Service Authentication](../app-service/overview-authentication-authorization.md) , aby skonfigurować uwierzytelnianie przy użyciu wybranego przez siebie dostawcy tożsamości.

Po skonfigurowaniu uwierzytelnione żądania HTTP będą zawierać odpowiednio nagłówki `x-ms-client-principal-name` i `x-ms-client-principal-id` zawierające nazwę użytkownika i identyfikator logowania uwierzytelnionej tożsamości.

Za pomocą tych nagłówków w konfiguracji powiązania *SignalRConnectionInfo* można tworzyć połączenia uwierzytelnione. Poniżej znajduje się przykład C# negocjowania funkcji korzystającej z nagłówka `x-ms-client-principal-id`.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Następnie można wysyłać komunikaty do tego użytkownika przez ustawienie właściwości `UserId` komunikatu sygnalizującego.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Aby uzyskać informacje o innych językach, zapoznaj się z tematem [powiązania usługi Azure Signal](../azure-functions/functions-bindings-signalr-service.md) reference dla Azure Functions.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia i konfigurowania aplikacji usługi sygnałów bezserwerowych przy użyciu Azure Functions. Spróbuj samodzielnie utworzyć aplikację, korzystając z przewodnika Szybki start lub samouczków na [stronie Przegląd usługi sygnalizującej](index.yml).