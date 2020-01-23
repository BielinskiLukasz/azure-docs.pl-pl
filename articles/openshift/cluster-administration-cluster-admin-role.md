---
title: Rola administratora klastrów Red Hat OpenShift na platformie Azure | Microsoft Docs
description: Przypisanie i użycie roli administratora klastra Red Hat OpenShift platformy Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 610b1e0112b8135aa09ade5c800eaed987635cb4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545640"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola Administrator klienta Red Hat OpenShift na platformie Azure

Jesteś administratorem klastra usługi Azure Red Hat OpenShift. Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Jeśli dla konta jest powiązana rola autoryzacji klient-administrator-klaster, może ona automatycznie zarządzać projektem.

> [!Note] 
> Rola klastra klient-administrator-klaster nie jest taka sama jak rola klastra-administratora klastra.

Na przykład można wykonać akcje skojarzone z zestawem czasowników (`create`) do działania na zestawie nazw zasobów (`templates`). Aby wyświetlić szczegóły tych ról i ich zestawów zleceń i zasobów, uruchom następujące polecenie:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Nazwy zleceń nie zawsze są mapowane bezpośrednio do poleceń `oc`. Są one bardziej ogólne dla typów operacji interfejsu wiersza polecenia, które można wykonywać. 

Na przykład, jeśli zlecenie `list` oznacza, że można wyświetlić listę wszystkich obiektów o nazwie zasobu (`oc get`). Zlecenie `get` oznacza, że w przypadku znajomości nazwy (`oc describe`) można wyświetlić szczegóły konkretnego obiektu.

## <a name="configure-the-customer-administrator-role"></a>Konfigurowanie roli administratora klienta

Rolę klastra klient-administrator-klaster można skonfigurować tylko podczas tworzenia klastra, dostarczając flagę `--customer-admin-group-id`. To pole nie jest obecnie konfigurowalne w Azure Portal. Aby dowiedzieć się, jak skonfigurować Azure Active Directory i grupę administratorów, zobacz [Azure Active Directory Integration for Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potwierdzenie członkostwa w roli administratora klienta

Aby potwierdzić członkostwo w grupie administratorów klienta, spróbuj użyć poleceń interfejsu wiersza polecenia OpenShift `oc get nodes` lub `oc projects`. `oc get nodes` pokaże listę węzłów, jeśli masz rolę klient-administrator-klaster, a błąd uprawnień, jeśli masz tylko rolę klient-administrator-projekt. w `oc projects` zostaną wyświetlone wszystkie projekty w klastrze, a nie tylko projekty, w których pracujesz.

Aby dowiedzieć się więcej o rolach i uprawnieniach w klastrze, można użyć [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) polecenie.

## <a name="next-steps"></a>Następne kroki

Skonfiguruj rolę klastra klient-administrator-klaster:
> [!div class="nextstepaction"]
> [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)
