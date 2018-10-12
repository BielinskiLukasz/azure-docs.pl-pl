---
title: Analizowanie wzorców użycia usługi Azure CDN | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne rodzaje raportów analizy dostępnych produktów Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 45b3698dd77bda815218b43405d64819c3e4789f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091270"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizowanie wzorców użycia usługi Azure CDN

Po włączeniu usługi CDN dla aplikacji można monitorować użycie sieci CDN, Sprawdź kondycję dostarczania i rozwiązywania potencjalnych problemów. Usługa Azure CDN udostępnia te możliwości w następujący sposób: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Podstawowa analiza za pośrednictwem dzienniki diagnostyczne platformy Azure

Core analytics jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. Dzienniki diagnostyczne platformy Azure umożliwiają core analytics mają zostać wyeksportowane do usługi Azure storage, usługa event hubs lub usługi Azure Log Analytics. Usługa Azure Log Analytics oferuje rozwiązanie za pomocą wykresów, które są konfigurowanych przez użytkownika i możliwe do dostosowania. Aby uzyskać więcej informacji na temat dzienniki diagnostyczne platformy Azure, zobacz [dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Raporty podstawowe z usługi Verizon

Jako użytkownik usługi Azure CDN przy użyciu **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu, Raporty podstawowe z usługi Verizon można wyświetlić w portalu dodatkowym Verizon. Raporty podstawowe z usługi Verizon jest dostępny za pośrednictwem **Zarządzaj** opcji w witrynie Azure portal i oferuje różne wykresy i widoki. Aby uzyskać więcej informacji, zobacz [Raporty podstawowe z usługi Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Raporty niestandardowe z usługi Verizon

Jako użytkownik usługi Azure CDN przy użyciu **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu, raportów niestandardowych Verizon można wyświetlić w portalu dodatkowym Verizon. Verizon raportów niestandardowych jest dostępny za pośrednictwem **Zarządzaj** opcji w witrynie Azure portal. Pokazuje strony raportów niestandardowych Verizon, liczba trafień lub danych przesłanych dla każdej krawędzi CName należące do profilu usługi sieć CDN systemu Azure. Dane mogą być grupowane według stanu odpowiedzi HTTP kodu lub pamięci podręcznej w dowolnym okresie. Aby uzyskać więcej informacji, zobacz [raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Raporty usługi Azure CDN Premium from Verizon

Za pomocą **Azure CDN Premium from Verizon**, można także przejść do następujących raportów:
   * [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
   * [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)
   * [Wydajność węzła brzegowego](cdn-edge-performance.md)

