---
title: Informacje o Azure Security Center dla linii bazowej usługi IoT | Microsoft Docs
description: Dowiedz się więcej na temat koncepcji Azure Security Center dla linii bazowej usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2019
ms.author: mlottner
ms.openlocfilehash: b0f5696b36082375a29e99298395cf16734d5bb4
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937154"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Azure Security Center dla linii bazowej i czeków niestandardowych

W tym artykule wyjaśniono Azure Security Center dla linii bazowej IoT oraz podsumowano wszystkie skojarzone właściwości niestandardowych kontroli linii bazowej.

## <a name="baseline"></a>Punkt odniesienia

Linia bazowa ustala standardowe zachowanie poszczególnych urządzeń i ułatwia ustalenie nietypowego zachowania lub odchylenia od oczekiwanych norm.  

## <a name="baseline-custom-checks"></a>Niestandardowe kontrole linii bazowej

Niestandardowe kontrole linii bazowej określają niestandardową listę kontroli dla każdej linii bazowej urządzenia przy użyciu **sznurka tożsamości modułu** urządzenia. 

## <a name="setting-baseline-properties"></a>Ustawianie właściwości linii bazowej

1. W IoT Hub Znajdź i wybierz urządzenie, które chcesz zmienić.
1. Kliknij urządzenie, a następnie kliknij moduł **azureiotsecurity** .
1. Kliknij pozycję **moduł identyfikacja sznurka**.
1. Przekaż **niestandardowy plik kontroli linii bazowej** na urządzenie.
1. Dodaj właściwości linii bazowej do modułu zabezpieczeń, a następnie kliknij przycisk **Zapisz**.

### <a name="baseline-custom-check-file-example"></a>Przykład podstawowego pliku sprawdzania niestandardowego

Aby skonfigurować niestandardowe kontrole linii bazowej:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFilePath": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Właściwości niestandardowego sprawdzania kontroli linii bazowej

| Nazwa| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|baselineCustomChecksEnabled|Wymagane: prawda |Prawidłowe wartości: **wartość logiczna** |Wartość domyślna: **Fałsz** |Maksymalny przedział czasu przed wysłaniem komunikatów o wysokim priorytecie.|
|baselineCustomChecksFilePath |Wymagane: prawda|Prawidłowe wartości: **String**, **null** |Wartość domyślna: **null** |Pełna ścieżka do podstawowej konfiguracji XML|
|baselineCustomChecksFileHash |Wymagane: prawda|Prawidłowe wartości: **String**, **null** |Wartość domyślna: **null** |`sha256sum` pliku konfiguracyjnego XML. Aby uzyskać dodatkowe informacje, użyj [odwołania sha256sum](https://linux.die.net/man/1/sha256sum) . |

Aby zapoznać się z dodatkowymi przykładami linii bazowej, zobacz [przykład niestandardowego punktu odniesienia-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) i [niestandardowy punkt odniesienia — 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Następne kroki

- Uzyskiwanie dostępu do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Omówienie i eksplorowanie [zaleceń dotyczących zabezpieczeń](concept-recommendations.md)
- Poznawanie i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
