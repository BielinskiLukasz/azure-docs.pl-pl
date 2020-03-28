---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 40cc1856a5e943ca5596e7d11712febadd30e3ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67132991"
---
## <a name="prerequisites"></a>Wymagania wstępne

### <a name="get-sample-code"></a>Pobieranie przykładowego kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](../articles/container-registry/container-registry-tutorial-quick-task.md) oraz że przykładowe repozytorium zostało rozwidlone i sklonowane. Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites).

### <a name="container-registry"></a>Rejestr kontenerów

Aby ukończyć ten samouczek, w Twojej subskrypcji platformy Azure musisz posiadać rejestr kontenerów platformy Azure. Jeśli potrzebujesz rejestru, zobacz [poprzedni samouczek](../articles/container-registry/container-registry-tutorial-quick-task.md) lub [Szybki start: tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Tworzenie osobistego tokenu dostępu GitHub

Aby wyzwolić zadanie w zatwierdzeniu do repozytorium Git, zadania ACR potrzebują osobistego tokenu dostępu (PAT), aby uzyskać dostęp do repozytorium. Jeśli nie masz jeszcze PAT, wykonaj następujące kroki, aby wygenerować jeden w GitHub:

1. Przejdź do strony tworzenia tokenu PAT w witrynie GitHub pod adresem https://github.com/settings/tokens/new
1. Wprowadź krótki **opis** dla tokenu, na przykład „Przykładowe zadanie ACR Tasks”
1. Wybierz zakresy dla usługi ACR, aby uzyskać dostęp do repozytorium. Aby uzyskać dostęp do publicznego repozytorium, jak w tym samouczku, w obszarze **repozytorium**włącz **repo:status** i **public_repo**

   ![Zrzut ekranu strony generowania osobistego tokenu dostępu w usłudze GitHub][build-task-01-new-token]

   > [!NOTE]
   > Aby wygenerować pat, aby uzyskać dostęp do *prywatnego* repozytorium, wybierz zakres pełnej kontroli **repozytorium.**

1. Wybierz przycisk **Generate token** (Generuj token) (może zostać wyświetlony monit o potwierdzenie hasła)
1. Skopiuj i zapisz wygenerowany token w **bezpiecznej lokalizacji** (użyjesz tego tokenu podczas definiowania zadania w następnej sekcji)

   ![Zrzut ekranu przedstawiający wygenerowany osobisty token dostępu w usłudze GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
