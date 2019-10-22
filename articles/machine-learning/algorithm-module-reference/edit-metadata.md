---
title: 'Edytowanie metadanych: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Informacje dotyczące zmiany metadanych skojarzonych z kolumnami w zestawie danych przy użyciu modułu Edytuj metadane w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b6e28577fefe4892d719e211e16edafed1504e87
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693829"
---
# <a name="edit-metadata-module"></a>Edytuj moduł metadanych

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użyj modułu Edytuj dane, aby zmienić metadane skojarzone z kolumnami w zestawie danych. Wartość i typ danych zestawu danych zmienią się po użyciu modułu Edytowanie metadanych.

Typowe zmiany metadanych mogą obejmować:
  
+ Traktowanie kolumn logicznych lub liczbowych jako wartości kategorii.
  
+ Wskazuje, która kolumna zawiera etykietę **klasy** lub zawiera wartości, które mają zostać skategoryzowane lub przewidywalne.
  
+ Oznaczanie kolumn jako funkcji.
  
+ Zmiana wartości daty/godziny na wartości liczbowe lub odwrotnie.
  
+ Zmiana nazw kolumn.
  
 W każdej chwili należy zmodyfikować definicję kolumny, zazwyczaj w celu spełnienia wymagań dotyczących modułu podrzędnego. Na przykład niektóre moduły działają tylko z określonymi typami danych lub wymagają flag w kolumnach, takich jak `IsFeature` lub `IsCategorical`.  
  
 Po wykonaniu wymaganej operacji możesz zresetować metadane do jego oryginalnego stanu.
  
## <a name="configure-edit-metadata"></a>Konfiguruj Edytowanie metadanych
  
1. W Azure Machine Learning Dodaj moduł Edytuj metadane do potoku i Połącz zestaw danych, który chcesz zaktualizować. Zestaw danych można znaleźć w obszarze **Przekształcanie danych** w kategorii **manipulowanie** .
  
1. Wybierz pozycję **Uruchom selektor kolumny** i wybierz kolumnę lub zestaw kolumn, z którymi chcesz współpracować. Możesz wybrać kolumny osobno według nazwy lub indeksu lub wybrać grupę kolumn według typu.  
  
1. Wybierz opcję **Typ danych** , jeśli musisz przypisać inny typ danych do wybranych kolumn. Może zajść potrzeba zmiany typu danych dla niektórych operacji. Na przykład, Jeśli źródłowy zestaw danych zawiera liczby obsługiwane jako tekst, należy zmienić je na typ danych liczbowych przed użyciem operacji matematycznych.

    + Obsługiwane typy danych to **String**, **Integer**, **Double**, **Boolean**i **DateTime**.

    + W przypadku wybrania wielu kolumn należy zastosować zmiany metadanych do *wszystkich* zaznaczonych kolumn. Załóżmy na przykład, że wybierasz dwie lub trzy kolumny liczbowe. Wszystkie te elementy można zmienić na dane typu String i zmienić ich nazwy w jednej operacji. Nie można jednak zmienić jednej kolumny na typ danych ciągu i innej kolumny z wartości zmiennoprzecinkowej na liczbę całkowitą.
  
    + Jeśli nie określisz nowego typu danych, metadane kolumn nie ulegają zmianie.

    + Typ kolumny i jej wartości zmienią się po wykonaniu operacji Edytuj metadane. Możesz w dowolnym momencie odzyskać oryginalny typ danych za pomocą polecenia Edytuj metadane, aby zresetować typ danych kolumny.  

    > [!NOTE]
    > W przypadku zmiany dowolnego typu liczby na typ **DateTime** pozostaw pole **format DateTime** puste. Obecnie nie można określić docelowego formatu danych.  

