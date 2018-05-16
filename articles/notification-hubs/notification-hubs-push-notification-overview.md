---
title: Co to jest usługa Azure Notification Hubs?
description: Dowiedz się, jak dodawać możliwości powiadomień push przy użyciu usługi Azure Notification Hubs.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 44086bc20966d9c01ff27dda68f837101c71a778
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-notification-hubs"></a>Co to jest usługa Azure Notification Hubs?
Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Usługa Notification Hubs działa świetnie w scenariuszach przeznaczonych dla firm i użytkowników. Poniżej przedstawiono kilka przykładowych scenariuszy:

- Wysyłanie powiadomień o najważniejszych wiadomościach do milionów osób z małym opóźnieniem.
- Wysyłanie kuponów opartych na lokalizacji do segmentów zainteresowanych użytkowników.
- Wysyłanie powiadomień o zdarzeniach do użytkowników lub grup dla aplikacji związanych z mediami, sportem i finansami oraz gier.
- Wypychanie promocyjnej zawartości do aplikacji w celu zaangażowania klientów i wprowadzania aplikacji na rynek.
- Powiadamianie użytkowników o zdarzeniach dotyczących przedsiębiorstwa, takich jak nowe wiadomości lub elementy robocze.
- Wysyłanie kodów uwierzytelniania wieloskładnikowego.

## <a name="what-are-push-notifications"></a>Co to są powiadomienia push?
Powiadomienia push to forma komunikacji między aplikacją i użytkownikiem, w przypadku której użytkownicy aplikacji mobilnych są powiadamiani o wybranych żądanych informacjach, zazwyczaj w oknie podręcznym lub oknie dialogowym. Ogólnie mówiąc, użytkownicy mogą wyświetlić lub odrzucić komunikat. Wybranie pierwszej opcji powoduje otwarcie aplikacji mobilnej, które przekazała powiadomienie.

Powiadomienia push są istotne w przypadku aplikacji użytkowników, ponieważ powodują zwiększenie zaangażowania i użycia aplikacji, a w przypadku aplikacji dla przedsiębiorstw, ponieważ są używane podczas codziennej komunikacji obejmującej informacje biznesowe. Jest to najlepszy sposób komunikowania się aplikacji z użytkownikiem, ponieważ umożliwia oszczędzanie zasilania w urządzeniach przenośnych, działa elastycznie dla nadawców powiadomień i jest dostępny, gdy odpowiednie aplikacje są nieaktywne.

Aby uzyskać więcej informacji o powiadomieniach push dla kilku popularnych platform, zobacz następujące tematy: 
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Jak działają powiadomienia push?
Powiadomienia push są dostarczane przy użyciu infrastruktur poszczególnych platform nazywanych *systemami powiadomień platformy* (PNS, Platform Notification System). Oferują one podstawowe funkcje wypychania, które służą do dostarczania komunikatu do urządzenia przy użyciu podanego dojścia i nie mają wspólnego interfejsu. Aby wysłać powiadomienie do wszystkich klientów w aplikacji w wersjach dla systemów iOS, Android i Windows, deweloper musi pracować z następującymi usługami: Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) i usługa powiadomień WNS.

Poniżej przedstawiono sposób działania na wysokim poziomie:

1. Aplikacja kliencka decyduje o tym, że chce otrzymywać powiadomienia. W związku z tym kontaktuje się z odpowiednim systemem powiadomień platformy, aby pobrać jego unikatowe i tymczasowe dojścia wypychania. Typ dojścia zależy od systemu (na przykład usługa WNS ma identyfikatory URI, a usługa APNS ma tokeny).
2. Aplikacja kliencka przechowuje to dojście w zapleczu aplikacji lub dostawcy.
3. W celu wysłania powiadomienia push zaplecze kontaktuje się z systemem powiadomień platformy przy użyciu dojścia do określonej aplikacji klienckiej.
4. System powiadomień platformy przekazuje następnie powiadomienie do urządzenia określonego przez dojście.

