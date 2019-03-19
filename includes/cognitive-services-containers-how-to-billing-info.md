---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964231"
---
Zapytania do kontenera są rozliczane według warstwa cenowa zasobu platformy Azure używane do `<ApiKey>`.

Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z punktem końcowym rozliczeń do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe z punktem końcowym rozliczeń przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (na przykład obraz lub tekst, który jest analizowana) do firmy Microsoft. 

### <a name="connecting-to-azure"></a>Łączenie z platformą Azure

Kontener musi rozliczeń wartości argumentu do uruchomienia. Wartości te umożliwiają kontener, aby nawiązać połączenie z punktu końcowego rozliczeń. Kontener raportów użycia dotyczących co 10 do 15 minut. Jeśli kontener nie łączą się w przedziale czasu dozwolony na platformie Azure, kontenera będzie działać, ale będzie nie obsługiwać stałe żądania do momentu przywrócenia rozliczeń punktu końcowego. Połączenie jest próba 10 razy o takim samym interwale czasowym z 10 do 15 minut. Jeśli nie może nawiązać połączenia rozliczeń punktu końcowego w ciągu 10 prób, kontener przestanie działać. 

### <a name="billing-arguments"></a>Argumenty rozliczeń

Wszystkich trzech z następujących opcji, należy określić prawidłowymi wartościami, aby `docker run` polecenie, aby uruchomić kontener:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu określonego w `Billing`. |
| `Billing` | Punkt końcowy zasobu usługi cognitive Services, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu LUIS Azure elastycznie.|
| `Eula` | Wskazuje, że zaakceptowano licencję dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |


