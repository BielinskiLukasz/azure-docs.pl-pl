---
title: Dodaj role aplikacji i uzyskaj je z tokenu | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dodawać role aplikacji w aplikacji zarejestrowanej w Azure Active Directory, przypisywać użytkownikom i grupom te role i odbierać je w ramach żądania `roles` w tokenie.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399079"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Instrukcje: Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie

Kontrola dostępu oparta na rolach (RBAC) to popularny mechanizm wymuszania autoryzacji w aplikacjach. W przypadku korzystania z RBAC administrator przyznaje uprawnienia do ról, a nie do poszczególnych użytkowników lub grup. Administrator może następnie przypisać role do różnych użytkowników i grup w celu kontrolowania, kto ma dostęp do zawartości i funkcjonalności.

Korzystając z funkcji RBAC z rolami aplikacji i oświadczeniami ról, deweloperzy mogą bezpiecznie wymuszać autoryzację w swoich aplikacjach, niewielkim nakładem pracy z ich częścią.

Innym podejściem jest użycie grup i oświadczeń grup usługi Azure AD, jak pokazano w [webapp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Grupy i role aplikacji usługi Azure AD nie wykluczają się wzajemnie. mogą one być używane wspólnie w celu zapewnienia jeszcze bardziej precyzyjnej kontroli dostępu.

## <a name="declare-roles-for-an-application"></a>Deklarowanie ról dla aplikacji

Te role aplikacji są zdefiniowane w [Azure Portal](https://portal.azure.com) w manifeście rejestracji aplikacji.  Gdy użytkownik loguje się do aplikacji, usługa Azure AD emituje `roles` rolę dla każdej roli, którą użytkownik otrzymał indywidualnie do użytkownika i od ich przynależności do grupy.  Przypisywanie użytkowników i grup do ról może odbywać się za pośrednictwem interfejsu użytkownika portalu lub programowo przy użyciu [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarowanie ról aplikacji przy użyciu Azure Portal

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** .
1. Z listy **Ulubione** lub **wszystkie katalogi** wybierz Active Directory dzierżawy, w której chcesz zarejestrować aplikację.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W okienku **Azure Active Directory** wybierz pozycję **rejestracje aplikacji** , aby wyświetlić listę wszystkich aplikacji.
1. Wybierz aplikację, w której chcesz zdefiniować role aplikacji. Następnie wybierz pozycję **manifest**.
1. Edytuj manifest aplikacji, lokalizowanie ustawienia `appRoles` i Dodawanie wszystkich ról aplikacji.

     > [!NOTE]
     > Każda definicja roli aplikacji w tym manifeście musi mieć inny prawidłowy identyfikator GUID w kontekście manifestu dla właściwości `id`.
     >
     > Właściwość `value` każdej definicji roli aplikacji powinna dokładnie pasować do ciągów, które są używane w kodzie w aplikacji. Właściwość `value` nie może zawierać spacji. Jeśli tak się stanie, podczas zapisywania manifestu zostanie wyświetlony komunikat o błędzie.

1. Zapisz manifest.

### <a name="examples"></a>Przykłady

Poniższy przykład pokazuje `appRoles`, które można przypisać do `users`.

> [!NOTE]
>`id` musi być unikatowym identyfikatorem GUID.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>`displayName` nie może zawierać spacji.

Możesz definiować role aplikacji, aby docelowa `users`, `applications`lub obie. Gdy jest dostępna do `applications`, role aplikacji są wyświetlane jako uprawnienia aplikacji w bloku **wymagane uprawnienia** . Poniższy przykład przedstawia rolę aplikacji ukierunkowaną na `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Liczba zdefiniowanych ról wpływa na limity, które ma manifest aplikacji. Zostały one szczegółowo omówione na stronie [limitów manifestu](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Przypisywanie użytkowników i grup do ról

Po dodaniu ról aplikacji w aplikacji można przypisać użytkowników i grupy do tych ról.

1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie **Azure Active Directory** .
1. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

     Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, Użyj różnych filtrów w górnej części listy **wszystkie aplikacje** , aby ograniczyć listę, lub przewiń w dół listy, aby zlokalizować aplikację.

1. Wybierz aplikację, do której chcesz przypisać użytkowników lub grupę zabezpieczeń do ról.
1. Wybierz okienko **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.
1. W górnej części listy **Użytkownicy i grupy** wybierz przycisk **Dodaj użytkownika** , aby otworzyć okienko **Dodaj przypisanie** .
1. Wybierz selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

     Zostanie wyświetlona lista użytkowników i grup zabezpieczeń wraz z polem tekstowym umożliwiającym wyszukiwanie i lokalizowanie określonego użytkownika lub grupy. Ten ekran umożliwia wybranie wielu użytkowników i grup w jednym przejściu.

1. Po wybraniu opcji użytkownicy i grupy naciśnij przycisk **Wybierz** na dole, aby przejść do następnej części.
1. Wybierz selektor **roli wybierz** z okienka **Dodaj przypisanie** . Zostaną wyświetlone wszystkie role zadeklarowane wcześniej w manifeście aplikacji.
1. Wybierz rolę i naciśnij przycisk **Wybierz** .
1. Naciśnij przycisk **Assign (Przypisz** ) u dołu, aby zakończyć przydziały użytkowników i grup do aplikacji.
1. Upewnij się, że dodani Użytkownicy i grupy znajdują się na liście zaktualizowanych **użytkowników i grup** .

## <a name="more-information"></a>Więcej informacji

- [Dodawanie autoryzacji przy użyciu ról aplikacji & oświadczenia ról do aplikacji sieci Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Używanie grup zabezpieczeń i ról aplikacji w aplikacjach (wideo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, teraz z oświadczeniami grupy i rolami aplikacji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokeny dostępu usługi AAD](access-tokens.md)
- [`id_tokens` usługi AAD](id-tokens.md)
