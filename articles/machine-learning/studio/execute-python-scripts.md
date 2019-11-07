---
title: Wykonywanie skryptów w języku Python
description: Dowiedz się, jak używać modułu skryptu języka Python do używania kodu Python w Machine Learning Studio (klasyczne) eksperymenty i usługi sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: dea8fd7971f0572350306ea11ee1f9d897438d11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684878"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Wykonywanie skryptów w języku Python Machine Learning w Azure Machine Learning Studio (klasyczny)

Język Python jest cennym narzędziem w obszarze klatki narzędzia wielu analityków danych. Jest on używany w każdym etapie typowych przepływów pracy uczenia maszynowego, takich jak Eksploracja danych, wyodrębnianie funkcji, szkolenia modeli i sprawdzanie poprawności oraz wdrażanie.

W tym artykule opisano, jak można użyć modułu skryptu języka Python do użycia kodu w języku Python w Azure Machine Learning Studio (klasyczny) eksperymenty i usługi sieci Web.

## <a name="using-the-execute-python-script-module"></a>Korzystanie z modułu skryptu języka Python

Interfejs podstawowy do języka Python w programie Studio (klasyczny) polega na użyciu modułu [uruchamiania skryptów języka Python][execute-python-script] . Akceptuje do trzech danych wejściowych i tworzy do dwóch wyjść, podobnie jak moduł [wykonywania skryptu języka R][execute-r-script] . Kod języka Python jest wprowadzany do pola parametru za pomocą specjalnie nazwanej funkcji punktu wejścia o nazwie `azureml_main`.

