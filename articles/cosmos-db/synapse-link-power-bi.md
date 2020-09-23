---
title: Power BI i Synapse bez programu SQL Server, aby analizować dane Azure Cosmos DB za pomocą linku Synapse
description: Dowiedz się, jak utworzyć Synapse bazę danych i widoki programu SQL Server za pośrednictwem linku Synapse dla Azure Cosmos DB, wysłać zapytanie do kontenerów Cosmos platformy Azure, a następnie skompilować model z Power BImi w tych widokach.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 03ea1b0cdfef30935b38078d0811d1408a78c41e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937980"
---
# <a name="use-power-bi-and-synapse-sql-serverless-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>Używaj Power BI i Synapse programu SQL Server, aby analizować dane Azure Cosmos DB za pomocą linku Synapse (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć Synapse bezserwer programu SQL Server (który wcześniej był określany jako baza danych **SQL na żądanie**) i widoki dla Azure Cosmos DB. Należy wykonać zapytanie dotyczące kontenerów usługi Azure Cosmos, a następnie skompilować model przy użyciu Power BI w tych widokach w celu odzwierciedlenia tej kwerendy.

> [!NOTE]
> Korzystanie z Azure Cosmos DBego sklepu analitycznego z programem SQL Server w wersji zapoznawczej jest obecnie objęte usługą. Aby zażądać dostępu, skontaktuj się z [zespołem Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

W tym scenariuszu będziesz używać fikcyjnych danych dotyczących sprzedaży produktu Surface w sklepie detalicznym partnera. Możesz analizować przychody dla sklepu w oparciu o bliskość do dużych gospodarstw domowych i wpływ anonsowania na określony tydzień. W tym artykule opisano tworzenie dwóch widoków o nazwach **RetailSales** i **StoreDemographics** oraz zapytania między nimi. Dane przykładowego produktu można pobrać z tego repozytorium [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały utworzone następujące zasoby:

* [Utwórz konto Azure Cosmos DB rodzaju SQL (rdzeń) lub MongoDB.](create-cosmosdb-resources-portal.md)

* Włącz link Synapse platformy Azure dla [konta usługi Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Utwórz bazę danych w ramach konta usługi Azure Cosmos i dwóch kontenerów z [włączonym magazynem analitycznym.](configure-synapse-link.md#create-analytical-ttl)

* Załaduj dane produktów do kontenerów usługi Azure Cosmos, zgodnie z opisem w tym notesie pozyskiwania [danych wsadowych](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Utwórz obszar roboczy Synapse](../synapse-analytics/quickstart-create-workspace.md) o nazwie **SynapseLinkBI**.

* [Połącz bazę danych usługi Azure Cosmos z obszarem roboczym Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Tworzenie bazy danych i widoków

W obszarze roboczym Synapse przejdź do karty **Tworzenie** , wybierz pozycję na **+** ikonie i wybierz pozycję **skrypt SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Dodawanie skryptu SQL do obszaru roboczego analizy Synapse":::

Każdy obszar roboczy jest dostarczany z Synapse bezserwerowym punktem końcowym programu SQL Server. Po utworzeniu skryptu SQL z paska narzędzi na górze Połącz się z serwerem **SQL na żądanie**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Włączanie przez skrypt SQL bezSynapseowego punktu końcowego programu SQL Server w obszarze roboczym":::

Utwórz nową bazę danych o nazwie **RetailCosmosDB**i Widok SQL dla kontenerów z włączonym linkiem Synapse. Następujące polecenie pokazuje, jak utworzyć bazę danych:

```sql
-- Create database
Create database RetailCosmosDB
```

Następnie Utwórz wiele widoków w ramach różnych linków Synapse z obsługą kontenerów usługi Azure Cosmos. Pozwoli to na używanie języka T-SQL do przyłączania i wykonywania zapytań dotyczących danych Azure Cosmos DB znajdujących się w różnych kontenerach.  Upewnij się, że podczas tworzenia widoków została wybrana baza danych **RetailCosmosDB** .

Poniższe skrypty pokazują, jak tworzyć widoki w poszczególnych kontenerach. Dla uproszczenia użyjemy funkcji [automatycznego wnioskowania schematu](analytical-store-introduction.md#analytical-schema) Synapse programu SQL Server za pośrednictwem kontenerów z włączonym linkiem Synapse:


### <a name="retailsales-view"></a>Widok RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Upewnij się, że wstawisz region Azure Cosmos DB i klucz podstawowy w poprzednim skrypcie SQL. Wszystkie znaki w nazwie regionu powinny być małymi literami bez spacji. W przeciwieństwie do innych parametrów `OPENROWSET` polecenia nazwę kontenera należy określić bez cudzysłowu.

### <a name="storedemographics-view"></a>Widok StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Teraz uruchom skrypt SQL, wybierając polecenie **Uruchom** .

## <a name="query-the-views"></a>Wykonywanie zapytań w widokach

Teraz, gdy dwa widoki są tworzone, zdefiniujmy zapytanie w celu dołączenia do tych dwóch widoków w następujący sposób:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Wybierz pozycję **Uruchom** , która daje w wyniku następującą tabelę:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Wyniki zapytania po dołączeniu do widoków StoreDemographics i RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Widoki modelu za pośrednictwem kontenerów z Power BI

Następnie otwórz Power BI pulpicie i Połącz się z bezSynapseowym punktem końcowym programu SQL Server, wykonując następujące czynności:

1. Otwórz aplikację Power BI Desktop. Wybierz pozycję **Pobierz dane** i wybierz pozycję **więcej**.

1. Wybierz pozycję **Azure Synapse Analytics (SQL DW)** z listy opcje połączenia.

1. Wprowadź nazwę punktu końcowego SQL, w którym znajduje się baza danych. Wprowadź `SynapseLinkBI-ondemand.sql.azuresynapse.net` wartość w polu **serwer** . W tym przykładzie  **SynapseLinkBI** jest nazwą obszaru roboczego. Zastąp ją, jeśli masz inną nazwę w obszarze roboczym. Wybierz opcję **bezpośrednie zapytanie** dla trybu łączności danych, a następnie kliknij przycisk **OK**.

1. Wybierz preferowaną metodę uwierzytelniania, taką jak Azure AD.

1. Wybierz **RetailCosmosDB** bazę danych i widoki **RetailSales**, **StoreDemographics** .

1. Wybierz pozycję **Załaduj** , aby załadować dwa widoki do trybu zapytania bezpośredniego.

1. Wybierz pozycję **model** , aby utworzyć relację między dwoma widokami za pomocą kolumny **StoreID** .

1. Przeciągnij kolumnę **StoreId** z widoku **RetailSales** do kolumny **StoreId** w widoku **StoreDemographics** .

1. Wybierz relację wiele do jednego (*: 1), ponieważ istnieje wiele wierszy z tym samym IDENTYFIKATORem magazynu w widoku **RetailSales** , ale **StoreDemographics** ma tylko jeden wiersz identyfikatora magazynu (jest to tabela wymiarów).

Teraz przejdź do okna **raport** i Utwórz raport, aby porównać względną ważność rozmiaru gospodarstwa domowego z średnim przychodem na sklep w oparciu o rozproszenie reprezentację przychodu i indeksu LargeHH:

1. Wybierz **Wykres punktowy**.

1. Przeciągnij i upuść **LargeHH** z widoku **StoreDemographics** do osi X.

1. Przeciągnij i upuść **przychód** z widoku **RetailSales** do osi Y. Wybierz pozycję **średnia** , aby uzyskać średnią sprzedaż na produkt i na tydzień.

1. Przeciągnij i upuść **productCode** z widoku **RetailSales** w legendzie, aby wybrać konkretny wiersz produktu.
Po wybraniu tych opcji powinien zostać wyświetlony wykres podobny do poniższego zrzutu ekranu:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Raport, który porównuje względną ważność rozmiaru gospodarstwa domowego z średnim przychodem na sklep":::

## <a name="next-steps"></a>Następne kroki

Używaj bezSynapseowego programu SQL Server do [analizowania otwartych zestawów danych platformy Azure i wizualizacji wyników w usłudze Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
