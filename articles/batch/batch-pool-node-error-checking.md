---
title: Sprawdź błędy puli i węzłów — Azure Batch
description: W tym artykule opisano operacje w tle, które mogą wystąpić, a także błędy do sprawdzenia i sposoby ich unikania podczas tworzenia pul i węzłów.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: dea45cec29101c3b085ab8098c3b05906e1049cd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449786"
---
# <a name="check-for-pool-and-node-errors"></a>Sprawdź, czy występują błędy puli i węzłów

Podczas tworzenia pul Azure Batch i zarządzania nimi, niektóre operacje są wykonywane natychmiast. Niektóre operacje są jednak asynchroniczne i uruchamiane w tle, co trwa kilka minut.

Wykrywanie błędów dla operacji, które są wykonywane natychmiast, jest proste, ponieważ wszystkie błędy są zwracane bezpośrednio przez interfejs API, interfejsu wiersza polecenia lub interfejsu użytkownika.

W tym artykule opisano operacje w tle, które mogą wystąpić w przypadku pul i węzłów puli. Określa sposób wykrywania i unikania niepowodzeń.

## <a name="pool-errors"></a>Błędy puli

### <a name="resize-timeout-or-failure"></a>Zmień limit czasu lub niepowodzenie

Podczas tworzenia nowej puli lub zmiany rozmiarów istniejącej puli należy określić docelową liczbę węzłów.  Operacja tworzenia lub zmiany rozmiaru zostanie zakończona natychmiast, ale rzeczywista alokacja nowych węzłów lub usunięcie istniejących węzłów może potrwać kilka minut.  Limit czasu można określić w interfejsie API [tworzenia](https://docs.microsoft.com/rest/api/batchservice/pool/add) lub [zmiany rozmiaru](https://docs.microsoft.com/rest/api/batchservice/pool/resize) . Jeśli w usłudze Batch nie można uzyskać docelowej liczby węzłów podczas okresu limitu czasu, Pula przechodzi w stan stały i raporty błędów zmieniania rozmiaru.

Właściwość [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) dla najnowszej oceny zawiera listę błędów, które wystąpiły.

Typowe przyczyny błędów zmiany rozmiaru obejmują:

- Limit czasu zmiany rozmiaru jest za krótki
  - W większości przypadków domyślny limit czasu wynoszący 15 minut jest wystarczająco długi dla węzłów puli do przydzielenia lub usunięcia.
  - W przypadku przydzielania dużej liczby węzłów zalecamy ustawienie limitu czasu na 30 minut. Na przykład po zmianie wielkości na więcej niż 1 000 węzłów z obrazu portalu Azure Marketplace lub do ponad 300 węzłów z niestandardowego obrazu maszyny wirtualnej.
- Niewystarczający przydział rdzeni
  - Konto w usłudze Batch jest ograniczone do liczby rdzeni, które można przydzielić między wszystkimi pulami. Zadanie wsadowe przerywa alokowanie węzłów po osiągnięciu limitu przydziału. [Możesz zwiększyć](https://docs.microsoft.com/azure/batch/batch-quota-limit) limit przydziału rdzeni, aby zadanie wsadowe mogły przydzielić więcej węzłów.
- Za mało adresów IP podsieci, gdy [Pula znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsieć sieci wirtualnej musi mieć wystarczającą liczbę nieprzypisanych adresów IP do przydzielenia do każdego żądanego węzła puli. W przeciwnym razie nie można utworzyć węzłów.
- Niewystarczające zasoby, gdy [Pula znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Możesz tworzyć zasoby, takie jak moduły równoważenia obciążenia, publiczne adresy IP i sieciowe grupy zabezpieczeń w tej samej subskrypcji, co konto usługi Batch. Sprawdź, czy limity przydziału subskrypcji są wystarczające dla tych zasobów.
- Duże pule z niestandardowymi obrazami maszyn wirtualnych
  - Duże pule, które używają niestandardowych obrazów maszyn wirtualnych, mogą być dłużej przydzielane i zmieniać limity czasu.  Zapoznaj się z tematem [Tworzenie puli za pomocą galerii obrazów udostępnionych](batch-sig-images.md) w celu uzyskania zaleceń dotyczących ograniczeń i konfiguracji.

### <a name="automatic-scaling-failures"></a>Automatyczne skalowanie błędów

Możesz również ustawić Azure Batch, aby automatycznie skalować liczbę węzłów w puli. Należy zdefiniować parametry dla [formuły automatycznego skalowania dla puli](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Usługa Batch używa formuły, aby okresowo oszacować liczbę węzłów w puli i ustawić nowy numer docelowy. Mogą wystąpić następujące typy problemów:

- Obliczanie automatycznego skalowania kończy się niepowodzeniem.
- Operacja powodująca zmianę rozmiaru kończy się niepowodzeniem i zostanie przeprowadzona.
- Problem z formułą automatycznego skalowania prowadzi do nieprawidłowych wartości docelowych węzła. Zmiana rozmiaru jest wykonywana lub przeprowadzona w czasie.

Możesz uzyskać informacje na temat ostatniej automatycznej oceny skalowania przy użyciu właściwości [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) . Ta właściwość służy do raportowania czasu oceny, wartości i wyniku oraz ewentualnych błędów wydajności.

[Zdarzenie ukończenia zmiany rozmiaru puli](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) przechwytuje informacje o wszystkich obliczeniach.

### <a name="delete"></a>Usuń

Po usunięciu puli zawierającej węzły, pierwsza partia danych Usuwa węzły. Spowoduje to usunięcie samego obiektu puli. Usunięcie węzłów puli może potrwać kilka minut.

Wsadowe ustawia [stan puli](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) do **usunięcia** podczas procesu usuwania. Aplikacja wywołująca może wykryć, czy usunięcie puli trwa zbyt długo przy użyciu właściwości **State** i **stateTransitionTime** .

## <a name="pool-compute-node-errors"></a>Błędy węzłów obliczeniowych puli

Nawet w przypadku pomyślnego przydzielenia węzłów w puli różne problemy mogą spowodować, że niektóre węzły są w złej kondycji i nie mogą uruchamiać zadań. W tych węzłach nadal są naliczane opłaty, dlatego ważne jest, aby wykrywać problemy, aby uniknąć płacenia za węzły, które nie mogą być używane. Oprócz typowych błędów węzłów, wiedząc, że bieżący [stan zadania](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate) jest przydatny do rozwiązywania problemów.

### <a name="start-task-failures"></a>Błędy uruchamiania zadań

Może być konieczne określenie opcjonalnego zadania podrzędnego [uruchamiania](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) dla puli. Podobnie jak w przypadku każdego zadania, można użyć wiersza polecenia i plików zasobów do pobrania z magazynu. Zadanie uruchamiania jest uruchamiane dla każdego węzła po jego uruchomieniu. Właściwość **waitForSuccess** określa, czy zadanie wsadowe ma czekać do momentu zakończenia zadania uruchamiania przed zaplanowaniem wykonywania zadań w węźle.

Co zrobić, jeśli węzeł został skonfigurowany tak, aby poczekać na zakończenie zadania uruchamiania, ale zadanie uruchamiania zakończy się niepowodzeniem? W takim przypadku węzeł nie będzie użyteczny, ale nadal będzie naliczane opłaty.

Można wykryć nieudane zadania uruchamiania przy użyciu właściwości [wynik](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) i [FailureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) właściwości węzła [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) najwyższego poziomu.

Zadanie uruchamiania zakończone niepowodzeniem powoduje także ustawienie [stanu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) węzła na **Starttaskfailed** , jeśli **waitForSuccess** został ustawiony na **wartość true**.

Podobnie jak w przypadku każdego zadania, może istnieć wiele przyczyn niepowodzenia zadania uruchamiania.  Aby rozwiązać problem, sprawdź plik stdout, stderr i wszelkie dalsze pliki dzienników specyficzne dla zadania.

Zadania uruchamiania muszą być ponownie współużytkowane, ponieważ możliwe jest uruchamianie zadania uruchamiania wiele razy w tym samym węźle. zadanie uruchamiania jest uruchamiane po odinstalowaniu lub ponownym uruchomieniu węzła. W rzadkich przypadkach zadanie uruchamiania zostanie uruchomione po zdarzeniu powodującym ponowne uruchomienie węzła, gdzie jeden z systemów operacyjnych lub dysków tymczasowych został odtworzony, a drugi nie. Ponieważ zadania uruchamiania wsadowego (takie jak wszystkie zadania wsadowe) są uruchamiane z dysku tymczasowych, zazwyczaj nie jest to problem, ale w niektórych przypadkach, gdy zadanie uruchamiania instaluje aplikację na dysku systemu operacyjnego i przechowując inne dane na dysku tymczasowych, może to spowodować problemy, ponieważ elementy nie są zsynchronizowane. Odpowiednio Chroń aplikację, jeśli używasz obu dysków.

### <a name="application-package-download-failure"></a>Niepowodzenie pobierania pakietu aplikacji

Dla puli można określić jeden lub więcej pakietów aplikacji. Program Batch pobiera określone pliki pakietu do każdego węzła i dekompresuje pliki po rozpoczęciu działania węzła, ale przed zaplanowaniem zadań. Często należy używać wiersza polecenia Uruchom zadanie w połączeniu z pakietami aplikacji. Na przykład, aby skopiować pliki do innej lokalizacji lub uruchomić Instalatora.

Właściwość [Błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza niepowodzenie pobrania i cofnięcia kompresji pakietu aplikacji; stan węzła jest ustawiony na **niezdatny do użytku**.

### <a name="container-download-failure"></a>Niepowodzenie pobierania kontenera

W puli można określić co najmniej jedno odwołanie do kontenera. Wsadowe pobiera określone kontenery do każdego węzła. Właściwość [Błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza niepowodzenie pobrania kontenera i ustawia stan węzła na **niezdatny do użytku**.

### <a name="node-in-unusable-state"></a>Węzeł w stanie niezdatnym do użytku

Azure Batch może ustawić [stan węzła](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) na **niezdatny do użytku** z wielu powodów. Gdy stan węzła jest ustawiony na **niezdatny do użytku**, zadania nie mogą być zaplanowane do węzła, ale nadal są naliczane opłaty.

Węzły w stanie **niezdatnym do użytku** , ale bez [błędów](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) oznacza, że partia nie może komunikować się z maszyną wirtualną. W takim przypadku program Batch zawsze próbuje odzyskać maszynę wirtualną. Funkcja Batch nie będzie automatycznie podejmować prób odzyskania maszyn wirtualnych, których nie udało się zainstalować pakietów lub kontenerów aplikacji, nawet jeśli ich stan nie **nadaje się do użytku**.

Jeśli partia może ustalić przyczynę, właściwość [Błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) węzła zgłasza ją.

Dodatkowe przykłady przyczyn **nieużytecznych** węzłów obejmują:

- Niestandardowy obraz maszyny wirtualnej jest nieprawidłowy. Na przykład obraz, który nie jest prawidłowo przygotowany.

- Maszyna wirtualna została przeniesiona z powodu awarii infrastruktury lub uaktualnienia niskiego poziomu. Partia odzyskuje węzeł.

- Obraz maszyny wirtualnej został wdrożony na sprzęcie, który go nie obsługuje. Na przykład próba uruchomienia obrazu CentOS HPC na maszynie wirtualnej [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) .

- Maszyny wirtualne znajdują się w [sieci wirtualnej platformy Azure](batch-virtual-network.md), a ruch został zablokowany do kluczowych portów.

- Maszyny wirtualne znajdują się w sieci wirtualnej, ale ruch wychodzący do usługi Azure Storage jest blokowany.

- Maszyny wirtualne znajdują się w sieci wirtualnej z konfiguracją DNS klienta, a serwer DNS nie może rozpoznać usługi Azure Storage.

### <a name="node-agent-log-files"></a>Pliki dziennika agenta węzła

Proces agenta wsadowego, który jest uruchamiany w poszczególnych węzłach puli, może dostarczyć pliki dziennika, które mogą być przydatne, jeśli trzeba skontaktować się z pomocą techniczną dotyczącą problemu z węzłem puli. Pliki dziennika dla węzła można przekazać za pomocą Azure Portal, Batch Explorer lub [interfejsu API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Warto przekazać i zapisać pliki dziennika. Następnie można usunąć węzeł lub pulę, aby zaoszczędzić koszt uruchomionych węzłów.

### <a name="node-disk-full"></a>Dysk węzła jest pełny

Dysk tymczasowy dla maszyny wirtualnej węzła puli jest używany przez partię dla plików zadań, plików zadań i plików udostępnionych.

- Pliki pakietów aplikacji
- Pliki zasobów zadań
- Pliki specyficzne dla aplikacji pobrane do jednego z folderów wsadowych
- Pliki stdout i stderr dla każdego zadania wykonywania aplikacji
- Pliki wyjściowe specyficzne dla aplikacji

Niektóre z tych plików są zapisywane tylko raz podczas tworzenia węzłów puli, takich jak pakiety aplikacji puli lub pliki zasobów zadania uruchamiania puli. Nawet jeśli tylko raz podczas tworzenia węzła, jeśli te pliki są zbyt duże, mogą wypełniać dysk tymczasowy.

Inne pliki są zapisywane dla każdego zadania uruchamianego w węźle, na przykład stdout i stderr. Jeśli duża liczba zadań uruchomionych w tym samym węźle i/lub pliki zadań są zbyt duże, może to spowodować wypełnienie dysku tymczasowego.

Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Należy wziąć pod uwagę podczas wybierania rozmiaru maszyny wirtualnej, aby upewnić się, że na dysku tymczasowym jest wystarczająca ilość miejsca.

- W Azure Portal podczas dodawania puli zostanie wyświetlona pełna lista rozmiarów maszyn wirtualnych, w której jest dostępna kolumna "rozmiar dysku zasobu".
- Artykuły opisujące wszystkie rozmiary maszyn wirtualnych mają tabele z kolumną "temp Storage"; na przykład [rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

W przypadku plików pisanych przez każde zadanie można określić czas przechowywania dla każdego zadania, które określa, jak długo pliki zadań są przechowywane przed automatycznym wyczyszczeniem. Czas przechowywania można zmniejszyć, aby zmniejszyć wymagania dotyczące magazynu.

Jeśli ilość miejsca na dysku jest wypełniona, obecnie węzeł przestanie uruchamiać zadania. W przyszłości zostanie zgłoszony [błąd węzła](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) .


## <a name="next-steps"></a>Następne kroki

Sprawdź, czy ustawiono aplikację, aby zaimplementować kompleksowe sprawdzanie błędów, szczególnie w przypadku operacji asynchronicznych. Może to być krytyczne, aby szybko wykrywać i diagnozować problemy.
