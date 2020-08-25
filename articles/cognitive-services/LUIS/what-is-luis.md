---
title: Co to jest usługa Language Understanding (LUIS)?
description: Language Understanding (LUIS) — usługa interfejsu API oparta na chmurze, która korzysta z uczenia maszynowego, w celu przewidywania znaczenia i wyodrębnienia informacji.
keywords: Azure, sztuczna inteligencja, AI, przetwarzanie języka naturalnego, NLP, interpretacja języka naturalnego, NLU, Konwersacja AI, konwersacje AI, AI chatbot, chatbot Maker, LUIS, NLP AI, Luis AI, Azure Luis, zrozumienie języka naturalnego
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 22fe99e1552a9612adfbc455d60852f1591a1a54
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752152"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.

Aplikacją kliencką dla usługi LUIS może być dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w naturalnym języku, aby wykonać zadanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.

![Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Korzystanie z usługi LUIS z czatbotami

<a name="Accessing-LUIS"></a>

Po opublikowaniu aplikacji usługi LUIS aplikacja kliencka wysyła wypowiedzi (tekst) do [interfejsu API][endpoint-apis] punktu końcowego przetwarzania języka naturalnego usługi LUIS i otrzymuje wyniki w formie odpowiedzi w formacie JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika za pomocą interpretacji języka naturalnego (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika przy użyciu funkcji interpretacji języka naturalnego (NLP")

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|LUIS umożliwia tworzenie niestandardowych modeli języka w celu dodania analizy do aplikacji. Oparte na maszynach modele języka pobierają tekst wejściowy bez struktury użytkownika i zwracają odpowiedź sformatowaną w formacie JSON z zamiarem najwyższego poziomu `HRContact` . Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak jednostka _typu kontaktu_ .|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Decyzje te mogą obejmować drzewo decyzyjne w kodzie bot Framework i wywołania do innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Interpretacja języka naturalnego (NLU)

[Luis zapewnia sztuczną inteligencję (AI)](artificial-intelligence.md) w postaci NLU, podzestawu przetwarzania języka naturalnego (NLP).

Twoja aplikacja LUIS zawiera model języka naturalnego specyficzny dla domeny. Możesz uruchomić aplikację LUIS ze wstępnie utworzonym modelem domeny, utworzyć własny model lub połączyć elementy wstępnie utworzonej domeny z własnymi informacjami.

* **Model wstępnie utworzony** usługi LUIS zawiera wiele wstępnie utworzonych modeli domeny, w tym intencje, wypowiedzi i wstępnie utworzone jednostki. Możesz skorzystać z wstępnie utworzonych jednostek bez konieczności korzystania z intencji i wypowiedzi wstępnie utworzonego modelu. [Wstępnie utworzone modele domeny](luis-how-to-use-prebuilt-domains.md) zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS.

* **Model niestandardowy** LUIS oferuje kilka sposobów identyfikacji własnych niestandardowych modeli, w tym intencje i jednostki. Jednostki obejmują jednostki uczenia maszynowego, określone lub literałowe jednostki oraz kombinację uczenia maszynowego i literału.

Dowiedz się więcej na temat [NLP](artificial-intelligence.md)i obszaru Luis dla NLU.

## <a name="step-1-design-and-build-your-model"></a>Krok 1. Projektowanie i kompilowanie modelu

Zaprojektuj model przy użyciu kategorii zamiarów użytkownika o nazwie **[intencje](luis-concept-intent.md)**. Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md)** użytkownika. Każdy wypowiedź może dostarczyć dane, które muszą zostać wyodrębnione za pomocą [jednostek uczenia maszynowego](luis-concept-entity-types.md#effective-machine-learned-entities).

|Przykładowa wypowiedź użytkownika|Zamiar|Wyodrębnione dane|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm, Bob|

Skompiluj model przy użyciu interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) lub [**portalu Luis**](https://www.luis.ai)lub obu. Dowiedz się więcej o tym, jak skompilować przy użyciu [portalu](get-started-portal-build-app.md) i [bibliotek klienckich SDK](quickstart-sdk.md).

## <a name="step-2-get-the-query-prediction"></a>Krok 2. Uzyskiwanie prognozowania zapytań

Gdy model aplikacji jest szkolony i publikowany w punkcie końcowym, aplikacja kliencka (na przykład Chat bot) wysyła wyrażenia długości [do interfejsu API](https://go.microsoft.com/fwlink/?linkid=2092356) usługi przewidywania. Interfejs API stosuje model do wypowiedź na potrzeby analizy i reaguje na wyniki prognozowania w formacie JSON.

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak następująca jednostka **typu kontaktu** i ogólna tonacji.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Krok 3. udoskonalenie prognozowania modelu

Po opublikowaniu aplikacji LUIS i otrzymaniu rzeczywistej wyrażenia długości użytkownika, LUIS zapewnia [aktywną naukę](luis-concept-review-endpoint-utterances.md) punktu końcowego wyrażenia długości, aby poprawić dokładność przewidywania. Zapoznaj się z tymi sugestiami w ramach zwykłych czynności konserwacyjnych w cyklu życia.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Cykl życia i narzędzia deweloperskie
LUIS oferuje narzędzia, przechowywanie wersji i współpracę z innymi autorami LUIS w celu zintegrowania z pełnym [cyklem życia](luis-concept-app-iteration.md).

Language Understanding (LUIS) jako interfejs API REST może być używany z dowolnym produktem, usługą lub platformą z żądaniem HTTP. LUIS udostępnia również biblioteki klienckie (SDK) dla kilku najpopularniejszych języków programowania. Dowiedz się więcej o udostępnionych [zasobach dewelopera](developer-reference-resource.md) .

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [interfejs wiersza polecenia Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Pakiet NPM zapewnia tworzenie i prognozowanie za pomocą autonomicznego narzędzia wiersza polecenia lub jako importu.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown to narzędzie wiersza polecenia, które ułatwia zarządzanie modelami języka dla bot.

## <a name="integrate-with-a-bot"></a>Integracja z usługą bot

Użyj [usługi Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) z [platformą Microsoft bot Framework](https://dev.botframework.com/) , aby skompilować i wdrożyć aplikację Chat bot. Projektuj i opracowuj przy użyciu narzędzia interfejsu graficznego, [kompozytora](https://docs.microsoft.com/composer/)lub [roboczych przykładów bot](https://github.com/microsoft/BotBuilder-Samples) zaprojektowanych dla najważniejszych scenariuszy bot.

## <a name="integrate-with-other-cognitive-services"></a>Integracja z innymi Cognitive Services

Inne usługi Cognitive Services używane z usługą LUIS:
* [QnA Maker][qnamaker] umożliwia łączenie kilku rodzajów tekstu w bazę wiedzy w formie pytań i odpowiedzi.
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md) konwertuje żądania w języku mówionym na tekst.

LUIS zapewnia funkcjonalność analiza tekstu w ramach istniejących zasobów LUIS. Ta funkcja obejmuje [analizę tonacji](luis-how-to-publish-app.md#configuring-publish-settings) i [wyodrębnianie kluczowych fraz](luis-reference-prebuilt-keyphrase.md) z wbudowaną jednostką keyPhrase.

## <a name="learn-with-the-quickstarts"></a>Zapoznaj się z przewodnikami Szybki Start

Dowiedz się więcej na temat LUIS z przewodnikami Szybki Start przy użyciu [portalu](get-started-portal-build-app.md) i [bibliotek klienckich SDK](quickstart-sdk.md).


## <a name="next-steps"></a>Następne kroki

* [Nowości dotyczące usługi](whats-new.md) i dokumentacji
* [Zaplanuj swoją aplikację](luis-how-plan-your-app.md) za pomocą [intencji](luis-concept-intent.md) i [jednostek](luis-concept-entity-types.md).
* [Zbadaj punkt końcowy przewidywania](luis-get-started-get-intent-from-browser.md).
* [Zasoby dla deweloperów](developer-reference-resource.md) dla Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
