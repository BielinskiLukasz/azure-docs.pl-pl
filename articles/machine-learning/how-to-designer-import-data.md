---
title: Importowanie danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak importować dane do programu Azure Machine Learning Designer z różnych źródeł danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 5df00d68b7114cc489b0708f5c2e401c87d0be15
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314032"
---
# <a name="import-your-data-into-azure-machine-learning-designer"></a>Importowanie danych do programu Azure Machine Learning Designer

W tym artykule dowiesz się, jak zaimportować własne dane do projektanta w celu utworzenia niestandardowych rozwiązań. Istnieją dwa sposoby importowania danych do projektanta: 

* **Azure Machine Learning zestawy danych** — umożliwia rejestrowanie [zestawów danych](concept-data.md#datasets) w programie Azure Machine Learning, aby umożliwić zaawansowane funkcje, które ułatwiają zarządzanie danymi.
* **Importuj moduł danych** — Użyj modułu [Import danych](algorithm-module-reference/import-data.md) , aby uzyskać bezpośredni dostęp do danych ze źródeł DataSources.

## <a name="use-azure-machine-learning-datasets"></a>Korzystanie z Azure Machine Learning zestawów danych

Zalecamy użycie [zestawów](concept-data.md#datasets) danych w celu zaimportowania ich do projektanta. Po zarejestrowaniu zestawu danych można w pełni korzystać z zaawansowanych funkcji danych, takich jak [przechowywanie wersji i śledzenie](how-to-version-track-datasets.md) i [monitorowanie danych](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Rejestrowanie zestawu danych

Istnieje możliwość rejestrowania istniejących zestawów danych [programowo za pomocą zestawu SDK](how-to-create-register-datasets.md#use-the-sdk) lub [wizualizacji w programie Azure Machine Learning Studio](how-to-create-register-datasets.md#use-the-ui).

Możesz również zarejestrować dane wyjściowe dla dowolnego modułu projektanta jako zestaw danych.

1. Wybierz moduł, który wyprowadza dane, które chcesz zarejestrować.

1. W okienku właściwości wybierz pozycję dane **wyjściowe** > **zarejestruj zestaw danych**.

    ![Zrzut ekranu przedstawiający sposób nawigowania do opcji Zarejestruj zestaw danych](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych

Zarejestrowane zestawy danych można znaleźć w palecie modułów w obszarze **zestawy danych** > **Moje zestawy danych**. Aby użyć zestawu danych, przeciągnij i upuść go na kanwie potoku. Następnie połącz port wyjściowy zestawu danych z innymi modułami w palecie.

![Zrzut ekranu przedstawiający lokalizację zapisanych zestawów danych w palecie projektanta](media/how-to-designer-import-data/use-datasets-designer.png)

Każdy [zestaw danych pliku](how-to-create-register-datasets.md#dataset-types) zarejestrowany w obszarze roboczym uczenia maszynowego zostanie wyświetlony w palecie modułu. Nie można używać zestawów danych utworzonych w projektancie.

> [!NOTE]
> Projektant obecnie obsługuje tylko przetwarzanie [tabelarycznych zestawów danych](how-to-create-register-datasets.md#dataset-types). Jeśli chcesz użyć [zestawów danych plików](how-to-create-register-datasets.md#dataset-types), użyj zestawu SDK Azure Machine Learning dostępnego dla języków Python i R.

## <a name="import-data-using-the-import-data-module"></a>Importowanie danych przy użyciu modułu Importuj dane

Chociaż zalecamy używanie zestawów danych do importowania danych, można również użyć modułu [Importuj dane](algorithm-module-reference/import-data.md) . Moduł Importuj dane Pomija rejestrowanie zestawu danych w Azure Machine Learning i importuje dane bezpośrednio z [magazynu](concept-data.md#datastores) danych lub adresu URL http.

Aby uzyskać szczegółowe informacje na temat korzystania z modułu Importuj dane, zobacz [stronę Importowanie danych źródłowych](algorithm-module-reference/import-data.md).


## <a name="supported-sources"></a>Obsługiwane źródła

Ta sekcja zawiera listę źródeł danych obsługiwanych przez projektanta. Dane wchodzą w skład projektanta z magazynu danych lub z [tabelarycznego zestawu](how-to-create-register-datasets.md#dataset-types)obiektów.

### <a name="datastore-sources"></a>Źródła danych
Listę obsługiwanych źródeł danych można znaleźć [w temacie Access Data (dostęp do usługi Azure Storage](how-to-access-data.md#supported-data-storage-service-types)).

### <a name="tabular-dataset-sources"></a>Tabelaryczne źródła danych

Projektant obsługuje tabelaryczne zestawy danych utworzone na podstawie następujących źródeł:
 * Rozdzielane pliki
 * Pliki JSON
 * Pliki Parquet
 * Zapytania SQL

## <a name="data-types"></a>Typy danych

Projektant wewnętrznie rozpoznaje następujące typy danych:

* Ciąg
* Liczba całkowita
* Decimal
* Wartość logiczna
* Data

Projektant używa wewnętrznego typu danych do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych przy użyciu modułu [Konwertuj na zestaw](algorithm-module-reference/convert-to-dataset.md) danych. Każdy moduł, który akceptuje formaty inne niż format wewnętrzny, przekonwertuje dane w trybie dyskretnym przed przekazaniem go do następnego modułu.

## <a name="data-constraints"></a>Ograniczenia danych

Moduły w projektancie są ograniczone przez rozmiar obiektu docelowego obliczeń. W przypadku większych zestawów danych należy użyć większego Azure Machine Learning zasobów obliczeniowych. Aby uzyskać więcej informacji na temat Azure Machine Learning obliczeń, zobacz [co to są cele obliczeń w Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Następne kroki

Poznaj podstawowe informacje na temat projektanta, korzystając z [samouczka: przewidywanie ceny za aplikacje dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md).