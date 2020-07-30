---
title: Konfigurowanie Access Control opartej na rolach (RBAC) na platformie Azure dla usługi Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania funkcji RBAC platformy Azure dla usługi Azure API dla płaszczyzny danych FHIR
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: 3fbbfd931efb0e570bc96639bc6b7943fddc0237
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417679"
---
# <a name="configure-azure-rbac-for-fhir"></a>Konfigurowanie kontroli RBAC platformy Azure dla usługi FHIR 

W tym artykule dowiesz się, jak za pomocą usługi Azure [Role Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) przypisywać dostęp do interfejsu API platformy Azure dla FHIRej płaszczyzny danych. Azure RBAC to preferowane metody przypisywania dostępu do płaszczyzny danych, gdy użytkownicy płaszczyzny danych są zarządzani w dzierżawie Azure Active Directoryej skojarzonej z subskrypcją platformy Azure. Jeśli używasz dzierżawy zewnętrznej Azure Active Directory, zapoznaj się z [lokalnym odwołaniem do przypisania RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Potwierdzenie trybu kontroli RBAC platformy Azure

Aby można było korzystać z usługi Azure RBAC, interfejs API platformy Azure dla usługi FHIR musi być skonfigurowany do korzystania z dzierżawy subskrypcji platformy Azure na potrzeby płaszczyzny danych i nie powinien mieć przypisanych identyfikatorów obiektów tożsamości. Ustawienia można sprawdzić, sprawdzając blok **uwierzytelnianie** w interfejsie API platformy Azure dla FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Potwierdzenie trybu kontroli RBAC platformy Azure":::

**Urząd** powinien mieć ustawioną dzierżawę usługi Azure Active Directory skojarzoną z subskrypcją, a w polu nie powinny znajdować się identyfikatory GUID **dozwolonych identyfikatorów obiektów**. Zauważ również, że pole jest wyłączone i Etykieta wskazuje, że do przypisywania ról płaszczyzny danych należy używać funkcji RBAC platformy Azure.

## <a name="assign-roles"></a>Przypisywanie ról

Aby udzielić użytkownikom, podmiotom usługi lub grupom dostępu do płaszczyzny danych FHIR, kliknij pozycję **Kontrola dostępu (IAM)**, a następnie kliknij pozycję **przypisania ról** i kliknij pozycję **+ Dodaj**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Dodawanie przypisania roli RBAC platformy Azure":::

W wyborze **roli** Wyszukaj jedną z wbudowanych ról dla płaszczyzny danych FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Wbudowane role danych FHIR":::

Można wybrać jedną z opcji:

* Czytnik danych FHIR: może odczytywać (i wyszukiwać) FHIR dane.
* Moduł zapisywania danych FHIR: może odczytywać, zapisywać i usuwać dane FHIR.
* FHIR: może odczytywać i eksportować `$export` dane (operator).
* Współautor danych FHIR: mogą wykonywać wszystkie operacje płaszczyzny danych.

Jeśli te role nie są wystarczające dla Twoich potrzeb, możesz również [utworzyć role niestandardowe](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

W polu **Wybierz** Wyszukaj użytkownika, nazwę główną usługi lub grupę, do której chcesz przypisać rolę.

## <a name="caching-behavior"></a>Zachowanie buforowania

Interfejs API platformy Azure dla usługi FHIR będzie buforować decyzje przez maksymalnie 5 minut. W przypadku przyznania użytkownikowi dostępu do serwera FHIR przez dodanie ich do listy dozwolonych identyfikatorów obiektów lub usunięcie ich z listy, należy oczekiwać, aż zmiany w uprawnieniach do propagowania mają trwać do pięciu minut.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób przypisywania ról RBAC platformy Azure dla płaszczyzny danych FHIR. Kolejne informacje o dodatkowych ustawieniach interfejsu API platformy Azure dla usługi FHIR:
 
>[!div class="nextstepaction"]
>[Dodatkowe ustawienia interfejsu API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)