![Przepływ pracy powiadomienia push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Wyzwania związane z powiadomieniami push
Systemy powiadomień platformy to zaawansowane rozwiązania. Deweloper aplikacji musi jednak wykonać dużo pracy, aby zaimplementować nawet typowe scenariusze dotyczące powiadomień push, takie jak emitowanie powiadomień push do segmentów użytkowników.

Wypychanie powiadomień wymaga złożonej infrastruktury niezwiązanej z główną logiką biznesową aplikacji. Niektóre wyzwania dotyczące infrastruktury to:

- **Zależności dotyczące platformy**
    - Zaplecze musi mieć złożoną i trudną w obsłudze logikę zależną od platformy, aby wysyłać powiadomienia do urządzeń na różnych platformach, ponieważ systemy powiadomień platformy nie są ujednolicone.
- **Skalowanie**
    - Zgodnie z zaleceniami dotyczącymi systemu powiadomień platformy tokeny urządzeń muszą być odświeżane przy każdym uruchomieniu aplikacji. Zaplecze obsługuje duży ruch i dużą liczbę operacji uzyskiwania dostępu do bazy danych w celu zachowania aktualności tokenów. W przypadku zwiększenia liczby urządzeń do setek i tysięcy milionów sztuk koszt tworzenia i obsługi tej infrastruktury jest ogromny.
    - Większość systemów powiadomień platformy nie obsługuje emisji do wielu urządzeń. Prosta emisja do milionów urządzeń powoduje miliony wywołań systemów powiadomień platformy. Skalowanie tej ilości ruchu sieciowego z minimalnym opóźnieniem to poważne zadanie.
- **Routing** 
    - Mimo że systemy powiadomień platformy umożliwiają wysyłanie komunikatów do urządzeń, większości powiadomień aplikacji jest przeznaczona dla użytkowników lub grup zainteresowań. Zaplecze musi obsługiwać rejestr, aby kojarzyć urządzenia z grupami zainteresowań, użytkownikami, właściwościami itd. Ten narzut zwiększa czas wprowadzania aplikacji na rynek oraz koszty jej obsługi.

## <a name="why-use-azure-notification-hubs"></a>Dlaczego warto korzystać z usługi Azure Notification Hubs?
Usługa Notification Hubs eliminuje wszystkie złożone elementy skojarzone z samodzielnym wypychaniem powiadomień z zaplecza aplikacji. Jej międzyplatformowa, skalowana w poziomie infrastruktura powiadomień push zmniejsza ilość kodowania i upraszcza zaplecze. Dzięki usłudze Notification Hubs urządzenia są odpowiedzialne jedynie za rejestrację swoich dojść systemu powiadomień platformy w centrum, a zaplecze wysyła komunikaty do użytkowników lub grup zainteresowań, jak przedstawiono na poniższym rysunku:

![Diagram centrum powiadomień](./media/notification-hubs-overview/notification-hub-diagram.png)

Centra powiadomień to gotowy do użycia aparat wypychania, który oferuje następujące korzyści:

- **Obsługa wielu platform**
    - Obsługa wszystkich głównych platform wypychania, w tym systemów iOS, Android, Windows, Kindle i Baidu.
    - Wspólny interfejs wypychania do wszystkich platform w formatach specyficznych dla platformy lub niezależnych od platformy bez zadań specyficznych dla platformy.
    - Zarządzanie dojściami urządzeń w jednym miejscu.
- **Obsługa wielu zapleczy**
    - Chmura lub środowisko lokalne
    - .NET, Node.js, Java itd.
- **Rozbudowany zestaw wzorców dostarczania**
    - Emisja do jednej lub wielu platform: możliwość natychmiastowej emisji do milionów urządzeń na wielu platformach przy użyciu pojedynczego wywołania interfejsu API.
    - Wypychanie do urządzenia: można kierować powiadomienia do poszczególnych urządzeń.
    - Wypychanie do użytkownika: funkcja tagów i szablonów ułatwia komunikowanie się ze wszystkimi urządzeniami użytkownika na wielu platformach.
    - Wypychanie do segmentu przy użyciu tagów dynamicznych: funkcja tagów ułatwia dzielenie urządzeń na segmenty i wypychanie do nich zgodnie z potrzebami, bez względu na to, czy jest to wysyłanie do jednego segmentu, czy też do wyrażenia złożonego z segmentów (na przykład: aktywny I mieszka w Warszawie NIE nowy użytkownik). Zamiast ograniczać się do publikowania/subskrybowania, można aktualizować tagi urządzeń w dowolnym miejscu i czasie.
    - Wypychanie zlokalizowane: funkcja szablonów ułatwia osiągnąć lokalizację bez wpływu na kod zaplecza.
    - Wypychanie dyskretne: można włączyć wzorzec wypychania i ściągania, wysyłając dyskretne powiadomienia do urządzeń i wyzwalając je w celu zakończenia wybranych operacji ściągnięcia lub akcji.
    - Wypychanie zaplanowane: można zaplanować wysyłanie powiadomień w dowolnym czasie.
    - Wypychania bezpośrednie: można pominąć rejestrowanie urządzeń w usłudze Notification Hubs i bezpośrednio wypchnąć partię do listy dojść urządzeń.
    - Wypychanie spersonalizowane: zmienne wypychania urządzenia ułatwiają wysyłanie specyficznych dla urządzenia, spersonalizowanych powiadomień push przy użyciu dostosowanych par klucz-wartość.
- **Rozbudowane dane telemetryczne**
    - Ogólne dane telemetryczne dotyczące wypychania, urządzeń, błędów i operacji są dostępne programowo oraz w witrynie Azure Portal.
    - Funkcja danych telemetrycznych dla poszczególnych komunikatów śledzi każde wypchnięcie z początkowego wywołania żądania do usługi Notification Hubs, które pozwala na pomyślne utworzenie partii operacji wypychania.
    - Funkcja informacji zwrotnych z systemu powiadomień platformy przekazuje wszystkie informacje zwrotne z systemów powiadomień platformy, aby pomóc w debugowaniu.
- **Skalowalność** 
    - Wysyłanie szybkich komunikatów do milionów urządzeń bez konieczności ponownego tworzenia architektury lub dzielenia na fragmenty dla urządzeń.
- **Bezpieczeństwo**
    - Uwierzytelnianie przy użyciu wpisu tajnego dostępu współdzielonego (SAS, Shared Access Secret) lub uwierzytelnianie federacyjne.

## <a name="integration-with-app-service-mobile-apps"></a>Integracja z usługą App Service Mobile Apps
W celu umożliwienia bezproblemowej i jednorodnej obsługi we wszystkich usługach Azure funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) ma wbudowaną obsługę powiadomień wypychanych przy użyciu usługi Notification Hubs. Funkcja [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) oferuje wysoce skalowalną, globalnie dostępną platformę tworzenia aplikacji mobilnych dla deweloperów w przedsiębiorstwach i integratorów systemów. Platforma ta oferuje bogaty zestaw funkcji dla deweloperów aplikacji mobilnych.

