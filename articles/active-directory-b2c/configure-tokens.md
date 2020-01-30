---
title: Konfigurowanie tokenów — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować okresy istnienia tokenu i ustawienia zgodności w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d6b18596082df6f1cfbe2a47627712b8b69cb355
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836613"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w Azure Active Directory B2C

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodność tokenu](tokens-overview.md) w Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) , aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie się do niej.

## <a name="configure-token-lifetime"></a>Skonfiguruj okres istnienia tokenu

Okres istnienia tokenu można skonfigurować w dowolnym przepływie użytkownika.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)** .
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz pozycję **Właściwości**.
7. W obszarze **okres istnienia tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości okresu istnienia tokenu w Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Kliknij pozycję **Zapisz**.

## <a name="configure-token-compatibility"></a>Konfigurowanie zgodności tokenu

1. Wybierz pozycję **przepływy użytkownika (zasady)** .
2. Otwórz wcześniej utworzony przepływ użytkownika.
3. Wybierz pozycję **Właściwości**.
4. W obszarze **Ustawienia zgodności tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości zgodności tokenu w Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [używania tokenów dostępu](access-tokens.md).



