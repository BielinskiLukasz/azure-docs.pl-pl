---
title: Pobierz metryki usługi Azure Load Balancer przy użyciu interfejsu API REST | Dokumentacja firmy Microsoft
description: Interfejsy API REST platformy Azure umożliwiają zbieranie metryk kondycji i użycia dla modułu równoważenia obciążenia dla określonego zakresu czasu i daty.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 1fac461c3af4ea0a2e1f2257256969c47bc3d134
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650006"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Pobierz metryki użycia modułu równoważenia obciążenia przy użyciu interfejsu API REST

Niniejszy instruktaż pokazuje, jak zbierać liczbę bajtów przetworzonych przez [Balancer w warstwie standardowa](/azure/load-balancer/load-balancer-standard-overview) interwał czasu przy użyciu [interfejsu API REST usługi Azure](/rest/api/azure/).

Dokumentacja pełną dokumentację i więcej przykładów dla interfejsu API REST są dostępne w [dokumentacja usługi Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Żądanie kompilacji

Użyj następującego żądania GET do zbierania [metryki ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#a-name--multidimensionalmetricsamulti-dimensional-metrics) z standardowego modułu równoważenia obciążenia. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Nagłówki żądań

Wymagane są następujące nagłówki: 

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Typ zawartości:*|Wymagany. Ustaw `application/json`.|  
|*Autoryzacja:*|Wymagany. Ustawić prawidłową `Bearer` [token dostępu](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identyfikatora URI

| Name (Nazwa) | Opis |
| :--- | :---------- |
| subscriptionId | Identyfikator subskrypcji, która identyfikuje subskrypcji platformy Azure. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). |
| resourceGroupName | Nazwa grupy zasobów, która zawiera zasób. Tę wartość można uzyskać z interfejsu API usługi Azure Resource Manager, interfejsu wiersza polecenia lub portalu. |
| loadBalancerName | Nazwa usługi Azure Load Balancer. |
| metricnames | Rozdzielana przecinkami lista prawidłowe [metryk modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-standard-diagnostics). |
| wersja interfejsu API | Wersja interfejsu API do użycia dla żądania.<br /><br /> W tym dokumencie opisano parametru api-version `2018-01-01`zawarte w powyższym adresie URL.  |
| Przedział czasu | Zakres czasu kwerendy. Jest to ciąg o następującym formacie `startDateTime_ISO/endDateTime_ISO`. Ten parametr opcjonalny jest ustawiony do zwrócenia dnia, przez które dane w przykładzie. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Treść żądania

Nie treści żądania jest wymagana dla tej operacji.

## <a name="handle-the-response"></a>Dojście do odpowiedzi

Pomyślnie zwrócona lista wartości metryk zwracany jest kod stanu 200. Pełną listę kodów błędów jest dostępna w [dokumentację referencyjną](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Przykładowa odpowiedź 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```