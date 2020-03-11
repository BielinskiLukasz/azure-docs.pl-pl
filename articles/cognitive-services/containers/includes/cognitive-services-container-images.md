---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Dwie tabele przedstawiające rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usług poznawczych.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082119"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services. Aby uzyskać pełną listę wszystkich dostępnych nazw obrazów kontenerów i ich dostępnych tagów, zobacz [Cognitive Services znacznika obrazu kontenera](../container-image-tags.md). Obecnie nie ma Cognitive Services kontenerów, które są ogólnie dostępne (GA). Przez czas do momentu wykonania dalszych anonsów — kontenery są dostępne jako publiczne, *niewarunkowe* lub *publicznej wersji zapoznawczej*.

 - *Publiczne niewarunkowe*: kontenery są dostępne publicznie bez mechanizmu kontroli.
 - *Publiczna wersja zapoznawcza*: kontenery są dostępne publicznie, ale najpierw wymagają formalnego żądania dostępu do rejestru kontenerów.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Public "ungated" (rejestr kontenerów: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) łączy wszystkie publicznie dostępne kontenery "niebrama" dla Cognitive Services. Kontenery są również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "Gated" (wersja zapoznawcza) (rejestr kontenerów: `containerpreview.azurecr.io`)

Rejestr w wersji zapoznawczej kontenera zawiera wszystkie publicznie dostępne kontenery "Gated" Cognitive Services. Te kontenery wymagają formalnego żądania dostępu do nich za pośrednictwem ich rejestru kontenerów.

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Wykrywacz anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) | Narzędzie do wykrywania anomalii | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Odczytywanie | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Stoi](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=stt) | Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech do tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=tts) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowa Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
