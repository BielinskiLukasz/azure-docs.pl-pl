---
title: Łączenie ze źródłem danych w kolekcji obszarów roboczych usługi Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się ze źródłem danych w obrębie kolekcji obszarów roboczych usługi Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c67369edec2f3c84ada14e6c2a3a3ab8056b3e0c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870099"
---
# <a name="connect-to-a-data-source"></a>Łączenie ze źródłem danych

Za pomocą **kolekcji obszarów roboczych usługi Power BI**, możesz osadzić raportach do własnej aplikacji. Po osadzeniu raportu usługi Power BI w swojej aplikacji raport łączy się z danymi źródłowymi, **importowania** kopię danych lub przez **nawiązanie bezpośredniego połączenia** do źródła danych przy użyciu **zapytania bezpośredniego** .

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Poniżej przedstawiono różnice między **importem** a **trybem DirectQuery**.

| Import | Tryb DirectQuery |
| --- | --- |
| Tabele, kolumny, *i danych* są importowane lub kopiowane do zestawu danych raportu. Aby zobaczyć zmiany, które nastąpiły w danych bazowych, należy odświeżyć lub zaimportować pełny bieżący zestaw danych ponownie. |Tylko *tabele i kolumny* są importowane lub kopiowane do zestawu danych raportu. Zawsze możesz wyświetlić najbardziej aktualne dane. |

Za pomocą kolekcji obszarów roboczych usługi Power BI możesz za pomocą zapytania bezpośredniego źródłami danych w chmurze, ale nie lokalnych źródeł danych w tej chwili.

> [!NOTE]
> Lokalna brama danych nie jest obsługiwana w tej chwili z kolekcji obszarów roboczych usługi Power BI. Oznacza to, że zapytania bezpośredniego nie można używać z lokalnymi źródłami danych.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Tryb **DirectQuery**
* Baza danych Azure SQL Database
* Azure SQL Data Warehouse

**Importowanie**

Można zaimportować za pomocą wszystkich źródeł danych dostępnych w programie Power BI Desktop. Wykonasz **nie** można było odświeżyć dane w kolekcji obszarów roboczych usługi Power BI. Musisz przekazać zmiany do pliku PBIX do kolekcji obszarów roboczych usługi Power BI. Jest to spowodowane dostępnej bramy. 

## <a name="benefits-of-using-directquery"></a>Korzyści z używania zapytania bezpośredniego

Istnieją dwie podstawowe korzyści w przypadku korzystania z **zapytania bezpośredniego**:

* **Zapytanie bezpośrednie** pozwala tworzyć wizualizacje dużych zestawach danych, których nie można byłoby od razu zaimportować wszystkich danych.
* Zmiany danych bazowych wymagają odświeżenia danych, a w przypadku niektórych raportów konieczność wyświetlenie aktualnych danych może wymagać dużych transferów danych, sprawiając, że dane są ponownie zaimportować niecelowe. Z drugiej strony **zapytania bezpośredniego** raporty zawsze korzystają z aktualnych danych.

## <a name="limitations-of-directquery"></a>Ograniczenia dotyczące zapytania bezpośredniego

Istnieją pewne ograniczenia dotyczące używania **zapytania bezpośredniego**:

* Wszystkie tabele muszą pochodzić z jednej bazy danych.
* Jeśli zapytanie jest zbyt skomplikowane, wystąpi błąd. Aby naprawić ten błąd, musisz wykonać refaktoryzację zapytania, jest mniej złożona. Jeśli zapytanie musi być złożone, musisz zaimportować dane zamiast korzystać z **zapytania bezpośredniego**.
* Filtrowanie relacji jest ograniczone do jednego kierunku, a nie w obu kierunkach.
* Nie można zmienić typu danych kolumny.
* Domyślnie ograniczenia są umieszczane w wyrażeniach języka DAX dozwolone w miarach. Zobacz [zapytania bezpośredniego i środków](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>Zapytanie bezpośrednie i miary
Aby upewnić się, że zapytania wysłane do bazowego źródła danych mają akceptowalną wydajność, ograniczenia są nakładane na wskaźnikach. Korzystając z **Power BI Desktop**, zaawansowany użytkownicy mogą zdecydować się na obejście tego ograniczenia, wybierając **Plik > Opcje i Ustawienia > Opcje**. W **opcje** okno dialogowe, wybierz **zapytania bezpośredniego**i wybierz opcję **Zezwalaj na nieograniczone miary w trybie zapytania bezpośredniego**. Po wybraniu tej opcji można dowolnego wyrażenia języka DAX prawidłowego dla miary. Użytkownicy muszą być świadome; jednak, że niektóre wydajnych po zaimportowaniu danych może spowodować wolne zapytań dotyczących wewnętrznej bazy danych źródłowych w **zapytania bezpośredniego** trybu. 

## <a name="see-also"></a>Zobacz też

* [Rozpoczynanie pracy z kolekcji obszarów roboczych pakietu Microsoft Power BI](get-started.md)
* [Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)

