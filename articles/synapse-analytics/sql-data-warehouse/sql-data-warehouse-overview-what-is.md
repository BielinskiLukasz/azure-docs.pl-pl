---
title: Co to jest Azure Synapse Analytics (dawniej SQL DW)?
description: Azure Synapse Analytics (dawniej SQL DW) to nieograniczona Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8840791c7b18d1efa499c2826a6eaf041a6da787
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317477"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Co to jest Azure Synapse Analytics (dawniej SQL DW)?

> [!NOTE]
>Zapoznaj się z [dokumentacją platformy Azure Synapse (wersja zapoznawcza)](../overview-what-is.md).
>

Azure Synapse to Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data. Zapewnia swobodę wykonywania zapytań dotyczących danych na Twoich warunkach, korzystając z bezserwerowych zasobów na żądanie lub aprowizowanych zasobów — w dużej skali. Usługa Azure Synapse łączy te dwa światy w ujednolicone środowisko umożliwiające pozyskiwanie, przygotowywanie i udostępnianie danych oraz zarządzanie nimi na potrzeby natychmiastowej analizy biznesowej oraz uczenia maszynowego.

Usługa Azure Synapse ma cztery składniki:

- Synapse SQL: Pełna analiza oparta na języku T-SQL — ogólnie dostępna
  - Dedykowana Pula SQL (płatność za jednostek dwu z obsługą administracyjną)
  - Bezserwerowa Pula SQL (płatność za 1 TB) (wersja zapoznawcza)
- Spark: głębokie zintegrowane Apache Spark (wersja zapoznawcza)
- Potoki Synapse: integracja danych hybrydowych (wersja zapoznawcza)
- Studio: ujednolicone środowisko użytkownika. (wersja zapoznawcza)

## <a name="dedicated-sql-pool-in-azure-synapse"></a>Dedykowana Pula SQL na platformie Azure Synapse

Dedykowana Pula SQL odwołuje się do funkcji magazynowania danych przedsiębiorstwa, które są ogólnie dostępne w usłudze Azure Synapse.

Dedykowana Pula SQL reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z programu Synapse SQL. Rozmiar dedykowanej puli SQL jest określany przez jednostki magazynowania danych (jednostek dwu).

Zaimportuj dane Big Data za [pomocą prostych zapytań](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL, a następnie użyj możliwości aparatu zapytań rozproszonych, aby uruchomić analizę o wysokiej wydajności. Po zintegrowaniu i przeanalizowaniu danych Synapse SQL stanie się jedną wersją prawdy, na którą firma może liczyć, aby szybciej i bardziej niezawodnie uzyskiwać szczegółowe informacje. 

## <a name="key-component-of-a-big-data-solution"></a>Kluczowy składnik rozwiązania danych Big Data

Magazynowanie danych to kluczowy składnik oparty na chmurze, kompleksowe rozwiązanie do obsługi danych Big Data.

![Rozwiązanie magazynu danych](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

W rozwiązaniu danych w chmurze dane są pozyskiwane do magazynów danych big data z wielu różnych źródeł. Kiedy już znajdą się w magazynie danych big data, algorytmy platform Hadoop, Spark i uczenia maszynowego przygotowują i trenują dane. Gdy dane są gotowe do złożonej analizy, dedykowana Pula SQL używa bazy danych do wykonywania zapytań dotyczących magazynów Big Data. Baza kodu używa standardowych zapytań T-SQL do przenoszenia danych do tabel dedykowanej puli SQL.

Dedykowana Pula SQL przechowuje dane w tabelach relacyjnych z magazynem kolumnowym. Ten format znacznie zmniejsza koszty przechowywania danych i poprawia wydajność zapytań. Gdy dane znajdują się w magazynie, można wykonywać analizy na wielką skalę. W porównaniu do tradycyjnych systemów baz danych zapytania analizy wykonywane są w ciągu kilku sekund (zamiast kilku minut) lub kilku godzin (zamiast kilku dni).

Wyniki analizy można odnieść do ogólnoświatowych baz danych raportowania lub aplikacji. Analitycy biznesowi mogą zatem uzyskiwać wyniki analizy w celu świadomego podejmowania decyzji biznesowych.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę usługi Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [Tworzenie dedykowanej puli SQL](create-data-warehouse-portal.md)
- [Ładowanie przykładowych danych](load-data-from-azure-blob-storage-using-polybase.md).
- Eksploruj [wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Lub zapoznaj się z innymi zasobami usługi Azure Synapse.

- Wyszukaj [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- Prześlij [żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
- Wyszukiwanie [w witrynie Microsoft Q&stronie pytania](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Forum wyszukiwania Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
