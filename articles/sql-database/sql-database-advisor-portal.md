---
title: Zastosuj zalecenia dotyczące wydajności — Azure SQL Database
description: Użyj Azure Portal, aby znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: 24a590a80e768bba2e9b102bdf200e0cd96858f8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691277"
---
# <a name="find-and-apply-performance-recommendations"></a>Znajdź i Zastosuj zalecenia dotyczące wydajności

Możesz użyć Azure Portal, aby znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność Azure SQL Database lub rozwiązać problem opisany w obciążeniu. Strona **zalecenia dotyczące wydajności** w Azure Portal umożliwia znalezienie najważniejszych zaleceń w oparciu o ich potencjalny wpływ. 

## <a name="viewing-recommendations"></a>Wyświetlanie zaleceń

Aby wyświetlać i stosować zalecenia dotyczące wydajności, potrzebne są odpowiednie uprawnienia [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) na platformie Azure. Aby można było wykonać wszystkie **akcje, wymagane**są uprawnienia współautora bazy danych **SQL** do wyświetlania zaleceń i **właściciel** **bazy danych SQL** . Utwórz lub upuść indeksy i Anuluj tworzenie indeksu.

Wykonaj następujące kroki, aby znaleźć zalecenia dotyczące wydajności na Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do obszaru **wszystkie usługi** > **bazy danych SQL**, a następnie wybierz bazę danych.
3. Przejdź do **zalecenia** dotyczącego wydajności, aby wyświetlić dostępne rekomendacje dla wybranej bazy danych.

Zalecenia dotyczące wydajności są wyświetlane w tabeli podobnej do przedstawionej na poniższej ilustracji:

![Polecane elementy](./media/sql-database-advisor-portal/recommendations.png)

Zalecenia są sortowane według ich potencjalnego wpływu na wydajność na następujące kategorie:

| Wpływ | Opis |
|:--- |:--- |
| Wysoka |Zalecenia o dużym wpływie powinny zapewnić najbardziej znaczący wpływ na wydajność. |
| Medium |Zalecenia dotyczące średniego wpływu powinny poprawić wydajność, ale nie w znaczący sposób. |
| Małe |Zalecenia dotyczące niskiego wpływu powinny zapewnić lepszą wydajność niż bez, ale ulepszenia mogą nie być znaczące. |


> [!NOTE]
> Azure SQL Database musi monitorować działania co najmniej dziennie, aby zidentyfikować pewne zalecenia. Azure SQL Database można łatwiej zoptymalizować pod kątem spójnych wzorców zapytań niż w przypadku losowych rozerwania aktywności. Jeśli rekomendacje nie są obecnie dostępne, na stronie **zalecenia dotyczące wydajności** zostanie wyświetlony komunikat z wyjaśnieniem dlaczego.
> 

Można również wyświetlić stan operacji historycznych. Wybierz zalecenie lub stan, aby wyświetlić więcej informacji.

Oto przykład rekomendacji "Utwórz indeks" w Azure Portal.