Deweloperzy aplikacji mobilnych mogą korzystać z usługi Notification Hubs przy użyciu następującego przepływu pracy:

1. Pobranie dojścia do urządzenia w systemie powiadomień platformy
2. Rejestracja urządzenia w usłudze Notification Hubs przy użyciu wygodnego interfejsu API rejestracji w ramach zestawu SDK klienta funkcji Mobile Apps

    > [!NOTE]
    > Zauważ, że usługa Mobile Apps usuwa wszystkie tagi rejestracji ze względów bezpieczeństwa. Pracuj z usługą Notification Hubs bezpośrednio z zaplecza, aby skojarzyć tagi z urządzeniami.
1. Wysyłanie powiadomień z zaplecza aplikacji przy użyciu usługi Notification Hubs

Oto niektóre udogodnienia dla deweloperów wynikające z tej integracji:

- **Zestawy SDK klienta funkcji Mobile Apps**: te wieloplatformowe zestawy SDK oferują proste interfejsy API do rejestracji i komunikacji z centrum powiadomień automatycznie skojarzone z aplikacją mobilną. Deweloperzy nie muszą odnajdywać poświadczeń usługi Notification Hubs i pracować przy użyciu dodatkowych usług.
    - *Wypychanie do użytkownika*: zestawy SDK automatycznie dodają tagi dla danego urządzenia przy użyciu identyfikatora uwierzytelnionego użytkownika funkcji Mobile Apps w celu umożliwienia scenariusza wypychania do użytkownika.
    - *Wypychanie do urządzenia*: zestawy SDK automatycznie używają identyfikatora instalacji funkcji Mobile Apps jako identyfikatora GUID do rejestracji w usłudze Notification Hubs, oszczędzając deweloperom pracy związanej z obsługą identyfikatorów GUID wielu usług.
- **Model instalacji**: funkcja Mobile Apps współpracuje z najnowszym modelem wypychania usługi Notification Hubs w celu reprezentowania wszystkich właściwości wypychania skojarzonych z urządzeniem w instalacji JSON, które są zgodne z usługami powiadomień push i łatwe w użyciu.
- **Elastyczność**: deweloperzy mogą zawsze pracować bezpośrednio za pomocą usługi Notification Hubs nawet po integracji.
- **Zintegrowane środowisko pracy w witrynie [Azure Portal](https://portal.azure.com)**: wypychanie jako możliwość ma wizualną reprezentację w funkcji Mobile Apps, a deweloperzy mogą z łatwością pracować przy użyciu skojarzonego centrum powiadomień za pomocą funkcji Mobile Apps.

## <a name="next-steps"></a>Następne kroki
Rozpocznij tworzenie centrum powiadomień i korzystanie z niego, wykonując kroki opisane w temacie [Samouczek: wypychanie powiadomień do aplikacji mobilnych](notification-hubs-android-push-notification-google-fcm-get-started.md). [0]: ./media/notification-hubs-overview/registration-diagram.png [1]: ./media/notification-hubs-overview/notification-hub-diagram.png [Jak klienci używają usługi Notification Hubs]: http://azure.microsoft.com/services/notification-hubs [Samouczki i przewodniki usługi Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started [Platforma uniwersalna systemu Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx [App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/ [szablony]: notification-hubs-templates-cross-platform-push-messages.md [Witryna Azure Portal]: https://portal.azure.com [tagi]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
