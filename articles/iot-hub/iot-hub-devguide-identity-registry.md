---
title: Informacje w rejestrze tożsamości usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Opis rejestru tożsamości usługi IoT Hub i jak go używać do zarządzania urządzeniami. Zawiera informacje dotyczące importowania i eksportowania tożsamości urządzeń w trybie zbiorczym.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: fdbe88492d6260d19955b39ac8eaf6cfb9dba130
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144550"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Informacje w rejestrze tożsamości w Centrum IoT hub

Każde wystąpienie usługi IoT hub zawiera rejestr tożsamości, która przechowuje informacje o urządzeniach i moduły uprawnienia do łączenia z usługą IoT hub. Zanim urządzenie lub moduł może połączyć się z Centrum IoT hub, musi to być wpis dla tego urządzenia lub moduł w rejestrze tożsamości usługi IoT hub. Urządzenie lub moduł również musi przeprowadzić uwierzytelnianie oparte na poświadczeniach przechowywane w rejestrze tożsamości Centrum IoT hub.

Identyfikator urządzenia lub modułu, przechowywane w rejestrze tożsamości jest uwzględniana wielkość liter.

Na wysokim poziomie w rejestrze tożsamości jest kolekcją obsługą REST zasobów tożsamości urządzenia lub modułu. Po dodaniu wpisu w rejestrze tożsamości Centrum IoT Hub tworzy zestaw zasobów na poziomie urządzenia, takich jak kolejki, która zawiera transmitowanych komunikatów z chmury do urządzenia.

Za pomocą rejestru tożsamości należy:

* Aprowizowanie urządzeń lub moduły, łączących się z Centrum IoT hub.
* Kontrola dostępu na — urządzenie/na — modułu na urządzeniu lub punktów końcowych modułu skierowaną do Twojego Centrum.

> [!NOTE]
> Rejestr tożsamości nie zawiera żadnych metadanych specyficznych dla aplikacji.

## <a name="identity-registry-operations"></a>Operacje rejestru tożsamości

Rejestr tożsamości usługi IoT Hub udostępnia następujące operacje:

* Tworzenie tożsamości urządzenia lub modułu
* Zaktualizuj tożsamość urządzenia lub modułu
* Pobieranie tożsamości urządzenia lub moduł według Identyfikatora
* Usuń tożsamość urządzenia lub modułu
* Listy maksymalnie 1000 tożsamości
> Bliźniacza reprezentacja modułu i tożsamości modułu jest w publicznej wersji zapoznawczej. Poniżej funkcji będą obsługiwane tożsamości modułu gdy jest ona ogólnych dostępne.
* Eksportuj tożsamości urządzeń do usługi Azure blob storage
* Importuj tożsamości urządzenia z usługi Azure blob storage

