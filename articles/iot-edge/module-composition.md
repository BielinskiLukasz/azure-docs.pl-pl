---
title: Wdróż & module Routes z manifestami wdrożenia — Azure IoT Edge
description: Dowiedz się, w jaki sposób manifest wdrożenia deklaruje, które moduły wdrożyć, jak je wdrożyć, oraz jak tworzyć trasy komunikatów między nimi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 31a83d3edb1bc297fc53b089384ab940482e5b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665835"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Dowiedz się, jak wdrażać moduły i ustanawiać trasy w IoT Edge

Każde urządzenie IoT Edge uruchamia co najmniej dwa moduły: $edgeAgent i $edgeHub, które są częścią środowiska uruchomieniowego IoT Edge. Na urządzeniu IoT Edge można uruchamiać wiele dodatkowych modułów dla dowolnej liczby procesów. Użyj manifestu wdrożenia, aby poinformować urządzenie, które moduły instalować i jak skonfigurować je do pracy ze sobą. 

*Manifest wdrożenia* jest dokumentem JSON opisującym:

* Moduł **IoT Edge Agent** , który obejmuje trzy składniki. 
  * Obraz kontenera dla każdego modułu, który działa na urządzeniu.
  * Poświadczenia umożliwiające dostęp do prywatnych rejestrów kontenerów zawierających obrazy modułów.
  * Instrukcje dotyczące sposobu tworzenia i zarządzania każdym modułem.
* **IoT Edgey moduł centralny** , który obejmuje sposób przepływu komunikatów między modułami i ostatecznie do IoT Hub.
* Opcjonalnie wymagane właściwości dowolnego dodatkowego modułu bliźniaczych reprezentacji.

Wszystkie urządzenia IoT Edge muszą być skonfigurowane przy użyciu manifestu wdrożenia. Nowo zainstalowany IoT Edge środowisko uruchomieniowe raportuje kod błędu do momentu skonfigurowania prawidłowego manifestu. 

W samouczkach Azure IoT Edge tworzysz manifest wdrożenia, przechodząc przez kreatora w portalu Azure IoT Edge. Można również zastosować manifest wdrożenia programowo przy użyciu interfejsu REST lub IoT Hub usługi SDK. Aby uzyskać więcej informacji, zobacz [Omówienie wdrożeń IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Tworzenie manifestu wdrożenia

Na wysokim poziomie manifest wdrożenia jest listą bliźniaczych reprezentacji modułów, które są skonfigurowane przy użyciu odpowiednich właściwości. Manifest wdrożenia informuje urządzenie IoT Edge (lub grupę urządzeń), które moduły zainstalować i jak je skonfigurować. Manifesty wdrożenia obejmują *odpowiednie właściwości* dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem *raportowane właściwości* dla każdego modułu. 

W każdym manifeście wdrożenia są wymagane dwa moduły: `$edgeAgent`i `$edgeHub`. Te moduły są częścią środowiska uruchomieniowego IoT Edge, które zarządza urządzeniem IoT Edge i uruchomionymi na nim modułami. Aby uzyskać więcej informacji o tych modułach, zobacz [Omówienie środowiska uruchomieniowego IoT Edge i jego architektury](iot-edge-runtime.md).

Oprócz dwóch modułów środowiska uruchomieniowego można dodać do 20 modułów do uruchomienia na urządzeniu IoT Edge. 

Manifest wdrożenia zawierający tylko środowisko uruchomieniowe IoT Edge (edgeAgent i edgeHub) jest prawidłowy.

Manifesty wdrożenia są zgodne z tą strukturą:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Konfiguruj moduły

Zdefiniuj, w jaki sposób środowisko uruchomieniowe IoT Edge instaluje moduły we wdrożeniu. Agent IoT Edge jest składnikiem środowiska uruchomieniowego, który zarządza instalacją, aktualizacjami i raportowaniem stanu dla urządzenia IoT Edge. Z tego względu sznury modułowe $edgeAgent wymaga konfiguracji i informacji o zarządzaniu dla wszystkich modułów. Te informacje obejmują parametry konfiguracji dla samego agenta IoT Edge. 

Aby uzyskać pełną listę właściwości, które mogą lub muszą być dołączone, zobacz [Właściwości agenta IoT Edge i IoT Edge centrum](module-edgeagent-edgehub.md).

Właściwości $edgeAgent są zgodne z tą strukturą:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Deklarowanie tras

IoT Edge Hub zarządza komunikacją między modułami, IoT Hub i dowolnym urządzeniem liścia. Z tego względu sznurek modułu $edgeHub zawiera pożądaną właściwość o nazwie *trasy* , które deklarują sposób przekazywania komunikatów w ramach wdrożenia. W ramach tego samego wdrożenia można mieć wiele tras.

Trasy są deklarowane w **$edgeHub** żądanych właściwości z następującą składnią:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Każda trasa wymaga źródła i ujścia, ale warunek jest opcjonalny element, którego można użyć do filtrowania komunikatów. 


### <a name="source"></a>Źródło

Źródło określa, skąd pochodzą komunikaty. IoT Edge może kierować wiadomości z modułów lub urządzeń liścia. 

Korzystając z zestawów SDK IoT, moduły mogą deklarować określone kolejki wyjściowe dla swoich komunikatów przy użyciu klasy ModuleClient. Kolejki wyjściowe nie są potrzebne, ale są pomocne w zarządzaniu wieloma trasami. Urządzenia liścia mogą używać klasy DeviceClient zestawów SDK IoT do wysyłania komunikatów do urządzeń bramy IoT Edge w taki sam sposób, że wysyłają komunikaty do IoT Hub. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md).

