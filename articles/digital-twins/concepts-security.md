---
title: Zabezpieczenia rozwiązań usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Poznaj najlepsze rozwiązania w zakresie zabezpieczeń dzięki usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 973eeebfdf9164cb50cf98ae8edc845a80a7e080
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794505"
---
# <a name="secure-azure-digital-twins"></a>Zabezpieczanie usługi Azure Digital bliźniaczych reprezentacji

W celu zapewnienia bezpieczeństwa usługa Azure Digital bliźniaczych reprezentacji zapewnia precyzyjną kontrolę dostępu do określonych danych, zasobów i akcji we wdrożeniu. Robi to za pośrednictwem szczegółowej strategii zarządzania rolami i uprawnieniami o nazwie **kontroli dostępu opartej na rolach (RBAC)**. W [tym miejscu](../role-based-access-control/overview.md)możesz zapoznać się z ogólnymi zasadami RBAC dla platformy Azure.

Usługa Azure Digital bliźniaczych reprezentacji obsługuje również szyfrowanie przechowywanych danych.

## <a name="granting-permissions-with-rbac"></a>Przyznawanie uprawnień przy użyciu RBAC

Kontrola RBAC jest dostarczana do usługi Azure Digital bliźniaczych reprezentacji przez integrację z usługą [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Za pomocą RBAC można przyznać uprawnienia *podmiotowi zabezpieczeń*, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD i odbiera token OAuth 2,0 w programie Return. Token ten może służyć do autoryzowania żądania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

W przypadku usługi Azure AD dostęp jest procesem dwuetapowym. Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, żądanie musi być *uwierzytelnione* i *autoryzowane*. 

1. Najpierw jest *uwierzytelniana*tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0.
2. Następnie token jest przesyłany w ramach żądania do usługi Azure Digital bliźniaczych reprezentacji, aby *autoryzować* dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby każde żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja [Azure Functions](../azure-functions/functions-overview.md) , może użyć **zarządzanej tożsamości** w celu uzyskania dostępu do zasobów. Więcej informacji na temat tożsamości zarządzanych można znaleźć w następnej sekcji.

Krok autoryzacji wymaga przypisania roli platformy Azure do podmiotu zabezpieczeń. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Usługa Azure Digital bliźniaczych reprezentacji oferuje role platformy Azure, które obejmują zestawy uprawnień dla zasobów usługi Azure Digital bliźniaczych reprezentacji. Te role są opisane w dalszej części tego artykułu.

Aby dowiedzieć się więcej o rolach i przypisaniach ról obsługiwanych na platformie Azure, zobacz [*Omówienie różnych ról*](../role-based-access-control/rbac-and-directory-admin-roles.md) w dokumentacji usługi Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Uwierzytelnianie za pomocą tożsamości zarządzanych

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja obejmująca wiele platform Azure, która umożliwia tworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w którym działa kod aplikacji. Następnie można powiązać tę tożsamość z rolami kontroli dostępu, aby przyznać niestandardowe uprawnienia dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Przy użyciu tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie jest konieczne przechowywanie i ochrona kluczy dostępu w kodzie lub konfiguracji aplikacji, zarówno w przypadku tożsamości, jak i dla zasobów, do których należy uzyskać dostęp. Aplikacja kliencka usługi Azure Digital bliźniaczych reprezentacji działająca w aplikacji Azure App Service nie musi obsługiwać reguł i kluczy SAS ani żadnych innych tokenów dostępu. Aplikacja kliencka wymaga tylko adresu punktu końcowego przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. Po nawiązaniu połączenia aplikacji usługa Azure Digital bliźniaczych reprezentacji wiąże kontekst jednostki zarządzanej z klientem. Po skojarzeniu jej z tożsamością zarządzaną klient Digital bliźniaczych reprezentacji może wykonać wszystkie autoryzowane operacje. Autoryzacja zostanie następnie przyznana przez skojarzenie zarządzanej jednostki z rolą platformy Azure Digital bliźniaczych reprezentacji na platformie Azure (opisaną poniżej).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autoryzacja: role platformy Azure dla usługi Azure Digital bliźniaczych reprezentacji

Platforma Azure oferuje następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do zasobu Digital bliźniaczych reprezentacji platformy Azure:
* *Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza)* — ta rola zapewnia pełen dostęp za pośrednictwem zasobów usługi Azure Digital bliźniaczych reprezentacji.
* *Azure Digital bliźniaczych reprezentacji Reader (wersja zapoznawcza)* — ta rola umożliwia dostęp tylko do odczytu do zasobów usługi Azure Digital bliźniaczych reprezentacji.

