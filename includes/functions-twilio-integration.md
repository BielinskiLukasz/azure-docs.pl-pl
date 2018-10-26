---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132744"
---
W tym przykładzie polega na użyciu [Twilio](https://www.twilio.com/) usługi by wysłać wiadomości SMS na telefon komórkowy. Usługa Azure Functions mają wbudowaną obsługę dla usługi Twilio za pomocą [powiązania usługi Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), a w przykładzie użyto tej funkcji.

Pierwszą rzeczą, potrzebne jest konto Twilio. Możesz utworzyć bezpłatne na https://www.twilio.com/try-twilio. Po utworzeniu konta usługi, należy dodać następujące trzy **ustawienia aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Wartość Opis |
| - | - |
| **TwilioAccountSid**  | Identyfikator SID dla konta usługi Twilio |
| **TwilioAuthToken**   | Token uwierzytelniania dla konta usługi Twilio |
| **TwilioPhoneNumber** | Numer telefonu skojarzony z Twoim kontem usługi Twilio. Służy do wysyłania wiadomości SMS. |