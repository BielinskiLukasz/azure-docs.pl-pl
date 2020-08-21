---
title: Rozwiązywanie problemów z połączeniami urządzeń z platformą Azure IoT Central | Microsoft Docs
description: Rozwiązywanie problemów z tym, dlaczego nie widzisz danych z urządzeń w IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 98d7566d5e9339ea2ac5d81d91f1d9f8ace5b0f4
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719668"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Rozwiązywanie problemów z tym, dlaczego dane z urządzeń nie są wyświetlane na platformie Azure IoT Central

Ten dokument ułatwia deweloperom urządzeń Dowiedz się, dlaczego dane wysyłane przez urządzenia do IoT Central mogą nie być wyświetlane w aplikacji.

Istnieją dwa główne obszary do zbadania:

- Problemy z łącznością urządzeń
  - Problemy z uwierzytelnianiem, takie jak urządzenie, ma nieprawidłowe poświadczenia
  - Problemy z łącznością sieciową
  - Urządzenie nie jest zatwierdzone lub zablokowane
- Problemy z kształtem ładunku urządzenia

Ten przewodnik rozwiązywania problemów koncentruje się na problemach z łącznością urządzeń i rozwiązaniu problemów z kształtem ładunku

## <a name="device-connectivity-issues"></a>Problemy z łącznością urządzeń

W tej sekcji można określić, czy dane docierają do IoT Central.

Jeśli jeszcze tego nie zrobiono, zainstaluj `az cli` Narzędzie i `azure-iot` rozszerzenie.

Aby dowiedzieć się, jak zainstalować `az cli` program, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby [zainstalować](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) `azure-iot` rozszerzenie, uruchom następujące polecenie:

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> Może zostać wyświetlony monit o zainstalowanie `uamqp` biblioteki przy pierwszym uruchomieniu polecenia rozszerzenia.

Po zainstalowaniu `azure-iot` rozszerzenia Uruchom urządzenie, aby sprawdzić, czy wysyłane przez niego wiadomości są w sposób IoT Central.

Użyj następujących poleceń, aby zalogować się do subskrypcji, w której masz aplikację IoT Central:

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

Aby monitorować dane telemetryczne wysyłane przez urządzenie, użyj następującego polecenia:

```cmd/bash
az iot central app monitor-events -n <app-id> -d <device-name>
```

Jeśli urządzenie zostało pomyślnie podłączone do IoT Central, zobaczysz dane wyjściowe podobne do następujących:

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Aby monitorować aktualizacje właściwości, urządzenie jest wymieniane z IoT Central, użyj następującego polecenia w wersji zapoznawczej:

```cmd/bash
az iot central app monitor-properties -n <app-id> -d <device-name>
```

Jeśli urządzenie pomyślnie wyśle aktualizacje właściwości, zobaczysz dane wyjściowe podobne do następujących:

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

### <a name="interpreting-terminal-output"></a>Interpretowanie danych wyjściowych terminalu

Jeśli zobaczysz, że dane są wyświetlane w terminalu, dane są tak samo, jak w przypadku aplikacji IoT Central.

Jeśli nie widzisz żadnych danych po kilku minutach, spróbuj nacisnąć klawisz `Enter` lub `return` na klawiaturze, w przypadku gdy dane wyjściowe są zablokowane.

Jeśli nadal nie widzisz żadnych danych w terminalu, prawdopodobnie urządzenie ma problemy z łącznością sieciową lub nie wysyła prawidłowo danych do IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Sprawdź stan aprowizacji urządzenia

Jeśli dane nie są wyświetlane na monitorze, sprawdź stan aprowizacji urządzenia, uruchamiając następujące polecenie:

```cmd/bash
az iot central app device registration-info -n <app-id> -d <device-id>
```

