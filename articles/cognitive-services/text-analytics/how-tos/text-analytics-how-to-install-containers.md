---
title: Instalowanie i uruchamianie kontenerów
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery analizy tekstu, w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.openlocfilehash: 6ad48bb6e7d9c2fd0365b26999b67ad8c62fc42c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000256"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalowanie i uruchamianie kontenerów analizy tekstu

Kontenery analizy tekstu zapewniają zaawansowane przetwarzanie języka naturalnego w tekście nieprzetworzonym i udostępnia trzy główne funkcje: analiza tonacji, wyodrębnianie kluczowych fraz oraz wykrywanie języka. Łączenie jednostek nie jest obecnie obsługiwane w kontenerze. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było uruchomić dowolny kontener, analiza tekstu, musi mieć następujące czynności:

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów analizy tekstu, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Tekst analizy zasobów |Aby można było używać kontenera, musisz mieć:<br><br>A [ _analizy tekstu_ ](text-analytics-how-to-access-key.md) zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach witryny Azure portal — omówienie analizy tekstu i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Request access to private preview](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli przedstawiono minimalne i zalecane rdzeni procesora CPU, co najmniej 2,6 gigaherc (GHz) lub szybszy i pamięci w gigabajtach (GB) do przydzielenia dla każdego kontenera analizy tekstu.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|Wyodrębnianie kluczowych fraz | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |
|Wykrywanie języka | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |
|Analiza tonacji | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Obrazy kontenerów dla analizy tekstu są dostępne z rejestru kontenerów firmy Microsoft. 

| Kontener | Repozytorium |
|-----------|------------|
|Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru kontenerów Microsoft...

Pełny opis dostępnych tagów dla kontenerów analizy tekstu zobacz następujące kontenery w usłudze Docker Hub:

* [Wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654)

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Polecenie docker pull kontenera klucza frazy wyodrębniania

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Polecenie docker pull dla kontenera wykrywanie języka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Polecenie docker pull dla kontenera tonacji

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), za pomocą wymaganych rozliczeń ustawienia. Więcej [przykłady](../text-analytics-resource-container-config.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze analizy tekstu w witrynie Azure portal.  |
|{BILLING_ENDPOINT_URI} | Rozliczeń wartość identyfikatora URI punktu końcowego jest dostępna na stronie Przegląd analizy tekstu witryny Azure portal.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener frazy kluczowe z obrazu kontenera
* Przydziela jeden rdzeni procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

Więcej [przykłady](../text-analytics-resource-container-config.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

Kontener udostępnia punkt końcowy usługi oparte na protokole REST zapytania prognozowania interfejsów API. 

Użyj hosta, `https://localhost:5000`, dla kontenera interfejsów API.

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](../text-analytics-resource-container-config.md#mount-settings) i Rejestrowanie włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera. 

## <a name="containers-api-documentation"></a>Dokumentacja interfejsu API kontenera

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Rozliczenia

Wyślij kontenery analizy tekstu, rozliczeń, informacje na platformie Azure, przy użyciu _analizy tekstu_ zasobów dla konta systemu Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów analizy tekstu. Podsumowanie:

* Analiza tekstu zawiera trzy kontenery systemu Linux dla platformy Docker, zawieranie, wyodrębnianie kluczowych fraz, wykrywanie języka i analiza opinii.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach analizy tekstu, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](../text-analytics-resource-container-config.md) ustawień konfiguracji
* Zapoznaj się [— często zadawane pytania (FAQ)](../text-analytics-resource-faq.md) Aby rozwiązać problemy związane z działaniem.

