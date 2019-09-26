---
title: Uprawnienia na platformie Azure — wskaźnik | Microsoft Docs
description: W tym artykule wyjaśniono, w jaki sposób wskaźnik platformy Azure używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikowania dozwolonych akcji dla każdej roli.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0bf95b499a7366dad1e7b78fa4298aa6a42bb5fe
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316801"
---
# <a name="permissions-in-azure-sentinel"></a>Uprawnienia na platformie Azure — wskaźnik

Wskaźnik platformy Azure używa [Access Control opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), aby zapewnić [wbudowane role](../role-based-access-control/built-in-roles.md) , które można przypisać do użytkowników, grup i usług na platformie Azure.

Za pomocą RBAC można użyć i utworzyć role w zespole operacji zabezpieczeń, aby udzielić odpowiedniego dostępu do usługi Azure wskaźnikowej. Na podstawie ról masz szczegółową kontrolę nad tym, co mogą widzieć użytkownicy mający dostęp do platformy Azure. Role RBAC można przypisać bezpośrednio do obszaru roboczego wskaźnikowego platformy Azure lub do subskrypcji lub grupy zasobów, do której należy obszar roboczy.

Istnieją trzy specyficzne role kontrolki wskaźnikowej platformy Azure.  
**Wszystkie wbudowane role funkcji wskaźnikowej platformy Azure umożliwiają dostęp do odczytu do danych w obszarze roboczym wskaźnikowego platformy Azure.**
- **Czytnik wskaźnikowy platformy Azure**: Użytkownik przypisany do tej roli ma uprawnienia do wyświetlania informacji o wskaźniku platformy Azure. Użytkownik może wyświetlać zdarzenia i dane, ale nie może wprowadzać zmian.
- **Obiekt odpowiadający wskaźnikowi platformy Azure**: Użytkownik przypisany za pomocą tej roli może odczytywać i wykonywać działania związane z zdarzeniami, takimi jak zmiany przydziału i ważności.
- **Współautor wskaźnikowego platformy Azure**: Użytkownik przypisany za pomocą tej roli może odczytywać i wykonywać działania dotyczące zdarzeń oraz tworzyć i usuwać reguły analityczne.

Oprócz ról RBAC opartych na platformie Azure, istnieją role RBAC platformy Azure i Log Analytics, które mogą udzielić szerszego zestawu uprawnień, które obejmują dostęp do obszaru roboczego wskaźnikowego platformy Azure i innych zasobów:

- **Role platformy Azure:** [Właściciel](../role-based-access-control/built-in-roles.md#owner), [współautor](../role-based-access-control/built-in-roles.md#contributor)i [czytelnik](../role-based-access-control/built-in-roles.md#reader). Role platformy Azure umożliwiają dostęp do wszystkich zasobów platformy Azure, w tym Log Analytics obszarów roboczych i zasobów wskaźnikowych platformy Azure.

-   **Role Log Analytics:** [Współautor log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role Log Analytics udzielą dostępu we wszystkich Log Analytics obszarach roboczych. 

> [!NOTE]
> Role Log Analytics zapewniają również dostęp do odczytu dla wszystkich zasobów platformy Azure, ale tylko uprawnienia do zapisu do zasobów Log Analytics.


Na przykład użytkownik przypisany za pomocą **czytnika wskaźnikowego platformy Azure** i **Współautorzy platformy** Azure (nie **współautor wskaźnikowego platformy Azure**) będzie mógł edytować dane na platformie Azure, chociaż mają tylko uprawnienia **czytelnika** . W związku z tym, jeśli chcesz udzielić uprawnień tylko do platformy Azure, należy uważnie usunąć wcześniejsze uprawnienia tego użytkownika, upewniając się, że nie wszystkie wymagane role uprawnień dla innego zasobu.

> [!NOTE]
>- Wskaźnik platformy Azure używa elementy PlayBook do automatycznej reakcji na zagrożenia. Elementy PlayBook wykorzystać Azure Logic Apps i są osobnym zasobem platformy Azure. Możesz chcieć przypisać określonych członków zespołu operacji zabezpieczeń, korzystając z opcji Logic Apps na potrzeby operacji aranżacji zabezpieczeń, automatyzacji i odpowiedzi (o). Rola [współautor aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor) lub rola [operatora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator) służy do przypisywania jawnego uprawnienia do korzystania z elementy PlayBook.
>- Aby dodać łączniki danych, wymagane role dla każdego łącznika są na typ łącznika i są wyświetlane na odpowiedniej stronie łącznika. Ponadto, aby połączyć każde źródło danych, musisz mieć uprawnienie do zapisu w obszarze roboczym wskaźnik platformy Azure.



## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli przedstawiono role i dozwolone akcje na platformie Azure — wskaźnik. Symbol X wskazuje, że akcja jest dozwolona dla tej roli.

| Role | Utwórz i uruchom elementy PlayBook| Twórz i edytuj pulpity nawigacyjne, reguły analityczne i inne zasoby wskaźnikowe platformy Azure | Zarządzanie zdarzeniami (odrzucanie, przypisywanie itp.) | Wyświetl dane, zdarzenia, pulpity nawigacyjne i inne zasoby wskaźnikowe platformy Azure |
|--- |---|---|---|---|
| Czytnik wskaźnikowy platformy Azure | -- | -- | -- | X |
| Obiekt odpowiadający wskaźnikowi platformy Azure|--|--| X | X |
| Współautor wskaźnikowego platformy Azure | -- | X | X | X |
| Współautor Azure wskaźnikowego i współautor aplikacji logiki | X | X | X | X |


> [!NOTE]
> - Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład Przypisz rolę współautor wskaźnikowego platformy Azure tylko do użytkowników, którzy muszą tworzyć reguły lub pulpity nawigacyjne.
> - Zalecamy Ustawianie uprawnień dla platformy Azure, w zakresie grupy zasobów, dzięki czemu użytkownik może mieć dostęp do wszystkich obszarów roboczych wskaźnikowych platformy Azure w tej samej grupie zasobów.
>
## <a name="building-custom-rbac-roles"></a>Tworzenie niestandardowych ról RBAC

Oprócz lub zamiast, przy użyciu wbudowanych ról RBAC, można utworzyć niestandardowe role RBAC dla platformy Azure. Niestandardowe role RBAC dla usługi Azure wskaźnikowej są tworzone w taki sam sposób, jak w przypadku tworzenia innych [niestandardowych ról RBAC platformy Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) , na podstawie określonych uprawnień do zasobów usługi Azure wskaźnikowych.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Zaawansowana kontrola RBAC danych przechowywanych na platformie Azure — wskaźnik
  
Możesz użyć zaawansowanej kontroli dostępu opartej na rolach Log Analytics w danych w obszarze roboczym wskaźnikowego platformy Azure. Dotyczy to zarówno kontroli dostępu opartej na rolach według typu danych, jak i zorientowanej na podstawie zasobów kontroli dostępu. Aby uzyskać więcej informacji na temat ról Log Analytics, zobacz [Zarządzanie danymi dzienników i obszarami roboczymi w programie Azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób pracy z rolami dla użytkowników wskaźnikowych platformy Azure oraz tego, co każda rola umożliwia użytkownikom.

* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
