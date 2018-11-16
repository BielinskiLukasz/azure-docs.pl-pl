---
title: Harmonogramy konserwacji platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Planowanie konserwacji umożliwia klientom w planowaniu wokół zdarzenia niezbędne zaplanowanej konserwacji, które korzysta z usługi Azure SQL Data Warehouse to dystrybucję nowych funkcji, uaktualnienia i poprawki.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 11/14/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 9d26c1d923294a149a57ef28ca03e77dfc2e1c87
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711909"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Umożliwia zarządzanie aktualizacjami usługi i konserwacja harmonogramy konserwacji

Azure SQL Data Warehouse konserwacji jest teraz w wersji zapoznawczej. Ta funkcja zintegrować planowanej konserwacji powiadomień dotyczących kondycji usługi, Monitor sprawdzanie kondycji zasobu usługi harmonogramu konserwacji usługi Azure SQL Data Warehouse.

Służy harmonogramów, wybierz przedział czasu, gdy jest to wygodne otrzymać nowe funkcje, uaktualnienia i poprawki konserwacji. Możesz wybrać podstawowy i pomocniczy konserwacyjne przed upływem siedmiu dni. Przykładem jest podstawowy okna sobota 22:00 do niedzieli 01:00 i okien podrzędnych z środę 19:00 do 22:00. Jeśli usługa SQL Data Warehouse, nie można wykonać konserwacji podczas okna obsługi podstawowego, spróbuje konserwację ponownie podczas okna obsługi dodatkowej.

Wszystkie nowo utworzone Azure SQL Data Warehouse wystąpienia będą mieć harmonogramu konserwacji zdefiniowaną przez system stosowane podczas wdrażania. Harmonogram może być edytowany, zaraz po zakończeniu wdrożenia.

Każde okno obsługi może być trzech do ośmiu godzin. Konserwacji może nastąpić w dowolnej chwili, w oknie. Jak usługa wdraża nowy kod z magazynem danych, należy się spodziewać krótki utraty łączności. 

Aby użyć tej funkcji należy zidentyfikować podstawowych i pomocniczych okna zakresów dni. Wszystkie operacje konserwacji powinno zostać zakończone w ciągu okna zaplanowanej konserwacji. Nie obsługi będzie miała miejsce poza określonym oknami bez wcześniejszego powiadomienia. Jeśli magazyn danych jest wstrzymany podczas zaplanowanej konserwacji, zostaną zaktualizowane podczas operacji wznowienia.  


## <a name="alerts-and-monitoring"></a>Alerty i monitorowanie

Integracja z usługami powiadomień dotyczących kondycji usługi i monitora Sprawdź kondycję zasobów umożliwia klientom uzyskiwania informacji o zbliżającym się związanych z konserwacją. Nowe usługi automation korzysta z usługi Azure Monitor. Aby zdecydować, jaki ma być powiadamiany o zbliżającym się zdarzenia konserwacji. Również zdecydować, które zautomatyzowane przepływy można łatwiej zarządzać przestojów i zminimalizować wpływ na operacje.

Powiadomienie o 24-godzinny poprzedza wszystkie zdarzenia konserwacji. Aby zminimalizować przestoje wystąpienia, upewnij się, czy magazynu danych nie ma długotrwałych transakcji przed okresu wybranym konserwacji. Po uruchomieniu konserwacji wszystkie aktywne sesje zostaną anulowane. Transakcje noncommitted zostanie wycofana, a Magazyn danych, mogą mieć krótki utraty łączności. Zostanie wyświetlone powiadomienie natychmiast, po zakończeniu konserwacji w magazynie danych. 

Jeśli otrzymasz powiadomienie o konserwacji będzie miała miejsce, że usługa SQL Data Warehouse nie można wykonać konserwacji, w tym samym czasie, otrzymasz powiadomienie anulowania. Konserwacja następnie zostanie wznowiona podczas kolejnego okresu zaplanowanej konserwacji.
 
Wszystkie zdarzenia aktywnego zarządzania są wyświetlane w **Service Health — planowaną konserwację** sekcji. Historia kondycji usługi zawiera pełną dokumentację przeszłych zdarzeń. Możesz monitorować konserwacji za pomocą usługi Azure Service Health sprawdzanie pulpitu nawigacyjnego w portalu podczas aktywne zdarzenie.

### <a name="maintenance-schedule-availability"></a>Dostępność harmonogram konserwacji

Nawet jeśli planowania konserwacji jest niedostępna w Twoim regionie wybrany, można wyświetlać i edytować harmonogramu konserwacji w dowolnym momencie. Podczas planowania konserwacji staje się dostępna w Twoim regionie, harmonogram zidentyfikowanych natychmiast stanie się aktywne w magazynu danych.


## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](viewing-maintenance-schedule.md) o wyświetlaniu harmonogram konserwacji. 
- [Dowiedz się więcej](changing-maintenance-schedule.md) o zmienianiu harmonogram konserwacji.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o tworzenie, wyświetlanie i Zarządzanie alertami przy użyciu usługi Azure Monitor.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcje elementu webhook dla reguł alertów dzienników.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/service-health/service-health-overview) dotyczące usługi Azure Service Health.







