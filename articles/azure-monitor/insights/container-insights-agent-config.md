---
title: Konfigurowanie Azure Monitor na potrzeby zbierania danych przez agentów kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor dla agenta kontenerów w celu sterowania kolekcją strumienia stdout/stderr i zmiennych środowiskowych.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933023"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie zbierania danych agenta dla Azure Monitor kontenerów

Azure Monitor kontenerów zbiera zmienne stdout, stderr i środowiskowe z obciążeń kontenera wdrożonych do zarządzanych klastrów Kubernetes z poziomu agenta kontenera. Ustawienia zbierania danych agentów można skonfigurować, tworząc niestandardowe Kubernetes ConfigMaps w celu kontrolowania tego środowiska. 

W tym artykule pokazano, jak utworzyć ConfigMap i skonfigurować zbieranie danych zgodnie z wymaganiami.

>[!NOTE]
>W przypadku usługi Azure Red Hat OpenShift plik szablonu ConfigMap jest tworzony w przestrzeni nazw *OpenShift-Azure-Logging* . 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap — Omówienie ustawień plików

Udostępniany jest plik ConfigMap szablonu, który umożliwia łatwe edytowanie go przy użyciu dostosowań bez konieczności tworzenia go od podstaw. Przed rozpoczęciem należy zapoznać się z dokumentacją Kubernetes dotyczącą [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się z tematem jak tworzyć, konfigurować i wdrażać ConfigMaps. Pozwoli to na filtrowanie stderr i stdout dla przestrzeni nazw lub całego klastra oraz zmiennych środowiskowych dla dowolnego kontenera uruchomionego we wszystkich/wszystkich węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w celu zbierania zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów to ciprod06142019 lub nowszy. Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** . Aby uzyskać dodatkowe informacje na temat wersji agenta i elementów uwzględnionych w każdej wersji, zobacz [Informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Ustawienia zbierania danych

Poniżej przedstawiono ustawienia, które można skonfigurować w celu kontrolowania zbierania danych.

|Klucz |Typ danych |Wartość |Opis |
|----|----------|------|------------|
|`schema-version` |Ciąg (z uwzględnieniem wielkości liter) |v1 |Jest to wersja schematu używana przez agenta podczas analizowania tego ConfigMap. Obecnie obsługiwana wersja schematu to v1. Modyfikowanie tej wartości nie jest obsługiwane i zostanie odrzucone po obliczeniu ConfigMap.|
|`config-version` |Ciąg | | Program obsługuje możliwość śledzenia wersji tego pliku konfiguracji w systemie/repozytorium kontroli źródła. Maksymalna dozwolona liczba znaków wynosi 10, a wszystkie inne znaki są obcinane. |
|`[log_collection_settings.stdout] enabled =` |Wartość logiczna | true lub false | Ta funkcja kontroluje, czy jest włączone zbieranie dzienników strumienia stdout. Gdy jest ustawiona na `true` i żadne obszary nazw nie są wykluczone dla zbierania dzienników stdout (ustawienie`log_collection_settings.stdout.exclude_namespaces` poniżej), dzienniki strumienia strumieniowego będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Ciąg | Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stdout nie będą zbierane. To ustawienie obowiązuje tylko wtedy, gdy `log_collection_settings.stdout.enabled` jest ustawiona na `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Wartość logiczna | true lub false |Ta funkcja kontroluje, czy jest włączona kolekcja dzienników kontenera stderr. Gdy jest ustawiona na `true` i żadne obszary nazw nie są wykluczone na potrzeby zbierania dzienników stdout (ustawienie`log_collection_settings.stderr.exclude_namespaces`), dzienniki stderr będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Ciąg |Tablica rozdzielona przecinkami |Tablica przestrzeni nazw Kubernetes, dla których dzienniki stderr nie będą zbierane. To ustawienie obowiązuje tylko wtedy, gdy `log_collection_settings.stdout.enabled` jest ustawiona na `true`. Jeśli nie zostanie określony w ConfigMap, wartość domyślna to `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Wartość logiczna | true lub false | To ustawienie steruje kolekcją zmiennych środowiskowych w każdym zasobniku/węzłach w klastrze i domyślnie `enabled = true`, gdy nie zostanie określony w ConfigMaps. Jeśli kolekcja zmiennych środowiskowych jest włączona globalnie, można ją wyłączyć dla określonego kontenera przez ustawienie zmiennej środowiskowej `AZMON_COLLECT_ENV` na **false** przy użyciu ustawienia pliku dockerfile lub w [pliku konfiguracji dla elementu pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) w sekcji **env:** . Jeśli zbieranie zmiennych środowiskowych jest wyłączone globalnie, nie można włączyć kolekcji dla określonego kontenera (oznacza to, że jedynym przesłonięciem, który można zastosować na poziomie kontenera, jest wyłączenie kolekcji, gdy jest ona już włączona globalnie). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Wartość logiczna | true lub false | To ustawienie kontroluje wzbogacanie dzienników kontenerów, aby wypełnić wartości nazwy i właściwości obrazu dla każdego rekordu dziennika zapisanego w tabeli ContainerLog dla wszystkich dzienników kontenerów w klastrze. Domyślnie `enabled = false`, gdy nie zostanie określony w ConfigMap. |

ConfigMaps jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMaps kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny ConfigMap w klastrze.

1. [Pobierz](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) plik Template ConfigMap YAML i Zapisz go jako Container-AZM-MS-agentconfig. YAML. 

   >[!NOTE]
   >Ten krok nie jest wymagany podczas pracy z usługą Azure Red Hat OpenShift, ponieważ szablon ConfigMap już istnieje w klastrze.

2. Edytuj plik YAML ConfigMap przy użyciu dostosowań, aby zbierać zmienne stdout, stderr i/lub środowiskowe. Jeśli edytujesz plik ConfigMap YAML dla usługi Azure Red Hat OpenShift, najpierw uruchom polecenie `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`, aby otworzyć plik w edytorze tekstu.

    - Aby wykluczyć określone przestrzenie nazw dla zbierania dzienników stdout, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Aby wyłączyć kolekcję zmiennych środowiskowych dla określonego kontenera, ustaw `[log_collection_settings.env_var] enabled = true` klucz/wartość, aby umożliwić globalne zbieranie zmiennych, a następnie wykonaj kroki opisane [tutaj](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , aby ukończyć konfigurację dla określonego kontenera.
    
    - Aby wyłączyć zbieranie dzienników stderr dla całego klastra, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stderr] enabled = false`.

3. W przypadku klastrów innych niż Azure Red Hat OpenShift Utwórz ConfigMap, uruchamiając następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` w klastrach innych niż Azure Red Hat OpenShift. 
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    W przypadku usługi Azure Red Hat OpenShift Zapisz zmiany w edytorze.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Zweryfikuj konfigurację

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana do klastra innego niż Azure Red Hat OpenShift, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Następujące opcje są dostępne, aby wykonać dodatkowe Rozwiązywanie problemów z zmianami konfiguracji:

- Z dzienników agenta pod użyciem tego samego polecenia `kubectl logs`. 

    >[!NOTE]
    >To polecenie nie ma zastosowania do klastra Red Hat OpenShift platformy Azure.
    > 

- Z dzienników na żywo. Dzienniki na żywo pokazują błędy podobne do następujących:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym log Analytics. Dane są wysyłane co godzinę z ważnością *błędu* w przypadku błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie zawierał dane z *informacjami*o ważności, które nie zgłaszają błędów. Właściwość **Tags** zawiera więcej informacji na temat identyfikatora kontenera i, na których wystąpił błąd, a także pierwszego wystąpienia, ostatniego wystąpienia i liczby w ciągu ostatniej godziny.

- Korzystając z rozwiązania Azure Red Hat OpenShift, Sprawdź dzienniki omsagent, przeszukując tabelę **ContainerLog** , aby sprawdzić, czy jest włączone zbieranie dzienników na platformie Azure.

Po naprawieniu błędów w ConfigMap w klastrach innych niż Azure Red Hat OpenShift Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml`. W przypadku rozwiązania Azure Red Hat OpenShift należy edytować i zapisać zaktualizowane ConfigMaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrach innych niż Azure Red Hat OpenShift i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz edytować poprzednio używany plik ConfigMap, a następnie zastosować go przy użyciu tego samego polecenia jak wcześniej, `kubectl apply -f <configmap_yaml_file.yaml`. W przypadku rozwiązania Azure Red Hat OpenShift należy edytować i zapisać zaktualizowane ConfigMaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Weryfikowanie wersji schematu

Obsługiwane wersje schematu konfiguracji są dostępne jako adnotacja pod (wersje schematu) na omsagent pod. Można je wyświetlić za pomocą następującego polecenia polecenia kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Dane wyjściowe będą wyświetlane podobnie jak w przypadku następujących wersji schematu adnotacji:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Następne kroki

- Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](container-insights-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps.

- Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów AKS lub hybrydowego klastra oraz obciążeń działających na nich można dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.
