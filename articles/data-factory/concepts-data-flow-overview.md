---
title: Przepływy danych mapowania
description: Omówienie mapowania przepływów danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243798"
---
# <a name="what-are-mapping-data-flows"></a>Czym są przepływy danych mapowania?

Przepływy danych mapowania są wizualnie zaprojektowane przekształcenia danych w usłudze Azure Data Factory. Przepływy danych umożliwiają inżynierom danych opracowanie logiki przekształcania danych graficznych bez pisania kodu. Wynikowe przepływy danych są wykonywane jako działania w potokach usługi Azure Data Factory, które używają skalowane w poziomie klastrów platformy Spark. Działania przepływu danych można zudralizować za pomocą istniejących funkcji planowania, sterowania, przepływu i monitorowania.

Mapowanie przepływów danych zapewnia w pełni wizualne środowisko bez konieczności kodowania. Przepływy danych będą uruchamiane we własnym klastrze wykonywania w celu skalowania w poziomie przetwarzania danych. Usługa Azure Data Factory obsługuje wszystkie translacji kodu, optymalizacji ścieżki i wykonywania zadań przepływu danych.

## <a name="getting-started"></a>Wprowadzenie

Aby utworzyć przepływ danych, wybierz znak plus w obszarze **Zasoby fabryczne,** a następnie wybierz pozycję **Przepływ danych**. 

![Nowy przepływ danych](media/data-flow/newdataflow2.png "nowy przepływ danych")

Spowoduje to przejście do obszaru roboczego przepływu danych, gdzie można utworzyć logikę transformacji. Wybierz **pozycję Dodaj źródło,** aby rozpocząć konfigurowanie transformacji źródła. Aby uzyskać więcej informacji, zobacz [Transformacja źródła](data-flow-source.md).

## <a name="data-flow-canvas"></a>Kanwa przepływu danych

Obszar roboczy przepływu danych jest podzielony na trzy części: górny pasek, wykres i panel konfiguracji. 

![Kanwa](media/data-flow/canvas1.png "Kanwa")

### <a name="graph"></a>Graph

Wykres wyświetla strumień transformacji. Pokazuje rodowód danych źródłowych, jak przepływa do jednego lub więcej pochłaniacze. Aby dodać nowe źródło, wybierz pozycję **Dodaj źródło**. Aby dodać nową transformację, wybierz znak plus w prawym dolnym poł.

![Kanwa](media/data-flow/canvas2.png "Kanwa")

### <a name="azure-integration-runtime-data-flow-properties"></a>Właściwości przepływu danych środowiska uruchomieniowego integracji platformy Azure

![Przycisk Debugowanie](media/data-flow/debugbutton.png "Przycisk Debugowanie")

Po rozpoczęciu pracy z przepływami danych w ujarzmieniu ADF należy włączyć przełącznik "Debug" dla przepływów danych w górnej części interfejsu użytkownika przeglądarki. Spowoduje to utworzenie klastra usługi Azure Databricks do użycia do interaktywnego debugowania, podglądu danych i wykonywania debugowania potoku. Rozmiar klastra można ustawić, wybierając niestandardowy [środowisko uruchomieniowe integracji platformy Azure.](concepts-integration-runtime.md) Sesja debugowania pozostanie przy życiu przez maksymalnie 60 minut po ostatnim podglądzie danych lub ostatnim wykonaniu potoku debugowania.

Podczas operacjonalizacji potoków za pomocą działań przepływu danych, usługa ADF użyje środowiska uruchomieniowego integracji platformy Azure skojarzonego z [działaniem](control-flow-execute-data-flow-activity.md) we właściwości "Uruchom włączone".

Domyślny środowiska uruchomieniowego integracji platformy Azure to mały 4-rdzeniowy klaster węzłów pojedynczego procesu roboczego, który umożliwia wyświetlanie podglądu danych i szybkie wykonywanie potoków debugowania przy minimalnych kosztach. Ustaw większą konfigurację podczerwania platformy Azure, jeśli wykonujesz operacje względem dużych zestawów danych.

Można poinstruować adf do obsługi puli zasobów klastra (VMs) przez ustawienie czasu wygaśnięcia we właściwościach przepływu danych usługi Azure IR. Spowoduje to szybsze wykonywanie zadań w kolejnych działaniach.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Strategie środowiska wykonawczego i przepływu danych integracji platformy Azure

##### <a name="execute-data-flows-in-parallel"></a>Równoległe wykonywanie przepływów danych

