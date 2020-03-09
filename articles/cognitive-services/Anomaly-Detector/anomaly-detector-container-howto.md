---
title: Jak zainstalować i uruchomić kontenery do korzystania z interfejsu API wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Użyj zaawansowanych algorytmów interfejsu API wykrywania anomalii, aby identyfikować anomalie w danych szeregów czasowych.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390717"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Instalowanie i uruchamianie kontenerów wykrywania anomalii (wersja zapoznawcza)

Detektor anomalii ma następujące funkcje funkcji kontenera:

| Funkcja | Funkcje |
|--|--|
| Wykrywacz anomalii | <li> Wykrywa anomalie w czasie rzeczywistym. <li> Wykrywa anomalie w zestawie danych jako zadanie wsadowe. <li> Wnioskuje oczekiwany normalny zakres danych. <li> Obsługuje dostosowanie wykrywania anomalii w celu lepszego dopasowania do danych. |

Aby uzyskać szczegółowe informacje na temat interfejsów API, zobacz:
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów wykrywania anomalii należy spełnić następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.| 
|Zasób wykrywania anomalii |Aby można było korzystać z tych kontenerów, musisz mieć:<br><br>Zasób _wykrywania anomalii_ platformy Azure w celu pobrania skojarzonego klucza interfejsu API i identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach omówienie i klucze **wykrywania anomalii** Azure Portal i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}** : punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać [formularz żądania kontenera wykrywania anomalii](https://aka.ms/adcontainer) , aby zażądać dostępu do kontenera.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalną i zalecaną liczbę rdzeni procesora CPU oraz ilość pamięci do przydzielenia dla kontenera wykrywania anomalii.

| ZAPYTAŃ (zapytania na sekundę) | Minimalne | Zalecane |
|-----------|---------|-------------|
| 10 ZAPYTAŃ | 4 rdzenie, 1 GB pamięci | 8 rdzeni 2 GB pamięci |
| 20 ZAPYTAŃ | 8 rdzeni, 2 GB pamięci | 16 rdzeni 4 GB pamięci |

Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają ustawieniom `--cpus` i `--memory`, które są używane jako część polecenia `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera.

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Wypychanie platformy Docker dla kontenera wykrywania anomalii

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](anomaly-detector-container-configuration.md#example-docker-run-commands) polecenia `docker run` są dostępne.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat pobierania wartości `{ENDPOINT_URI}` i `{API_KEY}`.

[Przykłady](anomaly-detector-container-configuration.md#example-docker-run-commands) polecenia `docker run` są dostępne.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener wykrywania anomalii z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, że każdy kontener jest uruchomiony z innym portem. Na przykład Uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Zastąp `<container-registry>` i `<container-name>` wartościami kontenerów, z których korzystasz. Nie muszą one być tym samym kontenerem. Możliwe jest posiadanie kontenera wykrywania anomalii i kontenera LUIS uruchomionego na HOŚCIE. można też korzystać z wielu kontenerów wykrywania anomalii. 

Uruchom pierwszy kontener na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Uruchom drugi kontener na porcie 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Każdy kolejny kontener powinien znajdować się na innym porcie. 

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań. 

Użyj hosta http://localhost:5000w przypadku interfejsów API kontenerów.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](anomaly-detector-container-configuration.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery wykrywania anomalii wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _wykrywania anomalii_ na Twoim koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów wykrywania anomalii. Podsumowanie:

* Detektor anomalii zawiera jeden kontener systemu Linux dla platformy Docker, Hermetyzowanie wykrywania anomalii przy użyciu usługi Batch i przesyłania strumieniowego z oczekiwanym zakresem oraz dostrajania dokładności.
* Obrazy kontenerów są pobierane z prywatnego Azure Container Registry dedykowanego dla usługi Containers w wersji zapoznawczej.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach wykrywania anomalii przez określenie identyfikatora URI hosta kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (np. dane szeregów czasowych, które są analizowane) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](anomaly-detector-container-configuration.md) dla ustawień konfiguracji
* [Wdróż kontener wykrywania anomalii w Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
