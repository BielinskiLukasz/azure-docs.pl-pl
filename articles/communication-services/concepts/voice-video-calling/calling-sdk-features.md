---
title: Omówienie wywoływania biblioteki klienta usługi Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie biblioteki klienta wywołującego.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 44365dec247b9f3135a090cee397cad32598fd29
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977871"
---
# <a name="calling-client-library-overview"></a>Omówienie biblioteki klienta połączeń

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Istnieją dwie oddzielne rodziny wywołujących biblioteki klienckie dla *klientów* i *usług.* Obecnie dostępne biblioteki klienckie są przeznaczone dla środowiska użytkownika końcowego: witryny sieci Web i aplikacje natywne.

Biblioteki klienta usługi nie są jeszcze dostępne i zapewniają dostęp do niesformatowanych płaszczyzn danych dźwiękowych i wideo, które są odpowiednie do integracji z botów i innymi usługami.

## <a name="calling-client-library-capabilities"></a>Wywoływanie możliwości biblioteki klienta

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w bibliotekach klienta wywołujących usługi komunikacyjne Azure.

| Grupa funkcji | Możliwość                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Podstawowe możliwości | Umieszczanie wywołania jeden-do-jednego między dwoma użytkownikami                                                                           | ✔️   | ✔️            | ✔️  
|                   | Umieść połączenie grupy z więcej niż dwoma użytkownikami (do 350 użytkowników)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Podnieś poziom wywołania "jeden do jednego" z dwoma użytkownikami do wywołania grupy z więcej niż dwoma użytkownikami                                 | ✔️   | ✔️            | ✔️ 
|                   | Dołącz do wywołania grupy po jego rozpoczęciu                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Zapraszanie innego uczestnika VoIP do dołączenia do trwającego wywołania grupy                                                       | ✔️   | ✔️            | ✔️
|                   | Włącz/Wyłącz wideo                                                         | ✔️   | ✔️            | ✔️ 
|                   | Wycisz/Wyłącz mikrofon                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Przełączanie między kamerami                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Blokada lokalna/wstrzymanie                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Aktywny głośnik                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Wybieranie osoby mówiącej dla wywołań                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Wybieranie mikrofonu dla wywołań                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Pokaż stan uczestnika<br/>*Bezczynne, wczesne nośniki, łączenie, połączone, wstrzymane, w poczekalni, rozłączone*         | ✔️   | ✔️            | ✔️           
|                   | Pokaż stan wywołania<br/>*Wczesny nośnik, przychodzący, łączący, dzwonienie, połączenie, wstrzymanie, rozłączanie, Rozłączono* | ✔️   | ✔️            | ✔️           
|                   | Pokaż, jeśli uczestnik jest wyciszony                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Pokaż powód, dla którego Uczestnik pozostawił wywołanie                                                                       | ✔️   | ✔️            | ✔️     
| Udostępnianie ekranu    | Udostępnianie całego ekranu z poziomu aplikacji                                                                 | ✔️   | ❌            | ❌           
|                   | Udostępnianie określonej aplikacji (z listy uruchomionych aplikacji)                                                | ✔️   | ❌            | ❌           
|                   | Udostępnianie karty przeglądarki sieci Web z listy otwartych kart                                                                  | ✔️   | ❌            | ❌           
|                   | Uczestnik może wyświetlić udział ekranu zdalnego                                                                            | ✔️   | ✔️            | ✔️         
| Spis            | Wyświetl listę uczestników                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Usuwanie uczestnika                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Umieszczanie wywołania "jeden do jednego" z uczestnikiem sieci PSTN                                                                     | ✔️   | ✔️            | ✔️   
|                   | Umieść połączenie grupy z uczestnikami PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Podwyższanie poziomu wywołania "jeden do jednego" uczestnika PSTN do wywołania grupy                                                 | ✔️   | ✔️            | ✔️
|                   | Wybieranie numeru z wywołania grupy jako uczestnika sieci PSTN                                                                    | ✔️   | ✔️            | ✔️   
| Ogólne           | Testowanie mikrofonu, głośników i aparatu za pomocą usługi testowania audio (dostępnej przez wywołanie 8: echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Obsługa wywoływania przeglądarki biblioteki klienta

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek i wersji, które są obecnie dostępne.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Wywoływanie biblioteki klienta** | Chrome *, Nowa krawędź | Chrome *, Safari** | Własnego  | Własnego | Własnego | Safari * * |


* Zwróć uwagę, że oprócz poprzednich dwóch wydań jest obsługiwana Najnowsza wersja programu Chrome.<br/>

* * Należy zauważyć, że obsługiwane są wersje Safari 13.1 +. Wychodzące wideo dla programu Safari macOS nie jest jeszcze obsługiwane, ale jest obsługiwane w systemie iOS. Udostępnianie ekranu wychodzącego jest obsługiwane tylko na komputerze stacjonarnym z systemem iOS.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami wywołań](../call-flows.md)
- Informacje o [typach wywołań](../voice-video-calling/about-call-types.md)
- [Planowanie rozwiązania PSTN](../telephony-sms/plan-solution.md)
