---
title: Jak używać prywatnego repozytorium Helm w Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Używaj prywatnego repozytorium Helm w obszarze dev platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery, Helm
manager: gwallace
ms.openlocfilehash: dddec69dc019f286c714a09e6f1a7e47240d5572
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867281"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Korzystanie z prywatnego repozytorium Helm w Azure Dev Spaces

[Helm][helm] to Menedżer pakietów dla Kuberentes. Helm używa formatu [wykresu][helm-chart] do zależności pakietów. Wykresy Helm są przechowywane w repozytorium, które może być publiczne lub prywatne. Azure Dev Spaces pobiera tylko wykresy Helm z repozytoriów publicznych podczas uruchamiania aplikacji. W przypadkach, gdy repozytorium Helm jest prywatne lub Azure Dev Spaces nie może uzyskać do niego dostępu, można dodać wykres z tego repozytorium bezpośrednio do aplikacji. Bezpośrednie dodanie wykresu umożliwia Azure Dev Spaces uruchomienie aplikacji bez konieczności uzyskiwania dostępu do prywatnego repozytorium Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Dodawanie prywatnego repozytorium Helm do komputera lokalnego

Użyj [Helm repozytorium Dodaj][helm-repo-add] i [Helm][helm-repo-update] repozytorium, aby uzyskać dostęp do prywatnego repozytorium Helm z komputera lokalnego.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Dodawanie wykresu do aplikacji

Przejdź do katalogu projektu i uruchom `azds prep`.

```cmd
azds prep --public
```

> [!TIP]
> Polecenie `prep` podejmuje próbę wygenerowania [wykresu pliku dockerfile i Helm](../how-dev-spaces-works.md#prepare-your-code) dla projektu. Azure Dev Spaces używa tych plików do kompilowania i uruchamiania kodu, ale te pliki można modyfikować, jeśli chcesz zmienić sposób kompilowania i wykonywania projektu.

Utwórz plik [Requirements. YAML][helm-requirements] z wykresem w katalogu wykresów aplikacji. Na przykład jeśli aplikacja ma nazwę *APP1*, utworzysz *wykresy/APP1/Requirements. YAML*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Przejdź do katalogu wykresów aplikacji i Użyj [aktualizacji zależności Helm][helm-dependency-update] w celu zaktualizowania zależności Helm dla aplikacji i pobrania wykresu z repozytorium prywatnego.

```cmd
helm dependency update
```

Sprawdź, czy podkatalog *wykresów* z plikiem *tgz* został dodany do katalogu wykresów aplikacji. Na przykład *wykresy/APP1/wykresy/MyChart-0.1.0. tgz*.

Wykres z prywatnego repozytorium Helm został pobrany i dodany do projektu. Usuń plik *Requirements. YAML* , aby spacje dev nie spróbowały zaktualizować tej zależności.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Przejdź do katalogu głównego projektu i uruchom `azds up`, aby sprawdzić, czy aplikacja została pomyślnie uruchomiona w obszarze dev.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Helm i sposobach jego działania][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://v2.helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://v2.helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies