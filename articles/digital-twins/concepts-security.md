---
title: Zabezpieczenia rozwiązań usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Poznaj najlepsze rozwiązania w zakresie zabezpieczeń dzięki usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0f1f9e17646c4432d9c1103b3c579fc6bb0d2f13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833168"
---
# <a name="secure-azure-digital-twins-with-role-based-access-control"></a>Zabezpieczanie usługi Azure Digital bliźniaczych reprezentacji z kontrolą dostępu opartą na rolach

W celu zapewnienia bezpieczeństwa usługa Azure Digital bliźniaczych reprezentacji zapewnia precyzyjną kontrolę dostępu do określonych danych, zasobów i akcji we wdrożeniu. Robi to za pośrednictwem szczegółowej strategii zarządzania rolami i uprawnieniami o nazwie **kontroli dostępu opartej na rolach (RBAC)**. W [tym miejscu](../role-based-access-control/overview.md)możesz zapoznać się z ogólnymi zasadami RBAC dla platformy Azure.

## <a name="rbac-through-azure-ad"></a>RBAC za pomocą usługi Azure AD

Kontrola RBAC jest dostarczana do usługi Azure Digital bliźniaczych reprezentacji przez integrację z usługą [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Za pomocą RBAC można przyznać uprawnienia *podmiotowi zabezpieczeń*, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD i odbiera token OAuth 2,0 w programie Return. Token ten może służyć do autoryzowania żądania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

W przypadku usługi Azure AD dostęp jest procesem dwuetapowym. Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, żądanie musi być *uwierzytelnione* i *autoryzowane*. 

1. Najpierw jest *uwierzytelniana*tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0.
2. Następnie token jest przesyłany w ramach żądania do usługi Azure Digital bliźniaczych reprezentacji, aby *autoryzować* dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby każde żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja [Azure Functions](../azure-functions/functions-overview.md) , może użyć **zarządzanej tożsamości** w celu uzyskania dostępu do zasobów. Więcej informacji na temat tożsamości zarządzanych można znaleźć w następnej sekcji.

Krok autoryzacji wymaga, aby rola RBAC była przypisana do podmiotu zabezpieczeń. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Usługa Azure Digital bliźniaczych reprezentacji oferuje role RBAC, które obejmują zestawy uprawnień dla zasobów usługi Azure Digital bliźniaczych reprezentacji. Te role są opisane w dalszej części tego artykułu.

Aby dowiedzieć się więcej o rolach i przypisaniach ról obsługiwanych na platformie Azure, zobacz [Omówienie różnych ról](../role-based-access-control/rbac-and-directory-admin-roles.md) w dokumentacji usługi Azure RBAC.

### <a name="authentication-with-managed-identities"></a>Uwierzytelnianie z tożsamościami zarządzanymi

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja obejmująca wiele platform Azure, która umożliwia tworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w którym działa kod aplikacji. Następnie można powiązać tę tożsamość z rolami kontroli dostępu, aby przyznać niestandardowe uprawnienia dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Przy użyciu tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie jest konieczne przechowywanie i ochrona kluczy dostępu w kodzie lub konfiguracji aplikacji, zarówno w przypadku tożsamości, jak i dla zasobów, do których należy uzyskać dostęp. Aplikacja kliencka usługi Azure Digital bliźniaczych reprezentacji działająca w aplikacji Azure App Service nie musi obsługiwać reguł i kluczy SAS ani żadnych innych tokenów dostępu. Aplikacja kliencka wymaga tylko adresu punktu końcowego przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. Po nawiązaniu połączenia aplikacji usługa Azure Digital bliźniaczych reprezentacji wiąże kontekst jednostki zarządzanej z klientem. Po skojarzeniu jej z tożsamością zarządzaną klient Digital bliźniaczych reprezentacji może wykonać wszystkie autoryzowane operacje. Autoryzacja zostanie następnie przyznana przez skojarzenie zarządzanej jednostki z rolą usługi Azure Digital bliźniaczych reprezentacji RBAC (opisanej poniżej).

### <a name="authorization-rbac-roles-for-azure-digital-twins"></a>Autoryzacja: role RBAC dla usługi Azure Digital bliźniaczych reprezentacji

Na platformie Azure dostępne są następujące wbudowane role RBAC umożliwiające autoryzowanie dostępu do zasobu Digital bliźniaczych reprezentacji systemu Azure:
* Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza) — Ta rola zapewnia pełen dostęp za pośrednictwem zasobów usługi Azure Digital bliźniaczych reprezentacji.
* Azure Digital bliźniaczych reprezentacji Reader (wersja zapoznawcza) — Ta rola umożliwia dostęp tylko do odczytu do zasobów usługi Azure Digital bliźniaczych reprezentacji.

> [!TIP]
> Rola usługi Azure Digital bliźniaczych reprezentacji Reader (wersja zapoznawcza) obsługuje teraz również Przeglądanie relacji.

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md) w dokumentacji usługi Azure RBAC. Aby uzyskać informacje na temat tworzenia niestandardowych ról RBAC, zobacz [role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

Role można przypisywać na dwa sposoby:
* za pośrednictwem okienka kontroli dostępu (IAM) dla usługi Azure Digital bliźniaczych reprezentacji w Azure Portal (zobacz [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](../role-based-access-control/role-assignments-portal.md))
* za pomocą poleceń interfejsu wiersza polecenia w celu dodania lub usunięcia roli

Aby uzyskać bardziej szczegółowe instrukcje, jak to zrobić, wypróbuj w [samouczku Digital bliźniaczych reprezentacji na platformie Azure: łączenie kompleksowego rozwiązania](tutorial-end-to-end.md).

## <a name="permission-scopes"></a>Zakresy uprawnień

Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można ograniczyć dostęp do zasobów usługi Azure Digital bliźniaczych reprezentacji.
* Modele: akcje dla tego zasobu wymuszają kontrolę nad [modelami](concepts-models.md) przekazanymi w usłudze Azure Digital bliźniaczych reprezentacji.
* Zapytanie Digital bliźniaczych reprezentacji Graph: akcje dla tego zasobu określają możliwość uruchamiania [operacji zapytania](concepts-query-language.md) na cyfrowym bliźniaczych reprezentacjiu w ramach grafu Digital bliźniaczych reprezentacji na platformie Azure.
* Cyfrowe sznurki: akcje dla tego zasobu zapewniają kontrolę nad operacjami CRUD na [bliźniaczych reprezentacji cyfrowym](concepts-twins-graph.md) na grafie bliźniaczym.
* Relacja Digital bliźniaczy: akcje dla tego zasobu definiują kontrolę nad operacjami CRUD na [relacjach](concepts-twins-graph.md) między cyframi cyfrowymi bliźniaczych reprezentacji na grafie bliźniaczym.
* Trasa zdarzenia: akcje dla tego zasobu określają uprawnienia do [kierowania zdarzeń](concepts-route-events.md) z usługi Azure Digital bliźniaczych reprezentacji do usługi punktu końcowego, takiej jak [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)lub [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak wykonać te kroki, korzystając z przykładowej aplikacji klienckiej w poradach [: uwierzytelnianie aplikacji klienckiej](how-to-authenticate-client.md).

* Przeczytaj więcej [na temat RBAC na platformie Azure](../role-based-access-control/overview.md).
