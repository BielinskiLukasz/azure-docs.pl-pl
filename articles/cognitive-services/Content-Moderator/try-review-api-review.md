---
title: Tworzenie przeglądów moderowania za pomocą konsoli interfejsu API REST — Content Moderator
titleSuffix: Azure Cognitive Services
description: Skorzystaj z interfejsów API usługi Azure Content Moderator, aby utworzyć przeglądy obrazów lub tekstu na potrzeby moderowania przez człowieka.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 581ab488337cfecae3f5dd97610c7f92c75af8b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564341"
---
# <a name="create-human-reviews-rest"></a>Tworzenie przeglądów ludzkich (REST)

[Przegląda](./review-api.md#reviews) i wyświetla zawartość dla moderatorów ludzkich do oceny. Gdy użytkownik ukończy przegląd, wyniki są wysyłane do określonego punktu końcowego wywołania zwrotnego. W tym przewodniku dowiesz się, jak skonfigurować przeglądy przy użyciu interfejsów API REST przeglądu za pośrednictwem konsoli interfejsu API. Po zrozumieniu struktury interfejsów API można łatwo przenieść te wywołania na dowolną platformę zgodną z usługą REST.

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-review"></a>Utwórz recenzję

Aby utworzyć przegląd, przejdź do strony **[Przegląd — tworzenie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** odwołania do interfejsu API i wybierz przycisk dla regionu klucza (można go znaleźć w adresie URL punktu końcowego na stronie **poświadczenia** [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/)). Spowoduje to uruchomienie konsoli interfejsu API, w której można łatwo tworzyć i uruchamiać wywołania interfejsu API REST.

![Przegląd — Pobierz wybór regionów](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Wprowadź parametry wywołania REST

Wprowadź wartości dla **TeamName**i **OCP-APIM-Subscription-Key**:

- **TeamName**: Identyfikator zespołu, który został utworzony podczas konfigurowania konta narzędzia do [przeglądania](https://contentmoderator.cognitive.microsoft.com/) (znajdującego się w polu **Identyfikator** na ekranie poświadczeń narzędzia przeglądu).
- **Ocp-Apim-Subscription-Key**: Klucz Content Moderator. Można to znaleźć na karcie **Ustawienia** w narzędziu do [przeglądu](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Wprowadź definicję przeglądu

Edytuj pole **treści żądania** , aby wprowadzić żądanie JSON z następującymi polami:

- **Metadane**: Niestandardowe pary klucz-wartość, które mają zostać zwrócone do punktu końcowego wywołania zwrotnego. Jeśli klucz jest krótkim kodem, który jest zdefiniowany w narzędziu do [przeglądu](https://contentmoderator.cognitive.microsoft.com), pojawia się jako tag.
- **Zawartość**: W przypadku zawartości obrazu i wideo jest to ciąg adresu URL wskazujący zawartość. W przypadku zawartości tekstowej jest to rzeczywisty ciąg tekstowy.
- **Identyfikatorze**: Niestandardowy ciąg identyfikatora. Ten ciąg jest przesyłany do interfejsu API i zwracany przez wywołanie zwrotne. Jest to przydatne w przypadku kojarzenia wewnętrznych identyfikatorów lub metadanych z wynikami zadania moderowania.
- **CallbackEndpoint**: Obowiązkowe Adres URL do odbierania informacji zwrotnych po zakończeniu przeglądu.

W domyślnej treści żądania przedstawiono przykłady różnych typów przeglądów, które można utworzyć:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Prześlij żądanie
  
Wybierz pozycję **Wyślij**. Jeśli operacja się powiedzie, **stan odpowiedzi** to `200 OK`, a w polu **zawartość odpowiedzi** zostanie wyświetlony Identyfikator przeglądu. Skopiuj ten identyfikator, aby użyć go w poniższych krokach.

![Przegląd — Tworzenie pola zawartość odpowiedzi konsoli wyświetla identyfikator przeglądu](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Zbadaj nowy przegląd

W narzędziu do [przeglądu](https://contentmoderator.cognitive.microsoft.com)wybierz pozycję **Przeglądaj** > **wideo** **tekst**/**obrazu**/(w zależności od użytej zawartości). Zaprzekazywana zawartość powinna zostać wyświetlona, gotowa do przeglądu przez ludzi.

![Zapoznaj się z obrazem narzędzia piłka nożna](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Pobierz szczegóły przeglądu

Aby pobrać szczegółowe informacje o istniejącym przeglądzie, przejdź do strony [Przegląd — Pobierz](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) odwołanie do interfejsu API i wybierz przycisk dla regionu (region, w którym jest zarządzany klucz).

![Przepływ pracy — dostęp do wyboru regionów](images/test-drive-region.png)

Wprowadź parametry wywołania REST zgodnie z powyższą sekcją. W tym kroku **reviewId** jest unikatowym ciągiem identyfikatora, który został otrzymany podczas tworzenia przeglądu.

![Przegląd — tworzenie wyników Get konsoli](images/test-drive-review-3.PNG)
  
Wybierz pozycję **Wyślij**. Jeśli operacja powiedzie się, **stan odpowiedzi** to, `200 OK`a w polu **zawartość odpowiedzi** zostaną wyświetlone szczegóły przeglądu w formacie JSON, takie jak następujące:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Zwróć uwagę na następujące pola w odpowiedzi:

- **status**
- **reviewerResultTags**: Ten komunikat jest wyświetlany, jeśli jakikolwiek tag został ręcznie dodany przez zespół ds. kontroli ludzkich (podano pole **createdBy** ).
- **metadane**: Przedstawiono w nim znaczniki, które zostały początkowo dodane w przeglądzie, zanim zespół ds. kontroli przez ludzi wprowadził zmiany.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób tworzenia przeglądów moderowania zawartości przy użyciu interfejsu API REST. Następnie należy zintegrować przeglądy ze scenariuszem kompleksowego łagodzenia, takim jak samouczek [moderowania handlu elektronicznego](./ecommerce-retail-catalog-moderation.md) .