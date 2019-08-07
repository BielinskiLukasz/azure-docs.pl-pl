---
title: Umiejętność wyszukiwania poznawczego OCR — Azure Search
description: Wyodrębnij tekst z plików obrazów przy użyciu funkcji optycznego rozpoznawania znaków (OCR) w potoku wzbogacenia Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 653fbfe8c20150b2aca3e0a45b2877af35e01fff
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840993"
---
# <a name="ocr-cognitive-skill"></a>Umiejętność OCR

Umiejętność optycznego rozpoznawania znaków (OCR) rozpoznaje drukowany i odręczny tekst w plikach obrazu. Ta umiejętność używa modeli uczenia maszynowego zapewnianych przez [Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) w Cognitive Services. Umiejętność **OCR** jest mapowana na następujące funkcje:

+ Interfejs API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) jest używany w językach innych niż angielski. 
+ W przypadku języka angielskiego jest używany nowy interfejs API ["read"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .

Umiejętność **OCR** wyodrębnia tekst z plików obrazów. Obsługiwane formaty plików to:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w Azure Search. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| detectOrientation | Włącza Autowykrywanie orientacji obrazu. <br/> Prawidłowe wartości: PRAWDA/FAŁSZ.|
|defaultLanguageCode | <p>  Kod języka tekstu wejściowego. Obsługiwane języki: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (czeski) <br/>da (duński) <br/>NL (holenderski) <br/>pl (angielski) <br/>Fi (fiński)  <br/>fr (francuski) <br/>  Niemcy (niemiecki) <br/>El (grecki) <br/> hu (węgierski) <br/> IT (włoski) <br/>  ja (japoński) <br/> ko (koreański) <br/> NB (norweski) <br/>   pl (Polski) <br/> pt (portugalski) <br/>  ru (rosyjski) <br/>  es (hiszpański) <br/>  OHR (szwedzki) <br/>  TR (turecki) <br/> AR (arabski) <br/> ro (rumuński) <br/> Wirtualizacja SR-Cyrl (SerbianCyrillic) <br/> Wirtualizacja sr-latn (SerbianLatin) <br/>  SK (słowacki). <br/>  UNK (nieznany) <br/><br/> Jeśli kod języka jest nieokreślony lub ma wartość null, język zostanie ustawiony na język angielski. Jeśli język jest jawnie ustawiony na wartość "UNK", język zostanie wykryty. </p> |
|lineEnding | Wartość do użycia między wykrytymi wierszami. Możliwe wartości: "Space", "CarriageReturn", "wysuw wiersza".  Wartość domyślna to "Space" |

Wcześniej był już parametr o nazwie "textExtractionAlgorithm", który określa, czy umiejętność powinna wyodrębnić tekst "drukowany" lub "odpisanych".  Ten parametr jest przestarzały i nie jest już potrzebny, ponieważ najnowszy algorytm interfejsu API odczytu jest w stanie wyodrębnić oba typy tekstu jednocześnie.  Jeśli Twoja definicja umiejętności zawiera już ten parametr, nie musisz jej usuwać, ale nie będzie można jej używać, a oba typy tekstu zostaną wyodrębnione w przód, niezależnie od tego, co jest ustawione na.

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Wprowadź nazwę      | Opis                                          |
|---------------|------------------------------------------------------|
| image         | Typ złożony. Obecnie działa tylko z polem "/Document/normalized_images" tworzonym przez indeksator usługi Azure Blob, gdy ```imageAction``` jest ustawiony na wartość inną niż. ```none``` Zobacz [przykład](#sample-output) , aby uzyskać więcej informacji.|


## <a name="skill-outputs"></a>Wyniki umiejętności
| Nazwa wyjściowa     | Opis                   |
|---------------|-------------------------------|
| text          | Czysty tekst wyodrębniony z obrazu.   |
| layoutText    | Typ złożony, który opisuje wyodrębniony tekst i lokalizację, w której został znaleziony tekst.|


## <a name="sample-definition"></a>Definicja Przykładowa

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Przykładowy tekst i layoutText danych wyjściowych

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Przykład: Scalanie tekstu wyodrębnionego z obrazów osadzonych przy użyciu zawartości dokumentu.

Typowym przypadkiem użycia scalania tekstu jest możliwość scalania tekstowej reprezentacji obrazów (tekst z umiejętności OCR lub podpisanie obrazu) do pola zawartość dokumentu.

Poniższy przykład zestawu umiejętności tworzy pole *merged_text* . To pole zawiera zawartość tekstową dokumentu oraz tekst OCRed z każdego obrazu osadzonego w tym dokumencie.

#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
W powyższym przykładzie zestawu umiejętności założono, że istnieje pole Normal-images. Aby wygenerować to pole, Ustaw konfigurację *imageAction* w definicji indeksatora na *generateNormalizedImages* , jak pokazano poniżej:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zobacz także
+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Umiejętność scalania](cognitive-search-skill-textmerger.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz indeksator (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
