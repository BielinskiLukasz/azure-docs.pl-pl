---
title: Samouczek — Tworzenie dzierżawy Azure Active Directory B2C
description: Dowiedz się, jak przygotować się do rejestracji aplikacji, tworząc dzierżawę Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4e2957ea179d9737bb6aad6fb8f121780575115b
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976397"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C

Aby aplikacje mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako **ulubiony** w Azure Portal

Dowiesz się, jak zarejestrować aplikację w następnym samouczku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Tworzenie dzierżawy usługi Azure AD B2C

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Zaloguj się przy użyciu konta platformy Azure, do którego przypisano co najmniej rolę [współautor](../role-based-access-control/built-in-roles.md) w ramach subskrypcji lub grupy zasobów w ramach subskrypcji.

1. Wybierz katalog, który zawiera twoją subskrypcję.

    Na pasku narzędzi Azure Portal wybierz ikonę **katalog i subskrypcja** , a następnie wybierz katalog, który zawiera subskrypcję. Ten katalog różni się od tego, który będzie zawierać dzierżawę Azure AD B2C.

    ![Dzierżawa subskrypcji, katalog + subskrypcja z wybraną dzierżawą subskrypcji](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
1. Wyszukaj **Azure Active Directory B2C**, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C**.

    ![Utwórz nową dzierżawę Azure AD B2C wybraną w Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Na stronie **Tworzenie katalogu** wprowadź następujące elementy:

   - **Nazwa organizacji** — wprowadź nazwę swojej organizacji.
   - **Początkowa nazwa domeny** — wprowadź nazwę domeny. Domyślnie ta nazwa jest dołączana do *. onmicrosoft.com*. Można to zmienić później przez dodanie nazwy domeny używanej przez organizację, na przykład "contoso.com".
   - **Kraj lub region** — wybierz z listy swój kraj lub region. Tego wyboru nie można później zmienić.
   - **Subskrypcja** — wybierz subskrypcję z listy.
   - **Grupa zasobów** — wybierz grupę zasobów, która będzie zawierać dzierżawę. Lub wybierz pozycję **Utwórz nowy**, wprowadź **nazwę** grupy zasobów, wybierz **lokalizację grupy zasobów**, a następnie wybierz przycisk **OK**.

    ![Utwórz formularz dzierżawy przy użyciu przykładowych wartości w Azure Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Wybierz pozycję **Przegląd + utwórz**.
1. Przejrzyj ustawienia katalogu. Następnie wybierz pozycję **Utwórz**.

Możesz połączyć wiele dzierżawców Azure AD B2C z pojedynczą subskrypcją platformy Azure na potrzeby rozliczeń.

## <a name="select-your-b2c-tenant-directory"></a>Wybierz katalog dzierżawy B2C

Aby rozpocząć korzystanie z nowej dzierżawy Azure AD B2C, musisz przełączyć się do katalogu, który zawiera dzierżawcę.

Wybierz filtr **katalogów i subskrypcji** w górnym menu Azure Portal, a następnie wybierz katalog, który zawiera dzierżawę Azure AD B2C.

Jeśli na liście nie zobaczysz nowej dzierżawy usługi Azure B2C, Odśwież okno przeglądarki, a następnie ponownie wybierz filtr **katalog + subskrypcja** w górnym menu.

![B2C dzierżawców — zawierający katalog wybrany w Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Dodaj Azure AD B2C jako ulubiony (opcjonalnie)

Ten opcjonalny krok ułatwia wybranie dzierżawy Azure AD B2C w następujących i kolejnych samouczkach.

Zamiast wyszukiwania *Azure AD B2C* we **wszystkich usługach** za każdym razem, gdy chcesz korzystać z dzierżawy, możesz zamiast tego przystąpić do ulubionego zasobu. Następnie możesz wybrać ją z sekcji **Ulubione** menu portalu, aby szybko przejść do dzierżawy Azure AD B2C.

Wystarczy wykonać tę operację tylko raz. Przed wykonaniem tych kroków upewnij się, że została przełączona do katalogu zawierającego dzierżawę Azure AD B2C, zgodnie z opisem w poprzedniej sekcji, [Wybierz katalog dzierżawy B2C](#select-your-b2c-tenant-directory).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu Azure Portal wybierz pozycję **wszystkie usługi**.
1. W polu wyszukiwania **wszystkie usługi** wyszukaj pozycję **Azure AD B2C**, umieść kursor nad wynikiem wyszukiwania, a następnie wybierz ikonę gwiazdki w etykietce narzędzia. **Azure AD B2C** teraz pojawia się w Azure Portal w obszarze **Ulubione**.
1. Jeśli chcesz zmienić położenie nowego elementu ulubionego, przejdź do menu Azure Portal, wybierz pozycję **Azure AD B2C**, a następnie przeciągnij go w górę lub w dół do żądanej pozycji.

    ![Azure AD B2C, menu Ulubione, Microsoft Azure Portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dzierżawy usługi Azure AD B2C
> * Łączenie dzierżawy z subskrypcją
> * Przejdź do katalogu zawierającego dzierżawcę Azure AD B2C
> * Dodaj zasób Azure AD B2C jako **ulubiony** w Azure Portal

Następnie Dowiedz się, jak zarejestrować aplikację sieci Web w nowej dzierżawie.

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji >](tutorial-register-applications.md)
