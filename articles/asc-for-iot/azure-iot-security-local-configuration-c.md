---
title: Konfiguracja lokalna agenta zabezpieczeń (C)
description: Dowiedz się więcej na temat Azure Security Center dla konfiguracji lokalnych agenta dla języka C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 842a69c27ceb0d56df5a7b49eb9922b88d8d4b32
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206941"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Omówienie pliku LocalConfiguration.json — agent oparty na języku C

Azure Security Center usługi IoT Security Agent używa konfiguracji z lokalnego pliku konfiguracji.
Agent zabezpieczeń odczytuje konfigurację raz, podczas uruchamiania agenta.
Konfiguracja znaleziona w lokalnym pliku konfiguracji zawiera konfigurację uwierzytelniania i inne konfiguracje powiązane z agentem.
Plik zawiera konfiguracje "pary klucz-wartość" w notacji JSON, a konfiguracje są wypełniane podczas instalowania agenta.

Domyślnie plik znajduje się w lokalizacji:/var/ASCIoTAgent/LocalConfiguration.jsna

Zmiany w pliku konfiguracji są wykonywane po ponownym uruchomieniu agenta.

## <a name="security-agent-configurations-for-c"></a>Konfiguracje agenta zabezpieczeń dla języka C

| Nazwa konfiguracji | Możliwe wartości | Szczegóły |
|:-----------|:---------------|:--------|
| Identyfikator agenta | GUID | Unikatowy identyfikator agenta |
| TriggerdEventsInterval | Ciąg ISO8601 | Interwał harmonogramu dla zbierania zdarzeń wyzwalanych |
| Parametru | Ciąg ISO8601 | Czas przed upłynięciem limitu czasu połączenia z usługą IoThub |
| Uwierzytelnianie | JsonObject | Konfiguracja uwierzytelniania. Ten obiekt zawiera wszystkie informacje, które są związane z uwierzytelnianiem w odniesieniu do IoTHub |
| Tożsamość | "DPS", "SecurityModule", "urządzenie" | Tożsamość uwierzytelniania — DPS jeśli następuje uwierzytelnianie za pośrednictwem platformy DPS, SecurityModule w przypadku uwierzytelniania za pomocą poświadczeń modułu zabezpieczeń lub urządzenia, jeśli uwierzytelnianie jest nawiązywane przy użyciu poświadczeń urządzenia |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | klucz tajny użytkownika do uwierzytelniania — wybierz opcję SasToken, jeśli klucz tajny jest kluczem symetrycznym, wybierz opcję certyfikat z podpisem własnym, jeśli klucz tajny jest certyfikatem z podpisem własnym  |
| Parametr | Ścieżka do pliku (ciąg) | Ścieżka do pliku, który zawiera klucz tajny uwierzytelniania |
| HostName | ciąg | Nazwa hosta usługi Azure IoT Hub. zwykle <my-Hub>. azure-devices.net |
| DeviceId | ciąg | Identyfikator urządzenia (zgodnie z zarejestrowaniem w usłudze Azure IoT Hub) |
| DOKUMENTY | JsonObject | Konfiguracje związane z usługą DPS |
| IDScope | ciąg | Zakres identyfikatorów DPS |
| Identyfikator rejestracji | ciąg  | Identyfikator rejestracji urządzenia DPS |
| Rejestrowanie | JsonObject | Konfiguracje związane z rejestratorem agentów |
| SystemLoggerMinimumSeverity | 0 <= liczba <= 4 | komunikaty dziennika równe i powyżej ważności będą rejestrowane w/var/log/syslog (0 to najniższa ważność) |
| DiagnosticEventMinimumSeverity | 0 <= liczba <= 4 | komunikaty dziennika równe i powyżej ważności będą wysyłane jako zdarzenia diagnostyczne (0 to najniższa ważność) |

## <a name="security-agent-configurations-code-example"></a>Przykład kodu konfiguracji agenta zabezpieczeń

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o [architekturze](architecture.md) Azure Security Center dla usługi IoT
- Włączanie Azure Security Center dla [usługi](quickstart-onboard-iot-hub.md) IoT
- Przeczytaj Azure Security Center dla usługi IoT [— często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak uzyskać dostęp do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- Omówienie [zaleceń](concept-recommendations.md)
- Informacje o [alertach](concept-security-alerts.md) zabezpieczeń
