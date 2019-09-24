---
title: Korelacja inteligentnego alertu w chmurze w Azure Security Center (zdarzenia) | Microsoft Docs
description: W tym temacie opisano sposób, w jaki funkcja Fusion używa korelacji alertów inteligentnych w chmurze do generowania zdarzeń zabezpieczeń w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 4c2f084fe03271b29a12aa6906f5e36612d50674
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202689"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelacja inteligentnego alertu w chmurze w Azure Security Center (zdarzenia)

Azure Security Center stale analizuje obciążenia chmury hybrydowej dzięki zaawansowanej analizie i analizie zagrożeń, aby ostrzec o złośliwych działaniach.

Rośnie zasięg zagrożeń. Konieczność wykrywania nawet niewielkich kompromisów jest istotna i może być trudne dla analityków zabezpieczeń, aby klasyfikacja różne alerty i identyfikować faktyczny atak. Security Center ułatwia analitykom radzenie sobie z tym zmęczeniem alertów. Pomaga w diagnozowaniu ataków w miarę ich występowania, przez skorelowanie różnych alertów i słabych sygnałów w przypadku zdarzeń zabezpieczeń.

Analiza Fusion jest technologią i zapleczem analitycznym, która umożliwia Security Center incydenty, co umożliwia działowi IT skorelowanie różnych alertów i sygnałów kontekstowych. Fusion przegląda różne sygnały raportowane w ramach subskrypcji dla zasobów. Funkcja Fusion odnajduje wzorce, które ujawniają postęp ataku lub sygnalizują się z udostępnionymi informacjami kontekstowymi, wskazując, że należy użyć ujednoliconej procedury odpowiedzi.

Funkcja Fusion Analytics łączy informacje o domenie zabezpieczeń z AI, aby analizować alerty, odkrywając nowe wzorce ataku w miarę ich występowania. 

Security Center korzysta z macierzy ataków MITRE do kojarzenia alertów z ich zamiarem, pomagając w prace prowadzone informacji o domenie zabezpieczeń. Ponadto, korzystając z informacji zebranych dla każdego kroku ataku, Security Center może wyrównać działanie, które wydaje się być czynnościami ataku, ale w rzeczywistości nie jest.  

Ponieważ ataki często występują między różnymi dzierżawcami, Security Center mogą łączyć algorytmy AI, aby analizować sekwencje ataków, które są zgłaszane w poszczególnych subskrypcjach. Ta technika identyfikuje sekwencje ataków jako nieznane wzorce alertów, a nie tylko incydentowo powiązane ze sobą.

Podczas badania zdarzenia analitykowie często potrzebują dodatkowego kontekstu, aby dotrzeć do Verdict o charakterze zagrożenia i sposobach ich rozwiązywania. Na przykład nawet w przypadku wykrycia nietypowej sieci, bez zrozumienia, co się dzieje w sieci lub w odniesieniu do zasobu, trudno jest zrozumieć, jakie działania należy podjąć dalej. Aby pomóc, zdarzenie zabezpieczeń może obejmować artefakty, powiązane zdarzenia i informacje. Dodatkowe informacje dostępne dla zdarzeń związanych z zabezpieczeniami różnią się w zależności od typu wykrytego zagrożenia i konfiguracji środowiska. 

![Zrzut ekranu przedstawiający raport wykrytych zdarzeń zabezpieczeń](./media/security-center-alerts-cloud-smart/security-incident.png)

Aby lepiej zrozumieć zdarzenia związane z zabezpieczeniami, zobacz [jak obsłużyć zdarzenia zabezpieczeń w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

