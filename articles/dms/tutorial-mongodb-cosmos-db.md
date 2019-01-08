---
title: 'Samouczek: Migrowanie bazy danych MongoDB do interfejsu API Mongo usługi Azure Cosmos DB w trybie offline za pomocą usługi Azure Database Migration Service | Microsoft Docs'
description: Dowiedz się, w jaki sposób przeprowadzić migrację z lokalnej bazy danych MongoDB do interfejsu API Mongo usługi Azure Cosmos DB w trybie offline za pomocą usługi Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 4651c9afab99577622af71297e1fb6465a20097f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713101"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Samouczek: Migrowanie bazy danych MongoDB do interfejsu API Mongo usługi Azure Cosmos DB w trybie offline za pomocą usługi DMS
Za pomocą usługi Azure Database Migration Service możesz przeprowadzić (jednorazową) migrację offline baz danych MongoDB znajdujących się w chmurze lub środowisku lokalnym do interfejsu API Mongo usługi Azure Cosmos DB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

W tym samouczku zmigrujemy zestaw danych **Wingtips** bazy danych MongoDB hostowanej na maszynie wirtualnej platformy Azure do interfejsu API usługi Azure Cosmos DB dla usługi MongoDB przy użyciu usługi Azure Database Migration Service. Jeśli nie masz jeszcze skonfigurowanego źródła bazy danych MongoDB, zobacz artykuł [Install and configure MongoDB on a Windows VM in Azure (Instalowanie i konfigurowanie bazy danych MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:
- [Utworzenie konta interfejsu API usługi Azure Cosmos DB dla usługi MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Sieć wirtualna dla usługi Azure Database Migration Service utworzona przy użyciu modelu wdrożenia usługi Azure Resource Manager, która zapewnia łączność między lokacjami dla lokalnych serwerów źródłowych, z użyciem usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub sieci [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Sprawdzenie, czy reguły sieciowej grupy zabezpieczeń usługi Azure Virtual Network nie blokują następujących portów komunikacji: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego serwera MongoDB, czyli domyślnie ustawionego portu TCP 27017.
- W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration
1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
 
    ![Wyświetlanie dostawców zasobów](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.
 
    ![Rejestrowanie dostawcy zasobów](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia
1.  W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.
 
    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    Jeśli potrzebujesz pomocy podczas wybierania właściwej warstwy usługi Azure Database Migration Service, zapoznaj się zaleceniami we wpisie w blogu. Jest on dostępny [tutaj](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.
 
      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MongoDB**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **CosmosDB (interfejs API MongoDB)**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**. 

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła
1. Na ekranie **Szczegóły źródła** określ szczegóły połączenia dla źródłowego serwera MongoDB.
    
   Możesz również użyć trybu parametrów połączenia i podać lokalizację kontenera pliku magazynu blogu, w którym zostały zrzucone dane kolekcji przeznaczone do zmigrowania.

   > [!NOTE]
   > Usługa Azure Database Migration Service umożliwia również migrowanie dokumentów bson lub json do kolekcji interfejsu API Mongo usługi Azure Cosmos DB.
    
   Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

   ![Określanie szczegółów źródła](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego
1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego konta usługi Azure Cosmos DB, czyli dla aprowizowanego wcześniej konta bazy danych MongoDB usługi Azure Cosmos DB, na które są migrowane dane MongoDB.

    ![Określanie szczegółów elementu docelowego](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wybierz pozycję **Zapisz**.

## <a name="map-to-target-databases"></a>Mapowanie do docelowych baz danych
1. Na ekranie **Mapowanie do docelowych baz danych** określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    Jeśli obok nazwy bazy danych pojawi się ciąg **Utwórz**, oznacza to, że usługa Azure Database Migration Service nie znalazła docelowej bazy danych — zostanie ona utworzona automatycznie.

    Na tym etapie migracji możesz [zaaprowizować przepływność](https://docs.microsoft.com/azure/cosmos-db/set-throughput). W usłudze Cosmos DB można aprowizować przepływność na poziomie bazy danych lub na poziomie poszczególnych kolekcji. Przepływność jest mierzona w [jednostkach żądań](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Dowiedz się więcej o [cenniku usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapowanie do docelowych baz danych](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wybierz pozycję **Zapisz**.
3. Na ekranie **Ustawienia kolekcji** rozwiń listę kolekcji, a następnie przejrzyj listę kolekcji, które będą migrowane.

    Należy pamiętać, że usługa Azure Database Migration Service automatycznie wybiera wszystkie kolekcje, które istnieją w źródłowym wystąpieniu bazy danych MongoDB i które nie istnieją na docelowym koncie usługi Azure Cosmos DB. Jeśli chcesz ponownie przeprowadzić migrację kolekcji, które już zawierają dane, musisz jawnie wybrać te kolekcje w tym bloku.

    Możesz określić liczbę jednostek RU używanych przez kolekcje. Usługa Azure Database Migration Service proponuje inteligentne wartości domyślne na podstawie rozmiaru kolekcji.

    Możesz również wskazać klucz fragmentu, aby skorzystać z [partycjonowania w usłudze Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview), które zapewnia optymalną skalowalność. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania klucza fragmentu/klucza partycji](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Wybieranie tabel kolekcji](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wybierz pozycję **Zapisz**.

5. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

    ![Podsumowanie migracji](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji
- Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **stan** działania będzie określony jako **Nierozpoczęte**.

    ![Stan działania](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji
- Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

   > [!NOTE]
   > Po wybraniu działania można uzyskać szczegółowe informacje o metrykach migracji na poziomie bazy danych i kolekcji.

    ![Stan działania — ukończono](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Weryfikowanie danych w usłudze Cosmos DB

- Po zakończeniu migracji możesz sprawdzić konto interfejsu API usługi Azure Cosmos DB dla usługi MongoDB, aby upewnić się, że wszystkie kolekcje zostały zmigrowane pomyślnie.

    ![Stan działania — ukończono](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

 * [Informacje o usłudze Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Następne kroki
- Wskazówki dotyczące innych scenariuszy migracji można znaleźć w dokumencie [Przewodnik po migracji baz danych](https://datamigration.microsoft.com/) opracowanym przez firmę Microsoft.