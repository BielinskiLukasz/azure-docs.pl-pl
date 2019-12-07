---
title: Integracja z partnerami obsługującymi czujniki
description: W tym artykule opisano integrację partnerów czujników.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1570e85d93e7d82b5a842697a7755603247375b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896528"
---
# <a name="sensor-partner-integration"></a>Integracja z partnerami obsługującymi czujniki

Ten artykuł zawiera informacje na temat składnika usługi Azure FarmBeats translator, który umożliwia integrację z partnerem czujnika.

Za pomocą tego składnika partnerzy mogą opracowywać czujniki, które integrują się z usługą FarmBeats, aby używać interfejsu API i wysyłać dane urządzenia i telemetrię do FarmBeats Datahub. Dane są wizualizowane przy użyciu akceleratora FarmBeats. Dane mogą służyć do łączenia danych i tworzenia modeli uczenia maszynowego/sztucznej analizy.

## <a name="link-a-farmbeats-account"></a>Łączenie konta FarmBeats

Po zakupionych i wdrożonych urządzeniach lub czujników możesz uzyskać dostęp do danych urządzenia i telemetrii w portalu "oprogramowanie jako usługa" (SaaS). Partnerzy urządzeń umożliwiają łączenie konta z wystąpieniem FarmBeats na platformie Azure. Następujące poświadczenia muszą zostać wypełnione przez użytkownika lub integratora systemu:

   - Nazwa wyświetlana (opcjonalne pole dla użytkowników, aby zdefiniować nazwę dla tej integracji)
   - Punkt końcowy interfejsu API
   - Identyfikator dzierżawy
   - Identyfikator klienta
   - Klucz tajny klienta
   - Parametry połączenia EventHub
   - Data rozpoczęcia

   > [!NOTE]
   > Data rozpoczęcia włącza strumieniowe źródło danych, czyli dane z daty określonej przez użytkownika.

## <a name="unlink-farmbeats"></a>Odłącz FarmBeats

Istnieje możliwość odłączenia istniejącej integracji FarmBeats. Odłączanie FarmBeats nie powinno usunąć żadnych metadanych urządzenia ani czujnika, które zostały utworzone w centrum danych. Odłączanie wykonuje następujące czynności:

   - Powoduje zatrzymanie przepływu telemetrii.
   - Usuwa i wymazuje poświadczenia integracji w partnerze urządzeń.

## <a name="edit-farmbeats-integration"></a>Edytuj integrację FarmBeats

Ustawienia integracji FarmBeats można edytować, jeśli klucz tajny klienta lub parametry połączenia są zmieniane. W takim przypadku można edytować tylko następujące pola:

   - Nazwa wyświetlana (jeśli dotyczy)
   - Wpis tajny klienta (powinien być wyświetlany w formacie "2X8 * * * * * * * * *
   - Parametry połączenia (powinny być wyświetlane w postaci "2X8 * * * * * * * * * * *," format lub Pokaż/Ukryj funkcję, a nie zwykły tekst)

## <a name="view-the-last-telemetry-sent"></a>Wyświetl ostatnie wysłane dane telemetryczne

Można wyświetlić sygnaturę czasową ostatniej wysyłanej telemetrii, która została znaleziona w obszarze **wysłane dane telemetryczne**. Jest to czas, w którym Najnowsza Telemetria została pomyślnie wysłana do FarmBeats.

## <a name="translator-development"></a>Programowanie w usłudze translator

**Integracja oparta na interfejsie API REST**

Możliwości integracji danych z czujnika FarmBeats są udostępniane za pośrednictwem interfejsu API REST. Możliwości obejmują definicje metadanych, urządzenia i aprowizacji czujników oraz zarządzanie urządzeniami i czujnikami.

**Pozyskiwanie danych telemetrycznych**

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

**Programowanie interfejsu API**

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Aby uzyskać więcej informacji na temat interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Uwierzytelnianie**

FarmBeats używa uwierzytelniania Microsoft Azure Active Directory. Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Datahub używa uwierzytelniania okaziciela, które wymaga następujących poświadczeń:
   - Identyfikator klienta
   - Klucz tajny klienta
   - Identyfikator dzierżawy

Przy użyciu tych poświadczeń wywołujący może zażądać tokenu dostępu. Token musi zostać wysłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Następujący przykładowy kod w języku Python daje token dostępu, który może być używany do kolejnych wywołań interfejsu API do FarmBeats.

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**Nagłówki żądań HTTP**

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do FarmBeats Datahub.


**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type | Format żądania (Content-Type: Application/<format>). W przypadku interfejsów API FarmBeats Datahub format jest JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: < tokenu dostępu >
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API FarmBeats Datahub format jest JSON. Akceptuj: Application/JSON

**Żądania interfejsu API**

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST lub PUT), adres URL usługi interfejsu API, Uniform Resource Identifier (URI) do zasobu, aby wykonać zapytanie, przesłać dane do, zaktualizować lub usunąć oraz co najmniej jeden nagłówek żądania HTTP. Adres URL usługi API Service to punkt końcowy interfejsu API, który jest udostępniany. Oto przykład: https://\<yourdatahub-Website-Name >. azurewebsites. NET

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Poniższe przykładowe żądanie polega na wyświetleniu listy urządzeń.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie polega na utworzeniu urządzenia. (Ten przykład zawiera wejściowy kod JSON z treścią żądania).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specyfikacje metadanych

