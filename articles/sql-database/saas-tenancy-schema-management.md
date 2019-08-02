---
title: Zarządzanie schematem Azure SQL Database w aplikacji z jedną dzierżawą | Microsoft Docs
description: Zarządzanie schematami dla wielu dzierżawców w aplikacji z jedną dzierżawą, która używa Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: 7b238044fd3795ae2f49c2fa21367e6499a65672
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570119"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Zarządzanie schematem w aplikacji SaaS przy użyciu wzorca bazy danych na dzierżawcę z Azure SQL Database
 
W miarę rozwoju aplikacji bazy danych zmiany muszą być wprowadzane w schemacie bazy danych lub danych referencyjnych.  Zadania konserwacji bazy danych są również okresowo zbędne. Zarządzanie aplikacją korzystającą z bazy danych na wzorzec dzierżawy wymaga zastosowania tych zmian lub zadań konserwacji w ramach floty baz danych dzierżaw.

W tym samouczku przedstawiono dwa scenariusze — wdrażanie aktualizacji danych referencyjnych dla wszystkich dzierżawców i Odbudowywanie indeksu tabeli zawierającej dane referencyjne. Funkcja [zadań elastycznych](elastic-jobs-overview.md) służy do wykonywania tych działań we wszystkich bazach danych dzierżaw oraz w bazie danych szablonów służącej do tworzenia nowych baz danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Utwórz agenta zadań
> * Uruchamianie zadań T-SQL we wszystkich bazach danych dzierżaw
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono Wingtip bilety bazy danych SaaS dla dzierżawy. Aby wdrożyć program w ciągu mniej niż pięciu minut, zobacz [wdrażanie i eksplorowanie bazy danych Wingtip biletów SaaS na aplikację dzierżawców](saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Zainstalowano najnowszą wersję programu SSMS (SQL Server Management Studio). [Pobieranie i instalowanie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> W tym samouczku są używane funkcje usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator SaaSFeedback@microsoft.com subskrypcji z tematem "zadania elastyczne" w wersji zapoznawczej. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczona SaaSFeedback@microsoft.com , dlatego należy skontaktować się z tematem powiązanych pytań lub pomocy technicznej.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematami SaaS

Wzorzec bazy danych na dzierżawcę pozwala efektywnie wyizolować dane dzierżawy, ale zwiększa liczbę baz danych do zarządzania i konserwacji. [Zadania elastyczne](elastic-jobs-overview.md) ułatwiają administrowanie bazami danych SQL i zarządzanie nimi. Zadania umożliwiają bezpieczne i niezawodne uruchamianie zadań (skryptów T-SQL) w odniesieniu do grupy baz danych. Zadania mogą wdrażać schemat i wspólne zmiany danych referencyjnych we wszystkich bazach danych dzierżaw w aplikacji. Zadania elastyczne mogą być również używane do obsługi bazy danych *szablonów* służącej do tworzenia nowych dzierżawców, co zapewnia, że zawsze ma najnowsze dane schematu i odwołania.

![ekran](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Istnieje nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ta ograniczona wersja zapoznawcza obsługuje obecnie używanie programu PowerShell do tworzenia agenta zadań i języka T-SQL w celu tworzenia zadań i zarządzania nimi.

> [!NOTE]
> W tym samouczku są używane funkcje usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator SaaSFeedback@microsoft.com subskrypcji z tematem "zadania elastyczne" w wersji zapoznawczej. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczona SaaSFeedback@microsoft.com , dlatego należy skontaktować się z tematem powiązanych pytań lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz Wingtip bilety bazy danych SaaS na skrypty aplikacji dzierżawców

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Zapoznaj się [](saas-tenancy-wingtip-app-guidance-tips.md) z ogólnymi wskazówkami dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Tworzenie bazy danych agenta zadań i nowego agenta zadań

Ten samouczek wymaga użycia programu PowerShell do utworzenia agenta zadań i jego kopii zapasowej bazy danych agenta zadań. Baza danych agenta zadań zawiera definicje zadań, stan zadania i historię. Po utworzeniu agenta zadań i jego bazy danych można natychmiast utworzyć i monitorować zadania.

1. **W programie POWERSHELL ISE**Otwórz... Zarządzanie schematami modułów\\uczenia*demo-SchemaManagement. ps1.* \\\\
1. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Skrypt *demo-SchemaManagement. ps1* wywołuje skrypt *Deploy-SchemaManagement. ps1* w celu utworzenia bazy danych SQL o nazwie *osagent* na serwerze wykazu. Następnie tworzy agenta zadań przy użyciu bazy danych jako parametru.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

W aplikacji bilety Wingtip każda baza danych dzierżawy zawiera zestaw obsługiwanych typów miejsc. Każdy miejscu ma określony typ miejsca, który definiuje rodzaj zdarzeń, które mogą być hostowane i określa obraz tła używany w aplikacji. Aby aplikacja obsługiwała nowe rodzaje zdarzeń, należy zaktualizować te dane referencyjne i dodać nowe typy miejsc.  W tym ćwiczeniu zostanie wdrożona Aktualizacja dla wszystkich baz danych dzierżawy w celu dodania dwóch dodatkowych typów miejsc: *Wyścigi motocyklowe* i *trefle*.

Najpierw przejrzyj typy miejsc zawarte w każdej bazie danych dzierżawcy. Połącz się z jedną z baz danych dzierżaw w SQL Server Management Studio (SSMS) i sprawdź tabelę VenueTypes.  Możesz również zbadać tę tabelę w edytorze zapytań w Azure Portal dostępnym ze strony baza danych. 

1. Otwórz narzędzie SSMS i Połącz się z serwerem dzierżawy: *tenants1-&lt;DPT&gt;-User. Database.Windows.NET*
1. Aby upewnić się, że **nie są** obecnie uwzględniane *wyścigi* i klubu z motocykla, przejdź do bazy danych _contosoconcerthall_ na serwerze *tenants1&gt; -&lt;DPT-User* i zapytaj  *Tabela VenueTypes* .

Teraz Utwórzmy zadanie aktualizowania tabeli *VenueTypes* we wszystkich bazach danych dzierżaw, aby dodać nowe typy miejsc.

Aby utworzyć nowe zadanie, należy użyć zestawu zadań procedury składowane systemu utworzone w bazie danych _jobagent_ podczas tworzenia agenta zadań.

1. W programie SSMS Połącz się z serwerem wykazu: *Catalog-DPT-&lt;User&gt;. Database.Windows.NET* Server 
1. W programie SSMS Otwórz plik... Moduły uczeniazarządzania\\schematami DeployReferenceData. SQL\\ \\
1. Zmodyfikuj instrukcję: Ustaw @wtpUser = &lt;User&gt; i Zastąp wartość użytkownika używaną podczas wdrażania Wingtip biletów SaaS bazy danych na aplikację dzierżawców
1. Upewnij się, że nawiązano połączenie z bazą danych _jobagent_ , a następnie naciśnij klawisz **F5** , aby uruchomić skrypt

Obserwuj następujące elementy w skrypcie *DeployReferenceData. SQL* :
* **SP\_Add\_TargetGroup\_** tworzy nazwę grupy docelowej DemoServerGroup.
* **SP\_Dodajczłonka\_grupy\_docelowej służydodefiniowaniazestawudocelowychbazdanych\_** .  Najpierw zostanie dodany serwer _tenants1-&lt;DPT&gt; -użytkownika_ .  Dodanie serwera jako obiektu docelowego powoduje, że bazy danych na tym serwerze w momencie wykonania zadania zostaną uwzględnione w zadaniu. Następnie baza danych _basetenantdb_ i baza danych *adhocreporting* (używane w późniejszym samouczku) zostaną dodane jako elementy docelowe.
* **SP\_Add\_Job** tworzy zadanie o nazwie _wdrożenie danych referencyjnych_.
* **SP\_Add\_JobStep** tworzy krok zadania zawierający tekst polecenia T-SQL w celu zaktualizowania tabeli referencyjnej, VenueTypes.
* Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Użyj tych zapytań, aby przejrzeć wartość stanu w kolumnie **cykl życia** , aby określić, kiedy zadanie zostało zakończone na wszystkich docelowych bazach danych.

Po zakończeniu skryptu możesz sprawdzić, czy dane referencyjne zostały zaktualizowane.  W programie SSMS przejdź do bazy danych *contosoconcerthall* na serwerze *tenants1-DPT-&lt;User&gt;*  i zbadaj tabelę *VenueTypes* .  Sprawdź, czy **są** teraz obecne *wyścigi* i *kluby* w motocyklach.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

To ćwiczenie używa zadania do odbudowania indeksu w kluczu podstawowym tabeli referencyjnej.  Jest to typowa operacja konserwacji bazy danych, którą można wykonać po załadowaniu dużej ilości danych.

Utwórz zadanie, używając tych samych procedur składowanych „system”.

1. Otwórz narzędzie SSMS i Połącz się z serwerem _Catalog-&lt;DPT&gt;-User. Database.Windows.NET_
1. Otwórz plik _... Moduły uczeniazarządzania\\schematami OnlineReindex. SQL\\ \\_
1. Kliknij prawym przyciskiem myszy, wybierz opcję połączenie i nawiąż połączenie z serwerem _Catalog-DPT-&lt;&gt;User. Database.Windows.NET_ , jeśli nie jest jeszcze połączony
1. Upewnij się, że nawiązano połączenie z bazą danych _jobagent_ , a następnie naciśnij klawisz **F5** , aby uruchomić skrypt

Obserwuj następujące elementy w skrypcie _OnlineReindex. SQL_ :
* **SP\_Add\_Job** tworzy nowe zadanie o nazwie "online reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885".
* **SP\_Add\_JobStep** tworzy krok zadania zawierający tekst polecenia T-SQL w celu zaktualizowania indeksu
* Pozostałe widoki w ramach wykonywania zadania monitora skryptów. Użyj tych zapytań, aby przejrzeć wartość stanu w kolumnie **cykl życia** , aby określić, kiedy zadanie zostało pomyślnie zakończone na wszystkich członkach grupy docelowej.



## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Tworzenie agenta zadań do uruchamiania w ramach zadań T-SQL z wieloma bazami danych
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie Wypróbuj [samouczek sprawozdawczości ad hoc](saas-tenancy-cross-tenant-reporting.md) , aby poznać uruchamianie zapytań rozproszonych w bazach danych dzierżaw.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które kompilują się na Wingtip bilety SaaS bazy danych na wdrożenie aplikacji dzierżawców](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](elastic-jobs-overview.md)