Wszystkie te operacje można użyć optymistycznej współbieżności, jak to określono w [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Jedynym sposobem, aby pobrać wszystkie tożsamości w rejestrze tożsamości usługi IoT hub jest użycie [wyeksportować] [ lnk-export] funkcji.

Rejestr tożsamości usługi IoT Hub:

* Nie zawiera żadnych metadanych aplikacji.
* Możliwy takiego jak słownik, za pomocą **deviceId** lub **moduleId** jako klucz.
* Nie obsługuje ekspresyjny zapytania.

Rozwiązania IoT jest zwykle ma oddzielny magazyn specyficznych dla rozwiązania, który zawiera metadane specyficzne dla aplikacji. Na przykład magazynu specyficznych dla rozwiązania w ramach rozwiązania inteligentne tworzenie będzie rejestrować miejsca, w której wdrożony jest czujnik temperatury.

> [!IMPORTANT]
> Rejestr tożsamości można używać tylko do zarządzania urządzeniami i inicjowania obsługi operacji. Wysoka przepływność operacji w czasie wykonywania nie powinno zależeć od wykonywania operacji w rejestrze tożsamości. Na przykład sprawdzanie stanu połączenia urządzenia przed wysłaniem polecenia nie jest obsługiwany wzorzec. Upewnij się sprawdzić [ograniczanie szybkości] [ lnk-quotas] rejestru tożsamości i [pulsu urządzenia] [ lnk-guidance-heartbeat] wzorca.

## <a name="disable-devices"></a>Wyłączanie urządzeń

Urządzenia można wyłączyć, aktualizując **stan** właściwości tożsamość w rejestrze tożsamości. Zazwyczaj używasz tej właściwości, w przypadku dwóch scenariuszy:

* Podczas inicjowania obsługi administracyjnej procesu aranżacji. Aby uzyskać więcej informacji, zobacz [Device Provisioning][lnk-guidance-provisioning].
* Jeśli z jakiegokolwiek powodu, Twoim zdaniem, urządzenie zostanie naruszony, czy stał się nieautoryzowanych.

Ta funkcja nie jest dostępne dla modułów.

## <a name="import-and-export-device-identities"></a>Importowanie i eksportowanie tożsamości urządzeń

Użyj operacji asynchronicznych na [punkt końcowy dostawcy zasobów usługi IoT Hub] [ lnk-endpoints] eksportowania tożsamości urządzeń w trybie zbiorczym z rejestru tożsamości usługi IoT hub. Eksporty są długotrwałych zadań, korzystających z kontenera obiektów blob z dostarczonego przez klienta w celu zapisywania danych tożsamości urządzenia odczytać z rejestru tożsamości.

Użyj operacji asynchronicznych na [punkt końcowy dostawcy zasobów usługi IoT Hub] [ lnk-endpoints] umożliwia zaimportowanie tożsamości urządzeń w trybie zbiorczym do rejestru tożsamości usługi IoT hub. Importy są długotrwałych zadań, korzystających z danych w kontenerze obiektów blob z dostarczonego przez klienta do zapisania danych tożsamości urządzenia w rejestrze tożsamości.

Aby uzyskać więcej informacji na temat import i eksport interfejsów API, zobacz [dostawcy zasobów usługi IoT Hub interfejsów API REST][lnk-resource-provider-apis]. Aby dowiedzieć się więcej o uruchamianiu Importuj i Eksportuj zadania, zobacz [zbiorcze Zarządzanie tożsamościami urządzeń usługi IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń

Dane urządzenia, która przechowuje dane rozwiązanie IoT, zależy od konkretnych wymaganiach dla tego rozwiązania. Ale co najmniej rozwiązanie musi przechowywać tożsamości urządzeń i klucze uwierzytelniania. Usługa Azure IoT Hub zawiera rejestr tożsamości, które mogą być przechowywane wartości dla każdego urządzenia, takich jak identyfikatory i klucze uwierzytelniania, kody stanu. Rozwiązanie można użyć innych usług platformy Azure, takich jak usługi table storage, blob storage lub usługi Cosmos DB do przechowywania wszelkich dodatkowych danych urządzeń.

*Inicjowanie obsługi administracyjnej urządzeń* to proces dodawania danych urządzenie początkowe sklepów w rozwiązaniu. Aby włączyć nowe urządzenie nawiązać połączenie z Centrum, należy dodać identyfikator urządzenia i klucze rejestru tożsamości usługi IoT Hub. Jako część procesu inicjowania obsługi administracyjnej może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązania. Azure IoT Hub Device Provisioning Service umożliwia również włączyć bezobsługowe, just-in-time aprowizacji IoT Hub, nie wymagając interwencji człowieka. Aby dowiedzieć się więcej, zobacz [inicjowania obsługi dokumentacji usługi][lnk-dps].

## <a name="device-heartbeat"></a>Urządzenie pulsu

Rejestr tożsamości usługi IoT Hub zawiera pole o nazwie **element connectionState**. Używaj tylko **element connectionState** pola podczas opracowywania i debugowania. Pole nie powinno zapytania rozwiązania IoT w czasie wykonywania. Na przykład nie kwerendy **element connectionState** pola, aby sprawdzić, czy urządzenie jest połączony, zanim wyślesz wiadomość SMS lub wiadomości chmury do urządzenia.

Jeśli rozwiązanie IoT musi znać, jeśli urządzenie jest podłączone, należy zaimplementować *wzorzec pulsu*.
We wzorcu pulsu urządzenie wysyła komunikaty z urządzenia do chmury co najmniej jeden raz każdego ustaloną ilość czasu (np. co najmniej raz na godzinę). W związku z tym nawet jeśli urządzenie nie ma żadnych danych do wysłania, nadal wysyła pustego komunikatu urządzenia do chmury (zazwyczaj z właściwością, która identyfikuje go jako pulsu). Po stronie usługi rozwiązania przechowuje mapę z ostatni Puls dla każdego urządzenia. Rozwiązania nie pojawi się komunikat pulsu w oczekiwanym czasie z urządzenia, założono, że występuje problem z urządzeniem.

Bardziej złożone wdrożenia mogą obejmować informacje z [monitorowanie operacji] [ lnk-devguide-opmon] do identyfikowania urządzeń, które są próby połączenia lub komunikację, ale kończy się niepowodzeniem. Podczas implementowania wzorca pulsu, upewnij się sprawdzić [IoT Hub przydziały i ograniczenia][lnk-quotas].

> [!NOTE]
> Jeśli rozwiązania IoT używa stanu połączenia wyłącznie w celu ustalenia, czy do wysyłania komunikatów z chmury do urządzeń, a komunikaty nie są emitowane do dużych zestawach urządzeń, należy wziąć pod uwagę przy użyciu prostszej *krótki czas wygaśnięcia* wzorca. Ten wzorzec osiąga ten sam wynik w postaci przechowywanie rejestru stanu połączenia urządzenia przy użyciu wzorca pulsu, będąc bardziej wydajne. Jeśli żądania potwierdzeń komunikatów usługi IoT Hub może generować powiadomienia o tym, które urządzenia są mogące odbierać komunikaty, które nie są.

## <a name="device-and-module-lifecycle-notifications"></a>Powiadomienia dotyczące cyklu życia urządzeń i modułu

Po utworzeniu lub usunięciu, wysyłając powiadomienia dotyczące cyklu życia tożsamości usługi IoT Hub może generować powiadomienia rozwiązania IoT. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustaw źródło danych jest równa *DeviceLifecycleEvents* lub *ModuleLifecycleEvents*. Domyślnie są wysyłane nie powiadomienia dotyczące cyklu życia, oznacza to, że istnieje wstępnie nie takich tras. Komunikat powiadomienia zawiera właściwości i treść.

Właściwości: Właściwości systemu komunikat mają prefiks `'$'` symboli.

Komunikat powiadomienia dla urządzenia:

| Name (Nazwa) | Wartość |
| --- | --- |
|$content — typ | application/json |
|$iothub-enqueuedtime |  Czas wysłania powiadomienia |
|$iothub-message-source | deviceLifecycleEvents |
|$content — kodowanie | UTF-8 |
|opType | **createDeviceIdentity** lub **deleteDeviceIdentity** |
|hubName | Nazwa centrum IoT Hub |
|deviceId | Identyfikator urządzenia |
|operationTimestamp | Sygnatura czasowa ISO8601 operacji |
|iothub-message-schema | deviceLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje bliźniaczą reprezentację urządzenia utworzonego tożsamości. Na przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Komunikat powiadomienia dla modułu:

| Name (Nazwa) | Wartość |
| --- | --- |
$content — typ | application/json |
$iothub-enqueuedtime |  Czas wysłania powiadomienia |
$iothub-message-source | moduleLifecycleEvents |
$content — kodowanie | UTF-8 |
opType | **createModuleIdentity** lub **deleteModuleIdentity** |
hubName | Nazwa centrum IoT Hub |
moduleId | Identyfikator modułu |
operationTimestamp | Sygnatura czasowa ISO8601 operacji |
iothub-message-schema | moduleLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje bliźniaczą reprezentację tożsamości utworzony moduł. Na przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Właściwości tożsamości urządzenia

Tożsamości urządzeń są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane tylko do odczytu na aktualizacje |Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaki alfanumeryczne znaki ASCII 7-bitowych oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| Identyfikator generacji |wymagane tylko do odczytu |IoT generowanych przez koncentrator, wielkość liter ciągu maksymalnie 128 znaków. Ta wartość jest używana do rozróżniania urządzeń o takiej samej **deviceId**, gdy został usunięty i utworzony ponownie. |
| Element etag |wymagane tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości urządzenia zgodnie [RFC7232][lnk-rfc7232]. |
| uwierzytelnianie |opcjonalne |Obiekt złożony zawierające uwierzytelnianie materiały informacji i zabezpieczeń. |
| auth.symkey |opcjonalne |Obiekt złożony, zawierający podstawowy i klucz pomocniczy, przechowywane w formacie base64. |
| status |wymagane |Wskazuje dostęp. Może być **włączone** lub **wyłączone**. Jeśli **włączone**, urządzenie może nawiązać połączenie. Jeśli **wyłączone**, to urządzenie nie może uzyskać dostępu dowolnego punktu końcowego dostępnego z urządzenia. |
| statusReason |opcjonalne |128 ciąg znaków długości, który przechowuje przyczynę stanu tożsamości urządzenia. Wszystkie znaki UTF-8 są dozwolone. |
| statusUpdateTime |tylko do odczytu |Wskaźnik danych czasowych, wyświetlane data i godzina ostatniej aktualizacji stanu. |
| Element connectionState |tylko do odczytu |Pola wskazujący stan połączenia: albo **połączono** lub **Disconnected**. To pole reprezentuje widok IoT Hub stanu połączenia urządzenia. **Ważne**: to pole powinno używane tylko do celów projektowania/debugowanie. Stan połączenia zostanie zaktualizowany tylko w przypadku urządzeń korzystających z protokołu MQTT lub AMQP. Ponadto jest on oparty na poziomie protokołu ping (polecenia ping protokołu MQTT lub polecenia ping protokołu AMQP) i może mieć Maksymalne opóźnienie tylko 5 minut. Z tego względu może istnieć wyniki fałszywie dodatnie, takie jak urządzeń zgłoszonych jako połączone, ale które są odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik danych czasowych przedstawiająca stan połączenia datę i godzinę ostatniego został zaktualizowany. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych zawierający datę i godzinę ostatniego urządzenie połączenia, odebranych lub wysłanych wiadomości. |

> [!NOTE]
> Stan połączenia może być reprezentowana przez widok stanu połączenia usługi IoT Hub. Aktualizacje do tego stanu mogą być opóźnione w zależności od tego, czy stan sieci i konfiguracji.

## <a name="module-identity-properties"></a>Właściwości tożsamości modułu

Moduł tożsamości są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane tylko do odczytu na aktualizacje |Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaki alfanumeryczne znaki ASCII 7-bitowych oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |wymagane tylko do odczytu na aktualizacje |Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaki alfanumeryczne znaki ASCII 7-bitowych oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| Identyfikator generacji |wymagane tylko do odczytu |IoT generowanych przez koncentrator, wielkość liter ciągu maksymalnie 128 znaków. Ta wartość jest używana do rozróżniania urządzeń o takiej samej **deviceId**, gdy został usunięty i utworzony ponownie. |
| Element etag |wymagane tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości urządzenia zgodnie [RFC7232][lnk-rfc7232]. |
| uwierzytelnianie |opcjonalne |Obiekt złożony zawierające uwierzytelnianie materiały informacji i zabezpieczeń. |
| auth.symkey |opcjonalne |Obiekt złożony, zawierający podstawowy i klucz pomocniczy, przechowywane w formacie base64. |
| status |wymagane |Wskazuje dostęp. Może być **włączone** lub **wyłączone**. Jeśli **włączone**, urządzenie może nawiązać połączenie. Jeśli **wyłączone**, to urządzenie nie może uzyskać dostępu dowolnego punktu końcowego dostępnego z urządzenia. |
| statusReason |opcjonalne |128 ciąg znaków długości, który przechowuje przyczynę stanu tożsamości urządzenia. Wszystkie znaki UTF-8 są dozwolone. |
| statusUpdateTime |tylko do odczytu |Wskaźnik danych czasowych, wyświetlane data i godzina ostatniej aktualizacji stanu. |
| Element connectionState |tylko do odczytu |Pola wskazujący stan połączenia: albo **połączono** lub **Disconnected**. To pole reprezentuje widok IoT Hub stanu połączenia urządzenia. **Ważne**: to pole powinno używane tylko do celów projektowania/debugowanie. Stan połączenia zostanie zaktualizowany tylko w przypadku urządzeń korzystających z protokołu MQTT lub AMQP. Ponadto jest on oparty na poziomie protokołu ping (polecenia ping protokołu MQTT lub polecenia ping protokołu AMQP) i może mieć Maksymalne opóźnienie tylko 5 minut. Z tego względu może istnieć wyniki fałszywie dodatnie, takie jak urządzeń zgłoszonych jako połączone, ale które są odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik danych czasowych przedstawiająca stan połączenia datę i godzinę ostatniego został zaktualizowany. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych zawierający datę i godzinę ostatniego urządzenie połączenia, odebranych lub wysłanych wiadomości. |

## <a name="additional-reference-material"></a>Dodatkowe materiały

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub] [ lnk-endpoints] w tym artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska wykonawczego.
* [Przydziału i ograniczanie przepływności] [ lnk-quotas] opisano limity przydziału i ograniczanie zachowań, które są stosowane do usługi IoT Hub.
* [Usługa Azure IoT usługi zestawy SDK urządzeń i] [ lnk-sdks] Wyświetla język różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.
* [Język zapytań usługi IoT Hub] [ lnk-query] opisuje język zapytań, można użyć, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.
* [Obsługa protokołu MQTT Centrum IoT] [ lnk-devguide-mqtt] zawiera więcej informacji na temat obsługi usługi IoT Hub dla protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak za pomocą rejestru tożsamości usługi IoT Hub, mogą być zainteresowane w następujących tematach przewodnik dla deweloperów usługi IoT Hub:

* [Kontrola dostępu do usługi IoT Hub][lnk-devguide-security]
* [Przy użyciu bliźniaczych reprezentacji urządzeń do stanu i konfiguracji][lnk-devguide-device-twins]
* [Wywoływanie metody bezpośredniej o urządzeniu][lnk-devguide-directmethods]
* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Rozpoczynanie pracy z usługą Azure IoT Hub][lnk-getstarted-tutorial]

Aby zapoznać się z pomocą IoT Hub Device Provisioning Service do włączenia aprowizacji bezobsługowe, just-in-time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning Service][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: quickstart-send-telemetry-dotnet.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
