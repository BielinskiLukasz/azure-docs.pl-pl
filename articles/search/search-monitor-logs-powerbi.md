---
title: Wizualizuj dzienniki i metryki usługi Azure Wyszukiwanie poznawcze przy użyciu Power BI
description: Wizualizuj dzienniki i metryki usługi Azure Wyszukiwanie poznawcze przy użyciu Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 2caf4c66cdbb11ed42c4131ef1584c372ec10332
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935113"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Wizualizuj dzienniki i metryki usługi Azure Wyszukiwanie poznawcze przy użyciu Power BI
Usługa [azure wyszukiwanie poznawcze](./search-what-is-azure-search.md) umożliwia przechowywanie dzienników operacji i metryk usług dotyczących usługi wyszukiwania na koncie usługi Azure Storage. Ta strona zawiera instrukcje dotyczące sposobu wizualizacji tych informacji za pomocą aplikacji Power BI Template. Aplikacja zapewnia szczegółowe informacje o usłudze wyszukiwania, w tym informacje na temat wyszukiwania, indeksowania, operacji i metryk usług.

Możesz znaleźć szablon Power BI App **Wyszukiwanie poznawcze Azure: Analizuj dzienniki i metryki** w [witrynie Power BI Apps Marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Jak rozpocząć pracę z aplikacją

1. Włącz rejestrowanie metryk i zasobów dla usługi wyszukiwania:

    1. Utwórz lub Zidentyfikuj istniejące [konto usługi Azure Storage](../storage/common/storage-account-create.md) , na którym można archiwizować dzienniki
    1. Przejdź do usługi Azure Wyszukiwanie poznawcze w Azure Portal
    1. W sekcji monitorowanie w lewej kolumnie Wybierz pozycję **Ustawienia diagnostyczne** .

        ![Zrzut ekranu przedstawiający sposób wybierania ustawień diagnostycznych w sekcji Monitorowanie usługi Azure Wyszukiwanie poznawcze.](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Wybierz **+ Dodaj ustawienie diagnostyczne**
    1. Sprawdź **Archiwum na koncie magazynu**, podaj informacje o koncie magazynu i sprawdź **OperationLogs** i **AllMetrics**

        ![Zrzut ekranu przedstawiający sposób wybierania metryk i rejestrowania zasobów na stronie ustawień diagnostycznych.](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Wybierz pozycję **Zapisz**

1. Po włączeniu rejestrowania Użyj usługi wyszukiwania, aby rozpocząć generowanie dzienników i metryk. Trwa to godzinę, zanim kontenery pojawią się w magazynie obiektów BLOB za pomocą tych dzienników. Zobaczysz kontener **Insights-Logs-operationlogs** dla dzienników ruchu wyszukiwania i kontenerów **Insights-Metrics-pt1m** for Metrics.

1. Znajdź aplikację Power BI Wyszukiwanie poznawcze platformy Azure w [witrynie marketplace Power BI Apps](https://appsource.microsoft.com/marketplace/apps) i zainstaluj ją w nowym obszarze roboczym lub istniejącym obszarze roboczym. Aplikacja jest nazywana **platformą Azure wyszukiwanie poznawcze: Analizuj dzienniki i metryki**.

1. Po zainstalowaniu aplikacji wybierz aplikację z listy aplikacji w Power BI.

    ![Zrzut ekranu przedstawiający aplikację Wyszukiwanie poznawcze platformy Azure do wybrania z listy aplikacji.](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Wybierz pozycję **Połącz** , aby połączyć swoje dane

    ![Zrzut ekranu przedstawiający sposób nawiązywania połączenia z danymi w aplikacji Wyszukiwanie poznawcze platformy Azure.](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Wprowadź nazwę konta magazynu zawierającego dzienniki i metryki. Domyślnie aplikacja będzie wyglądać od ostatnich 10 dni, ale tę wartość można zmienić za pomocą parametru **Days** .

    ![Zrzut ekranu przedstawiający sposób wprowadzania nazwy konta magazynu i liczby dni do wykonania zapytania na stronie łączenie z usługą Azure Wyszukiwanie poznawcze.](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Wybierz **klucz** jako metodę uwierzytelniania i Podaj klucz konta magazynu. Wybierz pozycję **prywatny** jako poziom prywatności. Kliknij przycisk Zaloguj i rozpocznij proces ładowania.

    ![Zrzut ekranu pokazujący, jak wprowadzić metodę uwierzytelniania, klucz konta i poziom prywatności na stronie Połącz z usługą Azure Wyszukiwanie poznawcze.](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Poczekaj na odświeżenie danych. Może to potrwać pewien czas w zależności od ilości posiadanych danych. Możesz sprawdzić, czy dane nadal są odświeżane na podstawie poniższego wskaźnika.

    ![Zrzut ekranu przedstawiający sposób odczytywania informacji na stronie odświeżanie danych.](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Po zakończeniu odświeżania danych wybierz pozycję **Raport usługi Azure wyszukiwanie poznawcze** , aby wyświetlić raport.

    ![Zrzut ekranu przedstawiający sposób wybierania raportu usługi Azure Wyszukiwanie poznawcze na stronie odświeżanie danych.](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Pamiętaj, aby odświeżyć stronę po otwarciu raportu, aby otworzyć go wraz z danymi.

    ![Zrzut ekranu przedstawiający raport Power BI Wyszukiwanie poznawcze platformy Azure.](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Jak zmienić parametry aplikacji
Jeśli chcesz wizualizować dane z innego konta magazynu lub zmienić liczbę dni, które mają być zapytania, wykonaj poniższe kroki, aby zmienić parametry **dni** i **StorageAccount** .

1. Przejdź do aplikacji Power BI, Znajdź aplikację Azure Wyszukiwanie poznawcze i wybierz przycisk **Edytuj aplikację** , aby wyświetlić obszar roboczy.

    ![Zrzut ekranu przedstawiający sposób wybierania przycisku Edytuj aplikację dla aplikacji Wyszukiwanie poznawcze platformy Azure.](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Wybierz pozycję **Ustawienia** z opcji zestaw danych.

    ![Zrzut ekranu przedstawiający sposób wybierania ustawień z opcji zestawu danych usługi Azure Congnitive Search.](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Na karcie zestawy danych Zmień wartości parametrów i wybierz pozycję **Zastosuj**. Jeśli wystąpił problem z połączeniem, zaktualizuj poświadczenia źródła danych na tej samej stronie.

1. Wróć do obszaru roboczego i wybierz pozycję **Odśwież teraz** z poziomu opcji zestawu danych.

    ![Zrzut ekranu przedstawiający sposób wybierania opcji Odśwież teraz w opcjach zestawu danych usługi Azure Congnitive Search.](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Otwórz raport, aby wyświetlić zaktualizowane dane. Może być również konieczne odświeżenie raportu w celu wyświetlenia najnowszych danych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli okaże się, że dane nie są widoczne, wykonaj następujące kroki rozwiązywania problemów:

1. Otwórz raport i Odśwież stronę, aby upewnić się, że oglądasz najnowsze dane. Istnieje możliwość odświeżenia danych w raporcie. Wybierz tę opcję, aby uzyskać najnowsze dane.

1. Upewnij się, że podana nazwa konta magazynu i klucz dostępu są poprawne. Nazwa konta magazynu powinna być zgodna z kontem skonfigurowanym na potrzeby dzienników usługi wyszukiwania.

1. Upewnij się, że konto magazynu zawiera informacje o kontenerach **Insights-Logs-operationlogs** i **Insights-Metrics-pt1m** i każdy kontener zawiera dane. Dzienniki i metryki będą znajdować się w kilku warstwach folderów.

1. Sprawdź, czy zestaw danych nadal jest odświeżany. Wskaźnik stanu odświeżania jest przedstawiony w kroku 8 powyżej. Jeśli nadal trwa odświeżanie, poczekaj na zakończenie odświeżania, aby otworzyć i odświeżyć raport.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o usłudze Azure Wyszukiwanie poznawcze](./index.yml)

[Co to jest usługa Power BI?](/power-bi/fundamentals/power-bi-overview)

[Podstawowe pojęcia dla projektantów w usłudze Power BI](/power-bi/service-basic-concepts)