Jeśli wykonujesz przepływy danych w potoku równolegle, usługa ADF spowoduje utworzenie oddzielnych klastrów usługi Azure Databricks dla każdego wykonania działania na podstawie ustawień w środowisku wykonawczym integracji platformy Azure dołączonych do każdego działania. Aby zaprojektować równoległe wykonania w potokach usługi ADF, dodaj działania przepływu danych bez ograniczeń pierwszeństwa w interfejsie użytkownika.

Z tych trzech opcji ta opcja prawdopodobnie zostanie wykonana w jak najkrótszym czasie. Jednak każdy równoległy przepływ danych będzie wykonywany w tym samym czasie w oddzielnych klastrach, więc kolejność zdarzeń jest niedeterminowa.

Jeśli wykonujesz działania przepływu danych równolegle wewnątrz potoków, zaleca się, aby nie używać czasu wygaśnięcia. Jest to spowodowane równoległe wykonanie przepływów danych jednocześnie przy użyciu tego samego środowiska uruchomieniowego integracji platformy Azure spowoduje wiele wystąpień ciepłej puli dla fabryki danych.

##### <a name="overload-single-data-flow"></a>Przeciążenie pojedynczego przepływu danych

Jeśli umieścisz całą logikę wewnątrz pojedynczego przepływu danych, ADF będzie wykonywany w tym samym kontekście wykonywania zadania w jednym wystąpieniu klastra platformy Spark.

Ta opcja może być prawdopodobnie trudniejsze do naśladowania i rozwiązywania problemów, ponieważ reguły biznesowe i logiki biznesowej będzie mieszać razem. Ta opcja również nie zapewnia wiele ponownego użycia.

##### <a name="execute-data-flows-serially"></a>Wykonywanie przepływów danych szeregowo

Jeśli wykonasz działania przepływu danych w potoku szeregowym i ustawisz czas wygaśnięcia w konfiguracji usługi Azure IR, a następnie ADF ponownie użyć zasobów obliczeniowych (VMs) w wyniku szybszego kolejnych czasów wykonywania. Nadal będziesz otrzymywać nowy kontekst platformy Spark dla każdego wykonania.

Z tych trzech opcji, będzie to prawdopodobnie trwać najdłużej, aby wykonać end-to-end. Ale zapewnia czyste oddzielenie operacji logicznych w każdym kroku przepływu danych.

### <a name="configuration-panel"></a>Panel konfiguracji

Panel konfiguracji pokazuje ustawienia specyficzne dla aktualnie wybranej transformacji. Jeśli nie jest zaznaczona żadna transformacja, pokazuje przepływ danych. W ogólnej konfiguracji przepływu danych można edytować nazwę i opis na karcie **Ogólne** lub dodawać parametry za pomocą karty **Parametry.** Aby uzyskać więcej informacji, zobacz [Mapowanie parametrów przepływu danych](parameters-data-flow.md).

Każda transformacja ma co najmniej cztery karty konfiguracji.

#### <a name="transformation-settings"></a>Ustawienia transformacji

Pierwsza karta w okienku konfiguracji każdej transformacji zawiera ustawienia specyficzne dla tej transformacji. Aby uzyskać więcej informacji, zobacz stronę dokumentacji tej transformacji.

![Karta Ustawienia źródła](media/data-flow/source1.png "Karta Ustawienia źródła")

#### <a name="optimize"></a>Optymalizacja

Karta **Optymalizacja** zawiera ustawienia konfigurowania schematów partycjonowania.

![Optymalizacji](media/data-flow/optimize1.png "Optymalizacja")

Domyślnym ustawieniem jest **Użyj bieżącej partycjonowania**, który nakazuje usługi Azure Data Factory używać schematu partycjonowania natywnego do przepływów danych uruchomionych w programie Spark. W większości scenariuszy zaleca się to ustawienie.

Istnieją przypadki, w których można dostosować partycjonowanie. Na przykład jeśli chcesz wyprowadzić przekształcenia do pojedynczego pliku w jeziorze, wybierz **pojedynczą partycję** w transformacji ujścia.

Innym przypadkiem, w którym można kontrolować schematy partycjonowania jest optymalizacja wydajności. Dostosowanie partycjonowania zapewnia kontrolę nad dystrybucją danych między węzłami obliczeniowymi i optymalizacjami lokalizacji danych, które mogą mieć zarówno pozytywny, jak i negatywny wpływ na ogólną wydajność przepływu danych. Aby uzyskać więcej informacji, zobacz [przewodnik po wydajności przepływu danych](concepts-data-flow-performance.md).

