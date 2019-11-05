---
title: 'Szybki Start: konwertowanie zamiany tekstu na mowę i języka Python na mowę'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak konwertować zamianę tekstu na mowę przy użyciu języka Python i interfejsu API REST zamiany tekstu na mowę. Przykładowy tekst zawarty w tym przewodniku jest uporządkowany jako SSML (Speech Syntezing Language). Pozwala to na wybranie głosu i języka odpowiedzi na mowę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a66835d605b9005b8f94eb79a3c266f735f0a3b6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467212"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Szybki Start: konwertowanie tekstu na mowę przy użyciu języka Python

W tym przewodniku szybki start dowiesz się, jak konwertować zamianę tekstu na mowę przy użyciu języka Python i interfejsu API REST zamiany tekstu na mowę. Treść żądania w tym przewodniku jest uporządkowana jako [SSML (Speech syntezing Language)](speech-synthesis-markup.md), która umożliwia wybranie głosu i języka odpowiedzi.

Ten przewodnik Szybki Start wymaga [konta Cognitive Services platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi Speech Services. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Środowisko Python 2.7.x lub 3.x
* Program [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) lub ulubiony edytor tekstów
* Klucz subskrypcji platformy Azure dla usługi Speech Services

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests`.

Te moduły są używane do zapisywania odpowiedzi mowy do pliku z sygnaturą czasową, konstruowania żądania HTTP i wywoływania interfejsu API zamiany tekstu na mowę.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ustawianie klucza subskrypcji i tworzenie monitu dla usługi TTS

W kilku następnych sekcjach utworzysz metody do obsługi autoryzacji, Wywołaj interfejs API zamiany tekstu na mowę i zweryfikuje odpowiedź. Zacznijmy od dodania kodu, który sprawdza, czy ten przykład będzie działał z językiem Python 2.7. x i 3. x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Następnie Utwórzmy klasę. W tym miejscu umieścimy nasze metody wymiany tokenów i wywołująmy interfejs API zamiany tekstu na mowę.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` jest unikatowym kluczem z Azure Portal. `tts` monituje użytkownika o wprowadzenie tekstu, który zostanie przekonwertowany na mowę. Ta wartość wejściowa jest literałem ciągu, dlatego znaki nie muszą być wyprowadzane. Na koniec `timestr` pobiera bieżącą godzinę, przy użyciu której będziemy nazwać plik.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Interfejs API REST zamiany tekstu na mowę wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagany jest program Exchange. Ten przykład umożliwia wymianę klucza subskrypcji usługi Speech Services w celu uzyskania tokenu dostępu przy użyciu punktu końcowego `issueToken`.

W tym przykładzie przyjęto założenie, że subskrypcja usługi Speech Services znajduje się w regionie zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość `fetch_token_url`. Aby uzyskać pełną listę, zobacz [regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj ten kod do klasy `TextToSpeech`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Utwórz żądanie i Zapisz odpowiedź

Tutaj można skompilować żądanie i zapisać odpowiedź na mowę. Najpierw należy ustawić `base_url` i `path`. W tym przykładzie przyjęto założenie, że używasz regionu zachodnie stany USA. Jeśli zasób jest zarejestrowany w innym regionie, upewnij się, że `base_url`. Aby uzyskać więcej informacji, zobacz [regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie musisz dodać wymagane nagłówki dla żądania. Upewnij się, że Zaktualizowano `User-Agent` przy użyciu nazwy zasobu (znajdującego się w Azure Portal) i ustaw `X-Microsoft-OutputFormat` na preferowany wynik audio. Aby zapoznać się z pełną listą formatów danych wyjściowych, zobacz [wyjście audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie Skonstruuj treść żądania przy użyciu języka SSML (Speech Syntezing Language). Ten przykład definiuje strukturę i używa utworzonych wcześniej `tts` danych wejściowych.

>[!NOTE]
> Ten przykład używa czcionki głosu `Guy24KRUS`. Aby zapoznać się z pełną listą podanych głosów/języków firmy Microsoft, zobacz temat [Obsługa języków](language-support.md).
> Jeśli interesuje Cię tworzenie unikatowego, rozpoznawalnego głosu dla marki, zobacz [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md).

Na koniec zostanie wysłane żądanie do usługi. Jeśli żądanie zakończy się pomyślnie i zostanie zwrócony kod stanu 200, odpowiedź mowy jest zapisywana w pliku z sygnaturą czasową.

Skopiuj ten kod do klasy `TextToSpeech`:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest utworzenie wystąpienia klasy i wywołanie funkcji.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, co jest gotowe do uruchamiania przykładowej aplikacji zamiany tekstu na mowę. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
python tts.py
```

Po wyświetleniu monitu wpisz w dowolny sposób, który ma zostać przekonwertowany z tekstu na mowę. Jeśli to się powiedzie, plik mowy znajduje się w folderze projektu. Odtwórz go przy użyciu ulubionego odtwarzacza multimedialnego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Konwertowanie zamiany tekstu na mowę przy użyciu języka Python i zestawu Speech SDK](quickstarts/speech-to-text-from-microphone.md)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Rejestruj przykłady głosu, aby utworzyć niestandardowy głos](record-custom-voice-samples.md)
