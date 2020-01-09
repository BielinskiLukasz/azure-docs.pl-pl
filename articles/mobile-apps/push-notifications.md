---
title: Ważność powiadomień wypychanych w aplikacjach mobilnych przy użyciu Visual Studio App Center i platformy Azure Notification Hubs
description: Dowiedz się więcej na temat usług, takich jak Visual Studio App Center, których możesz użyć do angażowania użytkowników aplikacji mobilnych.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 1717bf6da849218f60bae7dafca20cb304286976
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453141"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Zaangażuj użytkowników aplikacji, wysyłając powiadomienia wypychane

Niezależnie od tego, czy tworzysz aplikacje dla klientów, czy dla przedsiębiorstw, chcesz aktywnie korzystać z aplikacji. Często zdarza się, że chcesz udostępnić użytkownikom najświeższe wiadomości, aktualizacje gier, atrakcyjne oferty, aktualizacje produktów lub inne istotne informacje. Aby zwiększyć zaangażowanie z użytkownikami i uzyskać dostęp do swojej aplikacji, musisz mieć możliwość komunikowania się z użytkownikami w czasie rzeczywistym.

Powiadomienia wypychane zapewniają szybki i wydajny sposób komunikowania się z użytkownikami aplikacji. Możesz skontaktować się z użytkownikami w odpowiednim czasie i powiadomić o wymaganych informacjach, zazwyczaj w podręcznym elemencie lub oknie dialogowym na urządzeniu przenośnym, niezależnie od tego, co robią.

## <a name="value-of-push-notifications"></a>Wartość powiadomień wypychanych
Powiadomienia wypychane zapewniają wartości użytkownikom i firmom.

Firmy mogą:
- Komunikuj się bezpośrednio z użytkownikami, wysyłając wiadomości na obsługiwanych platformach w odpowiednim czasie.
- Zwiększ zaangażowanie użytkowników, wysyłając aktualizacje w czasie rzeczywistym i przypomnienia do użytkowników, zachęcając do ich regularnego angażowania się w aplikację.
- Zachowaj użytkowników i powiąż z nieaktywnymi użytkownikami, którzy pobrali aplikację, ale nie używają jej do ponownego uaktywnienia.
- Zwiększ szybkość konwersji, analizując zachowanie użytkowników, segmentację użytkowników i wykorzystując kampanie na podstawie mobilnych powiadomień wypychanych.
- Promuj produkty i usługi przez wysyłanie komunikatów wypychanych i okresowych aktualizacji do użytkowników.
- Użytkownicy docelowi, wysyłając spersonalizowane wiadomości wypychane.

Dla użytkowników aplikacji powiadomienia wypychane:
- Podaj wartość i informacje w czasie rzeczywistym.
- Przypomnij użytkownikom, aby korzystali z aplikacji.

Użyj następujących usług, aby włączyć powiadomienia wypychane w aplikacjach mobilnych.

## <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
Za pomocą [App Center wypychania](/appcenter/push/)możesz wysyłać do użytkowników z systemami iOS, Android i Windows, które nie mają być zarządzane przy użyciu usług powiadomień wypychanych (PNS). W oparciu o Notification Hubs platformy Azure usługa ta eliminuje złożone funkcje związane z wypychaniem powiadomień ręcznie, dostarczając zaawansowany pulpit nawigacyjny.

**Najważniejsze funkcje**
- Wysyłanie powiadomień wypychanych do urządzeń przenośnych na różnych platformach.
- Używaj powiadomień do wysyłania danych do aplikacji, wyświetlania komunikatu dla użytkownika lub wyzwalania akcji przez aplikację.
- Użyj celów powiadomień, aby: 
    - Emituj komunikaty do wszystkich zarejestrowanych urządzeń.
    - Wysyłaj powiadomienia do odbiorców na podstawie informacji o urządzeniu i właściwości niestandardowych.
    - Wysyłaj powiadomienia do określonych użytkowników.
    - Wysyłaj powiadomienia do określonych urządzeń.
- Korzystaj z rozbudowanej telemetrii dotyczącej wypychania, urządzeń i błędów, które są dostępne w portalu App Center.
- Uzyskaj wsparcie dla platformy dla systemów iOS, Android, macOS, Xamarin, w przypadku natywnych, aparatu Unity i oprogramowania Cordova.

**Odwołania**
- [Zarejestruj się w usłudze Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do App Center wypychania](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) zapewnia łatwy w użyciu i skalowany aparat wypychania. Można jej używać do wysyłania powiadomień do dowolnej platformy i z dowolnego zaplecza w chmurze lub lokalnie.

**Najważniejsze funkcje**
- Wysyłać powiadomienia wypychane do dowolnej platformy z dowolnego zaplecza, w chmurze lub lokalnie.
- Szybka emisja wypychana do milionów urządzeń przenośnych przy użyciu jednego wywołania interfejsu API.
- Dostosowywanie powiadomień wypychanych według klienta, języka i lokalizacji.
- Dynamiczne Definiowanie i powiadamianie segmentów klientów.
- Natychmiastowe skalowanie do milionów urządzeń przenośnych.
- Uzyskaj wsparcie dla platformy dla systemów iOS, Android, Windows, Kindle i Baidu.
        
**Odwołania**
- [Azure Portal](https://portal.azure.com) 
- [Wprowadzenie do usługi Azure Notification Hubs](/azure/notification-hubs/)
- [Przewodniki Szybki start](/azure/notification-hubs/create-notification-hub-portal)
- [Przykłady](/azure/notification-hubs/samples)
