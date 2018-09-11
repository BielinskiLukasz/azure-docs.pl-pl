---
title: Problem, zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak rozwiązywać typowe problemy zmierzyła się z zespołem podczas konfigurowania aprowizacji użytkowników do aplikacji już wyświetlane w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: fe96ecc0ba6904819f0262a2f470e37203a7952e
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357108"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem podczas zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników z aplikacją Galeria usługi Azure Active Directory 

Korzystając z witryny Azure portal skonfigurować [automatyczna aprowizacja użytkowników](user-provisioning.md) dla aplikacji dla przedsiębiorstw, może wystąpić sytuacja, gdy:

* **Poświadczeń administratora** wprowadzonych dla aplikacji są prawidłowe i **Testuj połączenie** działania przycisku. Nie można zapisać poświadczenia i witryny Azure portal zwraca ogólny komunikat o błędzie.

Jeśli opartej na SAML logowania jednokrotnego również jest skonfigurowane dla tej samej aplikacji, najbardziej prawdopodobną przyczyną tego błędu jest limit magazynu wewnętrznego, każdej aplikacji przez usługę Azure AD dla certyfikatów i poświadczenia został przekroczony.

Obecnie usługa Azure AD ma pojemność pamięci masowej jeden KB dla wszystkich certyfikatów, tajne tokenów, poświadczenia i elementami konfiguracji danych skojarzonych z pojedynczego wystąpienia aplikacji (znany także jako jednostki rekord usługi w usłudze Azure AD).

Opartej na SAML logowania jednokrotnego jest skonfigurowany, certyfikat używany do podpisywania tokenów SAML są przechowywane w tym miejscu i często zużywa ponad 50% procent miejsca.

Wszelkie tajne tokenów, identyfikatory URI, adresy e-mail powiadomień, nazwy użytkowników i hasła, które Pobierz wprowadzane podczas konfigurowania aprowizacji użytkowników może spowodować przekroczenie granicy magazynu.

## <a name="how-to-work-around-this-issue"></a>Jak obejść ten problem 

Istnieją dwa sposoby, aby obejść ten problem już dzisiaj:

1. **Użyj galerii dwóch wystąpień aplikacji, jeden dla logowania jednokrotnego i jeden dla aprowizacji użytkowników** -biorąc aplikacji z galerii [podnieść LinkedIn](../saas-apps/linkedinelevate-tutorial.md) na przykład można dodać podniesienie poziomu usługi LinkedIn z galerii i skonfigurować go do logowania jednokrotnego. Do obsługi administracyjnej, dodać inne wystąpienie podniesienie poziomu usługi LinkedIn z galerii aplikacji Azure AD i nadaj mu nazwę "Podniesienie uprawnień LinkedIn (aprowizacji)". To drugie wystąpienie, można skonfigurować [inicjowania obsługi administracyjnej](../saas-apps/linkedinelevate-provisioning-tutorial.md), ale nie logowania jednokrotnego. Korzystając z tego rozwiązania, tych samych użytkowników i grup muszą być [przypisane](assign-user-or-group-access-portal.md) do obydwu aplikacji. 

2. **Zmniejszenie ilości przechowywanych danych konfiguracji** — wszystkie dane wprowadzone w [poświadczeń administratora](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) inicjowania obsługi administracyjnej karcie znajduje się w tym samym miejscu co certyfikat SAML. Nie może być można zmniejszyć długość wszystkie te dane, takie jak niektóre pola konfiguracji opcjonalnej **wiadomość E-mail z powiadomieniem** może zostać usunięty.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie użytkownika aprowizację i anulowanie obsługi do aplikacji SaaS](user-provisioning.md)
