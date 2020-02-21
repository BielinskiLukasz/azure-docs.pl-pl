---
title: Zarządzanie zasobami za pomocą Microsoft Graph
titleSuffix: Azure AD B2C
description: Przygotuj się do zarządzania zasobami Azure AD B2C przy użyciu Microsoft Graph, rejestrując aplikację, której udzielono wymaganych uprawnień interfejs API programu Graph.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bcddcf051e73381af35ca40e22c443c7b9ae30a7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492944"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Zarządzanie Azure AD B2C przy użyciu Microsoft Graph

[Microsoft Graph][ms-graph] umożliwia zarządzanie wieloma zasobami w ramach dzierżawy Azure AD B2C, w tym kont użytkowników klienta i zasad niestandardowych. Pisząc skrypty lub aplikacje wywołujące [interfejs API Microsoft Graph][ms-graph-api], można zautomatyzować zadania zarządzania dzierżawcą, takie jak:

* Migrowanie istniejącego magazynu użytkownika do dzierżawy Azure AD B2C
* Wdrażanie zasad niestandardowych przy użyciu potoku platformy Azure w usłudze Azure DevOps i zarządzanie kluczami zasad niestandardowych
* Rejestracja użytkowników hosta na własnej stronie i tworzenie kont użytkowników w katalogu Azure AD B2C w tle
* Automatyzowanie rejestracji aplikacji
* Uzyskiwanie dzienników inspekcji

Poniższe sekcje ułatwiają przygotowanie się do korzystania z interfejsu API Microsoft Graph w celu zautomatyzowania zarządzania zasobami w katalogu Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Tryby interakcji interfejsu API Microsoft Graph

Istnieją dwa tryby komunikacji, których można używać podczas pracy z interfejsem API Microsoft Graph do zarządzania zasobami w dzierżawie Azure AD B2C:

* **Interaktywnie** — odpowiednie dla zadań wykonywanych jednorazowo należy użyć konta administratora w dzierżawie B2C do wykonywania zadań zarządzania. Ten tryb wymaga od administratora zalogowania się przy użyciu poświadczeń przed wywołaniem interfejsu API Microsoft Graph.

* **Zautomatyzowane** — w przypadku zaplanowanych lub ciągle wykonywanych zadań ta metoda korzysta z konta usługi skonfigurowanego z uprawnieniami wymaganymi do wykonywania zadań zarządzania. Tworzysz "konto usługi" w Azure AD B2C przez zarejestrowanie aplikacji, której aplikacje i skrypty używają do uwierzytelniania przy użyciu *identyfikatora aplikacji (klienta)* i przyznania poświadczeń klienta OAuth 2,0. W takim przypadku aplikacja działa jako sama do wywołania interfejsu API Microsoft Graph, a nie użytkownika administratora, tak jak w opisanej wcześniej metodzie interaktywnej.

Scenariusz interakcji **automatycznej** można włączyć przez utworzenie rejestracji aplikacji pokazanej w poniższych sekcjach.

## <a name="register-management-application"></a>Zarejestruj aplikację zarządzania

Zanim skrypty i aplikacje będą mogły korzystać z [interfejsu API Microsoft Graph][ms-graph-api] w celu zarządzania zasobami Azure AD B2C, należy utworzyć rejestrację aplikacji w dzierżawie Azure AD B2C, która przyznaje wymagane uprawnienia interfejsu API.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Udzielanie dostępu do interfejsu API

Następnie udziel zarejestrowanej aplikacji uprawnień do manipulowania zasobami dzierżawy za pomocą wywołań interfejsu API Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Utwórz klucz tajny klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Masz teraz aplikację, która ma uprawnienia do *tworzenia*, *odczytywania*, *aktualizowania*i *usuwania* użytkowników w dzierżawie Azure AD B2C. Przejdź do następnej sekcji, aby dodać uprawnienia do *aktualizacji haseł* .

## <a name="enable-user-delete-and-password-update"></a>Włącz usuwanie i aktualizowanie haseł użytkowników

Uprawnienie *Odczyt i zapis danych katalogu* **nie** obejmuje możliwości usuwania użytkowników ani aktualizowania haseł kont użytkowników.

Jeśli aplikacja lub skrypt musi usunąć użytkowników lub zaktualizować swoje hasła, należy przypisać rolę *administratora użytkownika* do aplikacji:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Użyj filtru **Directory + Subscription** , aby przełączyć się do dzierżawy Azure AD B2C.
1. Wyszukaj i wybierz **Azure AD B2C**.
1. W obszarze **Zarządzanie**wybierz pozycję **role i Administratorzy**.
1. Wybierz rolę **administratora użytkowników** .
1. Wybierz pozycję **Dodaj przypisania**.
1. W polu tekstowym **Wybierz** wpisz nazwę wcześniej zarejestrowanej aplikacji, na przykład *managementapp1*. Wybierz aplikację, gdy zostanie ona wyświetlona w wynikach wyszukiwania.
1. Wybierz pozycję **Dodaj**. Pełne propagowanie uprawnień może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

Teraz, po zarejestrowaniu aplikacji zarządzania i przyznaniu jej wymaganych uprawnień, Twoje aplikacje i usługi (na przykład Azure Pipelines) mogą używać swoich poświadczeń i uprawnień do współdziałania z interfejsem API Microsoft Graph.

* [B2C operacje obsługiwane przez Microsoft Graph](microsoft-graph-operations.md)
* [Zarządzanie kontami użytkowników Azure AD B2C przy użyciu Microsoft Graph](manage-user-accounts-graph-api.md)
* [Pobieranie dzienników inspekcji za pomocą interfejsu API raportowania usługi Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
