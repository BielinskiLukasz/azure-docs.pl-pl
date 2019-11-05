---
title: Miejsce zapisania plików eksperymentu & zapisu
titleSuffix: Azure Machine Learning
description: Dowiedz się, gdzie zapisywać pliki wejściowe eksperymentu i gdzie zapisywać pliki wyjściowe, aby zapobiec błędom ograniczenia magazynu i opóźnieniu eksperymentów.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 41b2602e57d295cfd7e475f4b3aa5657bd4e24d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489603"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Miejsce zapisywania i zapisywania plików do Azure Machine Learning eksperymentów
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, gdzie zapisywać pliki wejściowe i gdzie zapisywać pliki wyjściowe z eksperymentów, aby zapobiec błędom limitu magazynu i opóźnieniu eksperymentów.

Podczas uruchamiania szkoleń w [obiekcie docelowym obliczeń](how-to-set-up-training-targets.md)są one odizolowane od środowisk zewnętrznych. Celem tego projektu jest zapewnienie odtwarzalności i przenośności eksperymentu. W przypadku uruchomienia tego samego skryptu dwa razy w tym samym lub innym elemencie docelowym obliczeń otrzymujesz te same wyniki. Ten projekt umożliwia traktowanie obiektów docelowych obliczeń jako bezstanowych zasobów obliczeniowych, z których każdy nie ma koligacji do zadań, które są uruchomione po zakończeniu.

## <a name="where-to-save-input-files"></a>Gdzie należy zapisywać pliki wejściowe

Przed zainicjowaniem eksperymentu w elemencie docelowym obliczeń lub na komputerze lokalnym należy upewnić się, że wymagane pliki są dostępne dla tego obiektu docelowego obliczeń, takich jak pliki zależności i pliki danych, które muszą zostać uruchomione w kodzie.

Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały folder skryptu do docelowego kontekstu obliczeniowego, a następnie pobiera migawkę. Limit magazynu dla migawek eksperymentu to 300 MB i/lub 2000 plików.

Z tego powodu zalecamy:

* **Przechowywanie plików w Azure Machine Learning [magazynie](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)danych.** Zapobiega to problemom z opóźnieniami eksperymentu i ma zalety uzyskiwania dostępu do danych z zdalnego obiektu docelowego obliczeń, co oznacza, że uwierzytelnianie i Instalowanie jest zarządzane przez Azure Machine Learning. Dowiedz się więcej na temat określania magazynu danych jako katalogu źródłowego i przekazywania plików do magazynu danych w artykule [dostępnym ze sklepów datastores](how-to-access-data.md) .

* **Jeśli potrzebujesz tylko kilku plików danych i skryptów zależności i nie będzie można użyć sklepu datastore,** Umieść pliki w tym samym katalogu folderu co skrypt szkoleniowy. Określ ten folder jako `source_directory` bezpośrednio w skrypcie szkoleniowym lub w kodzie, który wywołuje skrypt szkoleniowy.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limity magazynu dla migawek eksperymentów

W przypadku eksperymentów Azure Machine Learning automatycznie tworzy migawkę eksperymentu kodu na podstawie katalogu zaproponowanego podczas konfigurowania przebiegu. Ma to łączny Limit wynoszący plików 300 MB i/lub 2000. W przypadku przekroczenia tego limitu zostanie wyświetlony następujący błąd:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Aby rozwiązać ten problem, należy przechowywać pliki eksperymentów w magazynie danych. Jeśli nie możesz użyć magazynu danych, Poniższa tabela oferuje możliwe alternatywne rozwiązania.

Opis&nbsp;eksperymentu|Rozwiązanie limitu magazynu
---|---
Mniej niż 2000 plików & nie może używać magazynu danych| Przesłoń limit rozmiaru migawki przy użyciu <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Może to potrwać kilka minut w zależności od liczby i rozmiaru plików.
Musi używać określonego katalogu skryptów| Utwórz plik `.amlignore`, aby wykluczyć z migawki eksperymentu pliki, które nie są częścią kodu źródłowego. Dodaj nazwy plików do pliku `.amlignore` i umieść je w tym samym katalogu, w którym znajduje się Twój skrypt szkoleniowy. Plik `.amlignore` używa tej samej [składni i wzorców](https://git-scm.com/docs/gitignore) co plik `.gitignore`.
Potok|Użyj innego podkatalogu dla każdego kroku
Notesy programu Jupyter| Utwórz plik `.amlignore` lub Przenieś Notes do nowego, pustego, podkatalogu i ponownie uruchom kod.

## <a name="where-to-write-files"></a>Miejsce zapisu plików

W związku z izolacją eksperymentów szkoleniowych zmiany w plikach, które są wykonywane podczas przebiegów, nie muszą być utrwalane poza środowiskiem. Jeśli skrypt modyfikuje pliki lokalnie do obliczeń, zmiany nie są utrwalane dla następnego eksperymentu i nie są automatycznie propagowane do komputera klienckiego. W związku z tym zmiany wprowadzone podczas pierwszego eksperymentu nie mają wpływu na te w drugim.

Podczas zapisywania zmian zalecamy zapisanie plików do magazynu danych Azure Machine Learning. Zobacz [dostęp do danych z Twoich magazynów](how-to-access-data.md).

Jeśli nie jest wymagane przechowywanie danych, Zapisz pliki w folderze `./outputs` i/lub `./logs`.

>[!Important]
> Dwa foldery, dane *wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie według Azure Machine Learning. Podczas uczenia pliki do`./outputs` i`./logs` folderów są automatycznie przekazywane do historii uruchamiania, dzięki czemu będziesz mieć do nich dostęp po zakończeniu przebiegu.

* W **przypadku danych wyjściowych, takich jak komunikaty o stanie lub wyniki oceniania,** pliki są zapisywane do folderu `./outputs`, więc są utrwalane jako artefakty w historii uruchamiania. Należy mieć na uwadze liczbę i rozmiar plików zapisaną w tym folderze, ponieważ opóźnienie może wystąpić podczas przekazywania zawartości do historii uruchamiania. Jeśli opóźnienie jest istotna, zaleca się zapisanie plików do magazynu danych.

* **Aby zapisać zapisany plik jako dzienniki w historii uruchamiania,** Zapisz pliki do folderu `./logs`. Dzienniki są przekazywane w czasie rzeczywistym, więc ta metoda jest odpowiednia do przesyłania strumieniowego aktualizacji na żywo z przebiegu zdalnego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [uzyskiwaniu dostępu do danych z Twoich magazynów](how-to-access-data.md).

* Dowiedz się więcej [na temat konfigurowania celów szkoleniowych](how-to-set-up-training-targets.md).
