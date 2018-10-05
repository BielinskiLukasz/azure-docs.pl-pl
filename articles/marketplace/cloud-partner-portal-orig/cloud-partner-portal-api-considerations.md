---
title: Zagadnienia dotyczące interfejsu API | Dokumentacja firmy Microsoft
description: Przechowywanie wersji, obsługa błędów i autoryzacji problemy dotyczące korzystania z interfejsów API portalu marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810661"
---
<a name="api-considerations"></a>Zagadnienia dotyczące interfejsu API
=================

<a name="api-versioning"></a>Obsługa wersji interfejsu API
--------------

Może istnieć wiele wersji interfejsu API, które są dostępne w tym samym czasie. Klienci muszą wskazywać wersję chcą wywoływać użycie, zapewniając `api-version` parametr jako część ciągu zapytania.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpowiedź na żądanie z nieznanej lub nieprawidłowej wersji interfejsu API jest kod HTTP 400. Ten błąd zwraca kolekcję znane wersje interfejsu API w treści odpowiedzi.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Błędy
------

Interfejs API reaguje na błędy za pomocą odpowiednich kodów stanu HTTP i, opcjonalnie, dodatkowe informacje w odpowiedzi zserializowanym w formacie JSON.
Jeśli otrzymasz komunikat o błędzie, szczególnie błąd 400 klasy, nie należy wykonywać ponowień żądania przed naprawieniem podstawowych przyczyn. Na przykład w przykładowej odpowiedzi powyżej, Usuń parametr wersji interfejsu API przed ponownym wysłaniem żądania.

<a name="authorization-header"></a>Nagłówek uwierzytelnienia
--------------------

Dla wszystkich interfejsów API w ramach tego odwołania należy przekazać nagłówka autoryzacji, wraz z tokenu elementu nośnego uzyskane z usługi Azure Active Directory (Azure AD). Tego pliku nagłówkowego jest wymagany na prawidłową odpowiedź; Jeśli nie występuje `401 Unauthorized` zostanie zwrócony błąd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
