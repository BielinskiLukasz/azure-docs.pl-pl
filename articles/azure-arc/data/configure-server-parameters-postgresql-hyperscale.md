---
title: Skonfiguruj parametry serwera aparatu Postgres dla grupy serwerów PostgreSQL w usłudze Azure Arc
titleSuffix: Azure Arc enabled data services
description: Skonfiguruj parametry serwera aparatu Postgres dla grupy serwerów PostgreSQL w usłudze Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4e8813647211e0adbfe43a45ae0d19dc12a4a165
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939206"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Ustawianie ustawień aparatu bazy danych dla usługi Azure ARC z włączonym skalowaniem PostgreSQL

W tym dokumencie opisano procedurę ustawiania ustawień aparatu bazy danych dla grupy serwerów PostgreSQL w ramach skalowania do wartości niestandardowych (niedomyślnych). Aby uzyskać szczegółowe informacje na temat tego, jakie parametry aparatu bazy danych można ustawić i jakie są ich wartości domyślne, zapoznaj się z dokumentacją PostgreSQL [tutaj](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> Wersja zapoznawcza nie obsługuje ustawiania następujących parametrów: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Składnia

Ogólny format polecenia konfigurowania ustawień aparatu bazy danych:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-the-current-custom-values-of-the-parameters-settings"></a>Pokaż bieżące wartości niestandardowe ustawień parametrów

## <a name="with-azdata-cli-command"></a>Przy użyciu interfejsu wiersza polecenia azdata

```console
azdata arc postgres server show -n <server group name>
```

Przykład:

```console
azdata arc postgres server show -n postgres01
```

To polecenie zwraca specyfikację grupy serwerów, w której można zobaczyć ustawiane parametry. Jeśli nie ma sekcji engine\settings, oznacza to, że wszystkie parametry są uruchomione w wartości domyślnej:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

## <a name="with-kubectl-command"></a>Za pomocą polecenia polecenia kubectl

Wykonaj poniższe czynności.

### <a name="1-retrieve-the-kind-of-custom-resource-definition-for-your-server-group"></a>1. Pobierz rodzaj niestandardowej definicji zasobu dla grupy serwerów

Uruchom:

```console
azdata arc postgres server show -n <server group name>
```

Przykład:

```console
azdata arc postgres server show -n postgres01
```

To polecenie zwraca specyfikację grupy serwerów, w której można zobaczyć ustawiane parametry. Jeśli nie ma sekcji engine\settings, oznacza to, że wszystkie parametry są uruchomione w wartości domyślnej:

```
> {
  >"apiVersion": "arcdata.microsoft.com/v1alpha1",
  >"**kind**": "**postgresql-12**",
  >"metadata": {
    >"creationTimestamp": "2020-08-25T14:32:23Z",
    >"generation": 1,
    >"name": "postgres01",
    >"namespace": "arc",
```

W tym miejscu poszukaj pola "Kind" i Wykorzystaj wartość, na przykład: `postgresql-12` .

### <a name="2-describe-the-kubernetes-custom-resource-corresponding-to-your-server-group"></a>2. opisz zasób niestandardowy Kubernetes odpowiadający Twojej grupie serwerów 

Ogólny format polecenia to:

```console
kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
```

Przykład:

```console
kubectl describe postgresql-12 postgres01
```

Jeśli dla ustawień aparatu określono wartości niestandardowe, zostaną one zwrócone. Przykład:

```console
Engine:
...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
```

Jeśli nie ustawisz wartości niestandardowych dla żadnego z ustawień aparatu, sekcja ustawień aparatu zestawu wyników będzie pusta, taka jak:

```console
Engine:
...
    Settings:
      Default:
```

## <a name="set-custom-values-for-the-engine-settings"></a>Ustawianie wartości niestandardowych dla ustawień aparatu

Poniższe polecenia ustawiają parametry węzła koordynatora i węzły procesu roboczego PostgreSQL ze skalowaniem do tych samych wartości. Nie można jeszcze ustawić parametrów dla roli w grupie serwerów. Oznacza to, że nie można jeszcze skonfigurować danego parametru do określonego w węźle koordynatora i innej wartości węzłów procesu roboczego.

## <a name="set-a-single-parameter"></a>Ustawianie jednego parametru

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Przykład:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

## <a name="set-multiple-parameters-with-a-single-command"></a>Ustawianie wielu parametrów za pomocą jednego polecenia

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Przykład:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

## <a name="reset-a-parameter-to-its-default-value"></a>Zresetuj parametr do wartości domyślnej

Aby zresetować parametr do wartości domyślnej, ustaw ją bez wskazywania wartości. 

Przykład:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

## <a name="reset-all-parameters-to-their-default-values"></a>Zresetuj wszystkie parametry do ich wartości domyślnych

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Przykład:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Uwagi specjalne

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Ustaw parametr, który zawiera wartość zawierającą przecinek, spację lub znak specjalny

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Przykład:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Przekaż zmienną środowiskową do wartości parametru

Zmienna środowiskowa powinna być opakowana wewnątrz "" ", aby nie została rozwiązany przed jej ustawieniem.

Przykład: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```



## <a name="next-steps"></a>Następne kroki
- Przeczytaj o [skalowaniu (Dodawanie węzłów procesu roboczego)](scale-out-postgresql-hyperscale-server-group.md) do grupy serwerów
- Przeczytaj o [skalowaniu w górę lub w dół (Zwiększanie/zmniejszanie ilości pamięci/rdzeni wirtualnych)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) grupy serwerów