Poniższe dane wyjściowe przedstawiają przykład urządzenia, które jest blokowane przed połączeniem:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Stan aprowizacji urządzeń | Opis | Możliwe środki zaradcze |
| - | - | - |
| Aprowizowane | Brak natychmiastowego rozpoznawania problemu. | Brak |
| Zarejestrowany | Urządzenie nie zostało jeszcze podłączone do IoT Central. | Sprawdź dzienniki urządzeń pod kątem problemów z łącznością. |
| Zablokowane | Urządzenie ma zablokowany dostęp do IoT Central. | Urządzenie ma zablokowany dostęp do aplikacji IoT Central. Odblokuj urządzenie w IoT Central i ponów próbę. Aby dowiedzieć się więcej, zobacz [blokowanie urządzeń](concepts-get-connected.md#device-status-values). |
| Niezatwierdzonych | Urządzenie nie jest zatwierdzone. | Urządzenie nie jest zatwierdzone do nawiązania połączenia z aplikacją IoT Central. Zatwierdź urządzenie w IoT Central i ponów próbę. Aby dowiedzieć się więcej, zobacz [zatwierdzanie urządzeń](concepts-get-connected.md#connect-without-registering-devices) |
| Nieskojarzone | Urządzenie nie jest skojarzone z szablonem urządzenia. | Skojarz urządzenie z szablonem urządzenia, aby IoT Central wie, jak analizować dane. |

Dowiedz się więcej na temat [kodów stanu urządzeń](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Kody błędów

Jeśli nadal nie można zdiagnozować, dlaczego dane nie są wyświetlane w programie `monitor-events` , następnym krokiem jest wyszukiwanie kodów błędów raportowanych przez urządzenie.

Rozpocznij sesję debugowania na urządzeniu lub Zbierz dzienniki z urządzenia. Sprawdź kody błędów, które są zgłaszane przez urządzenie.

W poniższych tabelach przedstawiono typowe kody błędów i możliwe akcje, które należy wyeliminować.

Jeśli widzisz problemy związane z przepływem uwierzytelniania:

| Kod błędu | Opis | Możliwe środki zaradcze |
| - | - | - |
| 400 | Treść żądania jest nieprawidłowa. Na przykład nie można jej przeanalizować lub nie można sprawdzić poprawności obiektu. | Upewnij się, że wysyłasz poprawną treść żądania w ramach przepływu zaświadczania lub użyj zestawu SDK urządzeń. |
| 401 | Nie można zweryfikować tokenu autoryzacji. Na przykład wygasła lub nie dotyczy identyfikatora URI żądania. Ten kod błędu jest również zwracany do urządzeń w ramach przepływu zaświadczania modułu TPM. | Upewnij się, że urządzenie ma poprawne poświadczenia. |
| 404 | Wystąpienie usługi Device Provisioning lub zasób, taki jak rejestracja, nie istnieje. | Wykorzystaj [bilet z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | `ETag`W żądaniu nie jest zgodny z `ETag` istniejącym zasobem, zgodnie z RFC7232. | Wykorzystaj [bilet z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Operacje są ograniczane przez usługę. Aby uzyskać szczegółowe limity usługi, zobacz [limity IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | Zmniejsz częstotliwość komunikatów, Podziel odpowiedzialności między więcej urządzeń. |
| 500 | Wystąpił błąd wewnętrzny. | Zapoznaj się [z biletem z obsługą klienta](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , aby sprawdzić, czy może Ci pomóc. |

## <a name="payload-shape-issues"></a>Problemy z kształtem ładunku

Po ustaleniu, że urządzenie wysyła dane do IoT Central, następnym krokiem jest upewnienie się, że urządzenie wysyła dane w prawidłowym formacie.

Istnieją dwie główne kategorie typowych problemów, które powodują, że dane urządzenia nie są wyświetlane w IoT Central:

- Niezgodność szablonu urządzenia z danymi urządzenia:
  - Niezgodność nazw, takich jak literówki lub problemy z dopasowywaniem wielkości liter.
  - Właściwości niemodelowane, w których schemat nie jest zdefiniowany w szablonie urządzenia.
  - Niezgodność schematu, taka jak typ zdefiniowany w szablonie `boolean` , ale dane są ciągami.
  - Ta sama nazwa telemetrii jest zdefiniowana w wielu interfejsach, ale urządzenie nie jest zgodne z Plug and Play IoT.
- Kształt danych jest nieprawidłowy w formacie JSON. Aby dowiedzieć się więcej, zobacz dane [telemetryczne, właściwości i poleceń](concepts-telemetry-properties-commands.md).

Aby wykryć, których kategorii dotyczy problem, uruchom najbardziej odpowiednie polecenie dla danego scenariusza:

- Aby sprawdzić poprawność telemetrii, użyj polecenia podglądu:

    ```cmd/bash
    az iot central app validate-messages -n <app-id> -d <device-name>
    ```

- Aby sprawdzić poprawność aktualizacji właściwości, użyj polecenia podglądu

    ```cmd/bash
    az iot central app validate-properties -n <app-id> -d <device-name>
    ```

- Jeśli wolisz używać graficznego interfejsu użytkownika, użyj widoku **nieprzetworzonych danych** IoT Central, aby sprawdzić, czy coś nie jest modelowane. Widok **nieprzetworzonych danych** nie wykrywa, czy urządzenie wysyła źle sformułowany kod JSON.

Może zostać wyświetlony monit o zainstalowanie `uamqp` biblioteki podczas pierwszego uruchomienia `validate` polecenia.

Następujące dane wyjściowe pokazują przykład błędu i komunikaty ostrzegawcze z polecenia Validate:

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Zrzut ekranu przedstawiający widok danych nieprzetworzonych":::

### <a name="interpreting-terminal-output"></a>Interpretowanie danych wyjściowych terminalu

Po wykryciu problemu może być konieczne zaktualizowanie oprogramowania układowego urządzenia lub utworzenie nowego szablonu urządzenia, który modeluje poprzednio niemodelowane dane.

W przypadku wybrania opcji utworzenia nowego szablonu, który poprawnie modeluje dane, Migruj urządzenia ze starego szablonu do nowego szablonu. Aby dowiedzieć się więcej, zobacz [Zarządzanie urządzeniami w aplikacji IoT Central platformy Azure](howto-manage-devices.md).

## <a name="next-steps"></a>Następne kroki

Jeśli nie możesz rozwiązać problemu i rozwiązać ten problem za pomocą tego przewodnika, Otwórz bilet pomocy technicznej. Klienci platformy Azure mogą tworzyć żądania pomocy technicznej i zarządzać nimi w Azure Portal:

- [Witryna Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
- [Azure Portal dla instytucji rządowych Stany Zjednoczone](https://portal.azure.us/)

Aby uzyskać więcej informacji, zobacz temat pomoc [techniczna i opcje pomocy usługi Azure IoT](../../iot-fundamentals/iot-support-help.md).
