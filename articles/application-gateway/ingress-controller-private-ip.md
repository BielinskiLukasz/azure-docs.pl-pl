---
title: Używanie prywatnego adresu IP do routingu wewnętrznego dla punktu końcowego transferu danych przychodzących
description: Ten artykuł zawiera informacje na temat używania prywatnych adresów IP na potrzeby routingu wewnętrznego, a tym samym udostępniania punktu końcowego transferu danych przychodzących w ramach klastra do pozostałej części sieci wirtualnej.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795491"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Używanie prywatnego adresu IP do routingu wewnętrznego dla punktu końcowego transferu danych przychodzących 

Ta funkcja pozwala uwidocznić punkt końcowy transferu danych przychodzących w ramach `Virtual Network` prywatnego adresu IP.

## <a name="pre-requisites"></a>Wymagania wstępne  
Application Gateway z [konfiguracją prywatnego adresu IP](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Istnieją dwa sposoby konfigurowania kontrolera do korzystania z prywatnego adresu IP na potrzeby ruchu przychodzącego.

## <a name="assign-to-a-particular-ingress"></a>Przypisywanie do określonego ruchu przychodzącego
Aby uwidocznić określony ruch przychodzący przez prywatny adres IP, [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) należy użyć adnotacji w danych wejściowych.

### <a name="usage"></a>Sposób użycia
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

W przypadku bram aplikacji bez prywatnego adresu IP Ingresses z `appgw.ingress.kubernetes.io/use-private-ip: "true"` adnotacją zostanie zignorowane. Zostanie to wskazane w dzienniku zdarzeń przychodzących i AGIC pod.

* Błąd, jak wskazano w zdarzeniu transferu danych przychodzących

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Błąd, jak wskazano w dziennikach AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Przypisz globalnie
W przypadku wymogu jest ograniczenie, że wszystkie Ingresses mają być udostępniane za pośrednictwem prywatnego `appgw.usePrivateIP: true` adresu `helm` IP. w tym celu należy użyć konfiguracji.

### <a name="usage"></a>Sposób użycia
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Dzięki temu kontroler transferu danych przychodzących będzie przefiltrować konfiguracje adresów IP dla prywatnego adresu IP podczas konfigurowania odbiorników frontonu na Application Gateway.
AGIC będzie awaryjnego i ulega awarii `usePrivateIP: true` , jeśli nie zostanie przypisany prywatny adres IP.

> [!NOTE]
> Jednostka SKU Application Gateway v2 wymaga publicznego adresu IP. Jeśli potrzebujesz Application Gateway być prywatnym, Dołącz [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) do podsieci Application Gateway, aby ograniczyć ruch.