Aby zmienić partycjonowanie w dowolnej transformacji, wybierz kartę **Optymalizuj** i wybierz przycisk opcji **Ustaw partycjonowanie.** Następnie zostanie wyświetlony szereg opcji partycjonowania. Najlepsza metoda partycjonowania będzie się różnić w zależności od woluminów danych, kluczy kandydujących, wartości null i kardynalność. 

Najlepszym rozwiązaniem jest, aby rozpocząć od domyślnego partycjonowania, a następnie spróbuj różnych opcji partycjonowania. Można przetestować przy użyciu przebiegów debugowania potoku i wyświetlić czas wykonywania i użycie partycji w każdej grupowania transformacji z widoku monitorowania. Aby uzyskać więcej informacji, zobacz [Monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

Dostępne są następujące opcje partycjonowania.

##### <a name="round-robin"></a>Okrężnego 

Działanie okrężne to prosta partycja, która automatycznie dystrybuuje dane w równym stopniu między partycjami. Użyj okrężnego, gdy nie masz dobrych kluczowych kandydatów do wdrożenia solidnej, inteligentnej strategii partycjonowania. Można ustawić liczbę partycji fizycznych.

##### <a name="hash"></a>Skrót

Usługa Azure Data Factory spowoduje powstanie skrótu kolumn do tworzenia jednolitych partycji, tak aby wiersze o podobnych wartościach spadły w tej samej partycji. W przypadku użycia opcji Hash, sprawdź, czy możliwe pochylenie partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="dynamic-range"></a>Zakres dynamiczny

Zakres dynamiczny będzie używał zakresów dynamicznych platformy Spark na podstawie kolumn lub wyrażeń, które podajesz. Można ustawić liczbę partycji fizycznych. 

##### <a name="fixed-range"></a>Stały zasięg

Tworzenie wyrażenia, które zapewnia stały zakres wartości w kolumnach danych podzielonych na partycje. Aby uniknąć pochylenia partycji, przed użyciem tej opcji należy dobrze zrozumieć dane. Wartości wprowadzone dla wyrażenia będą używane jako część funkcji partycji. Można ustawić liczbę partycji fizycznych.

##### <a name="key"></a>Klucz

Jeśli masz dobre zrozumienie kardynalności danych, partycjonowanie kluczy może być dobrą strategią. Partycjonowanie kluczy utworzy partycje dla każdej unikatowej wartości w kolumnie. Nie można ustawić liczby partycji, ponieważ liczba będzie oparta na unikatowych wartościach w danych.

#### <a name="inspect"></a>Sprawdzić

Karta **Inspekcja** udostępnia widok do metadanych strumienia danych, który przekształcasz. Możesz zobaczyć liczby kolumn, zmienione kolumny, dodane kolumny, typy danych, kolejność kolumn i odwołania do kolumn. **Inspekcja** to widok metadanych tylko do odczytu. Nie trzeba mieć włączony tryb debugowania, aby wyświetlić metadane w okienku **Sprawdź.**

![Sprawdzić](media/data-flow/inspect1.png "Sprawdzić")

Jak zmienić kształt danych za pomocą przekształceń, zobaczysz przepływ zmian metadanych w **okienku Inspekcja.** Jeśli nie ma zdefiniowanego schematu w transformacji źródła, metadane nie będą widoczne w **okienku Sprawdź.** Brak metadanych jest powszechne w scenariuszach dryfu schematu.

#### <a name="data-preview"></a>Podgląd danych

Jeśli tryb debugowania jest włączony, karta **Podgląd danych** udostępnia interaktywną migawkę danych przy każdej transformacji. Aby uzyskać więcej informacji, zobacz [Podgląd danych w trybie debugowania](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Górny pasek

Górny pasek zawiera akcje, które wpływają na cały przepływ danych, takie jak zapisywanie i sprawdzanie poprawności. Można również przełączać się między trybami wykresu i konfiguracji za pomocą przycisków **Pokaż wykres** i **Ukryj wykres.**

![Ukryj wykres](media/data-flow/hideg.png "Ukryj wykres")

Jeśli ukryjesz wykres, możesz przeglądać węzły transformacji bocznie za pomocą przycisków **Poprzedni** i **Następny.**

![Poprzednie i następne przyciski](media/data-flow/showhide.png "poprzednie i następne przyciski")

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [transformację źródłową](data-flow-source.md).
* Dowiedz się, jak tworzyć przepływy danych w [trybie debugowania](concepts-data-flow-debug-mode.md).
