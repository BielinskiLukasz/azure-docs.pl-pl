---
title: Dodawanie istniejącej subskrypcji platformy Azure do dzierżawy — Azure AD
description: Instrukcje dotyczące sposobu dodawania istniejącej subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f44e9a1e43da2b9ce6c817898c1722fba715c4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262180"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Skojarzyć lub dodać subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory

Subskrypcja platformy Azure ma relację zaufania z usługą Azure Active Directory (Azure AD). Subskrypcja ufa usłudze Azure AD w celu uwierzytelniania użytkowników, usług i urządzeń.

Wiele subskrypcji może ufać temu samemu katalogowi usługi Azure AD. Każda subskrypcja może ufać tylko jednemu katalogowi.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z tą subskrypcją. Jednak katalog usługi Azure AD pozostaje na platformie Azure. Możesz skojarzyć katalog i zarządzać nim przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden katalog *macierzysty* na potrzeby uwierzytelniania. Użytkownicy mogą również być Gośćmi w innych katalogach. Możesz zobaczyć oba katalogi gości i macierzystego dla każdego użytkownika w usłudze Azure AD.

> [!Important]
> Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którzy mają przypisane role przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) , utracą dostęp. Klasyczni administratorzy subskrypcji, w tym administrator usługi i współadministratorzy, również utracą dostęp.
>
> Przypisania zasad są również usuwane z subskrypcji, gdy subskrypcja jest kojarzona z innym katalogiem.
>
> Przeniesienie klastra usługi Azure Kubernetes Service (AKS) do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem klastra do nowej dzierżawy powoduje utratę funkcjonalności klastra z powodu utraty przypisań ról i uprawnień jednostki usługi. Aby uzyskać więcej informacji na temat AKS, zobacz [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było skojarzyć lub dodać subskrypcję, wykonaj następujące zadania:

- Przejrzyj poniższą listę zmian i dowiesz się, jak to miało wpływ:

  - Użytkownicy, którym przypisano role przy użyciu RBAC, utracą dostęp
  - Administrator usługi i współadministratorzy utracą dostęp
  - Jeśli masz jakieś magazyny kluczy, staną się niedostępne i będziesz musiał rozwiązać te problemy po skojarzeniu
  - Jeśli masz jakiekolwiek zarządzane tożsamości dla zasobów, takich jak Virtual Machines lub Logic Apps, musisz je ponownie włączyć lub utworzyć po skojarzeniu
  - Jeśli masz zarejestrowaną Azure Stack, musisz ponownie zarejestrować ją po skojarzeniu

- Zaloguj się przy użyciu konta, które:

  - Ma przypisanie roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) do subskrypcji. Aby uzyskać informacje na temat sposobu przypisywania roli właściciela, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Istnieje zarówno w bieżącym katalogu, jak i w nowym katalogu. Bieżący katalog jest skojarzony z subskrypcją. Nowy katalog zostanie skojarzony z subskrypcją. Aby uzyskać więcej informacji o uzyskiwaniu dostępu do innego katalogu, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../b2b/add-users-administrator.md).

- Upewnij się, że nie używasz subskrypcję dostawcy usługi Azure w chmurze (CSP) (MS-AZR - 0145 P, MS - AZR - 0146 P, MS - AZR - 159 P), subskrypcję Internal firmy Microsoft (MS-AZR - 0015 P) lub subskrypcję programu Microsoft Imagine (MS-AZR - 0144 P).

## Kojarzenie subskrypcji z katalogiem<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Aby skojarzyć istniejącą subskrypcję z katalogiem usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć na [stronie Subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Wybierz pozycję **Zmień katalog**.

    ![Strony subskrypcje, z podświetloną opcją katalogu zmiany](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Przejrzyj wszystkie wyświetlone ostrzeżenia, a następnie wybierz pozycję **Zmień**.

    ![Zmień przedstawiający katalogu, aby zmienić stronę katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Zmianie katalogu subskrypcji, i otrzymujesz komunikat o powodzeniu.

    ![Komunikat o powodzeniu zmiany katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Użyj **katalogu przełącznika** , aby przejść do nowego katalogu. Poprawne wyświetlanie wszystkiego może potrwać kilka godzin. Jeśli wydaje się, że trwa zbyt długo, sprawdź **globalny filtr subskrypcji**. Upewnij się, że przeniesiona subskrypcja nie jest ukryta. Może być konieczne wylogowanie się z Azure Portal i ponowne zalogowanie się w celu wyświetlenia nowego katalogu.

![Strona przełączników katalogów z przykładowymi informacjami](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmienianie katalogu subskrypcji jest operacją poziomu usług, dzięki czemu nie ma wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmienić administratora usługi z [centrum konta](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, należy przenieść własność rozliczeń subskrypcji do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroki po skojarzeniu

Po skojarzeniu subskrypcji z innym katalogiem, może być konieczne wykonanie następujących zadań w celu wznowienia operacji:

- Jeśli masz jakieś magazyny kluczy, musisz zmienić identyfikator dzierżawy magazynu kluczy. Aby uzyskać więcej informacji, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../../key-vault/key-vault-subscription-move-fix.md).

- W przypadku używania tożsamości zarządzanych przypisanych przez system do zasobów należy ponownie włączyć te tożsamości. W przypadku używania tożsamości zarządzanych przypisanych przez użytkownika należy ponownie utworzyć te tożsamości. Po ponownym włączeniu lub odtworzeniu tożsamości zarządzanych należy ponownie ustanowić uprawnienia przypisane do tych tożsamości. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

- Jeśli zarejestrowano Azure Stack przy użyciu tej subskrypcji, należy ponownie zarejestrować. Aby uzyskać więcej informacji, zobacz [rejestrowanie Azure Stack na platformie Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory](active-directory-access-create-new-tenant.md).

- Aby dowiedzieć się więcej o tym, jak Microsoft Azure kontroluje dostęp do zasobów, zobacz Role [administratora subskrypcji klasycznej, role RBAC platformy Azure i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Aby dowiedzieć się więcej o sposobach przypisywania ról w usłudze Azure AD, zobacz [Przypisywanie ról administratora i niebędących administratorami do użytkowników z Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
