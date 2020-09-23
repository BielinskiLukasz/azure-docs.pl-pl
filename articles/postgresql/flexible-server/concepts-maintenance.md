---
title: Planowana konserwacja — serwer elastyczny Azure Database for PostgreSQL
description: W tym artykule opisano funkcję zaplanowanej konserwacji na serwerze elastycznym Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 35e5e688f5589c23c7c583c7bcbca3fd0956eec7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937252"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Zaplanowana konserwacja w Azure Database for PostgreSQL — elastyczny serwer
 
Azure Database for PostgreSQL — elastyczny serwer przeprowadza okresową konserwację, aby zachować bezpieczeństwo i stabilność zarządzanej bazy danych. Podczas konserwacji serwer pobiera nowe funkcje, aktualizacje i poprawki.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej
 
## <a name="selecting-a-maintenance-window"></a>Wybieranie okna obsługi
 
Możesz zaplanować konserwację w określonym dniu tygodnia i przedział czasu w tym dniu. Możesz też zezwolić na automatyczne wybieranie dnia i godziny okna przez system. W każdym przypadku system wyśle alert o pięć dni przed uruchomieniem dowolnej konserwacji. System będzie również wiedział, gdy konserwacja jest uruchomiona, i po pomyślnym zakończeniu.
 
Powiadomienia dotyczące nadchodzącej zaplanowanej konserwacji mogą być następujące:
 
* Wyślij wiadomość e-mail na określony adres
* Wyślij wiadomość e-mail do roli Azure Resource Manager
* Wysyłane w wiadomości tekstowej (SMS) do urządzeń przenośnych
* Wypychane jako powiadomienie do aplikacji platformy Azure
* Dostarczone jako wiadomość głosowa
 
Określając preferencje dla harmonogramu konserwacji, można wybrać dzień tygodnia i przedział czasu. Jeśli nie zostanie to określone, system wybierze godzinę między 23:00 a 7:00 czasu regionu serwera. Można zdefiniować różne harmonogramy dla każdego elastycznego serwera w ramach subskrypcji platformy Azure. 
 
> [!IMPORTANT]
> Zwykle między pomyślnymi zdarzeniami konserwacji zaplanowanej dla serwera istnieje odstęp co najmniej 30 dni.
>
> Jednak w przypadku krytycznej aktualizacji awaryjnej, takiej jak poważna luka w zabezpieczeniach, okno powiadomień może być krótsze, niż pięć dni. Aktualizacja krytyczna może zostać zastosowana do serwera, nawet jeśli konserwacja zaplanowana została wykonana w ciągu ostatnich 30 dni.

Ustawienia planowania można aktualizować w dowolnym momencie. Jeśli zaplanowano konserwację dla elastycznego serwera i zaktualizujesz preferencje planowania, bieżące zdarzenie będzie obowiązywać zgodnie z harmonogramem, a zmiana ustawień planowania zacznie działać po pomyślnym zakończeniu. 

Jeśli zdarzenie konserwacji zostało anulowane przez system lub zakończy się niepowodzeniem, system utworzy powiadomienie dotyczące zdarzenia anulowania lub niepowodzenia konserwacji odpowiednio. Kolejna próba przeprowadzenia konserwacji zostanie zaplanowana zgodnie z bieżącymi ustawieniami planowania i wkrótce otrzymasz powiadomienie o pięciu dniach.
 
## <a name="next-steps"></a>Następne kroki
 
* Dowiedz się [, jak zmienić harmonogram konserwacji](how-to-maintenance-portal.md)
* Dowiedz się, jak [otrzymywać powiadomienia o nadchodzącej konserwacji](/azure/service-health/service-notifications.md) przy użyciu Azure Service Health
* Dowiedz się, jak [skonfigurować alerty dotyczące nadchodzących zdarzeń konserwacji zaplanowanej](/azure/service-health/resource-health-alert-monitor-guide.md)