FarmBeats Datahub zawiera następujące interfejsy API, które umożliwiają partnerom urządzeń tworzenie metadanych urządzenia lub czujnika oraz zarządzanie nimi. 

- /**DeviceModel**: DeviceModel odpowiada metadanych urządzenia, takich jak producent i typ urządzenia, który jest bramą lub węzłem. 
- **urządzenie**/: urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie.
- /**SensorModel**: SensorModel odpowiada metadanych czujnika, takich jak producent, typ czujnika, który jest analogowy lub cyfrowy i pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
- **czujnik**/: czujnik odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.
  
  **DeviceModel** |  |
  --- | ---
  Typ (węzeł, brama)  | 1 gwiazdka |
  Producent  | 2 gwiazdki |
  ProductCode  | Kod produktu urządzenia lub nazwa modelu lub numer. Na przykład EnviroMonitor # 6800. |
  Porty  | Nazwa i typ portu, które są cyfrowe lub analogowe.  |
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu. |
  Opis  | Podaj znaczący opis modelu. |
  Właściwości  | Dodatkowe właściwości producenta. |
  **urządzenia** |  |
  DeviceModelId  |Identyfikator skojarzonego modelu urządzenia. |
  HardwareId   |Unikatowy identyfikator urządzenia, na przykład adres MAC.  |
  ReportingInterval |Interwał raportowania (w sekundach). |
  Lokalizacja    |Urządzenia Latitude (-90 do + 90), długości geograficznej (-180 do 180) i podniesienia uprawnień (w metrach). |
  ParentDeviceId | Identyfikator urządzenia nadrzędnego, z którym jest połączone to urządzenie. Na przykład jeśli węzeł jest połączony z bramą, węzeł ma parentDeviceID jako bramę. |
  Nazwa  | Nazwa identyfikująca zasób. Partnerzy urządzeń muszą wysłać nazwę zgodną z nazwą urządzenia po stronie partnera urządzeń. Jeśli nazwa urządzenia jest zdefiniowana przez użytkownika po stronie partnera urządzeń, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do FarmBeats.  |
  Opis  | Podaj znaczący opis.  |
  Właściwości  |Dodatkowe właściwości producenta.  |
  **SensorModel** |  |
  Typ (analogowy, cyfrowy)  |Wzmianka o czujniku analogowym lub cyfrowym.|
  Producent  | Nazwa producenta. |
  ProductCode  | Kod produktu lub nazwa modelu lub numer. Na przykład RS-CO2-N01.  |
  Nazwa > SensorMeasures  | Nazwa miary czujnika. Obsługiwane są tylko małe litery. W przypadku pomiarów z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być spójna z danymi telemetrycznymi. |
  SensorMeasures > DataType  | Typ danych telemetrii. Obecnie jest obsługiwana Podwójna precyzja. |
  Typ > SensorMeasures  | Typ pomiaru danych telemetrii czujnika. Poniżej przedstawiono typy zdefiniowane przez system: AmbientTemperature, CO2, Głębokość, ElectricalConductivity, LeafWetness, długość, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType.
  Jednostka > SensorMeasures | Jednostka danych telemetrii czujnika. Poniżej przedstawiono jednostki zdefiniowane przez system: nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetr, centymetr, metr, cal, stopy, mila, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stopień, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, wartość procentowa, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond i InchesPerHour. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType.
  SensorMeasures > agregacji  | None, Average, maksimum, minimum lub StandardDeviation.
  Głębokość > SensorMeasures  | Głębokość czujnika w centymetrach. Na przykład pomiar wilgoci 10 cm pod ziemią.
  SensorMeasures > Opis  | Podaj znaczący opis miary.
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu.
  Opis  | Podaj znaczący opis modelu.
  Właściwości  | Dodatkowe właściwości producenta.
  **Czujnik**  |  |
  HardwareId  | Unikatowy identyfikator czujnika określonego przez producenta.
  SensorModelId  | Identyfikator skojarzonego modelu czujnika.
  Lokalizacja  | Czujnik Latitude (-90 do + 90), Długość geograficzna (-180 do 180) i podniesienie (w metrach).
  Nazwa > portu  |Nazwa i typ portu, z którym jest połączony czujnik na urządzeniu. Ta nazwa musi być taka sama jak zdefiniowana w modelu urządzenia.
  DeviceId  | Identyfikator urządzenia, z którym jest połączony czujnik.
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa czujnika lub nazwa produktu oraz numer modelu lub kod produktu.
  Opis  | Podaj znaczący opis.
  Właściwości  | Dodatkowe właściwości producenta.

 Aby uzyskać informacje na temat poszczególnych obiektów i ich właściwości, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Ten identyfikator musi być zachowywany przez translator w celu zarządzania urządzeniami i synchronizowania metadanych.


