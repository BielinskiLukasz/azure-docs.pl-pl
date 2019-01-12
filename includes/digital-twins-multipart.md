---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/10/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 47f66fc61fdd397350efb934bf21f488960a27a2
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233063"
---
> [!NOTE]
> Wieloczęściowy żądań zwykle wymagają trzy części:
> * A **Content-Type** nagłówka:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Zawartość pliku do przekazania
>
> **Content-Type** i **Content-Disposition** różnią się w zależności od scenariusza użycia.

Wieloczęściowy żądań jest możliwe programowo (za pośrednictwem C#), za pomocą klienta REST lub narzędzia, takie jak [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Narzędzia klienta REST mogą mieć różne poziomy pomocy technicznej w przypadku złożonych żądań wieloczęściową. Sprawdź, które narzędzia najlepiej nadaje się do własnych potrzeb.

> [!IMPORTANT]
> Wieloczęściowy żądań do cyfrowego Twins zarządzania interfejsów API usługi Azure zazwyczaj ma dwie części:
> * Metadane obiektu blob (np. skojarzony typ MIME), która jest zadeklarowana za **Content-Type** i **Content-Disposition**
> * Zawartość obiektu blob, obejmujących bez określonej struktury zawartość pliku do przekazania
>
> Żadna z dwóch części nie jest wymagana dla **PATCH** żądań. Oba są wymagane dla **WPIS** lub operacji tworzenia.

[Szybki Start zajętość kodu źródłowego](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) zawiera pełną C# przykłady pokazujące sposób wprowadzania wieloczęściowego żądań cyfrowego Twins zarządzania interfejsów API usługi Azure.