---
title: Zmienne środowiskowe środowiska uruchomieniowego zadania — Azure Batch | Microsoft Docs
description: Wskazówki dotyczące zmiennych środowiskowych środowiska uruchomieniowego zadań i informacje dotyczące analizy Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: lahugh
ms.openlocfilehash: 995dfeaf26180b74f135ed2b74fa40faf3a12c08
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958786"
---
# <a name="azure-batch-runtime-environment-variables"></a>Zmienne środowiskowe środowiska uruchomieniowego Azure Batch

[Usługa Azure Batch](https://azure.microsoft.com/services/batch/) ustawia następujące zmienne środowiskowe w węzłach obliczeniowych. Można odwoływać się do tych zmiennych środowiskowych w wierszach poleceń zadań, a w programach i skryptach uruchamianych przez wiersze polecenia.

Aby uzyskać więcej informacji o używaniu zmiennych środowiskowych w usłudze Batch, zobacz [Ustawienia środowiska dla zadań](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Widoczność zmiennej środowiskowej

Te zmienne środowiskowe są widoczne tylko w kontekście **użytkownika zadania**, konta użytkownika w węźle, w którym wykonywane jest zadanie. Będą one *niewidoczne*, jeśli [zdalnie połączysz się](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) z węzłem obliczeniowym, za pomocą protokołu RDP lub SSH i wyświetlisz listę zmiennych środowiskowych. Dzieje się tak dlatego, że konto użytkownika, używane na potrzeby połączenia zdalnego jest inne niż konto używane przez zadanie podrzędne.

Aby uzyskać bieżącą wartość zmiennej środowiskowej, uruchom `cmd.exe` polecenie w węźle obliczeniowym systemu Windows lub `/bin/sh` węźle z systemem Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Rozszerzanie wiersza polecenia zmiennych środowiskowych

Wiersze poleceń wykonywane przez zadania w węzłach obliczeniowych nie są uruchamiane w ramach powłoki. W związku z tym te linie poleceń nie mogą natywnie korzystać z zalet funkcji powłoki, takich jak rozszerzenie zmiennej środowiskowej (obejmuje `PATH`to). Aby skorzystać z takich funkcji, należy **wywołać powłokę** w wierszu polecenia. Na przykład uruchom `cmd.exe` w węzłach obliczeniowych systemu `/bin/sh` Windows lub w węzłach Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Zmienne środowiskowe

| Nazwa zmiennej                     | Opis                                                              | Dostępność | Przykład |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Nazwa konta wsadowego, do którego należy zadanie.                  | Wszystkie zadania.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Adres URL konta wsadowego. | Wszystkie zadania. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Prefiks wszystkich zmiennych środowiskowych pakietu aplikacji. Na przykład jeśli aplikacja "foo" w wersji "1" jest zainstalowana w puli, zmienna środowiskowa to AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 wskazuje lokalizację, do której pakiet został pobrany (folder). W przypadku używania domyślnej wersji pakietu aplikacji należy użyć zmiennej środowiskowej AZ_BATCH_APP_PACKAGE bez numerów wersji. | Dowolne zadanie ze skojarzonym pakietem aplikacji. Dostępne również dla wszystkich zadań, jeśli węzeł ma pakiety aplikacji. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Token uwierzytelniania, który udziela dostępu do ograniczonego zestawu operacji usługi Batch. Ta zmienna środowiskowa jest obecna tylko wtedy, gdy [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) są ustawiane podczas [dodawania zadania](/rest/api/batchservice/task/add#request-body). Wartość tokenu jest używana w interfejsie API usługi Batch jako poświadczenia do tworzenia klienta usługi Batch, takiego jak [BatchClient. Open () .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Wszystkie zadania. | Token dostępu OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Katalog w [katalogu roboczym zadania][files_dirs] , w którym certyfikaty są przechowywane dla węzłów obliczeniowych systemu Linux. Ta zmienna środowiskowa nie ma zastosowania do węzłów obliczeniowych systemu Windows.                                                  | Wszystkie zadania.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Lista węzłów, które są przydzielono do [zadania o wiele wystąpień][multi_instance] w formacie `nodeIP,nodeIP`. | Podstawowe i podrzędne podzadania. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Określa, czy bieżący węzeł jest węzłem głównym zadania o [wielu wystąpieniach][multi_instance]. Możliwe wartości to `true` i `false`.| Podstawowe i podrzędne podzadania. | `true` |
| AZ_BATCH_JOB_ID                 | Identyfikator zadania, do którego należy zadanie podrzędne. | Wszystkie zadania poza uruchomieniem zadania. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Pełna ścieżka do [katalogu zadania][files_dirs] przygotowania zadania w węźle. | Wszystkie zadania z wyjątkiem zadania uruchamiania zadania i przygotowania zadania. Dostępne tylko wtedy, gdy zadanie zostało skonfigurowane przy użyciu zadania przygotowania zadania. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Pełna ścieżka [katalogu roboczego zadania][files_dirs] przygotowania zadania w węźle. | Wszystkie zadania z wyjątkiem zadania uruchamiania zadania i przygotowania zadania. Dostępne tylko wtedy, gdy zadanie zostało skonfigurowane przy użyciu zadania przygotowania zadania. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Adres IP i port węzła obliczeniowego, na którym jest uruchamiane podstawowe zadanie z [wieloma wystąpieniami][multi_instance] . | Podstawowe i podrzędne podzadania. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | IDENTYFIKATOR węzła, do którego zadanie jest przypisane. | Wszystkie zadania. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Jeśli `true`bieżący węzeł jest dedykowanym węzłem. Jeśli `false`jest to [węzeł o niskim priorytecie](batch-low-pri-vms.md). | Wszystkie zadania. | `true` |
| AZ_BATCH_NODE_LIST              | Lista węzłów, które są przydzielono do [zadania o wiele wystąpień][multi_instance] w formacie `nodeIP;nodeIP`. | Podstawowe i podrzędne podzadania. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Pełna ścieżka lokalizacji [instalacji systemu plików](virtual-file-mount.md) na poziomie węzła, w której znajdują się wszystkie katalogi instalacji. Udziały plików systemu Windows używają litery dysku, więc dla systemu Windows dysk instalacji jest częścią urządzeń i dysków.  |  Wszystkie zadania, w tym uruchamianie zadania, mają dostęp do użytkownika, na przykład użytkownik ma świadomość uprawnień instalacji dla zainstalowanego katalogu. | Na przykład w Ubuntu lokalizacja:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Pełna ścieżka katalogu głównego wszystkich [katalogów wsadowych][files_dirs] w węźle. | Wszystkie zadania. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Pełna ścieżka [katalogu udostępnionego][files_dirs] w węźle. Wszystkie zadania wykonywane w węźle mają dostęp do odczytu i zapisu do tego katalogu. Zadania wykonywane w innych węzłach nie mają dostępu zdalnego do tego katalogu (nie jest to katalog sieciowy "udostępniony"). | Wszystkie zadania. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Pełna ścieżka do [katalogu zadań początkowych][files_dirs] w węźle. | Wszystkie zadania. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Identyfikator puli, w której jest uruchamiane zadanie podrzędne. | Wszystkie zadania. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Pełna ścieżka [katalogu zadań][files_dirs] w węźle. Ten katalog zawiera `stdout.txt` i `stderr.txt` dla zadania oraz AZ_BATCH_TASK_WORKING_DIR. | Wszystkie zadania. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Identyfikator bieżącego zadania. | Wszystkie zadania poza uruchomieniem zadania. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Ścieżka katalogu, która jest taka sama dla zadania podstawowego i każde podzadanie zadania o [wielu wystąpieniach][multi_instance]. Ścieżka istnieje w każdym węźle, w którym jest uruchamiane zadanie wielowystąpienie, i ma dostęp do odczytu/zapisu dla poleceń zadań uruchomionych w tym węźle ( [polecenie koordynacyjne][coord_cmd] i [polecenie aplikacji][app_cmd]). Podzadania lub zadanie podstawowe wykonywane na innych węzłach nie mają dostępu zdalnego do tego katalogu (nie jest to katalog sieciowy "udostępniony"). | Podstawowe i podrzędne podzadania. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Pełna ścieżka [katalogu roboczego zadania][files_dirs] w węźle. Aktualnie uruchomione zadanie ma dostęp do odczytu/zapisu do tego katalogu. | Wszystkie zadania. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Lista węzłów i liczba rdzeni na węzeł, które są przydzieleni do zadania o [wielu wystąpieniach][multi_instance]. Węzły i rdzenie są wymienione w formacie`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, gdzie po liczbie węzłów następuje jeden lub więcej adresów IP węzła i liczba rdzeni dla każdej z nich. |  Podstawowe i podrzędne podzadania. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
