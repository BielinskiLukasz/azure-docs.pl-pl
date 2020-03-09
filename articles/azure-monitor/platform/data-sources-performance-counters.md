---
title: Zbieranie i analizowanie liczników wydajności w Azure Monitor | Microsoft Docs
description: Liczniki wydajności są zbierane przez Azure Monitor w celu przeanalizowania wydajności agentów systemu Windows i Linux.  W tym artykule opisano sposób konfigurowania kolekcji liczników wydajności dla agentów systemu Windows i Linux. szczegóły są przechowywane w obszarze roboczym i sposób analizowania ich w Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394408"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Źródła danych wydajności systemów Windows i Linux w Azure Monitor
Liczniki wydajności w systemach Windows i Linux zapewniają wgląd w wydajność składników sprzętowych, systemów operacyjnych i aplikacji.  Azure Monitor może zbierać liczniki wydajności w częstych odstępach czasu dla analizy prawie w czasie rzeczywistym (NRT), a także do agregowania danych dotyczących wydajności na potrzeby analizy i raportowania w dłuższym okresie.

![Liczniki wydajności](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności
Skonfiguruj liczniki wydajności z [menu dane w oknie Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).

Podczas pierwszej konfiguracji liczników wydajności systemu Windows lub Linux dla nowego obszaru roboczego można szybko utworzyć kilka typowych liczników.  Na liście obok każdego z nich znajduje się pole wyboru.  Upewnij się, że wszystkie liczniki, które chcesz utworzyć, są zaznaczone, a następnie kliknij przycisk **Dodaj wybrane liczniki wydajności**.

W przypadku liczników wydajności systemu Windows można wybrać określone wystąpienie dla każdego licznika wydajności. W przypadku liczników wydajności systemu Linux wystąpienie każdego wybranego licznika ma zastosowanie do wszystkich liczników podrzędnych licznika nadrzędnego. W poniższej tabeli przedstawiono typowe wystąpienia, które są dostępne dla liczników wydajności systemu Linux i Windows.

| Nazwa wystąpienia | Opis |
| --- | --- |
| \_łącznie |Suma wszystkich wystąpień |
| \* |Wszystkie wystąpienia |
| (/&#124;/var) |Dopasowuje wystąpienia o nazwie:/lub/var |

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

![Konfigurowanie liczników wydajności systemu Windows](media/data-sources-performance-counters/configure-windows.png)

Postępuj zgodnie z tą procedurą, aby dodać nowy licznik wydajności systemu Windows do zbierania danych.

1. Wpisz nazwę licznika w polu tekstowym w *obiekcie format (wystąpienie) \Counter*.  Po rozpoczęciu wpisywania zostanie wyświetlona zgodna lista typowych liczników.  Możesz wybrać licznik z listy lub wpisać własny.  Możesz również zwrócić wszystkie wystąpienia dla określonego licznika, określając *object\counter*.  

    Podczas zbierania SQL Server liczników wydajności z nazwanych wystąpień wszystkie liczniki nazwanego wystąpienia zaczynają się od *MSSQL $* i po nim nazwa wystąpienia.  Na przykład, aby zebrać licznik Współczynnik trafień pamięci podręcznej dzienników dla wszystkich baz danych z obiektu wydajności bazy danych dla nazwanego wystąpienia SQL INST2, określ `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Kliknij przycisk **+** lub naciśnij klawisz **Enter** , aby dodać licznik do listy.
3. Po dodaniu licznika zostanie użyta wartość domyślna wynosząca 10 sekund dla **interwału próbkowania**.  Można zmienić tę wartość na wyższą niż 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
4. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

![Konfigurowanie liczników wydajności systemu Linux](media/data-sources-performance-counters/configure-linux.png)

Postępuj zgodnie z tą procedurą, aby dodać nowy licznik wydajności systemu Linux do zebrania.

1. Domyślnie wszystkie zmiany konfiguracji są automatycznie przekazywane do wszystkich agentów.  W przypadku agentów systemu Linux plik konfiguracji jest wysyłany do programu zbierającego dane.  Jeśli chcesz zmodyfikować ten plik ręcznie na każdym agencie systemu Linux, usuń zaznaczenie pola wyboru *Zastosuj poniższą konfigurację do maszyn z systemem Linux* i postępuj zgodnie z poniższymi wskazówkami.
2. Wpisz nazwę licznika w polu tekstowym w *obiekcie format (wystąpienie) \Counter*.  Po rozpoczęciu wpisywania zostanie wyświetlona zgodna lista typowych liczników.  Możesz wybrać licznik z listy lub wpisać własny.  
3. Kliknij przycisk **+** lub naciśnij klawisz **Enter** , aby dodać licznik do listy innych liczników dla obiektu.
4. Wszystkie liczniki dla obiektu używają tego samego **interwału próbkowania**.  Wartość domyślna to 10 sekund.  Tę zmianę można zmienić na maksymalnie 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
5. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurowanie liczników wydajności systemu Linux w pliku konfiguracyjnym
Zamiast konfigurować liczniki wydajności systemu Linux przy użyciu Azure Portal istnieje możliwość edytowania plików konfiguracji w agencie systemu Linux.  Metryki wydajności do zebrania są kontrolowane przez konfigurację w **/etc/opt/microsoft/omsagent/\<\>obszaru roboczego**.

Każdy obiekt lub kategoria metryk wydajności do zebrania należy zdefiniować w pliku konfiguracji jako pojedynczy element `<source>`. Składnia jest zgodna z wzorcem poniżej.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Parametry w tym elemencie są opisane w poniższej tabeli.

| Parametry | Opis |
|:--|:--|
| Nazwa\_obiektu | Nazwa obiektu dla kolekcji. |
| wystąpienie\_wyrażenie regularne |  *Wyrażenie regularne* definiujące, które wystąpienia mają być zbierane. Wartość: `.*` określa wszystkie wystąpienia. Aby zbierać metryki procesora tylko dla \_wystąpienia całkowitego, można określić `_Total`. Aby zbierać metryki procesów tylko dla wystąpień z identyfikatorem "lub" SSHD, można określić: `(crond\|sshd)`. |
| Nazwa\_licznika\_wyrażenie regularne | *Wyrażenie regularne* definiujące, które liczniki (dla obiektu) mają być zbierane. Aby zebrać wszystkie liczniki dla obiektu, określ: `.*`. Aby zebrać tylko liczniki przestrzeni wymiany dla obiektu pamięci, można na przykład określić: `.+Swap.+` |
| interval | Częstotliwość, z jaką są zbierane liczniki obiektu. |


W poniższej tabeli wymieniono obiekty i liczniki, które można określić w pliku konfiguracji.  Dostępne są dodatkowe liczniki dla niektórych aplikacji, zgodnie z opisem w temacie [Zbieranie liczników wydajności dla aplikacji systemu Linux w Azure monitor](data-sources-linux-applications.md).

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Dysk logiczny | % Wolnego węzłów i |
| Dysk logiczny | % Wolnego miejsca |
| Dysk logiczny | % Użytych węzłów i |
| Dysk logiczny | Procent wykorzystania miejsca |
| Dysk logiczny | Bajty odczytu z dysku/s |
| Dysk logiczny | Odczyty dysku/s |
| Dysk logiczny | Transfery dyskowe/s |
| Dysk logiczny | Bajty zapisu na dysku/s |
| Dysk logiczny | Zapisy dysku/s |
| Dysk logiczny | Wolne megabajty |
| Dysk logiczny | Bajty dysku logicznego/s |
| Memory (Pamięć) | Dostępna pamięć (%) |
| Memory (Pamięć) | Dostępny obszar wymiany (%) |
| Memory (Pamięć) | Używana pamięć (%) |
| Memory (Pamięć) | Używany obszar wymiany (%) |
| Memory (Pamięć) | Dostępna pamięć (MB) |
| Memory (Pamięć) | Dostępny obszar wymiany (MB) |
| Memory (Pamięć) | Odczyty stron/s |
| Memory (Pamięć) | Zapisy stron/s |
| Memory (Pamięć) | Strony/s |
| Memory (Pamięć) | Używany obszar wymiany (MB) |
| Memory (Pamięć) | Używana pamięć (MB) |
| Network | Całkowita liczba przesłanych bajtów |
| Network | Całkowita liczba odebranych bajtów |
| Network | Łączna liczba bajtów |
| Network | Całkowita liczba przesłanych pakietów |
| Network | Całkowita liczba odebranych pakietów |
| Network | Całkowita liczba błędów odbierania |
| Network | Całkowita liczba błędów transmisji |
| Network | Łączna liczba kolizji |
| Dysk fizyczny | Średni czas dysku w s/odczyt |
| Dysk fizyczny | Średni czas dysku w s/transfer |
| Dysk fizyczny | Średni czas dysku w s/zapis |
| Dysk fizyczny | Bajty dysku fizycznego/s |
| Proces | Czas uprzywilejowany PCT |
| Proces | Czas użytkownika PCT |
| Proces | Używana pamięć (w kilobajtach) |
| Proces | Wirtualna pamięć udostępniona |
| Procesor | Czas DPC (%) |
| Procesor | Czas bezczynności (%) |
| Procesor | Czas przerwań (%) |
| Procesor | Czas oczekiwania operacji we/wy (%) |
| Procesor | % Całkiem czasu |
| Procesor | Czas uprzywilejowany (%) |
| Procesor | Czas procesora (%) |
| Procesor | Czas użytkownika (%) |
| System | Wolna pamięć fizyczna |
| System | Wolne miejsce w plikach stronicowania |
| System | Wolna pamięć wirtualna |
| System | Procesy |
| System | Rozmiar zapisany w plikach stronicowania |
| System | Czas pracy |
| System | Użytkownicy |


Poniżej przedstawiono domyślną konfigurację metryk wydajności.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Zbieranie danych
Azure Monitor zbiera wszystkie określone liczniki wydajności w określonym interwale próbkowania na wszystkich agentach, na których zainstalowano ten licznik.  Dane nie są agregowane i dane pierwotne są dostępne we wszystkich widokach zapytania dziennika przez czas określony przez subskrypcję.

## <a name="performance-record-properties"></a>Właściwości rekordu wydajności
Rekordy wydajności mają typ **wydajności** i mają właściwości opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer |Komputer, który zostały zebrane zdarzenia. |
| CounterName |Nazwa licznika wydajności |
| CounterPath |Pełna ścieżka licznika w formularzu \\\\\<komputerze >\\obiekt (wystąpienie)\\licznik. |
| CounterValue |Wartość liczbowa licznika. |
| InstanceName |Nazwa wystąpienia zdarzenia.  Puste, jeśli żadne wystąpienie nie jest. |
| Obiektu |Nazwa obiektu wydajności |
| SourceSystem |Typ agenta, z którego zostały zebrane dane. <br><br>OpsManager — Agent systemu Windows, bezpośrednie połączenie lub SCOM <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina próbkowania danych. |

## <a name="sizing-estimates"></a>Ustalanie wielkości oszacowań
 Przybliżone oszacowanie dla zbierania określonego licznika w 10-sekundowych odstępach wynosi około 1 MB dziennie na wystąpienie.  Wymagania dotyczące magazynu określonego licznika można oszacować przy użyciu następującej formuły.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Rejestruj zapytania z rekordami wydajności
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy wydajności.

| Zapytanie | Opis |
|:--- |:--- |
| Perf |Wszystkie dane dotyczące wydajności |
| Wydajność &#124; , w której komputer = = "MójKomputer" |Wszystkie dane dotyczące wydajności z określonego komputera |
| Wydajność &#124; , gdzie CounterName = = "Bieżąca długość kolejki dysku" |Wszystkie dane dotyczące wydajności dla określonego licznika |
| Wydajność &#124; , gdzie ObjectName = = "Processor" i CounterName = = "% Time procesora" i InstanceName = = " &#124; _TOTAL" podsumowuje AVGCPU = AVG (CounterValue) według komputera |Średnie użycie procesora CPU na wszystkich komputerach |
| Wydajność &#124; , gdzie CounterName = = "% Time procesora &#124; " podsumowuje AggregatedValue = Max (CounterValue) według komputera |Maksymalne wykorzystanie procesora CPU na wszystkich komputerach |
| Wydajność &#124; , gdzie ObjectName = = "dysk logiczny" i CounterName = = "Bieżąca długość kolejki dysku" i Computer = = "Moja ComputerName" &#124; podsumowuje AggregatedValue = AVG (CounterValue) przez InstanceName |Średnia bieżąca długość kolejki dysku we wszystkich wystąpieniach danego komputera |
| Wydajność &#124; , gdzie CounterName = = "transfery dysku/ &#124; s" podsumowuje AggregatedValue = percentyl (CounterValue, 95) przez komputer |używany 95. percentyl transferu dysku/s na wszystkich komputerach |
| Wydajność &#124; , gdzie CounterName = = "% Time procesora" i InstanceName = = "_Total &#124; " podsumowuje AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), Computer |Średnia godzinowa użycia procesora CPU na wszystkich komputerach |
| Wydajność &#124; , gdzie Computer = = "MójKomputer" i CounterName startswith_cs "%" i InstanceName = = "_Total &#124; " podsumowuje AggregatedValue = percentyl (CounterValue, 70) według bin (TimeGenerated, 1 godz), CounterName | Co godzinę 70 percentyl każdego licznika% procent dla określonego komputera |
| Wydajność &#124; , gdzie CounterName = = "% Time procesora" i InstanceName = = "_Total" i Computer = = "MójKomputer &#124; " podsumowuje ["min (CounterValue)"] = min (CounterValue), ["AVG (CounterValue)"] = AVG (CounterValue), ["percentile75 (CounterValue)"] = percentyl (CounterValue, 75), ["Max (CounterValue)"] = Max (CounterValue) według bin (TimeGenerated, 1 godz), Computer |Średnia godzinowa, minimalna, maksymalna i 75-percentyl użycia procesora CPU dla określonego komputera |
| Wydajność &#124; , gdzie ObjectName = = "MSSQL $ INST2: Databases" i InstanceName = = "Master" | Wszystkie dane dotyczące wydajności z obiektu wydajności bazy danych dla bazy danych Master z nazwanego wystąpienia SQL Server INST2.  




## <a name="next-steps"></a>Następne kroki
* [Zbieraj liczniki wydajności z aplikacji systemu Linux, w](data-sources-linux-applications.md) tym MySQL i Apache HTTP Server.
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.  
* Eksportuj zebrane dane do [Power BI](powerbi.md) , aby uzyskać dodatkowe wizualizacje i analizę.
