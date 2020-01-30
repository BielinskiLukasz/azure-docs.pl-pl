---
title: Definiowanie atrybutów niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj atrybuty niestandardowe dla aplikacji w Azure Active Directory B2C, aby zbierać informacje o klientach.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 433eff8f7ec22a3484e8e7f38dab2bb1c24e2fcc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848072"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w Azure Active Directory B2C

 Każda aplikacja dołączona do klienta ma unikatowe wymagania dotyczące informacji, które należy zebrać. Dzierżawa Azure Active Directory B2C (Azure AD B2C) zawiera wbudowany zestaw informacji przechowywanych w atrybutach, takich jak imię, nazwisko, miejscowość i kod pocztowy. Za pomocą Azure AD B2C można rozłożyć zestaw atrybutów przechowywanych na poszczególnych kontach klientów.

 Można tworzyć niestandardowe atrybuty w [Azure Portal](https://portal.azure.com/) i używać ich w przepływach użytkownika tworzenia konta, tworzeniu kont lub zalogowaniu lub przepisywaniu przepływów użytkowników. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejs API programu Graph usługi Azure AD](manage-user-accounts-graph-api.md). Atrybuty niestandardowe w Azure AD B2C używają [rozszerzeń schematu usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> Obsługa nowszych [Microsoft Graph interfejsu API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) na potrzeby wykonywania zapytań dotyczących Azure AD B2C dzierżawy jest nadal w fazie projektowania.
>

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutu niestandardowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**.

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wyświetlona dzierżawa B2C w katalogu i filtrze subskrypcji](./media/user-flow-custom-attributes/select-directory.PNG)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **atrybuty użytkownika**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwę** dla atrybutu niestandardowego (na przykład "ShoeSize")
6. Wybierz **Typ danych**. Dostępne są tylko **ciągi**, **wartości logiczne**i **int** .
7. Opcjonalnie wprowadź **Opis** dla celów informacyjnych.
8. Kliknij pozycję **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście **atrybutów użytkownika** i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko za pierwszym razem, gdy jest używany w dowolnym przepływie użytkownika, a nie po dodaniu go do listy **atrybutów użytkownika**.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Używanie atrybutu niestandardowego w przepływie użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
2. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
4. Wybierz pozycję **atrybuty użytkownika** , a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij pozycję **Zapisz**.
5. Wybierz pozycję **oświadczenia aplikacji** , a następnie wybierz atrybut niestandardowy.
6. Kliknij pozycję **Zapisz**.

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który korzysta z nowo utworzonego atrybutu niestandardowego, można zbadać obiekt w [Eksploratorze Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Alternatywnie możesz użyć funkcji [**Uruchom przepływ użytkownika**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) w przepływie użytkownika, aby zweryfikować środowisko klienta. Na liście atrybutów zebranych podczas rejestracji można teraz zobaczyć **ShoeSize** i zobaczyć je w tokenie wysyłanym z powrotem do aplikacji.

