---
title: Rozdystrybuowane programowanie do współpracy Azure DevTest Labs zasobów
description: Zapewnia najlepsze rozwiązania dotyczące konfigurowania rozproszonego i współpracy środowiska programistycznego w celu opracowywania zasobów DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170119"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Najlepsze rozwiązania dotyczące rozbudowanych i rozwijających się zasobów Azure DevTest Labs
Rozdystrybuowane programowanie do współpracy umożliwia różnym zespołom i innym osobom programowanie i konserwację kodu. Aby się powieść, proces programowania zależy od możliwości tworzenia, udostępniania i integrowania informacji. Ta zasada tworzenia klucza może być używana w Azure DevTest Labs. Istnieje kilka rodzajów zasobów w środowisku laboratoryjnym, które są powszechnie dystrybuowane między różnymi laboratoriami w przedsiębiorstwie. Różne typy zasobów koncentrują się na dwóch obszarach:

- Zasoby, które są wewnętrznie przechowywane w laboratorium (oparte na laboratorium)
- Zasoby przechowywane w [zewnętrznych repozytoriach, które są połączone z laboratorium](devtest-lab-add-artifact-repo.md) (oparte na repozytorium kodu). 

W tym dokumencie opisano niektóre najlepsze rozwiązania, które pozwalają na współpracę i dystrybucję w wielu zespołach przy jednoczesnym zapewnieniu dostosowania i jakości na wszystkich poziomach.

## <a name="lab-based-resources"></a>Zasoby oparte na laboratorium

### <a name="custom-virtual-machine-images"></a>Niestandardowe obrazy maszyn wirtualnych
Możesz mieć wspólne źródło obrazów niestandardowych, które są wdrażane w laboratoriach w nocy. Aby uzyskać szczegółowe informacje, zobacz [Image Factory](image-factory-create.md).    

### <a name="formulas"></a>formuły
[Formuły](devtest-lab-manage-formulas.md) są specyficzne dla laboratorium i nie mają mechanizmu dystrybucji. Członkowie laboratorium wykonują wszystkie czynności w zakresie tworzenia formuł. 

## <a name="code-repository-based-resources"></a>Zasoby oparte na repozytorium kodu
Istnieją dwie różne funkcje, które są oparte na repozytoriach kodu, artefaktach i środowiskach. W tym artykule opisano funkcje i sposób najwydajniejszego konfigurowania repozytoriów i przepływu pracy w celu umożliwienia dostosowywania dostępnych artefaktów i środowisk na poziomie organizacji lub na szczeblu zespołu.  Ten przepływ pracy jest oparty na standardowej [strategii rozgałęziania kontroli kodu źródłowego](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Kluczowe pojęcia
Informacje o źródle artefaktów obejmują metadane i skrypty. Informacje źródłowe dla środowisk obejmują metadane i szablony Menedżer zasobów z dowolnymi plikami pomocniczymi, takimi jak skrypty programu PowerShell, skrypty DSC, pliki zip itd.  

### <a name="repository-structure"></a>Struktura repozytorium  
Najbardziej typową konfiguracją kontroli kodu źródłowego (SCC) jest skonfigurowanie struktury wielowarstwowej do przechowywania plików kodu (Menedżer zasobów szablonów, metadanych i skryptów) używanych w laboratoriach. W tym celu należy utworzyć różne repozytoria do przechowywania zasobów zarządzanych przez różne poziomy działalności:   

- Zasoby w całej firmie.
- Zasoby jednostkowe/działy dla całej firmy
- Zasoby specyficzne dla zespołu.

Każdy z tych poziomów łączy się z innym repozytorium, w którym główna gałąź musi być jakością produkcyjną. [Gałęzie](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) w każdym repozytorium są przeznaczone do tworzenia tych konkretnych zasobów (artefaktów lub szablonów). Ta struktura jest dobrze dopasowana do DevTest Labs, ponieważ w tym samym czasie można łatwo połączyć wiele repozytoriów i wiele gałęzi z laboratoriami organizacji. Nazwa repozytorium jest zawarta w interfejsie użytkownika, aby uniknąć pomyłek w przypadku, gdy istnieją identyczne nazwy, opis i Wydawca.
     
Na poniższym diagramie przedstawiono dwa repozytoria: repozytorium firmy utrzymywane przez dział IT oraz repozytorium dzielenia utrzymywane przez dział R & D.

![Przykładowe rozdzielne i wspólne środowisko programistyczne](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Ta struktura warstwowa umożliwia programowanie z zachowaniem wyższego poziomu jakości w gałęzi głównej, przy czym wiele repozytoriów podłączonych do laboratorium zapewnia większą elastyczność.

## <a name="next-steps"></a>Następne kroki    
Zobacz następujące artykuły:

- Dodawanie repozytorium do laboratorium przy użyciu [Azure Portal](devtest-lab-add-artifact-repo.md) lub [szablonu usługi Azure Resource Management](add-artifact-repository.md)
- [Artefakty DevTest Labs](devtest-lab-artifact-author.md)
- [Środowiska DevTest Labs](devtest-lab-create-environment-from-arm.md).
