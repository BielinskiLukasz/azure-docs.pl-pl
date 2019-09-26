---
title: Zapytanie Azure Data Lake Analytics — Visual Studio
description: Poznaj sposoby instalowania narzędzi Data Lake Tools for Visual Studio oraz tworzenia i testowania skryptów U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315769"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Narzędzia Azure Data Lake i Stream Analytics obejmują funkcje związane z dwiema usługami platformy Azure, Azure Data Lake Analytics i Azure Stream Analytics. Aby uzyskać więcej informacji na temat scenariuszy Azure Stream Analytics, zobacz [narzędzia Azure Stream Analytics Tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

W tym artykule opisano, jak używać programu Visual Studio do tworzenia kont Azure Data Lake Analytics. Możesz definiować zadania w [języku U-SQL](data-lake-analytics-u-sql-get-started.md)i przesyłać zadania do usługi Data Lake Analytics. Więcej informacji na temat usługi Data Lake Analytics można znaleźć w artykule [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Zalecamy uaktualnienie do Azure Data Lake Tools for Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje są niedostępne do pobrania i przestarzałe.
>
> 1. Sprawdź, czy używasz narzędzi Azure Data Lake Tools for Visual Studio w wersji starszej niż 2.3.3000.4.
>
>    ![Sprawdzanie wersji narzędzia](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Jeśli używana wersja jest starsza niż 2.3.3000.4, zaktualizuj narzędzia Azure Data Lake Tools for Visual Studio, odwiedzając centrum pobierania:
>    - [Dla programu Visual Studio 2017 i 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Dla programu Visual Studio 2013 i 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio**: Obsługiwane są wszystkie wersje poza Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Zestaw Microsoft Azure SDK dla platformy .NET** w wersji 2.7.1 lub nowszej. Można go zainstalować przy użyciu [Instalatora platformy internetowej](https://www.microsoft.com/web/downloads/platform.aspx).
* Konto usługi **Data Lake Analytics**. Aby utworzyć konto, zobacz artykuł [Wprowadzenie do pracy z usługą Azure Data Lake Analytics za pomocą witryny Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalowanie narzędzi Azure Data Lake Tools for Visual Studio

Ten samouczek wymaga zainstalowania narzędzi Data Lake Tools for Visual Studio. Aby uzyskać więcej informacji, zobacz [Install Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Łączenie z kontem usługi Azure Data Lake Analytics

1. Otwórz program Visual Studio.

1. Otwórz **Eksplorator serwera** , wybierając pozycję **Wyświetl** > **Eksplorator serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **Azure**, a następnie wybierz pozycję **Połącz z subskrypcją Microsoft Azure**. W obszarze **Zaloguj się do swojego konta**postępuj zgodnie z instrukcjami.

1. W **Eksplorator serwera**wybierz pozycję **Azure** > **Data Lake Analytics**. Zobaczysz listę swoich kont usługi Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Pisanie pierwszego skryptu U-SQL

Poniższy tekst to prosty skrypt U-SQL. Definiuje on mały zestaw danych i zapisuje go w domyślnym magazynie Data Lake Store jako plik o nazwie `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Przesyłanie zadania usługi Data Lake Analytics

1. W programie Visual Studio, wybierz **pliku** > **New** > **projektu**.

1. Wybierz typ **projektu U-SQL** , a następnie wybierz przycisk **dalej**. W obszarze **Konfigurowanie nowego projektu**wybierz pozycję **Utwórz**.

   Program Visual Studio tworzy rozwiązanie, które zawiera plik **Script. usql** .

1. Wklej skrypt, [pisząc swój pierwszy skrypt U-SQL](#write-your-first-u-sql-script) do okna **Script. usql** .

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Script. usql**, a następnie wybierz polecenie **Prześlij skrypt**.

1. W obszarze **Prześlij zadanie**wybierz konto Data Lake Analytics i wybierz pozycję **Prześlij**.

   ![Przesyłanie projektu U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Po przesłaniu zadania zostanie otwarta karta **Widok zadania**, na której widoczny będzie postęp zadania.

* Karta **Podsumowanie zadania** zawiera podsumowanie zadania.
* Karta **Graf zadania** wizualizuje postęp zadania.
* Karta **Operacje dotyczące metadanych** pokazuje wszystkie akcje, które zostały wykonane na wykazie języka U-SQL.
* Karta **Dane** przedstawia wszystkie dane wejściowe i wyjściowe.
* Karta **Historia stanu** zawiera szczegóły osi czasu i stanu.
* **Analiza au** pokazuje, ile jednostek określania zostało użytych w zadaniu i eksploruje symulacje różnych strategii alokacji au.
* Karta **Diagnostyka** udostępnia zaawansowaną analizę wykonywania zadania i optymalizacji wydajności.

![Wykres wydajności zadania skryptu U-SQL programu Visual Studio w usłudze Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Aby wyświetlić najnowszy stan zadania i odświeżyć ekran, wybierz pozycję **Odśwież**.

## <a name="check-job-status"></a>Sprawdzanie stanu zadania

1. W **Eksplorator serwera**wybierz pozycję **Azure** > **Data Lake Analytics**.

1. Rozwiń nazwę konta usługi Data Lake Analytics.

1. Kliknij dwukrotnie pozycję **Zadania**.

1. Wybierz wcześniej przesłane zadanie.

## <a name="see-the-job-output"></a>Wyświetlanie danych wyjściowych zadania

1. W **Eksplorator serwera**przejdź do przesłanego zadania.

1. Kliknij kartę **Dane**.

1. Na karcie **Dane wyjściowe zadania** wybierz plik `"/data.csv"`.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie skryptów U-SQL na swojej stacji roboczej w celu testowania i debugowania](data-lake-analytics-data-lake-tools-local-run.md)
* [Debugowanie kodu w języku C# w zadaniach U-SQL za pomocą narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Korzystanie z narzędzi Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
