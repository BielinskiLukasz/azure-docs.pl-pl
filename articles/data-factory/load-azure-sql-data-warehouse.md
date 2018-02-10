---
title: "Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft"
description: "Użyj fabryki danych Azure, aby skopiować dane do magazynu danych SQL Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: eec6eeb3419c5f5f4c8d22398051f7cf057ac980
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure

[Usługa Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) oparte na chmurze, skalowalnych w poziomie baza danych jest w stanie przetwarzanie bardzo dużych woluminów danych relacyjnych i nierelacyjnych. Usługa SQL Data Warehouse jest zbudowana na architekturę masowego przetwarzania równoległego (MPP), która jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa. Zapewnia elastyczność chmury elastycznie skalować magazyn i zasobów obliczeniowych niezależnie.

Wprowadzenie do korzystania z usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek Jeśli używasz usługi fabryka danych Azure. Fabryka danych Azure to usługa integracji pełni zarządzanych danych oparte na chmurze. Usługa służy do wypełniania SQL Data Warehouse przy użyciu danych z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań analizy.

Fabryka danych Azure oferuje następujące korzyści ładowania danych do usługi Azure SQL Data Warehouse:

* **Można skonfigurować**: bez skryptu wymagane intuicyjne kreatora krok 5.
* **Obsługa magazynu danych sformatowanego**: wbudowaną obsługę bogaty zestaw lokalnymi i magazyny danych oparte na chmurze. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczne i zgodne**: dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute. Obecność usługi global service zapewnia, że dane nigdy nie przekracza granic geograficznych.
* **Bezkonkurencyjne wydajności przy użyciu programu PolyBase**: Polybase jest najbardziej wydajnym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Funkcja przemieszczania obiektu blob do uzyskania szybkości wysokie obciążenie ze wszystkich typów magazynów danych, w tym z magazynu obiektów Blob platformy Azure i usługi Data Lake Store. (Aparat Polybase obsługuje magazynu obiektów Blob platformy Azure i usługi Azure Data Lake Store domyślnie). Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule przedstawiono sposób użycia narzędzia kopii fabryki danych do _ładowanie danych z bazy danych SQL Azure do usługi Azure SQL Data Warehouse_. Można wykonać podobne kroki można skopiować danych z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure](connector-azure-sql-data-warehouse.md).
>
> Ten artykuł dotyczy wersji 2 usługi Azure Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [działanie kopiowania w fabryce danych Azure w wersji 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Usługa Azure SQL Data Warehouse: Magazyn danych zawiera dane, które są kopiowane z bazy danych SQL. Jeśli nie masz usługi Azure SQL Data Warehouse, zapoznaj się z instrukcjami w [Tworzenie usługi SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: W tym samouczku kopiuje dane z bazy danych Azure SQL z firmy Adventure Works LT przykładowych danych. Można utworzyć bazy danych SQL, postępując zgodnie z instrukcjami w [tworzenie bazy danych Azure SQL](../sql-database/sql-database-get-started-portal.md). 
* Konto magazynu Azure: Usługa Azure Storage jest używany jako _przemieszczania_ obiektów blob w operacji kopiowania zbiorczego. Jeśli nie masz konta magazynu platformy Azure, zobacz instrukcje w [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **nowy** > **dane i analiza** > **fabryki danych**: 
   
   ![Tworzenie nowego fabryki danych](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji:
      
   ![Strona Nowa fabryka danych](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź globalnie unikatowej nazwy dla fabryką danych Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadSQLDWDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_**ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabryki danych. Zasady nazewnictwa artefaktów fabryki danych, zobacz [regułami nazywania dla fabryki danych](naming-rules.md).
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w którym można utworzyć fabryki danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz **Utwórz nowy** opcję i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2 (wersja zapoznawcza)**.
    * **Lokalizacja**: Wybierz lokalizację dla fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może być w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, usługi Azure Storage, baza danych SQL Azure i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia go z fabryką danych. Zostanie wyświetlony **fabryki danych** strony głównej, jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Wybierz **autora & Monitor** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

1. W **wprowadzenie** wybierz pozycję **kopiowania danych** Kafelek, aby uruchomić narzędzie kopii danych:

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. W **właściwości** określ **CopyFromSQLToSQLDW** dla **Nazwa zadania** pola i wybierz pozycję **dalej**:

    ![Strona właściwości](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. W **magazynu danych źródła** wybierz pozycję **bazy danych SQL Azure**i wybierz **dalej**:

    ![Strona Źródłowy magazyn danych](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Na stronie **Określanie bazy danych SQL Azure** wykonaj następujące czynności: 
   1. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
   2. W polu **Nazwa bazy danych** wybierz swoją bazę danych usługi Azure SQL.
   3. W polu **Nazwa użytkownika** podaj nazwę użytkownika.
   4. Określ hasło użytkownika **hasło**.
   5. Wybierz opcję **Dalej**.
   
   ![Określ bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. W **wybierz tabele, z których chcesz skopiować dane, lub użyć niestandardowej kwerendy** wprowadź **SalesLT** do filtrowania tabel. Wybierz **(Zaznacz wszystko)** korzystać ze wszystkich tabel dla kopii, a następnie wybierz **dalej**: 

    ![Wybierz tabele źródła](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. W **magazyn danych docelowy** wybierz pozycję **magazyn danych SQL Azure**i wybierz **dalej**:

    ![Strona Docelowy magazyn danych](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. W **określić magazyn danych SQL Azure** wykonaj następujące czynności: 

   1. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
   2. Wybierz magazyn danych SQL Azure do **Nazwa bazy danych**.
   3. W polu **Nazwa użytkownika** podaj nazwę użytkownika.
   4. Określ hasło użytkownika **hasło**.
   5. Wybierz opcję **Dalej**.
   
   ![Określ magazyn danych Azure SQL](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. W **mapowania tabeli** strony, przejrzyj zawartość, a następnie wybierz **dalej**. Wyświetla mapowania tabeli inteligentnego. Tabel źródłowych są mapowane na podstawie tabeli nazw tabel docelowych. Jeśli tabela źródłowa nie istnieje w docelowym, fabryki danych Azure tworzy domyślnie docelowej tabeli o takiej samej nazwie. Można również mapować tabelę źródłową do istniejącej tabeli docelowej. 

   > [!NOTE]
   > Tworzenie tabeli automatycznej dla ujścia magazynu danych SQL ma zastosowanie, gdy serwer SQL lub bazy danych SQL Azure jest źródłem. Po skopiowaniu danych z innego źródła danych magazynu, należy wstępnie utworzyć schematu w ujściu usługi Azure SQL Data Warehouse przed wykonaniem kopii danych.

   ![Strona Mapowanie tabeli](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. W **mapowanie schematu** strony, przejrzyj zawartość, a następnie wybierz **dalej**. Mapowania tabeli inteligentnego opiera się na nazwę kolumny. Jeśli umożliwisz fabryka danych automatycznie tworzy tabele konwersji typów danych może wystąpić, gdy między magazynami źródłowy i docelowy są niezgodne. W przypadku konwersji typu nieobsługiwanych danych między kolumną źródłowym i docelowym, zostanie wyświetlony komunikat o błędzie obok tej tabeli.

    ![Strona Mapowanie schematu](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. W **ustawienia** wybierz konto magazynu Azure w **nazwy konta magazynu** listy rozwijanej. Konto jest używane do przemieszczania danych przed załadowaniem do usługi SQL Data Warehouse przy użyciu programu PolyBase. Po zakończeniu kopiowania danych tymczasowych w usłudze Azure Storage zostanie automatycznie oczyszczony. W obszarze **Zaawansowane ustawienia**, usuń zaznaczenie **domyślny typ użycia** opcji:

    ![Strona Ustawienia](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. W **Podsumowanie** strony, przejrzyj ustawienia, a następnie wybierz **dalej**:

    ![Strona podsumowania](./media/load-azure-sql-data-warehouse/summary-page.png)
13. W **stronę wdrożenia**, wybierz pozycję **monitora** do monitorowania procesu (zadania):

    ![Strona Wdrażanie](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera łącza, aby wyświetlić szczegóły uruchomienia działania i ponownie uruchomić potoku: 

    ![Monitorowanie uruchomień potoku](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Zaznacz, aby wyświetlić uruchomień działania, które są skojarzone z potoku Uruchom **odbywa się działanie widoku** łącze w **akcje** kolumny. Brak 10 działania kopiowania w potoku, a każde działanie kopiuje jedna tabela danych. Wybierz, aby wrócić do potoku uruchamia widok **potoki** łącze u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Aby monitorować szczegóły wykonywania dla każdego działania kopiowania, wybierz **szczegóły** łącze w obszarze **akcje** w działaniu widok monitorowania. Możliwość monitorowania szczegółów, takich jak ilość danych skopiowana ze źródła do zbiornika, przepływność danych, wykonywanie czynności z odpowiedni czas trwania używane konfiguracje:

    ![Monitorowanie aktywności szczegóły uruchomienia](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Kolejne kroki

Przejdź do następujących artykuł, aby dowiedzieć się więcej na temat obsługi usługi Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Łącznik usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)