> [!TIP]
> Rola *usługi Azure Digital bliźniaczych reprezentacji Reader (wersja zapoznawcza)* obsługuje teraz również Przeglądanie relacji.

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [*Omówienie definicji ról*](../role-based-access-control/role-definitions.md) w dokumentacji usługi Azure RBAC. Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [*role niestandardowe platformy Azure*](../role-based-access-control/custom-roles.md).

Role można przypisywać na dwa sposoby:
* za pośrednictwem okienka kontroli dostępu (IAM) dla usługi Azure Digital bliźniaczych reprezentacji w Azure Portal (zobacz [*Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* za pomocą poleceń interfejsu wiersza polecenia w celu dodania lub usunięcia roli

Aby uzyskać bardziej szczegółowe instrukcje, jak to zrobić, wypróbuj w samouczku Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md).

### <a name="permission-scopes"></a>Zakresy uprawnień

Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można ograniczyć dostęp do zasobów usługi Azure Digital bliźniaczych reprezentacji.
* Modele: akcje dla tego zasobu wymuszają kontrolę nad [modelami](concepts-models.md) przekazanymi w usłudze Azure Digital bliźniaczych reprezentacji.
* Zapytanie Digital bliźniaczych reprezentacji Graph: akcje dla tego zasobu określają możliwość uruchamiania [operacji zapytania](concepts-query-language.md) na cyfrowym bliźniaczych reprezentacjiu w ramach grafu Digital bliźniaczych reprezentacji na platformie Azure.
* Cyfrowe sznurki: akcje dla tego zasobu zapewniają kontrolę nad operacjami CRUD na [bliźniaczych reprezentacji cyfrowym](concepts-twins-graph.md) na grafie bliźniaczym.
* Relacja Digital bliźniaczy: akcje dla tego zasobu definiują kontrolę nad operacjami CRUD na [relacjach](concepts-twins-graph.md) między cyframi cyfrowymi bliźniaczych reprezentacji na grafie bliźniaczym.
* Trasa zdarzenia: akcje dla tego zasobu określają uprawnienia do [kierowania zdarzeń](concepts-route-events.md) z usługi Azure Digital bliźniaczych reprezentacji do usługi punktu końcowego, takiej jak [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)lub [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Rozwiązywanie problemów z uprawnieniami

Jeśli użytkownik próbuje wykonać akcję niedozwoloną przez ich rolę, może otrzymać błąd od odczytu żądania obsługi `403 (Forbidden)` . Aby uzyskać więcej informacji i kroków rozwiązywania problemów, zobacz [*Rozwiązywanie problemów: żądanie usługi Azure Digital bliźniaczych reprezentacji nie powiodło się. stan: 403 (niedozwolone)*](troubleshoot-error-403.md).

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych

Usługa Azure Digital bliźniaczych reprezentacji zapewnia szyfrowanie danych przechowywanych i przesyłanych w czasie, gdy jest ona zapisywana w centrach danych i odszyfrowuje ją, gdy uzyskujesz do niej dostęp.

## <a name="next-steps"></a>Następne kroki

* Zobacz te koncepcje w akcji w czynnościach opisanych w temacie [*: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-scripted.md).

* Zapoznaj się z tematem jak korzystać z tych koncepcji z kodu aplikacji klienta w artykule [*instrukcje: pisanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md).

* Przeczytaj więcej [na temat RBAC na platformie Azure](../role-based-access-control/overview.md).
