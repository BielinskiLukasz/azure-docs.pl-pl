---
title: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów w środowisku platformy Azure
description: W tym artykule opisano kroki tworzenia definicji zasad umożliwiających identyfikację niezgodnych zasobów.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 43f069fbd8f4fcc13bbc4d9e75763fa98aec1065
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601466"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów w środowisku platformy Azure

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one *niezgodne* z przypisaniem zasad.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki start utworzysz przypisanie zasad i przypiszesz definicję zasad *Audit Virtual Machines without Managed Disks* (Przeprowadź inspekcję maszyn wirtualnych bez dysków zarządzanych).

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

   ![Wyszukiwanie zasad](media/assign-policy-definition/search-policy.png)

2. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.
3. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   ![Przypisywanie definicji zasad](media/assign-policy-definition/select-assign-policy.png)

4. Na stronie **Przypisywanie zasad** wybierz pozycję **Zakres**, klikając wielokropek i wybierając subskrypcję (wymagane) i grupę zasobów (opcjonalnie). Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad.  Następnie kliknij przycisk **Wybierz** w dolnej części strony **Zakres**.

   W tym przykładzie użyto **subskrypcji Contoso**. Twoja subskrypcja będzie inna.

5. Jeśli chcesz wykluczyć jedną grupę zasobów lub większą ich liczbę (w przypadku wybrania tylko subskrypcji) albo określone zasoby w grupie zasobów (w obu przypadkach), w przypisaniu zasad możesz skonfigurować **Wykluczenia**. Chwilowo pozostaw to pole puste.

6. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Usługa Azure Policy zawiera już wbudowane definicje zasad, których możesz używać. Dostępne są liczne wbudowane definicje zasad, takie jak:

   - Enforce tag and its value (Wymuś tag i jego wartość)
   - Apply tag and its value (Zastosuj tag i jego wartość)
   - Require SQL Server Version 12.0 (Wymagaj programu SQL Server w wersji 12.0)

    Aby uzyskać pełną listę wszystkich dostępnych zasad wbudowanych, zobacz [Przykłady zasad](json-samples.md).

7. Przeszukaj listę definicji zasad, aby znaleźć definicję *Audit VMs that do not use managed disks* (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych). Kliknij te zasady, a następnie kliknij pozycję **Wybierz**.

   ![Wyszukiwanie prawidłowej definicji zasad](media/assign-policy-definition/select-available-definition.png)

8. Pole **Nazwa przypisania** jest automatycznie wypełniane przy użyciu nazwy wybranych zasad, ale można ją zmienić. W tym przykładzie pozostaw nazwę *Audit VMs that do not use managed disks* (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych). Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.

9. Kliknij przycisk **Przypisz**.

Teraz możesz zidentyfikować niezgodne zasoby, aby poznać stan zgodności Twojego środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

![Zgodność zasad](media/assign-policy-definition/policy-compliance.png)

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Jeśli warunek zostanie oceniony dla istniejących zasobów i okaże się prawdziwy, zasoby te zostaną oznaczone jako niezgodne z zasadami. W poniższej tabeli przedstawiono sposób, w jaki różne akcje dotyczące zasad wpływają na ocenę warunku na potrzeby wynikowego stanu zgodności. Mimo że logika oceny nie jest widoczna w witrynie Azure Portal, wyniki stanu zgodności są wyświetlane. Wynik stanu zgodności może być zgodny lub niezgodny.

| **Stan zasobu** | **Efekt** | **Ocena zasad** | **Stan zgodności** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Zgodne |
| Nowa | Audit, AuditIfNotExist\* | True | Niezgodne |
| Nowa | Audit, AuditIfNotExist\* | False | Zgodne |

\* Efekty Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE. Ponadto efekty wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki w tej kolekcji bazują na tym przewodniku Szybki start. Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

2. Kliknij prawym przyciskiem myszy przypisanie **Audit VMs that do not use managed disks** (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych), a następnie wybierz pozycję **Usuń przypisanie**

   ![Usuwanie przypisania](media/assign-policy-definition/delete-assignment.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przypisano definicję zasad do zakresu i oceniono raport jego zgodności. Definicja zasad zapewnia, że wszystkie zasoby w ramach zakresu są zgodne, a także określa niezgodne zasoby.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu zapewnienia zgodności zasobów tworzonych w **przyszłości**, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](create-manage-policy.md)