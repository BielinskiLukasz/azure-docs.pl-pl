---
title: Uzyskiwanie dostępu do danych za pomocą Azure Security Center dla usługi IoT | Microsoft Docs
description: Dowiedz się więcej na temat sposobu uzyskiwania dostępu do alertów zabezpieczeń i danych rekomendacji w przypadku korzystania z Azure Security Center dla usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597184"
---
# <a name="access-your-security-data"></a>Dostęp do danych zabezpieczeń 

Azure Security Center dla usługi IoT przechowuje alerty zabezpieczeń, zalecenia i pierwotne dane zabezpieczeń (Jeśli użytkownik zdecyduje się je zapisać) w obszarze roboczym Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Aby skonfigurować, który obszar roboczy Log Analytics jest używany:

1. Otwórz Centrum IoT Hub.
1. Kliknij blok **Przegląd** w sekcji **zabezpieczenia** .
2. Kliknij pozycję **Ustawienia**, a następnie Zmień konfigurację obszaru roboczego log Analytics.

Aby uzyskać dostęp do alertów i rekomendacji w obszarze roboczym Log Analytics po zakończeniu konfiguracji:

1. Wybierz Alert lub zalecenie w Azure Security Center dla IoT. 
2. Kliknij pozycję **dalsze badanie**, a następnie kliknij, **Aby zobaczyć, które urządzenia mają ten alert kliknij tutaj i Wyświetl kolumnę DeviceID**.

Aby uzyskać szczegółowe informacje na temat wykonywania zapytań dotyczących danych z Log Analytics, zobacz [Rozpoczynanie pracy z zapytaniami w log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings. SecurityAlert_ w obszarze roboczym log Analytics skonfigurowanym dla Azure Security Center rozwiązania IoT.

Udostępniamy kilka przydatnych zapytań, które ułatwiają rozpoczęcie eksplorowania alertów zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Opis                                             | Właściwości ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018 R-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | Atak typu "odprowadzenie" zakończył się pomyślnie           | Atak z przeprowadzeniem pełnego wymuszenia na urządzeniu zakończył się pomyślnie        |    {"Pełny adres źródłowy": "[\"10.165.12.18:\"]", "nazwy użytkowników": "[\"\"]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | Wykryto Pomyślne logowanie lokalne na urządzeniu     | {"Adres zdalny": "?", "Port zdalny": "", "Port lokalny": "", "Shell logowania": "/bin/Su", "Identyfikator procesu logowania": "28207", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | Nieudana próba logowania lokalnego na urządzeniu  | Wykryto nieudaną próbę zalogowania lokalnego do urządzenia |  {"Adres zdalny": "?", "Port zdalny": "", "Port lokalny": "", "Shell logowania": "/bin/Su", "Identyfikator procesu logowania": "22644", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Podsumowanie urządzenia

Pobierz liczbę unikatowych alertów zabezpieczeń wykrytych w ostatnim tygodniu, pogrupowanych według IoT Hub, urządzenia, ważności alertu, typu alertu.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | Atak typu "odprowadzenie" zakończył się pomyślnie           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni        | Nieudana próba logowania lokalnego na urządzeniu  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni        | Miner monety kryptograficznej                     | 4   |

### <a name="iot-hub-summary"></a>Podsumowanie Centrum IoT

Wybierz wiele różnych urządzeń z alertami w ciągu ostatniego tygodnia według IoT Hub, ważności alertu, typu alertu

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Atak typu "odprowadzenie" zakończył się pomyślnie           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Średni        | Nieudana próba logowania lokalnego na urządzeniu  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Średni        | Miner monety kryptograficznej                     | 1          |

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Zalecenia dotyczące zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings. SecurityRecommendation_ w obszarze roboczym log Analytics skonfigurowanym dla Azure Security Center rozwiązania IoT.

Udostępniamy kilka przydatnych zapytań, które ułatwiają rozpoczęcie eksplorowania zaleceń dotyczących zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Opis | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni | Aktywne | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Znaleziono regułę zapory, która zawiera liberalny wzorzec dla szerokiego zakresu adresów IP lub portów | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni | Aktywne | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Znaleziono regułę zapory, która zawiera liberalny wzorzec dla szerokiego zakresu adresów IP lub portów | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Podsumowanie urządzenia

Uzyskaj liczbę odrębnych zaleceń dotyczących zabezpieczeń, pogrupowanych według IoT Hub, urządzeń, ważności i rodzaju zalecenia.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Wysoka          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Średni        | 4   |


## <a name="next-steps"></a>Następne kroki

- Przeczytaj Azure Security Center dla usługi IoT [— Omówienie](overview.md)
- Dowiedz się więcej o Azure Security Center [architektury](architecture.md) IoT
- Poznawanie i eksplorowanie [Azure Security Center alertów IoT](concept-security-alerts.md)
- Poznaj i Eksploruj [Azure Security Center na potrzeby rekomendacji IoT](concept-recommendations.md)
