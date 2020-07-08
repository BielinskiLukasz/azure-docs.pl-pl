---
title: Zbieranie niestandardowych danych JSON w Azure Monitor | Microsoft Docs
description: Niestandardowe źródła danych JSON można zbierać do Azure Monitor przy użyciu agenta Log Analytics dla systemu Linux.  Te niestandardowe źródła danych mogą być prostymi skryptami zwracającymi kod JSON, taki jak zwinięcie lub jeden z elementów, które zostały połamane 300 +. W tym artykule opisano konfigurację wymaganą dla tej kolekcji danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77662165"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Zbieranie niestandardowych źródeł danych JSON przy użyciu agenta Log Analytics dla systemu Linux w systemie Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Niestandardowe źródła danych JSON można zbierać do [Azure monitor](data-platform.md) przy użyciu agenta log Analytics dla systemu Linux.  Te niestandardowe źródła danych mogą być prostymi skryptami zwracającymi kod JSON, taki jak [zwinięcie](https://curl.haxx.se/) lub jeden z elementów, które zostały [połamane 300 +](https://www.fluentd.org/plugins/all). W tym artykule opisano konfigurację wymaganą dla tej kolekcji danych.


> [!NOTE]
> Agent Log Analytics dla systemu Linux v 1.1.0-217 + jest wymagany w przypadku niestandardowych danych JSON

## <a name="configuration"></a>Konfigurowanie

### <a name="configure-input-plugin"></a>Konfigurowanie wtyczki wejściowej

Aby zebrać dane JSON w Azure Monitor, należy dodać `oms.api.` do początku taga Fluent w wtyczki wejściowej.

Na przykład poniżej znajduje się oddzielny plik konfiguracji `exec-json.conf` w programie `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  Powoduje to użycie pozostałej wtyczki `exec` do uruchomienia polecenia zwinięcie co 30 sekund.  Dane wyjściowe tego polecenia są zbierane przez wtyczkę wyjściową JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Dodany plik konfiguracji musi `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` mieć zmienioną własność przy użyciu następującego polecenia.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Skonfiguruj wtyczkę wyjściową 
Dodaj następującą konfigurację wtyczki wyjściowej do konfiguracji głównej w programie `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` lub jako oddzielny plik konfiguracji umieszczony w`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Uruchom ponownie agenta Log Analytics dla systemu Linux
Uruchom ponownie usługę Log Analytics Agent dla systemu Linux przy użyciu następującego polecenia.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Dane wyjściowe
Dane będą zbierane w Azure Monitor z typem rekordu `<FLUENTD_TAG>_CL` .

Na przykład tag niestandardowy `tag oms.api.tomcat` w Azure monitor z typem rekordu `tomcat_CL` .  Można pobrać wszystkie rekordy tego typu z następującym zapytaniem dziennika.

    Type=tomcat_CL

Zagnieżdżone źródła danych JSON są obsługiwane, ale są indeksowane na podstawie pola nadrzędnego. Na przykład następujące dane JSON są zwracane z zapytania dziennika jako `tag_s : "[{ "a":"1", "b":"2" }]` .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 
