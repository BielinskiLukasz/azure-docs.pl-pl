---
title: Co to są powiadomienia dotyczące usługi Azure Service Health?
description: Powiadomienia o kondycji usługi umożliwiają wyświetlanie komunikatów kondycji usługi opublikowanych przez Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77653972"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal

Powiadomienia o kondycji usługi są publikowane przez platformę Azure i zawierają informacje o zasobach w ramach subskrypcji. Te powiadomienia są podklasą zdarzeń dziennika aktywności i można je również znaleźć w dzienniku aktywności. Powiadomienia o kondycji usługi mogą być informacyjne lub funkcjonalne w zależności od klasy.

Istnieją różne klasy powiadomień o kondycji usługi:  

- **Wymagana akcja:** Platforma Azure może zauważyć coś nietypowego w Twoim koncie i poradzić sobie z nim w celu rozwiązania tego problemu. Na platformie Azure jest wysyłane powiadomienie, które zawierają szczegółowe informacje o akcjach, które należy podjąć lub sposobie kontaktu z inżynierem lub pomocą techniczną platformy Azure.  
- **Zdarzenie:** Zdarzenie wpływające na usługę ma obecnie wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Konserwacja:** Planowane działanie konserwacji, które może mieć wpływ na co najmniej jeden z zasobów w ramach subskrypcji.  
- **Informacje:** Potencjalna Optymalizacja, która może pomóc w ulepszaniu użycia zasobów. 
- **Zabezpieczenia:** Pilne informacje dotyczące zabezpieczeń dotyczące rozwiązań, które działają na platformie Azure.

Każde powiadomienie o kondycji usługi zawiera szczegółowe informacje o zakresie i wpływie na zasoby. Szczegóły obejmują:

Nazwa właściwości | Opis
-------- | -----------
dyplomatyczn | Jedna z następujących wartości: **administrator** lub **operacja**.
correlationId | Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia należące do tej samej akcji zwykle mają ten sam identyfikator korelacji.
eventDataId | Unikatowy identyfikator zdarzenia.
eventName | Tytuł zdarzenia.
poziom | Poziom zdarzenia
resourceProviderName | Nazwa dostawcy zasobów dla zasobu, którego dotyczy problem.
resourceType| Typ zasobu zasobu, którego to dotyczy.
subStatus | Zazwyczaj kod stanu HTTP odpowiadającego wywołania REST, ale może również zawierać inne ciągi opisujące podstan. Na przykład: OK (kod stanu HTTP: 200), utworzono (kod stanu HTTP: 201), zaakceptowane (kod stanu HTTP: 202), brak zawartości (kod stanu http: 204), nieprawidłowe żądanie (kod stanu http: 400), nie znaleziono (kod stanu http: 404), konflikt (kod stanu http: 409), serwer wewnętrzny Błąd (kod stanu HTTP: 500), Usługa niedostępna (kod stanu HTTP: 503) i limit czasu bramy (kod stanu HTTP: 504).
eventTimestamp | Sygnatura czasowa, gdy zdarzenie zostało wygenerowane przez usługę platformy Azure przetwarzające żądanie odpowiadające zdarzeniu.
submissionTimestamp | Sygnatura czasowa, gdy zdarzenie stało się dostępne na potrzeby wykonywania zapytań.
subscriptionId | Subskrypcja platformy Azure, w której zostało zarejestrowane to zdarzenie.
status | Ciąg opisujący stan operacji. Niektóre typowe wartości to: **rozpoczęte**, **w toku**, **zakończone**powodzeniem, **zakończone niepowodzeniem**, **aktywne**i **rozwiązane**.
operationName | Nazwa operacji.
category | Ta właściwość jest zawsze **servicehealth**.
resourceId | Identyfikator zasobu zasobu, którego dotyczy problem.
Properties. title | Zlokalizowany tytuł tej komunikacji. Wartość domyślna to angielski.
Właściwości. Komunikacja | Zlokalizowane szczegóły komunikacji z adiustacją HTML. Wartość domyślna to angielski.
Właściwości. zdarzenietype | Jedną z następujących wartości: **wymagana akcja**, **informacyjny**, **zdarzenie**, **konserwacja**lub **zabezpieczenia**.
Properties.trackingId | Zdarzenie, z którym jest skojarzone to zdarzenie. Służy do skorelowania zdarzeń związanych ze zdarzeniem.
Properties.impactedServices | Zmieniony obiekt BLOB JSON, który opisuje usługi i regiony, na które wpływa zdarzenie. Właściwość zawiera listę usług, z których każdy ma **nazwę ServiceName**, i listę regionów, których dotyczy problem, z których każdy ma **region regionu**.
Properties.defaultLanguageTitle | Komunikacja w języku angielskim.
Properties.defaultLanguageContent | Komunikacja w języku angielskim jako znacznik HTML lub zwykły tekst.
Właściwości. etap | Możliwe wartości dla **zdarzenia**i **zabezpieczeń** są **aktywne,** **rozwiązane** lub według nazwy **głównej.** Dla **wymagana akcja** lub **informacyjnego** jedyną wartością jest **aktywny.** W przypadku **konserwacji** : **aktywne**, **zaplanowane**, w **toku**, **anulowane**, **ponownie zaplanowane**, **rozwiązane**lub **ukończone**.
Properties.communicationId | Komunikacja, z którą jest skojarzone to zdarzenie.

### <a name="details-on-service-health-level-information"></a>Szczegóły informacji o poziomie kondycji usługi

**Wymagana akcja** (właściwości. incydenttype = = wymagana akcja)
- Aby zapobiec wpływowi istniejących usług, wymagana jest akcja administratora.
    
**Konserwacja** (właściwości. incydenttype = = konserwacja)
- Ostrzeżenie — konserwacja awaryjna
- Informacyjny — planowana konserwacja standardowa

**Informacje** (właściwości. incydenttype = = informacje)
- Aby zapobiec wpływowi istniejących usług, może być wymagane przeprowadzenie przez administratora.

**Zabezpieczenia** (właściwości. incydenttype = = Security)
- Ostrzeżenie — Poradnik zabezpieczeń, który ma wpływ na istniejące usługi i może wymagać akcji administratora.
- Informacyjny — Poradnik zabezpieczeń dotyczący istniejących usług.

**Problemy z usługą** (właściwości. incydenttype = = zdarzenie)
- Błędy — rozległe problemy z dostępem do wielu usług w wielu regionach mają wpływ na szeroki zestaw klientów.
- Ostrzeżenie — problemy z dostępem do określonych usług i/lub określonych regionów mają wpływ na podzbiór klientów.
- Informacje o problemach związanych z operacjami zarządzania i/lub opóźnieniami, które nie mają wpływu na dostępność usługi.
