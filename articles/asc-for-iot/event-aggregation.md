---
title: Informacje o Azure Security Center dla agregacji zdarzeń IoT | Microsoft Docs
description: Dowiedz się więcej na temat Azure Security Center dla agregacji zdarzeń IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928972"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center dla agregacji zdarzeń IoT

Azure Security Center dla agentów zabezpieczeń IoT zbiera dane i zdarzenia systemowe z urządzenia lokalnego i wysyła te dane do chmury platformy Azure w celu przetworzenia i analizy. Agent zabezpieczeń zbiera wiele typów zdarzeń urządzeń, w tym nowy proces i nowe zdarzenia połączenia. Zarówno nowy proces, jak i nowe zdarzenia połączeń mogą być często wykonywane na urządzeniu w ciągu sekundy, a chociaż ważne dla niezawodnego i kompleksowego zabezpieczenia, liczba agentów zabezpieczeń komunikatów jest zmuszona do wysłania lub przekroczenia IoT Hub limity przydziału i kosztów. Zdarzenia te zawierają jednak wysoce cenne informacje zabezpieczające, które są kluczowe dla ochrony urządzenia.

Aby zmniejszyć dodatkowy przydział i koszty przy zachowaniu ochrony urządzeń, Azure Security Center dla agentów IoT agregują te typy zdarzeń.

Agregacja zdarzeń jest domyślnie **włączona** i chociaż nie jest zalecana, można ją **wyłączyć** ręcznie w dowolnym momencie.

Agregacja jest obecnie dostępna dla następujących typów zdarzeń:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (tylko system Windows)

## <a name="how-does-event-aggregation-work"></a>Jak działa agregacja zdarzeń?
Po podaniu agregacjizdarzeń Azure Security Center dla agentów IoT agreguje zdarzenia dla okresu interwału lub przedziału czasu.
Po upływie okresu interwału Agent wysyła zagregowane zdarzenia do chmury platformy Azure w celu przeprowadzenia dalszej analizy.
Zagregowane zdarzenia są przechowywane w pamięci do momentu wysłania ich do chmury platformy Azure.

Aby zmniejszyć wykorzystanie pamięci przez agenta, za każdym razem, gdy Agent zbiera identyczne zdarzenie do takiego, który jest już przechowywany w pamięci, Agent zwiększa liczbę trafień tego zdarzenia. Gdy przejdzie przedział czasu agregacji, Agent wysyła liczbę trafień każdego określonego typu zdarzenia, które wystąpiło. Agregacja zdarzeń jest po prostu agregacją liczby trafień każdego zebranego typu zdarzenia.

Zdarzenia są uważane za identyczne tylko wtedy, gdy są spełnione następujące warunki: 

* Zdarzenia ProcessCreate — gdy są identyczne elementy **CommandLine**, **Executable**, **username**i **UserID**
* Zdarzenia ConnectionCreate — gdy **wiersz polecenia**, **userId, nazwa użytkownika**, **kierunek**, **adres lokalny**, **adres zdalny**, * * protokół i **port docelowy** są identyczne
* Zdarzenia ProcessTerminate — gdy **plik wykonywalny** i **stan zakończenia** są identyczne

### <a name="working-with-aggregated-events"></a>Praca z zagregowanymi zdarzeniami

Podczas agregacji właściwości zdarzeń, które nie są agregowane, są odrzucane i pojawiają się w usłudze log Analytics z wartością 0. 
* Zdarzenia ProcessCreate- **ProcessId**i **parentProcessId** są ustawione na 0
* Zdarzenia ConnectionCreate — **Identyfikator procesu**i **port źródłowy** są ustawione na 0

## <a name="event-aggregation-based-alerts"></a>Alerty oparte na agregacji zdarzeń 
Po analizie Azure Security Center dla usługi IoT tworzy alerty zabezpieczeń dla podejrzanych zdarzeń agregowanych. Alerty utworzone na podstawie zagregowanych zdarzeń są wyświetlane tylko raz dla każdego zagregowanego zdarzenia.

Czas rozpoczęcia agregacji, czas zakończenia i liczba trafień dla każdego zdarzenia są rejestrowane w polu usługi Event **ExtraDetails** w log Analytics do użycia podczas badania. 

Każde zagregowane zdarzenie przedstawia 24-godzinny okres zebranych alertów. Korzystając z menu Opcje zdarzenia w lewym górnym rogu każdego zdarzenia, można **odrzucić** każde pojedyncze zagregowane zdarzenie.    

## <a name="event-aggregation-twin-configuration"></a>Konfiguracja dwuosiowa agregacji zdarzeń
Wprowadź zmiany w konfiguracji Azure Security Center dla agregacji zdarzeń IoT w [obiekcie konfiguracji agenta](how-to-agent-configuration.md) dla **nieazureiotsecurityego** modułu w module.

| Nazwa konfiguracji | Możliwe wartości | Szczegóły | Uwagi |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | wartość logiczna | Włącz/Wyłącz agregację zdarzeń dla zdarzeń tworzenia procesu |
| aggregationIntervalProcessCreate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń tworzenia procesu |
| aggregationEnabledConnectionCreate | wartość logiczna| Włącz/Wyłącz agregację zdarzeń dla zdarzeń tworzenia połączenia |
| aggregationIntervalConnectionCreate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń tworzenia połączenia |
| aggregationEnabledProcessTerminate | wartość logiczna | Włącz/Wyłącz agregację zdarzeń dla zdarzeń zakończenia procesu | Tylko Windows|
| aggregationIntervalProcessTerminate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń zakończenia procesu | Tylko Windows|
|

## <a name="default-configurations-settings"></a>Ustawienia konfiguracji domyślnej

| Nazwa konfiguracji | Wartości domyślne |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje dotyczące agregacji agentów zabezpieczeń IoT Azure Security Center i dostępnych opcji konfiguracji zdarzeń.

Aby kontynuować wprowadzenie do usługi Azure Security Center na potrzeby wdrażania IoT, użyj następujących artykułów:

- Informacje na temat [metod uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybierz i Wdróż [agenta zabezpieczeń](how-to-deploy-agent.md)
- Przegląd [wymagań wstępnych dotyczących usługi](service-prerequisites.md) IoT Azure Security Center
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [Azure Security Center na potrzeby usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)
