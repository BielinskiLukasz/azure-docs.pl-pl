---
title: Wprowadzenie do usługi Azure IoT Hub | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure IoT Hub. Ta usługa IoT powstała z myślą o skalowalnym pozyskiwaniu danych, zarządzaniu urządzeniami i bezpieczeństwie.
author: nberdy
ms.author: nberdy
ms.date: 07/04/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
manager: briz
ms.openlocfilehash: 6dadd746bccd028a2b81a980d99ab47ec9e6e2a3
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726741"
---
# <a name="what-is-azure-iot-hub"></a>Czym jest usługa Azure IoT Hub?

Usługa IoT Hub jest usługą zarządzaną, hostowaną w chmurze, która działa jako centrum komunikatów w dwukierunkowej komunikacji między aplikacją IoT a zarządzanymi przez nią urządzeniami. Za pomocą usługi Azure IoT Hub możesz tworzyć rozwiązania IoT zapewniające niezawodną i bezpieczną komunikację między milionami urządzeń IoT a hostowanym w chmurze zapleczem rozwiązania. Z usługą IoT Hub możesz połączyć praktycznie każde urządzenie.

Usługa IoT Hub obsługuje komunikację zarówno z urządzenia do chmury, jak i z chmury do urządzenia. Usługa IoT Hub obsługuje wiele wzorców przesyłania komunikatów takich jak telemetria z urządzenia do chmury, przekazywanie plików z urządzeń oraz metody żądanie-odpowiedź umożliwiające sterowanie urządzeniem z chmury. Funkcja monitorowania w usłudze IoT Hub ułatwia utrzymywanie rozwiązania w dobrej kondycji przez śledzenie zdarzeń takich jak tworzenie urządzeń, awarie urządzeń i połączenia urządzeń.

Możliwości usługi IoT Hub ułatwiają tworzenie skalowalnych, kompleksowych rozwiązań IoT takich jak zarządzanie sprzętem przemysłowym używanym w produkcji, śledzenie ważnych zasobów w opiece zdrowotnej czy monitorowanie użycia budynków biurowych.

## <a name="scale-your-solution"></a>Skalowanie rozwiązania

Usługa IoT Hub może zarządzać milionami równocześnie połączonych urządzeń i obsługiwać miliony zdarzeń na sekundę w celu obsługi obciążeń IoT. Usługa IoT jest dostępna w kilku warstwach, aby jak najlepiej spełniała Twoje wymagania dotyczące skalowalności. Dowiedz się więcej, sprawdzając [stronę cennika](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Zabezpieczanie komunikacji

Usługa IoT Hub zapewnia bezpieczny kanał komunikacyjny, za pomocą którego urządzenia mogą przesyłać dane.

* Uwierzytelnianie poszczególnych urządzeń umożliwia każdemu urządzeniu nawiązanie bezpiecznego połączenia z usługą IoT Hub oraz zapewnia bezpieczne zarządzanie urządzeniami.

* Masz pełną kontrolę nad dostępem do urządzenia i możesz kontrolować połączenia na poziomie poszczególnych urządzeń.

* [Usługa IoT Hub Device Provisioning](https://docs.microsoft.com/azure/iot-dps/) automatycznie aprowizuje urządzenia w odpowiednim centrum IoT podczas pierwszego uruchomienia urządzenia.

* Różne typy uwierzytelniania obsługują szeroką gamę możliwości urządzeń:

  * Uwierzytelnianie oparte na tokenie sygnatury dostępu współdzielonego umożliwiające szybkie rozpoczęcie pracy z rozwiązaniem IoT.

  * Uwierzytelnianie indywidualne certyfikatem X.509 zapewniające bezpieczne i oparte na standardach uwierzytelnianie.

  * Uwierzytelnianie urzędem certyfikacji X.509 zapewniające prostą i opartą na standardach rejestrację.

## <a name="route-device-data"></a>Przesyłanie danych urządzeń

Wbudowana funkcja routingu komunikatów zapewnia elastyczność konfigurowania automatycznego rozdysponowywania komunikatów opartego na regułach:

* Za pomocą routingu komunikatów możesz kontrolować, dokąd Twoje centrum wysyła dane telemetryczne urządzenia.

* Nie ma żadnych dodatkowych opłat za routing komunikatów do wielu punktów końcowych.

* Reguły routingu bez kodu zastępują kod dyspozytora komunikatów niestandardowych.

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Usługę IoT Hub możesz zintegrować z innymi usługami platformy Azure, aby tworzyć kompletne i kompleksowe rozwiązania. Możesz na przykład użyć następujących usług:

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/), aby pozwolić Twojej firmie szybko reagować na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób.

* [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/), aby zautomatyzować procesy biznesowe.

* [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/), aby dodać uczenie maszynowe i modele SI do rozwiązania.

* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), aby uruchamiać obliczenia analityczne wykonywane w czasie rzeczywistym na danych przesyłanych strumieniowo z urządzeń.

