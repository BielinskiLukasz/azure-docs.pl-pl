---
title: Konfigurowanie tokenów — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować okresy istnienia tokenu i ustawienia zgodności w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dca235374ca20ef8a17e685706f952127d5ece2a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869439"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w Azure Active Directory B2C

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodność tokenu](tokens-overview.md) w Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) , aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie się do niej.

## <a name="configure-jwt-token-lifetime"></a>Skonfiguruj okres istnienia tokenu JWT

Okres istnienia tokenu można skonfigurować w dowolnym przepływie użytkownika.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)**.
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz pozycję **Właściwości**.
7. W obszarze **okres istnienia tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości okresu istnienia tokenu w Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Kliknij przycisk **Zapisz**.

## <a name="configure-jwt-token-compatibility"></a>Konfigurowanie zgodności tokenów JWT

1. Wybierz pozycję **przepływy użytkownika (zasady)**.
2. Otwórz wcześniej utworzony przepływ użytkownika.
3. Wybierz pozycję **Właściwości**.
4. W obszarze **Ustawienia zgodności tokenu**Dostosuj następujące właściwości, aby dopasować je do potrzeb aplikacji:

    ![Ustawienia właściwości zgodności tokenu w Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [żądania tokenów dostępu](access-tokens.md).