1. Wybierz opcję **kategorii** , aby określić, że wartości w wybranych kolumnach mają być traktowane jako kategorie.

    Na przykład może być kolumną zawierającą liczby 0, 1 i 2, ale wiadomo, że cyfry rzeczywiście oznaczają "dym", "non-dym" i "nieznany". W takim przypadku przez Oflagowanie kolumny jako kategorii upewnij się, że wartości są używane tylko do grupowania danych, a nie do obliczeń liczbowych.
  
1. Użyj opcji **pola** , jeśli chcesz zmienić sposób, w jaki Azure Machine Learning używa danych w modelu.

    + **Funkcja**: Użyj tej opcji, aby oflagować kolumnę jako funkcję w modułach, które działają tylko w kolumnach funkcji. Domyślnie wszystkie kolumny są początkowo traktowane jako funkcje.  
  
    + **Etykieta**: Użyj tej opcji, aby oznaczyć etykietę, która jest również znana jako atrybut przewidywalny lub zmienna docelowa. Wiele modułów wymaga, aby dokładnie jedna kolumna etykiety znajdowała się w zestawie danych.

        W wielu przypadkach Azure Machine Learning może wnioskować, że kolumna zawiera etykietę klasy. Ustawiając te metadane, można upewnić się, że kolumna jest poprawnie identyfikowana. Ustawienie tej opcji nie powoduje zmiany wartości danych. Zmiana ta zmienia się tylko w sposób, w jaki niektóre algorytmy uczenia maszynowego obsługują dane.
  
    > [!TIP]
    > Czy masz dane, które nie mieszczą się w tych kategoriach? Na przykład zestaw danych może zawierać wartości, takie jak unikatowe identyfikatory, które nie są przydatne jako zmienne. Czasami takie identyfikatory mogą spowodować problemy, gdy są używane w modelu.
    >
    > Na szczęście Azure Machine Learning utrzymuje wszystkie dane, dzięki czemu nie trzeba usuwać takich kolumn z zestawu danych. Gdy konieczne jest wykonanie operacji na niektórych specjalnych zestawach kolumn, wystarczy usunąć wszystkie inne kolumny tymczasowo przy użyciu modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) ). Później można scalić kolumny z powrotem do zestawu danych przy użyciu modułu [Dodaj kolumny](add-columns.md) .  
  
1. Użyj następujących opcji, aby wyczyścić poprzednie wybory i przywrócić metadane do wartości domyślnych.  
  
    + **Wyczyść funkcję**: Użyj tej opcji, aby usunąć flagę funkcji.  
  
         Wszystkie kolumny są początkowo traktowane jako funkcje. W przypadku modułów wykonujących operacje matematyczne może być konieczne użycie tej opcji w celu uniemożliwienia traktowania kolumn liczbowych jako zmiennych.
  
    + **Wyczyść etykietę**: Użyj tej opcji, aby usunąć metadane **etykiet** z określonej kolumny.  
  
    + **Wyczyść ocenę**: Użyj tej opcji, aby usunąć metadane **oceny** z określonej kolumny.  
  
         Obecnie nie można jawnie oznaczyć kolumny jako wyniku w Azure Machine Learning. Jednak niektóre operacje powodują, że kolumna jest oflagowana jako wynik wewnętrznie. Ponadto niestandardowy moduł języka R może wyprowadzać wartości wyniku.

1. Dla **nowych nazw kolumn**wprowadź nową nazwę zaznaczonej kolumny lub kolumny.  
  
    + Nazwy kolumn mogą zawierać tylko znaki obsługiwane przez kodowanie UTF-8. Puste ciągi, wartości null lub nazwy składające się wyłącznie z spacji nie są dozwolone.  
  
    + Aby zmienić nazwę wielu kolumn, wprowadź nazwy jako listę rozdzieloną przecinkami w kolejności indeksów kolumn.  
  
    + Należy zmienić nazwę wszystkich wybranych kolumn. Nie można pominąć ani pominąć kolumn.  
  
1. Uruchamianie potoku.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) dla usługi Azure Machine Learning.
