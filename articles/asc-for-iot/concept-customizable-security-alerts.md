---
title: Dostosowywalny Przewodnik po alertach zabezpieczeń dla Azure Security Center usługi IoT | Microsoft Docs
description: Dowiedz się więcej o dostosowywaniu alertów zabezpieczeń i zalecanych korygowaniu za pomocą Azure Security Center dla funkcji i usług IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: f10604d23901c2d7ed23f8b4f32a296f063d8620
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776276"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Azure Security Center alertów zabezpieczeń IoT

Azure Security Center IoT stale analizuje Twoje rozwiązanie IoT przy użyciu zaawansowanych analiz i analizy zagrożeń, aby ostrzec użytkownika o złośliwych działaniach.

Zachęcamy do tworzenia niestandardowych alertów na podstawie wiedzy o oczekiwanym zachowaniu urządzenia, aby zapewnić, że alerty działają jak najbardziej wydajne wskaźniki potencjalnego naruszenia w unikatowym wdrożeniu organizacji i poziomie. 

Poniższa lista Azure Security Center dla alertów IoT jest definiowanych przez użytkownika w oparciu o oczekiwane IoT Hub i/lub zachowanie urządzeń. Aby uzyskać więcej informacji na temat sposobu dostosowywania poszczególnych alertów, zobacz [tworzenie alertów niestandardowych](quickstart-create-custom-alerts.md).

## <a name="azure-security-center-for-iot-alerts-available-for-customization"></a>Azure Security Center dla alertów usługi IoT dostępnych do dostosowania 



| Ważność | Nazwa alertu | Źródło danych | Opis | Sugerowane korygowanie|
|---|---|---|---|---|
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole AMQP jest poza dozwolonym zakresem          | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole AMQP jest poza dozwolonym zakresem | IoT Hub     | Liczba komunikatów z chmury do urządzeń (protokół AMQP) odrzuconych przez urządzenie w określonym przedziale czasu poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole AMQP jest poza dozwolonym zakresem      | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół AMQP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niska      | Alert niestandardowy — liczba wywoływanych metod bezpośrednich jest poza dozwolonym zakresem | IoT Hub     | Liczba metod bezpośrednich wywoływanych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba operacji przekazywania plików jest poza dozwolonym zakresem | IoT Hub     | Ilość przekazywania plików w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.| |
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole HTTP jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w przedziale czasu nie jest w skonfigurowanym dozwolonym zakresie                                  |
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole HTTP nie jest w dozwolonym zakresie | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole HTTP jest poza dozwolonym zakresem | IoT Hub| Ilość urządzenia do komunikatów w chmurze (protokół HTTP) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|    |
| Niska      | Alert niestandardowy — liczba komunikatów z chmury do urządzeń w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|   |
| Niska      | Alert niestandardowy — Liczba odrzuconych chmur do komunikatów urządzenia w protokole MQTT jest poza dozwolonym zakresem | IoT Hub     | Ilość komunikatów z chmury do urządzeń (protokół MQTT) odrzuconych przez urządzenie w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |
| Niska      | Alert niestandardowy — liczba urządzeń z komunikatami w chmurze w protokole MQTT jest poza dozwolonym zakresem          | IoT Hub     | Ilość urządzenia do komunikatów w chmurze (protokół MQTT) w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba przeczyszczania kolejki poleceń jest poza dozwolonym zakresem                               | IoT Hub     | Ilość przeczyszczania kolejki poleceń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.||
| Niska      | Alert niestandardowy — liczba aktualizacji bliźniaczych modułu jest poza dozwolonym zakresem                                       | IoT Hub     | Liczba aktualizacji sieci dwuosiowych modułu w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba niedozwolonych operacji jest poza dozwolonym zakresem  | IoT Hub     | Ilość niedozwolonych operacji w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|
| Niska      | Alert niestandardowy — liczba aktywnych połączeń jest poza dozwolonym zakresem  | Agent       | Liczba aktywnych połączeń w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem.|  Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych połączeń.  |
| Niska      | Alert niestandardowy — połączenie wychodzące utworzone w niedozwolonym adresie IP                             | Agent       | Połączenie wychodzące zostało utworzone w adresie IP spoza listy dozwolonych adresów IP. |Sprawdź dzienniki urządzeń. Dowiedz się, skąd pochodzi połączenie i ustal, czy jest ono niegroźne lub złośliwe. Jeśli jest to złośliwe, Usuń możliwe złośliwe oprogramowanie i poznanie źródła. Jeśli niegroźne, Dodaj źródło do listy dozwolonych adresów IP.                        |
| Niska      | Alert niestandardowy — liczba nieudanych logowań lokalnych jest poza dozwolonym zakresem                               | Agent       | Liczba nieudanych logowań lokalnych w określonym przedziale czasu jest poza aktualnie skonfigurowanym i dozwolonym zakresem. |   |
| Niska      | Niestandardowy alert — Logowanie użytkownika, który nie znajduje się na liście dozwolonych użytkowników | Agent       | Użytkownik lokalny spoza listy dozwolonych użytkowników zalogował się na urządzeniu.|  Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.|
| Niska      | Alert niestandardowy — proces został wykonany, który jest niedozwolony | Agent       | Proces, który jest niedozwolony, został wykonany na urządzeniu. |Jeśli zapisujesz dane pierwotne, przejdź do swojego konta usługi log Analytics i Użyj danych do zbadania urządzenia, Zidentyfikuj źródło, a następnie napraw listę dozwolonych/zablokowanych dla tych ustawień. Jeśli obecnie nie zapisujesz danych pierwotnych, przejdź do urządzenia i popraw listę dozwolonych/zablokowanych dla tych ustawień.  |
|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować alert](quickstart-create-custom-alerts.md)
- Azure Security Center usługi IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)