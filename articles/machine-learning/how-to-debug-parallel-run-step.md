---
title: Debugowanie i rozwiązywanie problemów z ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debugowanie i rozwiązywanie problemów z ParallelRunStep w potokach uczenia maszynowego w zestawie SDK Azure Machine Learning dla języka Python. Poznaj typowe pułapek na potrzeby programowania przy użyciu potoków oraz porady ułatwiające debugowanie skryptów przed i podczas wykonywania zdalnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122966"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debugowanie i rozwiązywanie problemów z ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak debugować i rozwiązywać problemy z klasą [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z poziomu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Lokalne testowanie skryptów

Zobacz [sekcję testowanie skryptów lokalnie](how-to-debug-pipelines.md#testing-scripts-locally) dla potoków uczenia maszynowego. ParallelRunStep działa jako krok w potokach ML, dlatego ta sama odpowiedź ma zastosowanie do obu tych metod.

## <a name="debugging-scripts-from-remote-context"></a>Debugowanie skryptów z kontekstu zdalnego

Przejście od debugowania skryptu oceniania lokalnie w celu debugowania skryptu oceniania w rzeczywistym potoku może być trudne. Aby uzyskać informacje na temat wyszukiwania dzienników w portalu, należy zapoznać się z [sekcją potoki uczenia maszynowego w przypadku debugowania skryptów z kontekstu zdalnego](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informacje zawarte w tej sekcji dotyczą również przebiegu równoległego kroku.

Na przykład plik dziennika `70_driver_log.txt` zawiera informacje z kontrolera, który uruchamia kod kroku przebiegu równoległego.

Ze względu na rozproszony charakter zadań przebiegów równoległych istnieją dzienniki z kilku różnych źródeł. Jednak tworzone są dwa skonsolidowane pliki, które udostępniają informacje wysokiego poziomu:

- `~/logs/overview.txt`: ten plik zawiera ogólne informacje o liczbie pośrednich partii (nazywanych również zadaniami), które zostały utworzone do tej pory i wiele z nich przetworzono do tej pory. Na tym końcu zostanie wyświetlony wynik zadania. Jeśli zadanie nie powiodło się, zostanie wyświetlony komunikat o błędzie i miejsce, w którym należy rozpocząć rozwiązywanie problemów.

- `~/logs/sys/master.txt`: ten plik zawiera widok węzła głównego (znanego również jako koordynator) uruchomionego zadania. Obejmuje tworzenie zadań, monitorowanie postępu, wynik uruchomienia.

Dzienniki wygenerowane na podstawie skryptu wprowadzania przy użyciu instrukcji EntryScript. rejestratora i drukowania będą znajdować się w następujących plikach:

- `~/logs/user/<ip_address>/Process-*.txt`: ten plik zawiera dzienniki zapisane z entry_script przy użyciu EntryScript. rejestratora. Zawiera również instrukcję Print Statement (stdout) z entry_script.

Jeśli potrzebujesz pełnego zrozumieć, jak każdy węzeł wykonał skrypt wynikowy, sprawdź poszczególne dzienniki procesów dla każdego węzła. Dzienniki procesów można znaleźć w folderze `sys/worker` pogrupowanym według węzłów procesu roboczego:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: ten plik zawiera szczegółowe informacje o każdej z grup minimalnej, które są pobierane lub wykonywane przez proces roboczy. Dla każdej grupy mini-Batch ten plik zawiera:

    - Adres IP i Identyfikator PID procesu roboczego. 
    - Całkowita liczba elementów, liczba pomyślnie przetworzonych elementów i liczba elementów zakończonych niepowodzeniem.
    - Czas rozpoczęcia, czas trwania, czas procesu i Metoda uruchomienia.

Możesz również znaleźć informacje o użyciu zasobów procesów dla każdego pracownika. Te informacje są w formacie CSV i znajdują się w `~/logs/sys/perf/<ip_address>/`. W przypadku jednego węzła pliki zadań będą dostępne w obszarze `~logs/sys/perf`. Na przykład podczas sprawdzania wykorzystania zasobów zapoznaj się z następującymi plikami:

- `Process-*.csv`: za użycie zasobów procesu roboczego. 
- `sys.csv`: dziennik na węzeł.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Jak mogę dziennika ze skryptu użytkownika ze zdalnego kontekstu?
Możesz uzyskać Rejestrator z EntryScript, jak pokazano poniżej przykładowego kodu, aby dzienniki były wyświetlane w **dzienniku/folderze użytkownika** w portalu.

**Przykładowy skrypt wprowadzania przy użyciu rejestratora:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak można przekazać dane wejściowe po stronie, takie jak plik lub pliki zawierające tabelę odnośników, do wszystkich pracowników?

Utwórz obiekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) zawierający dane wejściowe i zarejestruj się w obszarze roboczym. Po uzyskaniu dostępu do niego w skrypcie wnioskowania (na przykład w metodzie init ()) w następujący sposób:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Następne kroki

* Zobacz odwołanie do zestawu SDK, aby uzyskać pomoc dotyczącą pakietu [Azure-contrib-Pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) i [dokumentacji](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) klasy ParallelRunStep.

* Postępuj zgodnie z [zaawansowanym samouczkiem](tutorial-pipeline-batch-scoring-classification.md) dotyczącym używania potoków z etapem uruchamiania równoległego.
