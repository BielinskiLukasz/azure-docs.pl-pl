---
title: Safelist adresy URL Azure Portal na zaporze lub serwerze proxy
description: Dodaj te adresy URL do obejścia serwera proxy, aby komunikować się z Azure Portalą i jej usługami
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255053"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist adresy URL Azure Portal na zaporze lub serwerze proxy

Lokalne urządzenia zabezpieczeń można skonfigurować w celu obejścia ograniczeń zabezpieczeń dla adresów URL Azure Portal. Ta konfiguracja może zwiększyć wydajność i łączność między siecią lokalną lub rozległą a chmurą platformy Azure.

Administratorzy sieci często wdrażają serwery proxy, zapory lub inne urządzenia. Te urządzenia pomagają zabezpieczyć i zapewnić kontrolę nad tym, jak użytkownicy uzyskują dostęp do Internetu. Reguły służące do ochrony użytkowników mogą czasami blokować lub spowalniać słuszny ruch internetowy związany z firmą. Ten ruch obejmuje komunikację między ty i platformą Azure. Aby zoptymalizować łączność między siecią a Azure Portal i jej usługami, zalecamy dodanie Azure Portal adresów URL do safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Adresy URL Azure Portal dla obejścia serwera proxy

Punkty końcowe URL Safelist dla Azure Portal są specyficzne dla chmury platformy Azure, w której wdrożono organizację. Aby zezwolić na ruch sieciowy do tych punktów końcowych w celu obejścia ograniczeń, wybierz chmurę. Następnie Dodaj listę adresów URL do serwera proxy lub zapory.

#### <a name="public-cloud"></a>[Chmura publiczna](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Chmura dla instytucji rządowych USA](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Chmura dla instytucji rządowych](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Ruch do tych punktów końcowych używa standardowych portów TCP dla protokołu HTTP (80) i HTTPS (443).
>
>
