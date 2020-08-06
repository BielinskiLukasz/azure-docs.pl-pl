---
title: Instalowanie kontenerów mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na mowę, przypominającą człowieka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: 4f14a5cf21a372c66378521fd293b7569b181653
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808664"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalowanie i uruchamianie kontenerów usługi mowy (wersja zapoznawcza)

Kontenery umożliwiają uruchamianie niektórych interfejsów API usługi mowy w Twoim środowisku. Kontenery doskonale nadaje się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener mowy.

Kontenery mowy umożliwiają klientom tworzenie architektury aplikacji mowy, która jest zoptymalizowana pod kątem niezawodnych możliwości chmury i lokalizacji brzegowej. Dostępne są cztery różne kontenery. Dwa standardowe kontenery to **Zamiana mowy na tekst** i zamiana **tekstu na mowę**. Dwa kontenery niestandardowe są **Custom Speech do tekstu** i **niestandardowego tekstu na mowę**. Kontenery mowy mają takie same [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) jak usługi Azure Speech Services oparte na chmurze.

> [!IMPORTANT]
> Wszystkie kontenery mowy są obecnie oferowane w ramach [publicznej wersji zapoznawczej "Gated"](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services). Ogłoszenie zostanie wykonane, gdy kontenery mowy postępują z ogólnie dostępnymi wersjami.

