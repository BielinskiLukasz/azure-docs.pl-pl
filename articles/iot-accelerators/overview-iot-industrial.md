---
title: Omówienie usługi Azure Industrial IoT | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi IoT rzeczy. Wyjaśniono w nim informacje dotyczące połączonej fabryki, łączności fabryki i składników zabezpieczeń w programie IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c5d70e5a8784e88d3f2906d500768735dced1ee0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828129"
---
# <a name="what-is-industrial-iot-iiot"></a>Co to jest przemysł IoT (IIoT)

IIoT jest przemysłem Internet rzeczyowym. IIoT zwiększa efektywność przemysłową dzięki zastosowaniu IoT w branży produkcyjnej. 

## <a name="improve-industrial-efficiencies"></a>Poprawa efektywności przemysłowej

Zwiększ produktywność operacyjną i zyskowność dzięki akceleratorowi rozwiązania połączonej fabryki. Połącz sprzęt i urządzenia przemysłowe — w tym również maszyny, które już pracują w fabryce — i monitoruj je w chmurze. Analizuj dane IoT, aby wyciągać wnioski, które pomogą w zwiększeniu wydajności całej fabryki.

Skracaj czasochłonny proces uzyskiwania dostępu do fabryk fabryki przy użyciu sznurka OPC i skup się na czasie tworzenia rozwiązań IIoT. Usprawnij zarządzanie certyfikatami i integrację zasobów branżowych z magazynem OPC, aby mieć pewność, że łączność z zasobami jest zabezpieczona. Te mikrousługi zapewniają interfejs API podobny do użycia na [platformie Azure przemysłowych IoT](https://github.com/Azure/azure-iiot-opc-ua). Interfejs API usługi zapewnia kontrolę funkcjonalności modułu Edge. 

![Informacje przemysłowe dotyczące usługi IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat usług Azure Industrial IoT, zobacz [repozytorium](https://github.com/Azure/azure-iiot-services)GitHub.
Jeśli nie znasz sposobu działania modułów Azure IoT Edge, Rozpocznij od następujących artykułów:
- [Azure IoT Edge — informacje](../iot-edge/about-iot-edge.md)
- [Moduły Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Połączona fabryka

[Połączona fabryka](../iot-accelerators/iot-accelerators-connected-factory-features.md) to implementacja architektury referencyjnej usługi Azure Industrial IoT firmy Microsoft, którą można dostosować w celu spełnienia określonych wymagań firmy. Pełny kod rozwiązania to "open source" i dostępne w repozytorium z akceleratorem rozwiązania połączonej fabryki. Można go użyć jako punktu wyjścia dla produktu komercyjnego i wdrożyć wstępnie utworzone rozwiązanie w ramach subskrypcji platformy Azure w ciągu kilku minut. 

## <a name="factory-floor-connectivity"></a>Łączność z fabryką

OPC bliźniaczy jest składnikiem IIoT, który automatyzuje odnajdywanie i rejestrowanie urządzeń oraz oferuje zdalne sterowanie urządzeniami przemysłowymi za poorednictwem interfejsów API REST. OPC bliźniaczy, używa Azure IoT Edge i IoT Hub do łączenia chmur i sieci fabryki. OPC bliźniaczy pozwala deweloperom IIoT skupić się na tworzeniu aplikacji IIoT bez konieczności bezpiecznego uzyskiwania dostępu do maszyn lokalnych.

## <a name="security"></a>Bezpieczeństwo

Magazyn OPC to implementacja serwera wykrywania globalnego OPC UA (GDS), który umożliwia konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatu dla serwera OPC UA i aplikacji klienckich w chmurze. Magazyn OPC upraszcza implementację i konserwację bezpiecznej łączności zasobów w przestrzeni przemysłowej. Dzięki automatyzowaniu zarządzania certyfikatami magazyn OPC zwalnia operatorów fabryki z ręcznych i złożonych procesów skojarzonych z łącznością i zarządzaniem certyfikatami.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już masz wprowadzenie do przemysłu IoT i jego składników, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Co to jest OPC bliźniaczy](overview-opc-twin.md)
