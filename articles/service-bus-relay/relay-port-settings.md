---
title: Ustawienia portów w usłudze Azure Relay | Dokumentacja firmy Microsoft
description: Szczegółowe informacje o wartości portów usługi Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 3ef08cfc94a029f97250578e9b0366a18770c809
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696746"
---
# <a name="azure-relay-port-settings"></a>Ustawienia portów w usłudze Azure Relay

W poniższej tabeli opisano wymaganej konfiguracji dla wartości portów dla usługi Azure Relay.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe używa funkcji WebSockets na porcie 443 przy użyciu protokołu SSL jako podstawowy mechanizm transportu, która używa **HTTPS** tylko. 

## <a name="wcf-relays"></a>Przekaźniki WCF
  
|Powiązanie|Zabezpieczenia transportu|Port|  
|-------------|------------------------|----------|  
|[Klasa BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Yes|HTTPS| 
|" |Nie|HTTP|  
|[Klasa BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (usługa/klienta)|Albo|5671/9352/HTTP (9352/9353, jeśli za pomocą hybrydowych)|  
|[Klasa NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[Klasa NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Yes|HTTPS|  
|" |Nie|HTTP|  
|[Klasa WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Yes|HTTPS|  
|" |Nie|HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Albo|9351/HTTP|

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Azure Relay, skorzystaj z następujących linków:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)