![Wykonaj moduł skryptu języka Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Przykładowy kod w języku Python w polu parametru modułu](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametry wejściowe

Dane wejściowe modułu języka Python są ujawniane jako Pandas dataframes. Funkcja `azureml_main` akceptuje do dwóch opcjonalnych ramek danych Pandas jako parametry.

Mapowanie między portami wejściowymi i parametrami funkcji jest pozycyjne:

- Pierwszy połączony port wejściowy jest mapowany na pierwszy parametr funkcji.
- Drugie dane wejściowe (jeśli są połączone) są mapowane na drugi parametr funkcji.
- Trzecia wartość wejściowa jest używana do [importowania dodatkowych modułów języka Python](#import-modules).

Poniżej przedstawiono bardziej szczegółową semantykę sposobu, w jaki porty wejściowe są mapowane do parametrów funkcji `azureml_main`.

![Tabela konfiguracji portów wejściowych i wynikający z tego sygnatury języka Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Wyjściowe wartości zwracane

Funkcja `azureml_main` musi zwracać pojedynczą Pandas Dataframe spakowaną w [sekwencji](https://docs.python.org/2/c-api/sequence.html) w języku Python, taką jak krotka, lista lub tablica numpy. Pierwszy element tej sekwencji jest zwracany do pierwszego portu wyjściowego modułu. Drugi port wyjściowy modułu jest używany do [wizualizacji](#visualizations) i nie wymaga wartości zwracanej. Ten schemat jest przedstawiony poniżej.

![Mapowanie portów wejściowych do parametrów i zwracanie wartości do portu wyjściowego](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tłumaczenie typów danych wejściowych i wyjściowych

Zestawy danych programu Studio nie są takie same jak Panda dataframes. W związku z tym wejściowe zestawy danych w klasycznej wersji programu Studio są konwertowane na Pandas Dataframe, a wyjściowe ramki danych są konwertowane z powrotem do zestawu danych programu Studio (klasycznego). W trakcie tego procesu konwersji są również wykonywane następujące tłumaczenia:

 **Typ danych języka Python** | **Studio — procedura tłumaczenia** |
| --- | --- |
| Ciągi i wartości liczbowe| Przetłumaczone jako |
| Pandas "NA" | Przetłumaczone jako "brakująca wartość" |
| Wektory indeksu | Ich |
| Nazwy kolumn niebędących ciągami | Wywołaj `str` w nazwach kolumn |
| Zduplikowane nazwy kolumn | Dodaj sufiks liczbowy: (1), (2), (3) i tak dalej.

**wszystkie wejściowe ramki danych w funkcji języka Python zawsze mają 64-bitowy indeks liczbowy z przedziału od 0 do liczby wierszy pomniejszonych o 1*

## <a id="import-modules"></a>Importowanie istniejących modułów skryptów języka Python

Zaplecze używane do wykonywania języka Python opiera się na [Anaconda](https://www.anaconda.com/distribution/), szeroko używanym do nauki dystrybucji języka Python. Jest on dostępny w pobliżu 200 najpopularniejszych pakietów języka Python używanych w obciążeniu zorientowanym na dane. Klasyczna wersja programu Studio obecnie nie obsługuje korzystania z systemów zarządzania pakietami, takich jak PIP lub Conda, do instalowania bibliotek zewnętrznych i zarządzania nimi.  Jeśli okaże się, że musisz uwzględnić dodatkowe biblioteki, użyj następującego scenariusza jako przewodnika.

Typowym przypadkiem użycia jest dołączenie istniejących skryptów języka Python do klasycznej wersji eksperymentów Studio. Moduł [wykonywania skryptu języka Python][execute-python-script] akceptuje plik zip zawierający moduły Python na trzecim porcie wejściowym. Plik jest rozpakowany przez strukturę wykonywania w czasie wykonywania, a zawartość jest dodawana do ścieżki biblioteki interpretera języka Python. Funkcja punktu wejścia `azureml_main` może następnie importować te moduły bezpośrednio. 

Rozważmy na przykład plik Hello.py zawierający prostą funkcję "Hello, World".

![Funkcja zdefiniowana przez użytkownika w pliku Hello.py](./media/execute-python-scripts/figure4.png)

Następnie tworzymy plik Hello. zip, który zawiera Hello.py:

![Plik zip zawierający kod języka Python zdefiniowany przez użytkownika](./media/execute-python-scripts/figure5.png)

Przekaż plik zip jako zestaw danych do klasycznej wersji programu Studio. Następnie utwórz i uruchom eksperyment, który używa kodu Python w pliku Hello. zip przez dołączenie go do trzeciego portu wejściowego modułu **skryptu języka Python** , jak pokazano na poniższej ilustracji.

![Przykładowy eksperyment z Hello. zip jako dane wejściowe do modułu skryptu języka Python](./media/execute-python-scripts/figure6a.png)

![Zdefiniowany przez użytkownika kod języka Python przekazany jako plik zip](./media/execute-python-scripts/figure6b.png)

Dane wyjściowe modułu pokazują, że plik zip został usunięty z pakietu i że funkcja `print_hello` została uruchomiona.

![Dane wyjściowe modułu pokazujące funkcję zdefiniowaną przez użytkownika](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Uzyskiwanie dostępu do obiektów BLOB usługi Azure Storage

Możesz uzyskać dostęp do danych przechowywanych na koncie usługi Azure Blob Storage, wykonując następujące czynności:

1. Pobierz [pakiet BLOB Storage platformy Azure dla języka Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalnie.
1. Przekaż plik zip do obszaru roboczego programu Studio (klasycznego) jako zestaw danych.
1. Utwórz obiekt BlobService za pomocą `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Na karcie Ustawienia **konfiguracji** magazynu wymagane jest wyłączenie **bezpiecznego transferu**

![Należy wyłączyć bezpieczny transfer wymagany w Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacjonalizowania skrypty języka Python

Wszystkie moduły [uruchamiania skryptów języka Python][execute-python-script] używane w eksperymentie oceniania są wywoływane, gdy są publikowane jako usługa sieci Web. Na przykład na poniższym obrazie przedstawiono eksperyment oceniania zawierający kod służący do oceny pojedynczego wyrażenia w języku Python.

![Obszar roboczy programu Studio dla usługi sieci Web](./media/execute-python-scripts/figure3a.png)

![Wyrażenie Pandas języka Python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Usługa sieci Web utworzona na podstawie tego eksperymentu zajmie następujące działania:

1. Wypełnij wyrażenie języka Python jako dane wejściowe (jako ciąg)
1. Wyślij wyrażenie języka Python do interpretera języka Python
1. Zwraca tabelę zawierającą zarówno wyrażenie, jak i obliczony wynik.

## <a id="visualizations"></a>Praca z wizualizacjami

Wykresy utworzone za pomocą MatplotLib mogą być zwracane przez [skrypt wykonywania skryptu języka Python][execute-python-script]. Jednak wykresy nie są automatycznie przekierowywane do obrazów, ponieważ są używane w języku R. Dlatego użytkownik musi jawnie zapisać wszystkie wykresy w plikach PNG.

Aby generować obrazy z MatplotLib, należy wykonać następujące czynności:

1. Przełącz zaplecze do "AGG" z domyślnego modułu renderowania opartego na przełączniku QT.
1. Utwórz nowy obiekt rysunku.
1. Pobierz oś i Wygeneruj na niej wszystkie wykresy.
1. Zapisz rysunek w pliku PNG.

Ten proces jest przedstawiony w poniższych obrazach, które tworzą macierz punktową, przy użyciu funkcji scatter_matrix w Pandas.

![Kod umożliwiający zapisanie MatplotLib rysunków do obrazów](./media/execute-python-scripts/figure-v1-8.png)

![Kliknij pozycję Wizualizuj w module wykonaj skrypt języka Python, aby wyświetlić dane](./media/execute-python-scripts/figure-v2-9a.png)

![Wizualizacja wykresów na potrzeby przykładowego eksperymentu przy użyciu kodu języka Python](./media/execute-python-scripts/figure-v2-9b.png)

Istnieje możliwość zwrócenia wielu cyfr przez zapisanie ich w różnych obrazach. Klasyczna wersja środowiska uruchomieniowego Studio pobiera wszystkie obrazy i łączy je w celu wizualizacji.

## <a name="advanced-examples"></a>Zaawansowane przykłady

Środowisko Anaconda zainstalowane w klasycznej wersji programu Studio zawiera wspólne pakiety, takie jak NumPy, SciPy i Scikits — uczenie się. Te pakiety mogą być efektywnie używane do przetwarzania danych w potoku uczenia maszynowego.

Na przykład następujące eksperymenty i skrypty ilustrują korzystanie z wieloskładnikowych informacji w Scikits — Dowiedz się więcej na temat oceny ważności funkcji obliczeniowej dla zestawu danych. Wyniki mogą być używane do przeprowadzania nadzorowanego wyboru funkcji przed wprowadzeniem ich do innego modelu.

Oto funkcja języka Python użyta do obliczenia oceny ważności i uporządkowania funkcji na podstawie wyników:

![Funkcja do rangi funkcji według wyników](./media/execute-python-scripts/figure8.png)

Poniższy eksperyment następnie obliczy i zwróci wyniki ważności funkcji w zestawie danych "Pima indyjski cukrzycą" w klasycznej wersji Azure Machine Learning Studio:

![Eksperymentowanie z funkcjami rangi w Pima indyjskich zestawów danych cukrzycą przy użyciu języka Python](./media/execute-python-scripts/figure9a.png)

![Wizualizacja danych wyjściowych modułu skryptu języka Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Ograniczenia

Moduł [wykonywania skryptu języka Python][execute-python-script] ma obecnie następujące ograniczenia:

### <a name="sandboxed-execution"></a>Wykonywanie w trybie piaskownicy

Środowisko uruchomieniowe języka Python jest obecnie w piaskownicy i nie zezwala na dostęp do sieci lub lokalnego systemu plików w sposób trwały. Wszystkie pliki zapisane lokalnie są izolowane i usuwane po zakończeniu działania modułu. Kod języka Python nie może uzyskać dostępu do większości katalogów na komputerze, na którym jest ono uruchomione, wyjątek w bieżącym katalogu i jego podkatalogach.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brak zaawansowanej obsługi programowania i debugowania

Moduł Python obecnie nie obsługuje funkcji środowiska IDE, takich jak IntelliSense i debugowanie. Ponadto, jeśli moduł kończy się niepowodzeniem w czasie wykonywania, dostępny jest pełny ślad stosu języka Python. Ale musi być on wyświetlony w dzienniku wyjściowym modułu. Obecnie zalecamy tworzenie i debugowanie skryptów języka Python w środowisku, takim jak IPython, a następnie zaimportowanie kodu do modułu.

### <a name="single-data-frame-output"></a>Dane wyjściowe pojedynczej ramki danych

Punkt wejścia języka Python może zwrócić pojedynczą ramkę danych jako dane wyjściowe. Obecnie nie jest możliwe zwracanie dowolnych obiektów języka Python, takich jak przeszkolone modele, bezpośrednio z powrotem do środowiska uruchomieniowego Studio (klasycznego). Podobnie jak w przypadku [wykonywania skryptu języka R][execute-r-script], który ma takie samo ograniczenie, można w wielu przypadkach pakietu pickle obiekty do tablicy bajtów, a następnie zwrócić ją wewnątrz ramki danych.

### <a name="inability-to-customize-python-installation"></a>Brak możliwości dostosowania instalacji języka Python

Obecnie jedynym sposobem dodawania niestandardowych modułów języka Python jest za pośrednictwem opisanego wcześniej mechanizmu pliku zip. Chociaż jest to możliwe w przypadku małych modułów, jest to bardzo skomplikowane w przypadku dużych modułów (zwłaszcza modułów z natywnymi bibliotekami DLL) lub z dużą liczbą modułów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
