---
title: Ładowanie aplikacji serwera proxy aplikacji trwa zbyt długo | Microsoft Docs
description: Rozwiązywanie problemów z wydajnością ładowania stron serwer proxy aplikacji usługi Azure AD platformy Azure
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1dc1b14a917786e124775a4505c53f63238a383
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203861"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ładowanie aplikacji serwera proxy aplikacji trwa zbyt długo

Ten artykuł pomaga zrozumieć, dlaczego ładowanie aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure może trwać długo. Wyjaśniono również, co można zrobić, aby rozwiązać ten problem.

## <a name="overview"></a>Omówienie
Mimo że aplikacje działają, mogą wystąpić duże opóźnienia. Może istnieć większa topologia sieci, która pozwala zwiększyć szybkość działania. Aby uzyskać informacje na temat oceny różnych topologii, zapoznaj się z [dokumentem zagadnienia dotyczące sieci](application-proxy-network-topology.md).

Oprócz topologii sieci nie są obecnie dostępne dalsze zalecenia dotyczące dostrajania wydajności. Gdy usługa serwera proxy aplikacji zostanie rozwinięta, może to być centrum danych, które jest fizycznie bliższe. Bliższe zbliżenie może pomóc w opóźnieniu. Listę centrów danych platformy Azure można znaleźć na [stronie test opóźnienia](http://www.azurespeed.com/Azure/Latency). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Opinie dotyczące lokalizacji centrów danych serwera proxy aplikacji 
Mogą istnieć centra danych platformy Azure, które jeszcze nie obejmują serwera proxy aplikacji, ale doprowadziłoby do poprawy opóźnienia. Wyślij lokalizację centrum danych do programu aadapfeedback@microsoft.com . Firma Microsoft korzysta z opinii na temat planów rozszerzania.

Firma Microsoft pracuje nad dodatkowymi możliwościami w celu poprawy opóźnień. Po udostępnieniu tych ulepszeń dokumentacja zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki
[Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
