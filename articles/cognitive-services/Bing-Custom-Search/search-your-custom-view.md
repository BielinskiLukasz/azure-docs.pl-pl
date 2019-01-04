---
title: Wyszukaj widok niestandardowy — Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Opisuje sposób wyszukiwania niestandardowy widok sieci web.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 77a1756aba0d8473051cdf335f33ed9ca5a8fb24
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558332"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Wywoływanie wystąpienia wyszukiwania niestandardowego Bing z portalu

Po skonfigurowaniu środowiska wyszukiwania niestandardowego, można przetestować go w ciągu wyszukiwania niestandardowego Bing [portal](https://customsearch.ai). 

![Zrzut ekranu portalu wyszukiwania niestandardowego bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Utwórz zapytanie wyszukiwania 

Po zalogowaniu się do usługi Bing Custom Search [portal](https://customsearch.ai), wybierz wystąpienie usługi wyszukiwania i kliknij przycisk **produkcji** kartę. W obszarze **punktów końcowych**, wybierz punkt końcowy interfejsu API (na przykład interfejsu API sieci Web). Subskrypcja Określa, jakie punkty końcowe są wyświetlane.

Aby utworzyć zapytanie wyszukiwania, wprowadź wartości parametrów dla punktu końcowego usługi. Pamiętaj, że parametry wyświetlana w portalu mogą ulec zmianie w zależności od punktu końcowego, które wybierzesz. Zobacz[dokumentacja interfejsu API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) Aby uzyskać więcej informacji. 

Niektóre parametry ważne są poniżej:


|Parametr  |Opis  |
|---------|---------|
|Zapytanie     | Wyszukiwany termin do wyszukania. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i automatycznego sugerowania |
|Identyfikator konfiguracji niestandardowej | Identyfikator konfiguracji wybranego wystąpienia wyszukiwania niestandardowego. To pole jest tylko do odczytu. |
|Rynek     | Na rynku, które pochodzą od wyników. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazów, wideo i hostowanych interfejsu użytkownika.        |
|Klucz subskrypcji | Klucz subskrypcji na potrzeby testów. Można wybrać klucz z listy rozwijanej lub wprowadzić ręcznie.          |

Klikając **dodatkowe parametry** , co spowoduje wyświetlenie następujących parametrów:  

|Parametr  |Opis  |
|---------|---------|
|Bezpieczne wyszukiwanie     | Filtr używany do filtrowania strony internetowe dla dorosłych. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazów, wideo i hostowanych interfejsu użytkownika.        |
|Język interfejsu użytkownika    | Język używany do ciągi interfejsu użytkownika. Na przykład, jeśli zostanie włączone, obrazy i klipy wideo w interfejsie użytkownika usług hostowanych **obraz** i **wideo** karty użyć określonego języka.        |
|Licznik     | Liczba wyników wyszukiwania, aby zwrócić w odpowiedzi. Dostępne tylko dla punktów końcowych sieci Web, obrazów i filmów wideo.         |
|Przesunięcie    | Liczba wyników wyszukiwania, aby pominąć przed zwróceniem wyników. Dostępne tylko dla punktów końcowych sieci Web, obrazów i filmów wideo.        |
    
Po określeniu wszystkie wymagane opcje, kliknij przycisk **wywołania** Aby wyświetlić odpowiedź w formacie JSON w okienku po prawej stronie. Jeśli wybierzesz hostowanych interfejsu użytkownika punktu końcowego, możesz przetestować środowiska wyszukiwania w dolnym okienku.

## <a name="next-steps"></a>Kolejne kroki

- [Wywoływanie w widoku niestandardowym przy użyciu języka C#](./call-endpoint-csharp.md)
- [Wywoływanie w widoku niestandardowym przy użyciu języka Java](./call-endpoint-java.md)
- [Wywołania z usługą NodeJs w widoku niestandardowym](./call-endpoint-nodejs.md)
- [Wywoływanie w widoku niestandardowym przy użyciu języka Python](./call-endpoint-python.md)

- [Wywoływanie w widoku niestandardowym przy użyciu zestawu SDK języka C#](./sdk-csharp-quick-start.md)