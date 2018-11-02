---
title: Co to jest lokalne firmy interfejsu API wyszukiwania Bing? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Lokalnych firm interfejsu API wyszukiwania Bing jest usługą RESTful, który umożliwia aplikacji, aby znaleźć informacje o miejscach lokalnych i firm oparta na zapytaniach wyszukiwania.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: f6299a8241b4ce43dc9276070f06ae4cc6566d43
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748729"
---
# <a name="what-is-bing-local-business-search"></a>Co to jest lokalne wyszukiwanie firm Bing?
Lokalnych firm interfejsu API wyszukiwania Bing to usługi RESTful, który umożliwia aplikacji, aby znaleźć informacje o lokalnych firm w oparciu o zapytania wyszukiwania. Na przykład `q=<business-name> in Redmond, Washington`, lub `q=Italian restaurants near me`. 

## <a name="features"></a>Funkcje
| Cecha | Opis |  
| -- | -- | 
| [Znajdź lokalnych firm i lokalizacje](quickstarts/local-quickstart.md) | Lokalnych firm interfejsu API wyszukiwania Bing pobiera zlokalizowanych wyników z zapytania. Wyniki obejmują adres URL witryny sieci Web firmy i wyświetlać tekst, numeru telefonu i lokalizacji geograficznej, włączając: współrzędne GPS, Miasto, adres zamieszkania |  
| [Filtruj wyniki lokalnego o granicach geograficznych](specify-geographic-search.md) | Dodaj współrzędne jako parametry wyszukiwania, aby ograniczyć wyniki w określonym obszarze geograficznym, określony przez okrągłego obszaru lub kwadrat obwiedni. | 
| [Filtrowanie wyników lokalnych firmach według kategorii](local-categories.md) | Wyszukaj w wynikach lokalnych firmach według kategorii. Ta opcja zwracają zlokalizowane w różnych kategoriach działalności biznesowej przy użyciu odwrotnego Lokalizacja adresu IP lub współrzędne GPS obiektu wywołującego.|

## <a name="workflow"></a>Przepływ pracy
Wywoływanie lokalnych firm interfejsu API wyszukiwania Bing z dowolnego języka programowania, który może wysyłać żądania HTTP i przeanalizować odpowiedzi w formacie JSON. Ta usługa jest dostępna przy użyciu interfejsu API REST.
 
1. Tworzenie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dostęp do interfejsów API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Kodowanie adresu URL wyszukiwane terminy dla `q=""` parametr zapytania. Na przykład `q=nearby+restaurant` lub `q=nearby%20restaurant`. Jeśli to konieczne, należy ustawić dzielenia na strony, jak również. 
3. Wyślij [żądanie lokalnych firm interfejsu API wyszukiwania Bing](quickstarts/local-quickstart.md) 
4. Analizuje odpowiedź JSON 

> [!NOTE]
> Obecnie lokalnego wyszukiwania biznesowych obsługuje tylko `en-US` rynku. 
> [!NOTE]
> Obecnie lokalnego wyszukiwania biznesowych nie obsługuje automatycznego sugerowania. 

## <a name="next-steps"></a>Kolejne kroki
- [Zapytania i odpowiedzi](local-search-query-response.md)
- [Lokalne wyszukiwanie firm Szybki Start](quickstarts/local-quickstart.md)
- [Lokalne dokumentacja interfejsu API wyszukiwania biznesowych](local-search-reference.md)
- [Use and display requirements (Wymagania dotyczące użycia i wyświetlania)](use-display-requirements.md)