Właściwość Source może mieć jedną z następujących wartości:

| Źródło | Opis |
| ------ | ----------- |
| `/*` | Wszystkie komunikaty z urządzenia do chmury lub dwuosiowe powiadomienia o zmianie z dowolnego modułu lub urządzenia typu liść |
| `/twinChangeNotifications` | Wszelkie zmiany przędzenia (raportowane właściwości) pochodzące z dowolnego modułu lub urządzenia typu liść |
| `/messages/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez moduł za pomocą niektórych lub niewyjściowych lub przez urządzenie liścia |
| `/messages/modules/*` | Dowolny komunikat z urządzenia do chmury wysyłany przez moduł przez niektóre lub Brak danych wyjściowych |
| `/messages/modules/<moduleId>/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą niektórych lub braku danych wyjściowych |
| `/messages/modules/<moduleId>/outputs/*` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą niektórych danych wyjściowych |
| `/messages/modules/<moduleId>/outputs/<output>` | Wszystkie komunikaty z urządzenia do chmury wysyłane przez określony moduł za pomocą określonych danych wyjściowych |

### <a name="condition"></a>Warunek
Warunek jest opcjonalny w deklaracji trasy. Jeśli chcesz przekazać wszystkie komunikaty ze źródła do ujścia, po prostu pozostaw klauzulę **WHERE** całkowicie. Można też użyć [języka zapytań IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) do filtrowania pewnych komunikatów lub typów komunikatów, które spełniają warunek. Trasy IoT Edge nie obsługują filtrowania komunikatów na podstawie znaczników lub właściwości bliźniaczych. 

Komunikaty przekazywane między modułami w IoT Edge są sformatowane tak samo jak komunikaty przekazywane między urządzeniami i usługą Azure IoT Hub. Wszystkie komunikaty są sformatowane w formacie JSON i mają parametry **systemProperties**, **appProperties**i **Body** . 

Można tworzyć zapytania dotyczące każdego z trzech parametrów z następującą składnią: 

* Właściwości systemu: `$<propertyName>` lub `{$<propertyName>}`
* Właściwości aplikacji: `<propertyName>`
* Właściwości treści: `$body.<propertyName>` 

Aby zapoznać się z przykładami dotyczącymi tworzenia zapytań dotyczących właściwości wiadomości, zobacz [wyrażenia zapytania dotyczącego urządzenia do chmury](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Przykładem specyficznym dla IoT Edge jest przefiltrowanie komunikatów, które dotarły do urządzenia bramy z urządzenia typu liść. Komunikaty pochodzące z modułów obejmują Właściwość systemową o nazwie **connectionModuleId**. Dlatego jeśli chcesz skierować komunikaty z urządzeń liściowych bezpośrednio do IoT Hub, użyj następującej trasy, aby wykluczyć komunikaty modułu:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Ujście
Ujścia definiuje, gdzie są wysyłane komunikaty. Tylko moduły i IoT Hub mogą odbierać wiadomości. Wiadomości nie mogą być kierowane do innych urządzeń. Właściwość ujścia nie zawiera żadnych opcji symboli wieloznacznych. 

Właściwość ujścia może mieć jedną z następujących wartości:

| Ujście | Opis |
| ---- | ----------- |
| `$upstream` | Wyślij wiadomość do IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Wyślij wiadomość do określonego wejścia do określonego modułu |

IoT Edge zapewnia gwarancje co najmniej jednokrotne. Centrum IoT Edge przechowuje komunikaty lokalnie na wypadek, gdyby trasa nie mogła dostarczyć wiadomości do ujścia. Na przykład jeśli Centrum IoT Edge nie może połączyć się z IoT Hub lub moduł docelowy nie jest połączony.

IoT Edge Hub przechowuje komunikaty do czasu określonego we właściwości `storeAndForwardConfiguration.timeToLiveSecs` [centrum IoT Edge właściwości](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definiowanie lub aktualizowanie żądanych właściwości 

Manifest wdrożenia określa żądane właściwości dla każdego modułu wdrożonego na urządzeniu IoT Edge. Żądane właściwości w manifeście wdrożenia zastępują wszystkie wymagane właściwości, które są obecnie w postaci sznurka modułu.

Jeśli nie określisz odpowiednich właściwości sznurka modułu w manifeście wdrożenia, IoT Hub nie zmodyfikuje sznurka modułu w jakikolwiek sposób. Zamiast tego można ustawić odpowiednie właściwości programowo.

Te same mechanizmy, które umożliwiają modyfikowanie bliźniaczych reprezentacji urządzeń, służą do modyfikowania modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów modułu bliźniaczy](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Przykład manifestu wdrażania

Poniższy przykład pokazuje, jak może wyglądać prawidłowy dokument manifestu wdrożenia.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełną listę właściwości, które mogą lub muszą być zawarte w $edgeAgent i $edgeHub, zobacz [Właściwości agenta IoT Edge i IoT Edge centrum](module-edgeagent-edgehub.md).

* Teraz, gdy wiesz już, jak są używane moduły IoT Edge, [Poznaj wymagania i narzędzia do opracowywania modułów IoT Edge](module-development.md).
