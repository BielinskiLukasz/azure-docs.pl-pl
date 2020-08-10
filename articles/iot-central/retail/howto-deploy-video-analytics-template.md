---
title: Jak wdrożyć szablon aplikacji IoT Central analiza filmów wideo — obiekt i ruch
description: Ten przewodnik zawiera podsumowanie kroków związanych z wdrażaniem aplikacji IoT Central platformy Azure przy użyciu szablonu wideo Analiza obiektów i funkcji wykrywania ruchu.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d2ab93708950743eafdb6cf733273b602cfb5a68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038220"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Jak wdrożyć aplikację IoT Central przy użyciu szablonu wideo Analiza obiektów i wykrywania ruchu

Aby zapoznać się z omówieniem składników aplikacji Key *Analytics — obiekty i wykrywanie ruchu* , zobacz [Architektura aplikacji Analiza wideo dotycząca obiektów i ruchu](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Wykonaj następujące kroki, aby wdrożyć aplikację IoT Central przy użyciu szablonu aplikacji wideo Analytics:

1. Ukończ samouczek [Tworzenie aplikacji do analizy filmów wideo w usłudze Azure IoT Central](tutorial-video-analytics-create-app.md) , aby:
    - Tworzenie konta usługi Azure Media Services.
    - Utwórz aplikację IoT Central przy użyciu szablonu wideo Analiza obiektów i wykrywania ruchu.
    - Skonfiguruj urządzenie bramy w aplikacji IoT Central. Brama umożliwia urządzeniom kamer łączenie się z aplikacją.

1. Ukończ samouczek [Tworzenie wystąpienia IoT Edge dla usługi Video Analytics (maszyna wirtualna z systemem Linux)](tutorial-video-analytics-iot-edge-vm.md) , aby:
    - Utwórz maszynę wirtualną platformy Azure z zainstalowanym środowiskiem uruchomieniowym Azure IoT Edge.-Przygotuj instalację IoT Edge do hostowania modułu wideo Analytics.
    - Podłącz urządzenie IoT Edge do aplikacji IoT Central.

1. Wykonaj czynności [monitorowane i Zarządzaj samouczkiem aplikacji wideo Analytics](tutorial-video-analytics-manage.md) , aby:
    - Dodaj aparaty wykrywania obiektów i ruchu do bramy w aplikacji IoT Central.
    - Rozpocznij przetwarzanie aparatu.
    - Zainstaluj lokalny odtwarzacz multimedialny, aby wyświetlić przechwycone wideo w AMS.
    - Wyświetl przechwycone wideo pokazujące wykryte obiekty.
    - Uporządkowanego.

## <a name="next-steps"></a>Następne kroki

Teraz można zapoznać się z instrukcjami wdrażania i używania szablonu aplikacji wideo Analytics, aby rozpocząć pracę, zobacz [Tworzenie aplikacji wideo Analytics n na platformie Azure IoT Central](tutorial-video-analytics-create-app.md) .