![tworzenie indeksu](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Stosowanie zaleceń
Azure SQL Database zapewnia pełną kontrolę nad sposobem włączania zaleceń przy użyciu dowolnej z następujących trzech opcji: 

* Stosuj pojedyncze rekomendacje pojedynczo.
* Włącz automatyczne dostosowywanie, aby automatycznie stosować zalecenia.
* Aby ręcznie zaimplementować zalecenie, uruchom zalecany skrypt T-SQL w bazie danych.

Wybierz dowolne zalecenie, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Wyświetl skrypt** , aby przejrzeć dokładne szczegóły dotyczące sposobu tworzenia zalecenia.

Baza danych pozostaje w trybie online, gdy zalecenie jest stosowane — przy użyciu zalecenia dotyczącego wydajności lub dostrajania automatycznego nigdy nie przejdzie bazy danych w tryb offline.

### <a name="apply-an-individual-recommendation"></a>Zastosuj indywidualne zalecenie
Możesz przejrzeć i zaakceptować zalecenia po jednej naraz.

1. Na stronie **zalecenia** wybierz zalecenie.
2. Na stronie **szczegółów** kliknij przycisk **Zastosuj** .
   
    ![Zastosuj zalecenie](./media/sql-database-advisor-portal/apply.png)

Wybrane zalecenie jest stosowane do bazy danych.

### <a name="removing-recommendations-from-the-list"></a>Usuwanie rekomendacji z listy

Jeśli lista zaleceń zawiera elementy, które chcesz usunąć z listy, możesz odrzucić zalecenie:

1. Wybierz zalecenie z listy **zaleceń** , aby otworzyć Szczegóły.
2. Na stronie **szczegółów** kliknij pozycję **Odrzuć** .

W razie potrzeby można dodać odrzucone elementy z listy **rekomendacje** :

1. Na stronie **zalecenia** kliknij przycisk **Wyświetl odrzucony**.
2. Wybierz z listy odrzucony element, aby wyświetlić jego szczegóły.
3. Opcjonalnie kliknij przycisk **Cofnij Odrzuć** , aby dodać indeks z powrotem do głównej listy **zaleceń**.

> [!NOTE]
> Należy pamiętać, że jeśli SQL Database [dostrajania automatycznego](sql-database-automatic-tuning.md) jest włączona, a jeśli użytkownik ręcznie odrzuci zalecenie z listy, takie zalecenie nigdy nie będzie automatycznie stosowane. Odrzucanie zalecenia jest wygodnym sposobem, aby użytkownicy mogli korzystać z funkcji automatycznego dostrajania w przypadkach, gdy wymagają zastosowania określonego zalecenia.
> Możesz przywrócić to zachowanie, dodając odrzucone zalecenia z powrotem do listy rekomendacji, wybierając opcję Cofnij Odrzuć.
> 

### <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania
Można ustawić Azure SQL Database, aby zaimplementować zalecenia automatycznie. Gdy udostępnisz zalecenia, są one automatycznie stosowane. Podobnie jak w przypadku wszystkich zaleceń zarządzanych przez usługę, jeśli wpływ na wydajność jest negatywny, zalecenie zostanie cofnięte.

1. Na stronie **zalecenia** kliknij przycisk **Automatyzuj**:
   
    ![Ustawienia usługi Advisor](./media/sql-database-advisor-portal/settings.png)
2. Wybierz akcje do zautomatyzowania:
   
    ![Zalecane indeksy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że opcja **DROP_INDEX** nie jest obecnie zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek indeksu. 
>

Po wybraniu odpowiedniej konfiguracji kliknij przycisk Zastosuj.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ręcznie stosuj zalecenia przy użyciu języka T-SQL

Wybierz dowolne zalecenie, a następnie kliknij przycisk **Wyświetl skrypt**. Uruchom ten skrypt w bazie danych, aby ręcznie zastosować zalecenie.

*Indeksy, które są wykonywane ręcznie, nie są monitorowane i sprawdzane pod kątem wydajności przez usługę* , dlatego zaleca się monitorowanie tych indeksów po utworzeniu, aby upewnić się, że zapewniają one zyski z wydajności i dostosowywać je w razie potrzeby. Aby uzyskać szczegółowe informacje na temat tworzenia indeksów, zobacz [create index (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Ponadto ręcznie zastosowane rekomendacje pozostaną aktywne i wyświetlone na liście zaleceń dla 24-48 godzin. przed automatycznym wycofaniem ich przez system. Jeśli chcesz usunąć zalecenie wcześniej, możesz je ręcznie odrzucić.

### <a name="canceling-recommendations"></a>Anulowanie zaleceń

Zaleceń, które znajdują się w stanie **oczekiwania**, **sprawdzania poprawności**lub **sukcesu** , można anulować. Nie można anulować zaleceń dotyczących stanu **wykonywania** .

1. Wybierz zalecenie w obszarze **historia dostrajania** , aby otworzyć stronę **szczegóły rekomendacji** .
2. Kliknij przycisk **Anuluj** , aby przerwać proces stosowania zalecenia.

## <a name="monitoring-operations"></a>Operacje monitorowania

Zastosowanie zalecenia może nie być chwilowo wykonywane. Portal zawiera szczegółowe informacje dotyczące stanu zalecenia. Poniżej przedstawiono możliwe stany, w których może znajdować się indeks:

| Stan | Opis |
|:--- |:--- |
| Oczekujące |Odebrano polecenie zastosowania zalecenia i zaplanowano jego wykonanie. |
| Operacji |Zalecenie jest stosowane. |
| Ponownego |Zalecenie zostało pomyślnie zastosowane i usługa mierzy korzyści. |
| Powodzenie |Zalecenie zostało pomyślnie zastosowane i korzyści zostały zmierzone. |
| Błąd |Wystąpił błąd podczas procesu stosowania zalecenia. Może to być problem przejściowy lub prawdopodobnie zmiana schematu tabeli, a skrypt nie jest już prawidłowy. |
| Przywracanie |Zalecenie zostało zastosowane, ale zostało uznane za niewykonane i jest automatycznie przywracane. |
| Przywrócono |Zalecenie zostało cofnięte. |

Kliknij rekomendację w procesie z listy, aby wyświetlić więcej informacji:

![Zalecane indeksy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Cofanie zalecenia
Jeśli użyto zalecenia dotyczącego wydajności, aby zastosować zalecenie (oznacza to, że nie uruchomiono ręcznie skryptu T-SQL), program automatycznie przywróci zmianę, jeśli znajdzie wpływ na wydajność na wartość ujemną. Jeśli z jakiegoś powodu po prostu chcesz przywrócić zalecenie, możesz wykonać następujące czynności:

1. Wybierz pomyślnie zastosowane zalecenie w obszarze **historia dostrajania** .
2. Kliknij przycisk **Przywróć** na stronie **szczegółów rekomendacji** .

![Zalecane indeksy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorowanie wpływu zaleceń dotyczących indeksów
Po pomyślnym wdrożeniu zaleceń (obecnie, operacji indeksowania i zaleceń Sparametryzuj) można kliknąć pozycję **zapytanie** szczegółowe informacje na stronie Szczegóły zalecenia, aby otworzyć szczegółowe informacje o [wydajności zapytań](sql-database-query-performance.md) i zobaczyć wpływ na wydajność najważniejszych zapytań.

![Monitorowanie wpływu wydajności](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Podsumowanie
Azure SQL Database zawiera zalecenia dotyczące poprawy wydajności usługi SQL Database. Dostarczając skrypty T-SQL, uzyskasz pomoc w optymalizowaniu bazy danych i ostatecznie ulepszaniu wydajności zapytań.

## <a name="next-steps"></a>Następne kroki
Monitoruj swoje rekomendacje i Kontynuuj ich stosowanie w celu udoskonalenia wydajności. Obciążenia baz danych są dynamiczne i zmieniają się w sposób ciągły. Azure SQL Database kontynuuje monitorowanie i udostępnianie zaleceń, które mogą poprawić wydajność bazy danych. 

* Zobacz [Automatyczne dostrajanie](sql-database-automatic-tuning.md) , aby dowiedzieć się więcej na temat dostrajania automatycznego w Azure SQL Database.
* Zobacz [zalecenia dotyczące wydajności](sql-database-advisor.md) , aby zapoznać się z omówieniem zaleceń dotyczących wydajności Azure SQL Database.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) , aby dowiedzieć się więcej o wyświetlaniu wydajności najważniejszych zapytań.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [UTWÓRZ INDEKS](https://msdn.microsoft.com/library/ms188783.aspx)
* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)

