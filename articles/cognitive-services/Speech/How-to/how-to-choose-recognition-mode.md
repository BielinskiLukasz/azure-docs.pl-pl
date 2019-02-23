---
title: Wybieranie trybu rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Jak wybrać najlepszy tryb rozpoznawania w rozpoznawania mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669724"
---
# <a name="bing-speech-recognition-modes"></a>Tryby rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Zamiany mowy na tekst interfejsów API Bing obsługi wielu trybów rozpoznawania mowy. Wybierz tryb, który daje najlepsze rezultaty uznania dla swojej aplikacji.

| Tryb | Opis |
|---|---|
| *interactive* | Rozpoznawanie "Poleceń i kontroli" dla użytkownika interaktywnego zastosowaniach. Użytkownicy Mów krótkich fraz przeznaczony jako polecenia do aplikacji. |
| *Dyktowanie* | Ciągłe rozpoznawania dyktowanie scenariuszach. Użytkownicy Mów dłużej zdań, które są wyświetlane jako tekst. Użytkownicy przyjąć bardziej formalne stylu wypowiedzi. |
| *konwersacji* | Ciągłe doceniać przepisywania konwersacje między ludzi. Użytkownicy przyjmuje mniej formalny stylu wypowiedzi i może przełączać się między dłużej zdania i fraz krótszy.

> [!NOTE]
> Te tryby są stosowane, gdy używasz [interfejsów API REST](../GetStarted/GetStartedREST.md). [Biblioteki klienckie](../GetStarted/GetStartedClientLibraries.md) użyć innych parametrów, aby określić tryb rozpoznawania. Aby uzyskać więcej informacji zobacz Biblioteka klienta wybranych przez użytkownika.

Aby uzyskać więcej informacji, zobacz [tryby rozpoznawania](../concepts.md#recognition-modes) strony.