**Synchronizacja metadanych**

Translator powinien wysyłać aktualizacje metadanych. Na przykład scenariusze aktualizacji to zmiany nazwy urządzenia lub czujnika oraz zmiany lokalizacji urządzenia lub czujnika.

Translator powinien mieć możliwość dodawania nowych urządzeń lub czujników, które zostały zainstalowane przez użytkownika po utworzeniu linku FarmBeats. Podobnie, jeśli urządzenie lub czujnik został zaktualizowany przez użytkownika, ta sama powinna zostać zaktualizowana w FarmBeats dla odpowiedniego urządzenia lub czujnika. Typowe scenariusze, które wymagają zaktualizowania urządzenia lub czujnika, są zmianami w lokalizacji urządzenia lub dodaniem czujników w węźle.


> [!NOTE]
> Usuwanie nie jest obsługiwane dla metadanych urządzenia lub czujnika.
>
> Aby zaktualizować metadane, należy wywołać/Get/{ID} na urządzeniu lub czujniku, zaktualizować zmienione właściwości, a następnie wykonać/Put/{ID}, tak aby wszystkie właściwości ustawione przez użytkownika nie zostały utracone.

### <a name="add-new-types-and-units"></a>Dodawanie nowych typów i jednostek

FarmBeats obsługuje dodawanie nowych typów i jednostek miary czujnika. Aby uzyskać więcej informacji na temat interfejsu API/ExtendedType, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specyfikacje telemetrii

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

## <a name="send-telemetry-data-to-farmbeats"></a>Wyślij dane telemetryczne do FarmBeats

Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat danych telemetrycznych, zobacz [wysyłanie telemetrii do centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Oto przykładowy kod w języku Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Format komunikatu kanonicznego jest następujący:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
Wszystkie nazwy kluczy w formacie JSON danych telemetrycznych powinny być pisane małymi literami. Przykłady to DeviceID i sensordata.

Na przykład Oto komunikat telemetrii:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooting-and-error-management"></a>Rozwiązywanie problemów i zarządzanie błędami

**Opcja rozwiązywania problemów lub pomoc techniczna**

Jeśli nie można odbierać danych urządzenia lub telemetrii w określonym wystąpieniu usługi FarmBeats, partner urządzenia powinien zapewnić pomoc techniczną i mechanizm rozwiązywania problemów.

**Przechowywanie danych telemetrii**

Dane telemetryczne powinny być również przechowywane przez wstępnie zdefiniowany okres, dzięki czemu mogą być przydatne w debugowaniu lub ponownym wysyłaniu danych telemetrycznych w przypadku wystąpienia błędu lub utraty dane.

**Zarządzanie błędami lub powiadamianie o błędach**

Jeśli wystąpi błąd dotyczący metadanych urządzenia lub czujnika lub przepływu danych danych telemetrycznych w systemie partnerskim urządzenia, należy odebrać powiadomienie. Mechanizm rozwiązywania wszelkich błędów powinien również zostać zaprojektowany i wdrożony.

**Lista kontrolna połączenia**

Producenci urządzeń lub partnerzy mogą użyć poniższej listy kontrolnej, aby upewnić się, że poświadczenia podane przez klienta są dokładne:

   - Sprawdź, czy token dostępu został odebrany przy użyciu podanych poświadczeń.
   - Sprawdź, czy wywołanie interfejsu API powiedzie się z tokenem dostępu, który został odebrany.
   - Sprawdź, czy nawiązano połączenie z klientem EventHub.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu API REST, zobacz [interfejs API REST](references-for-farmbeats.md#rest-api).
