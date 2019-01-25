---
title: Eksportuj Twojej subskrypcji platformy Azure informacji najwyższego poziomu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można wyświetlić wszystkich subskrypcji platformy Azure identyfikatorów skojarzonych z Twoim kontem.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: a2716793a47eae56a8e6eef8416ff446b6f7176f
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902571"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlić najwyższego poziomu informacji o subskrypcji
Jeśli chcesz wyświetlić zestaw subskrypcji identyfikatorów skojarzonych z poświadczeń użytkownika [Pobierz plik JSON, informacje o subskrypcji z Centrum kont Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Plik JSON pobranych zawiera następujące informacje:
- Wiadomość e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z kontem rozliczeniowym.
- SubscriptionIds: Lista subskrypcji należących do swojego konta, wymienionych według identyfikatora subskrypcji.

### <a name="subscriptionsjson-sample"></a>Przykładowe Subscriptions.JSON

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