## <a name="configure-and-control-your-devices"></a>Konfigurowanie urządzeń i sterowanie nimi

Możesz zarządzać urządzeniami połączonymi z usługą IoT Hub za pomocą szerokiego wachlarza wbudowanych funkcji.

* Przechowuj i synchronizuj metadane urządzenia i informacje o stanie dla wszystkich urządzeń oraz twórz zapytania dotyczące tych metadanych.

* Ustawiaj stan urządzeń na poszczególnych urządzeniach lub na podstawie wspólnych charakterystyk urządzeń.

* Automatycznie odpowiadaj na zmiany stanu zgłoszone przez urządzenie dzięki integracji routingu komunikatów.

## <a name="make-your-solution-highly-available"></a>Uzyskaj wysoką dostępność rozwiązania

Obowiązuje [umowa dotycząca poziomu usług dla usługi IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) gwarantująca dostępność na poziomie 99,9%. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="connect-your-devices"></a>Łączenie urządzeń

Za pomocą bibliotek [zestawu SDK urządzeń Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) możesz tworzyć aplikacje działające na Twoich urządzeniach i wchodzące w interakcje z usługą IoT Hub. Do obsługiwanych platform należy wiele dystrybucji systemów Linux i Windows oraz systemów operacyjnych czasu rzeczywistego. Obsługiwane języki:

* C
* C#
* Java
* Python
* Node.js

Usługa IoT Hub i zestawy SDK urządzeń obsługują następujące protokoły łączenia urządzeń:

* HTTPS
* AMQP
* AMQP za pośrednictwem obiektów WebSocket
* MQTT
* MQTT za pośrednictwem obiektów WebSocket

Jeśli w danym rozwiązaniu nie można użyć bibliotek urządzeń, urządzenia mogą połączyć się natywnie z centrum przy użyciu protokołów MQTT v3.1.1, HTTPS 1.1 lub AMQP 1.0.

Jeśli w rozwiązaniu nie można użyć obsługiwanych protokołów, możesz rozszerzyć usługę IoT Hub pod kątem obsługi protokołów niestandardowych:

* Za pomocą usługi [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) możesz utworzyć bramę działającą w terenie wykonującą translację protokołów na urządzeniach brzegowych.

* Dostosuj [bramę protokołu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md), aby wykonywać translację protokołu w chmurze.

## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia

W każdej subskrypcji platformy Azure obowiązują domyślne limity przydziału mające uniemożliwiać nadużywanie usług. Te limity mogą mieć wpływ na zakres rozwiązania IoT. Aktualne ograniczenie na podstawie każdej subskrypcji wynosi 50 centrów IoT na subskrypcję. Możesz zażądać zwiększenia limitu przydziału, kontaktując się z pomocą techniczną. Więcej informacji na temat limitów przydziałów znajduje się w artykułach:

* [Limity usług subskrypcji platformy Azure](../azure-subscription-service-limits.md)

* [Ograniczanie przepustowości w usłudze IoT Hub](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>Kolejne kroki

Aby wypróbować kompleksowe rozwiązanie IoT, zapoznaj się z przewodnikami Szybki start:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-node.md)