| Funkcja | Funkcje | Najnowsza |
|--|--|--|
| Zamiana mowy na tekst | Analizuje tonacji i przekształca ciągłe nagrywanie mowy w czasie rzeczywistym lub nagrania audio wsadowe z wynikami pośrednimi.  | ppkt |
| Custom Speech do tekstu | Korzystając z modelu niestandardowego z [portalu Custom Speech](https://speech.microsoft.com/customspeech), przekształca ciągłe nagrywanie mowy w czasie rzeczywistym lub przetwarzanie wsadowe audio do tekstu z wynikami pośrednimi. | ppkt |
| Zamiana tekstu na mowę | Konwertuje tekst na mowę dźwiękową przy użyciu zwykłego tekstu lub języka SSML (Speech Syntezing Language). | 1.5.0 |
| Niestandardowa Zamiana tekstu na mowę | Przy użyciu modelu niestandardowego z [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal)program konwertuje tekst na mowę dźwiękową przy użyciu zwykłego tekstu lub języka SSML (Speech syntezing Language). | 1.5.0 |

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne przed użyciem kontenerów mowy:

| Wymagane | Przeznaczenie |
|--|--|
| Aparat platformy Docker | Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br> |
| Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń. |
| Zasób mowy | Aby można było korzystać z tych kontenerów, musisz mieć:<br><br>Zasób usługi Azure _Speech_ do pobrania skojarzonego klucza interfejsu API i identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów **mowy** i kluczy Azure Portal. Są one wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd** |

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Wypełnij i prześlij [formularz żądania](https://aka.ms/cognitivegate) , aby zażądać dostępu do kontenera. 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa zaawansowanych rozszerzeń wektora

**Host** to komputer, na którym jest uruchomiony kontener platformy Docker. Host *musi obsługiwać* [Zaawansowane rozszerzenia wektorów](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Obsługę AVX2 można sprawdzić na hostach z systemem Linux przy użyciu następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Komputer hosta jest *wymagany* do obsługi AVX2. Kontener *nie będzie* działać poprawnie bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera mowy.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Zamiana mowy na tekst | 2 rdzeń, 2 GB pamięci | 4 rdzenie, 4 GB pamięci |

# <a name="custom-speech-to-text"></a>[Custom Speech do tekstu](#tab/cstt)

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Custom Speech do tekstu | 2 rdzeń, 2 GB pamięci | 4 rdzenie, 4 GB pamięci |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzeń, 3 GB pamięci |

# <a name="custom-text-to-speech"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Niestandardowa Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzeń, 3 GB pamięci |

***

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.

> [!NOTE]
> Minimalne i zalecane są oparte na limitach platformy Docker, a *nie* na zasobach maszyn hosta. Na przykład kontenera zamiany mowy na tekst mapują fragmenty modelu dużego języka i *zaleca* się, aby cały plik mieścił się w pamięci, czyli dodatkowej 4-6 GB. Ponadto pierwsze uruchomienie jednego kontenera może trwać dłużej, ponieważ modele są stronicowane w pamięci.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów dla mowy są dostępne w następujących Container Registry.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech do tekstu](#tab/cstt)

| Kontener | Repozytorium |
|-----------|------------|
| Custom Speech do tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

| Kontener | Repozytorium |
|-----------|------------|
| Niestandardowa Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Wypychanie platformy Docker dla kontenerów mowy

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Wypychanie platformy Docker dla kontenera zamiany mowy na tekst

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru w wersji zapoznawczej.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest`Tag ściąga `en-US` Ustawienia regionalne. Aby uzyskać dodatkowe elementy lokalne, zobacz sekcję [Ustawienia regionalne Zamiana mowy na tekst](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Ustawienia regionalne zamiany mowy na tekst

Wszystkie Tagi, z wyjątkiem programu, `latest` są w następującym formacie i są rozróżniane wielkości liter:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
2.3.1-amd64-en-us-preview
```

W przypadku wszystkich obsługiwanych ustawień regionalnych kontenera **zamiany mowy na tekst** należy zapoznać się [ze znacznikami obrazu zamiany mowy na tekst](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech do tekstu](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Wypychanie platformy Docker dla kontenera Custom Speech-to-Text

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru w wersji zapoznawczej.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale`I `voice` dla niestandardowych kontenerów mowy jest określany przez niestandardowy model pozyskiwany przez kontener.

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Wypychanie platformy Docker dla kontenera zamiany tekstu na mowę

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru w wersji zapoznawczej.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Tag ściąga `en-US` Ustawienia regionalne i `ariarus` głos. W przypadku dodatkowych ustawień regionalnych zobacz [Ustawienia regionalne zamiany tekstu na mowę](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Ustawienia regionalne zamiany tekstu na mowę

Wszystkie Tagi, z wyjątkiem programu, `latest` są w następującym formacie i są rozróżniane wielkości liter:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
1.5.0-amd64-en-us-ariarus-preview
```

W przypadku wszystkich obsługiwanych ustawień regionalnych i odpowiadających im głosów kontenera **zamiany tekstu na mowę** należy zapoznać się ze [znacznikami obrazu zamiany tekstu na mowę](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Podczas konstruowania standardowego wpisu http zamiany *tekstu na mowę* , komunikat [języka SSML (Speech synteza Markup Language)](speech-synthesis-markup.md) wymaga `voice` elementu z `name` atrybutem. Wartość jest odpowiednimi ustawieniami regionalnymi kontenera i głosem, znanym również jako ["krótka nazwa"](language-support.md#standard-voices). Na przykład `latest` tag będzie miał nazwę głosu `en-US-AriaRUS` .

# <a name="custom-text-to-speech"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Wypychanie platformy Docker dla niestandardowego kontenera zamiany tekstu na mowę

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru w wersji zapoznawczej.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale`I `voice` dla niestandardowych kontenerów mowy jest określany przez niestandardowy model pozyskiwany przez kontener.

***

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](speech-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą`docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat sposobu pobierania `{Endpoint_URI}` `{API_Key}` wartości i. Dodatkowe [przykłady](speech-container-configuration.md#example-docker-run-commands) `docker run` polecenia są również dostępne.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

Aby uruchomić kontener *zamiany mowy na tekst* , wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *zamiany mowy na tekst* z obrazu kontenera.
* Przydziela 4 rdzenie procesora CPU i 4 gigabajty (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analizuj tonacji w danych wyjściowych zamiany mowy na tekst 

Począwszy od 2.2.0 kontenera zamiany mowy na tekst, można wywołać [interfejs API analizy tonacji](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) w wersji 3 w danych wyjściowych. Aby wywołać analizę tonacji, wymagany jest punkt końcowy zasobów interfejs API analizy tekstu. Na przykład: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Jeśli uzyskujesz dostęp do punktu końcowego analizy tekstu w chmurze, będziesz potrzebować klucza. Jeśli używasz analiza tekstu lokalnie, może nie musisz go podawać.

Klucz i punkt końcowy są przekazane do kontenera mowy jako argumenty, jak w poniższym przykładzie.

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

To polecenie:

* Wykonuje te same czynności co polecenie powyżej.
* Przechowuje interfejs API analizy tekstu punkt końcowy i klucz do wysyłania żądań analizy tonacji. 


# <a name="custom-speech-to-text"></a>[Custom Speech do tekstu](#tab/cstt)

Kontener *Custom Speech do tekstu* opiera się na niestandardowym modelu mowy. Model niestandardowy musi być [szkolony](how-to-custom-speech-train-model.md) przy użyciu [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Model Custom Speech musi być przeszkolony z jednej z następujących wersji modelu:
> * **20181201 (wersja 3.3 ujednolicona)**
> * **20190520 (wersja 4.14, ujednolicona)**
> * **20190701 (wersja 4.17**<br>
> ![Custom Speech model kontenera uczenia](media/custom-speech/custom-speech-train-model-container-scoped.png)

Do uruchomienia kontenera jest wymagany niestandardowy **Identyfikator modelu** mowy. Można je znaleźć na stronie **uczenie** niestandardowego portalu mowy. W portalu niestandardowych mowy przejdź do strony **szkoleń** i wybierz model.
<br>

![Strona niestandardowego szkolenia mowy](media/custom-speech/custom-speech-model-training.png)

Uzyskaj **Identyfikator modelu** , który ma być używany jako argument `ModelId` parametru `docker run` polecenia.
<br>

![Szczegóły niestandardowego modelu mowy](media/custom-speech/custom-speech-model-details.png)

Poniższa tabela przedstawia różne `docker run` Parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Instalacja woluminu](https://docs.docker.com/storage/volumes/)komputera hosta, który jest używany przez platformę Docker do utrwalania modelu niestandardowego. Na przykład *C:\CustomSpeech* , gdzie *dysk C* znajduje się na komputerze-hoście. |
| `{MODEL_ID}` | **Identyfikator modelu** Custom Speech ze strony **szkoleń** w portalu Custom Speech. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiaru i rozliczania. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Wymagany jest klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić kontener *Custom Speech-to-Text* , wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *Custom Speech do tekstu* z obrazu kontenera.
* Przydziela 4 rdzenie procesora CPU i 4 gigabajty (GB) pamięci.
* Ładuje model *Custom Speech-to-Text* z instalacji wejściowej woluminu, na przykład *C:\CustomSpeech*.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model `ModelId` (jeśli nie został znaleziony w instalacji woluminu).
* Jeśli model niestandardowy został wcześniej pobrany, `ModelId` jest ignorowany.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

Aby uruchomić kontener *zamiany tekstu na mowę* , wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *zamiany tekstu na mowę* z obrazu kontenera.
* Przypisuje 1 rdzeń procesora CPU i 2 gigabajty (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="custom-text-to-speech"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

*Niestandardowy kontener zamiany tekstu na mowę* zależy od niestandardowego modelu głosu. Model niestandardowy musi być [szkolony](how-to-custom-voice-create-voice.md) przy użyciu [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Niestandardowy **Identyfikator modelu** głosu jest wymagany do uruchomienia kontenera. Można je znaleźć na stronie **uczenie** niestandardowego portalu głosowego. W portalu niestandardowego programu Voice przejdź do strony **szkoleń** i wybierz model.
<br>

![Niestandardowa strona szkoleń dotyczących głosu](media/custom-voice/custom-voice-model-training.png)

Uzyskaj **Identyfikator modelu** , który ma być używany jako argument `ModelId` parametru polecenia Docker Run.
<br>

![Szczegóły niestandardowego modelu głosu](media/custom-voice/custom-voice-model-details.png)

Poniższa tabela przedstawia różne `docker run` Parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Instalacja woluminu](https://docs.docker.com/storage/volumes/)komputera hosta, który jest używany przez platformę Docker do utrwalania modelu niestandardowego. Na przykład *C:\CustomSpeech* , gdzie *dysk C* znajduje się na komputerze-hoście. |
| `{MODEL_ID}` | **Identyfikator modelu** Custom Speech ze strony **uczenie** niestandardowego portalu głosu. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiaru i rozliczania. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Wymagany jest klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić *niestandardowy kontener zamiany tekstu na mowę* , wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *niestandardowy kontener zamiany tekstu na mowę* z obrazu kontenera.
* Przypisuje 1 rdzeń procesora CPU i 2 gigabajty (GB) pamięci.
* Ładuje *niestandardowy model zamiany tekstu na mowę* z instalacji wejścia woluminu, na przykład *C:\CustomVoice*.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model `ModelId` (jeśli nie został znaleziony w instalacji woluminu).
* Jeśli model niestandardowy został wcześniej pobrany, `ModelId` jest ignorowany.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

***

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

> [!NOTE]
> Użyj unikatowego numeru portu, jeśli korzystasz z wielu kontenerów.

| Containers | Adres URL hosta zestawu SDK | Protokół |
|--|--|--|
| Zamiana mowy na tekst i Custom Speech na tekst | `ws://localhost:5000` | WS |
| Zamiana tekstu na mowę i niestandardowego tekstu na mowę | `http://localhost:5000` | HTTP |

Aby uzyskać więcej informacji na temat korzystania z protokołów WSS i HTTPS, zobacz [zabezpieczenia kontenera](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analiza tonacji

Jeśli podano poświadczenia interfejs API analizy tekstu [do kontenera](#analyze-sentiment-on-the-speech-to-text-output), można użyć zestawu Speech SDK, aby wysyłać żądania rozpoznawania mowy z analizą tonacji. Można skonfigurować odpowiedzi interfejsu API w taki sposób, aby korzystały z *prostego* lub *szczegółowego* formatu.

# <a name="simple-format"></a>[Format prosty](#tab/simple-format)

Aby skonfigurować klienta mowy do używania prostego formatu, należy dodać `"Sentiment"` jako wartość dla `Simple.Extensions` . Jeśli chcesz wybrać konkretną wersję modelu analiza tekstu, Zastąp `'latest'` wartość w obszarze `speechcontext-phraseDetection.sentimentAnalysis.modelversion` Konfiguracja właściwości.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`zwróci wynik tonacji w warstwie głównej odpowiedzi.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Format szczegółowy](#tab/detailed-format)

Aby skonfigurować klienta mowy do używania formatu szczegółowego, Dodaj `"Sentiment"` jako wartość dla `Detailed.Extensions` , `Detailed.Options` lub obie. Jeśli chcesz wybrać konkretną wersję modelu analiza tekstu, Zastąp `'latest'` wartość w obszarze `speechcontext-phraseDetection.sentimentAnalysis.modelversion` Konfiguracja właściwości.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`dostarcza wynik tonacji w warstwie głównej odpowiedzi. `Detailed.Options`zapewnia wynik w `NBest` warstwie odpowiedzi. Mogą być używane osobno lub razem.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Jeśli chcesz całkowicie wyłączyć analizę tonacji, Dodaj `false` wartość do `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>Zamiana tekstu na mowę lub niestandardowej zamiany tekstu na mowę

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, że każdy kontener jest uruchamiany z innym uwidocznionym portem. Na przykład Uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Można korzystać z tego kontenera i innego kontenera Cognitive Services platformy Azure uruchomionego na HOŚCIE. Można również mieć wiele kontenerów tego samego kontenera Cognitive Services uruchomione.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania lub uruchamiania kontenera mogą wystąpić problemy. Należy użyć [instalacji](speech-container-configuration.md#mount-settings) wyjściowej i włączyć rejestrowanie. Wykonanie tej operacji umożliwi kontenerowi generowanie plików dziennika, które są przydatne podczas rozwiązywania problemów.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery mowy wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu *mowy* na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów mowy. Podsumowanie:

* Funkcja rozpoznawania mowy oferuje cztery kontenery systemu Linux dla platformy Docker, hermetyzowając różne możliwości:
  * *Zamiana mowy na tekst*
  * *Custom Speech do tekstu*
  * *Zamiana tekstu na mowę*
  * *Niestandardowa Zamiana tekstu na mowę*
* Obrazy kontenerów są pobierane z rejestru kontenerów na platformie Azure.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Bez względu na to, czy korzystasz z interfejsu API REST (tylko Zamiana tekstu na mowę), czy zestawu SDK (zamiana mowy na tekst lub zamiany tekstu na mowę), należy określić identyfikator URI hosta kontenera. 
* Podczas tworzenia wystąpienia kontenera wymagane jest podanie informacji dotyczących rozliczeń.

> [!IMPORTANT]
>  Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (np. obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](speech-container-configuration.md) dla ustawień konfiguracji
* Dowiedz się [, jak używać kontenerów usługi mowy z Kubernetes i Helm](speech-container-howto-on-premises.md)
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
