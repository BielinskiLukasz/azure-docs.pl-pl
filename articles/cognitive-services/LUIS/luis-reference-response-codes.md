---
title: Kody odpowiedzi HTTP interfejsu API
titleSuffix: Azure
description: Zrozumienie, jakie kody odpowiedzi HTTP są zwracane z tworzenia usługi LUIS i interfejsów API punktu końcowego
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: fd0d8cc492a8a35d66aa4b20b792bf317b9e8c11
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218856"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Typowe kody odpowiedzi interfejsu API oraz ich znaczenie

[Tworzenia](https://aka.ms/luis-authoring-apis) i [punktu końcowego](https://aka.ms/luis-endpoint-apis) interfejsy API zwracają kody odpowiedzi HTTP. Chociaż komunikatach odpowiedzi zawiera informacje specyficzne dla żądania, kod stanu odpowiedzi HTTP jest ogólny. 

## <a name="common-status-codes"></a>Typowe kody stanu
W poniższej tabeli wymieniono niektóre najbardziej typowe kody stanu odpowiedzi HTTP dla [tworzenia](https://aka.ms/luis-authoring-apis) i [punktu końcowego](https://aka.ms/luis-endpoint-apis) interfejsów API:

|Kod|Interfejs API|Wyjaśnienie|
|:--|--|--|
|400|Tworzenie punktu końcowego|Parametry żądania są nieprawidłowe, co oznacza, że wymagane parametry są brakujące, źle sformułowane lub zbyt duży|
|400|Tworzenie punktu końcowego|Treść żądania jest nieprawidłowa, co oznacza, że za pomocą pliku JSON jest Brak, źle sformułowane lub zbyt duży|
|401|Tworzenie|używany klucz subskrypcji dla punktu końcowego, zamiast tworzenia klucza|
|401|Tworzenie punktu końcowego|nieprawidłowy, źle sformułowane lub pusty klucz|
|401|Tworzenie punktu końcowego| klucz nie jest zgodna regionu|
|401|Tworzenie|nie jesteś właścicielem lub współpracownika|
|401|Tworzenie|Nieprawidłowa kolejność wywołań interfejsu API|
|403|Tworzenie punktu końcowego|Przekroczono Całkowity miesięczny limit przydziału klucza|
|409|Endpoint|nadal trwa ładowanie aplikacji|
|410|Endpoint|Aplikacja musi być retrained i ponowne opublikowanie|
|414|Endpoint|kwerendy przekracza maksymalną dozwoloną długość|
|429|Tworzenie punktu końcowego|Przekroczono limit szybkości (żądań na sekundę)|

## <a name="next-steps"></a>Kolejne kroki

* Interfejs API REST [tworzenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) i [punktu końcowego](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentacji
