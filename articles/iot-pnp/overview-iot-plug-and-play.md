---
title: Wprowadzenie do Plug and Play IoT | Microsoft Docs
description: Dowiedz się więcej o usłudze IoT Plug and Play. Plug and Play IoT jest oparta na otwartym języku modelowania, który umożliwia inteligentnym urządzeniom IoT zadeklarować swoje możliwości. Urządzenia IoT stanowią tę deklarację nazywaną modelem urządzenia, gdy łączą się z rozwiązaniami w chmurze. Rozwiązanie w chmurze może następnie automatycznie zrozumieć urządzenie i zacząć z niego korzystać bez konieczności pisania kodu.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: d6cba4bcb76094ed156ef35b1ea76cea7001c372
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486300"
---
# <a name="what-is-iot-plug-and-play"></a>Co to jest usługa IoT Plug and Play?

Plug and Play IoT umożliwia konstruktorom rozwiązań integrację urządzeń inteligentnych z ich rozwiązaniami bez konieczności ręcznej konfiguracji. Na początku Plug and Play IoT to _model_ urządzenia, którego urządzenie używa do anonsowania swoich możliwości aplikacji z obsługą Plug and Play IoT. Ten model jest strukturalny jako zestaw elementów, które definiują:

- _Właściwości_ reprezentujące stan tylko do odczytu lub do zapisu urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a docelowa temperatura w termostatie może być właściwością umożliwiającą zapis.
- Dane _telemetryczne_ , które są emitowane przez urządzenie, niezależnie od tego, czy dane są regularnym strumieniem odczytów czujnika, okazjonalnym błędem czy komunikatem informacyjnym.
- _Polecenia_ opisujące funkcję lub operację, którą można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie przy użyciu zdalnej kamery.

Można grupować te elementy w interfejsach do wielokrotnego użycia w ramach modeli, aby ułatwić współpracę i przyspieszyć programowanie.

Aby usługa IoT Plug and Play działała z [usługą Azure Digital bliźniaczych reprezentacji](../digital-twins/overview.md), należy zdefiniować modele i interfejsy przy użyciu [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Plug and Play IoT i DTDL są otwarte dla społeczności, a firma Microsoft zachęca do współpracy z klientami, partnerami i branżą. Oba są oparte na otwartych standardach W3C, takich jak JSON-LD i RDF, co umożliwia łatwiejsze wdrażanie w ramach usług i narzędzi.

Nie ma dodatkowych kosztów związanych z korzystaniem z usługi IoT Plug and Play i DTDL. Standardowe stawki za [usługę azure IoT Hub](../iot-hub/about-iot-hub.md) i inne usługi platformy Azure pozostają bez zmian.

Ten artykuł zawiera opis:

- Typowe role skojarzone z projektem, który używa Plug and Play IoT.
- Jak korzystać z urządzeń Plug and Play IoT w aplikacji.
- Jak opracowywać aplikację urządzenia IoT, która obsługuje Plug and Play IoT.

## <a name="user-roles"></a>Role użytkownika

Plug and Play IoT jest przydatne dla dwóch typów deweloperów:

- _Konstruktor rozwiązań_ jest odpowiedzialny za opracowywanie rozwiązania IoT przy użyciu usługi Azure IoT Hub i innych zasobów platformy Azure oraz do identyfikowania urządzeń IoT do integracji.
- _Konstruktor urządzeń_ tworzy kod, który jest uruchamiany na urządzeniu podłączonym do rozwiązania.

## <a name="use-iot-plug-and-play-devices"></a>Korzystanie z urządzeń Plug and Play IoT

Jako Konstruktor rozwiązań można opracowywać rozwiązanie IoT hostowane w chmurze, które korzysta z urządzeń Plug and Play IoT. Użyj [IoT Hub](../iot-hub/about-iot-hub.md) — zarządzanej usługi w chmurze, która działa jako centrum komunikatów w celu bezpiecznej, dwukierunkowej komunikacji między aplikacją IoT i urządzeniami.

Po podłączeniu urządzenia Plug and Play IoT do usługi IoT Hub można użyć narzędzia [Azure IoT Explorer](./howto-use-iot-explorer.md) , aby wyświetlić dane telemetryczne, właściwości i polecenia zdefiniowane w interfejsach, które tworzą model.

Jeśli masz istniejące czujniki dołączone do bramy systemu Windows lub Linux, możesz użyć usługi [iot Plug and Play Bridge](./concepts-iot-pnp-bridge.md), aby połączyć te czujniki i utworzyć urządzenia usługi IoT Plug and Play bez konieczności pisania oprogramowania/oprogramowania układowego (dla [obsługiwanych protokołów](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors) ).

## <a name="develop-an-iot-device-application"></a>Opracowywanie aplikacji urządzenia IoT

Jako Konstruktor urządzeń można opracowywać produkt sprzętu IoT, który obsługuje Plug and Play IoT. Proces zawiera trzy kluczowe kroki:

1. Zdefiniuj model urządzenia. Tworzysz zestaw plików JSON, które definiują możliwości urządzenia przy użyciu [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Model opisuje kompletną jednostkę, taką jak produkt fizyczny, i definiuje zestaw interfejsów implementowanych przez tę jednostkę. Interfejsy są wspólnymi kontraktami, które jednoznacznie identyfikują dane telemetryczne, właściwości i polecenia obsługiwane przez urządzenie. Interfejsy mogą być ponownie używane między różnymi modelami.

1. Twórz oprogramowanie lub oprogramowania układowego urządzenia w taki sposób, że ich dane telemetryczne, właściwości i polecenia są zgodne z konwencjami Plug and Play IoT. W przypadku łączenia istniejących czujników z bramą systemu Windows lub Linux, [mostek Plug and Play IoT](./concepts-iot-pnp-bridge.md) może uprościć ten krok.

1. Urządzenie anonsuje Identyfikator modelu w ramach połączenia MQTT. Zestaw SDK usługi Azure IoT zawiera nowe konstrukcje umożliwiające podanie identyfikatora modelu w czasie połączenia.

> [!Important]
> Urządzenia Plug and Play IoT muszą używać MQTT lub MQTT za pośrednictwem obiektów WebSockets. Inne protokoły, takie jak AMQP lub HTTP, nie są prawidłowe do implementowania urządzeń Plug and Play IoT.

## <a name="device-certification"></a>Certyfikacja urządzenia

[Program certyfikacji urządzenia Plug and Play IoT](howto-certify-device.md) sprawdza, czy urządzenie spełnia wymagania certyfikacji Plug and Play IoT. Można dodać certyfikowane urządzenie do [wykazu urządzeń usługi Azure IoT z certyfikatem](https://aka.ms/devicecatalog)publicznym.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd Plug and Play IoT, sugerowanym następnym krokiem jest wypróbowanie jednego z przewodników szybki start:

- [Łączenie urządzenia z usługą IoT Hub](./quickstart-connect-device.md)
- [Interakcja z urządzeniem z rozwiązania](./quickstart-service.md)
