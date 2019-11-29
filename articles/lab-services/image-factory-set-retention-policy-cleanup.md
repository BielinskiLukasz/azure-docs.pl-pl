---
title: Konfigurowanie zasad przechowywania w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować zasady przechowywania, wyczyścić fabrykę i wycofać stare obrazy z DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1c18fc799014ad862c93076d695f2516c6363d
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560168"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Utwórz niestandardową fabrykę obrazów w Azure DevTest Labs
W tym artykule opisano konfigurowanie zasad przechowywania, czyszczenie fabryki oraz wycofywanie starych obrazów ze wszystkich innych laboratoriów DevTest w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że zostały wykonane następujące artykuły:

- [Tworzenie fabryki obrazu](image-factory-create.md)
- [Uruchamianie fabryki obrazu z usługi Azure DevOps](image-factory-set-up-devops-lab.md)
- [Zapisywanie obrazów niestandardowych i dystrybucja do wielu laboratoriów](image-factory-save-distribute-custom-images.md)

Następujące elementy powinny być już na miejscu:

- Laboratorium dla fabryki obrazów w Azure DevTest Labs
- Co najmniej jeden Azure DevTest Labs docelowy, w którym fabryka będzie dystrybuować złote obrazy
- Projekt usługi Azure DevOps używany do automatyzowania fabryki obrazu.
- Lokalizacja kodu źródłowego zawierającego skrypty i konfigurację (w naszym przykładzie w tym samym projekcie DevOps użytym powyżej)
- Definicja kompilacji służąca do organizowania zadań programu Azure PowerShell
 
## <a name="setting-the-retention-policy"></a>Ustawianie zasad przechowywania
Przed skonfigurowaniem kroków oczyszczania Zdefiniuj liczbę obrazów historycznych, które chcesz zachować w laboratorium DevTest Labs. Po wykonaniu artykułu [Uruchom fabrykę obrazu z usługi Azure DevOps](image-factory-set-up-devops-lab.md) , skonfigurowano różne zmienne kompilacji. Jedna z nich została **ImageRetention**. Ta zmienna jest ustawiana na `1`, co oznacza, że laboratoria DevTest nie będą zachować historii obrazów niestandardowych. Dostępne są tylko najnowsze obrazy rozproszone. Jeśli zmienisz tę zmienną na `2`, zostanie zachowany najnowszy obraz rozproszony oraz poprzednie. Możesz ustawić tę wartość, aby zdefiniować liczbę obrazów historycznych, które mają być utrzymywane w DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Czyszczenie fabryki
Pierwszym krokiem w czyszczeniu fabryki jest usunięcie złota maszyn wirtualnych obrazów z fabryki obrazu. Istnieje skrypt służący do wykonywania tego zadania podobnie jak w przypadku poprzednich skryptów. Pierwszym krokiem jest dodanie kolejnego zadania **programu Azure PowerShell** do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Krok programu PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Po utworzeniu nowego zadania na liście Wybierz element i Wypełnij wszystkie szczegóły, jak pokazano na poniższej ilustracji:

![Zadanie programu PowerShell oczyszczania starych obrazów](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skryptu są następujące: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Wycofywanie starych obrazów 
To zadanie usuwa wszystkie stare obrazy, zachowując tylko historię pasującą do zmiennej kompilacji **ImageRetention** . Dodaj do naszej definicji kompilacji dodatkowe zadanie kompilacji **programu Azure PowerShell** . Po dodaniu wybierz zadanie i wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Wycofywanie starych obrazów — zadanie programu PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skryptu są następujące: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Kolejkowanie kompilacji
Po zakończeniu definiowania kompilacji Utwórz kolejkę nowej kompilacji, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe są wyświetlane w laboratorium docelowym, a w przypadku zaznaczenia programu Image Factory Lab nie są wyświetlane maszyny wirtualne z obsługą administracyjną. Ponadto w przypadku tworzenia kolejek dalszych kompilacji są wyświetlane zadania oczyszczania, które wycofywanie starych obrazów niestandardowych z laboratoriów DevTest zgodnie z wartością przechowywania ustawioną w zmiennych kompilacji.

> [!NOTE]
> Jeśli potok kompilacji został wykonany na końcu ostatniego artykułu w serii, ręcznie usuń maszyny wirtualne, które zostały utworzone w laboratorium fabryki obrazu przed przystąpieniem do kolejkowania nowej kompilacji.  Krok ręcznego czyszczenia jest wymagany tylko wtedy, gdy ustawimy wszystko i sprawdzimy, czy działa.



## <a name="summary"></a>Podsumowanie
Teraz masz działającą fabrykę obrazu, która umożliwia generowanie i dystrybucję obrazów niestandardowych do laboratoriów na żądanie. W tym momencie jest to bardzo ważne, aby zapewnić prawidłowe skonfigurowanie obrazów i zidentyfikowanie laboratoriów docelowych. Jak wspomniano w poprzednim artykule, plik **Labs. JSON** znajdujący się w folderze **konfiguracyjnym** określa, które obrazy należy udostępnić w każdej z laboratoriów docelowych. Po dodaniu innych laboratoriów DevTest do organizacji wystarczy dodać wpis w pliku Labs. JSON dla nowego laboratorium.

Dodawanie nowego obrazu do fabryki jest również proste. Jeśli chcesz dołączyć nowy obraz do fabryki, Otwórz [Azure Portal](https://portal.azure.com), przejdź do portalu fabryki DevTest Labs, wybierz przycisk, aby dodać maszynę wirtualną, a następnie wybierz żądany obraz i artefakty witryny Marketplace. Zamiast wybierać przycisk **Utwórz** , aby utworzyć nową maszynę wirtualną, wybierz pozycję **Wyświetl Azure Resource Manager szablon**i Zapisz szablon jako plik JSON w miejscu w folderze **GoldenImages** w repozytorium. Przy następnym uruchomieniu fabryki obrazu zostanie utworzony niestandardowy obraz.


## <a name="next-steps"></a>Następne kroki
1. [Zaplanuj kompilację/wydanie](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) , aby okresowo uruchamiać fabrykę obrazu. W regularnych odstępach czasu odświeżane są obrazy generowane przez fabrykę.
2. Utwórz więcej złota obrazów dla fabryki. Możesz również rozważyć [utworzenie artefaktów](devtest-lab-artifact-author.md) , aby dodać do skryptu dodatkowe fragmenty zadań konfiguracyjnych maszyny wirtualnej i uwzględnić artefakty w obrazach fabryki.
4. Utwórz [oddzielną kompilację/wydanie](/azure/devops/pipelines/overview?view=azure-devops-2019) , aby osobno uruchomić skrypt **DistributeImages** . Ten skrypt można uruchomić, gdy wprowadzisz zmiany w pliku Labs. JSON i pobierzesz obrazy skopiowane do laboratoriów docelowych bez konieczności ponownego tworzenia wszystkich obrazów.

