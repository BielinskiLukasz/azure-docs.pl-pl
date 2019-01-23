---
title: Problem z konfigurowaniem aprowizacji użytkowników do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak rozwiązywać typowe problemy zmierzyła się z zespołem podczas konfigurowania aprowizacji użytkowników do aplikacji już wyświetlane w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 340a7dfdf9698019eeb2c96dc56411afb7a78f50
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471994"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem z konfigurowaniem aprowizacji użytkowników do aplikacji galerii usługi Azure AD

Konfigurowanie [automatyczna aprowizacja użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) dla aplikacji (jeśli są obsługiwane), wymaga, aby uzyskać szczegółowe instrukcje występować do przygotowania aplikacji do automatycznej aprowizacji. Następnie można użyć witryny Azure portal, aby skonfigurować usługę aprowizacji, aby zsynchronizować konta użytkowników do aplikacji.

Zawsze należy zacząć, wyszukując samouczek ustawienia specyficzne dla konfiguracji aprowizacji dla aplikacji. Następnie wykonaj te kroki, aby skonfigurować aplikację i Azure AD, aby utworzyć połączenie inicjowania obsługi administracyjnej. Lista samouczków aplikacji znajduje się w temacie [listę samouczków dotyczących integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak sprawdzić, czy działa inicjowania obsługi administracyjnej 

Po skonfigurowaniu usługi wgląd w większość operacji usługi mogą być wystawiane z dwóch miejsc:

-   **Dzienniki inspekcji** — dzienniki inicjowania obsługi administracyjnej inspekcji wszystkich operacji wykonywanych przez usługę aprowizacji, łącznie z zapytań usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji rekordu. Wyślij zapytanie do aplikacji docelowej, aby istnienie tych użytkowników, porównywanie obiektów użytkownika między systemem. Następnie dodaj, Aktualizuj lub wyłączyć konto użytkownika w systemie docelowym na podstawie porównania. Inicjowania obsługi dzienników inspekcji można uzyskać w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; dzienników inspekcji** kartę. Filtruj dzienniki na **Inicjowanie obsługi administracyjnej konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla tej aplikacji.

-   **Stan — aprowizacji** podsumowanie, ostatnie aprowizacji uruchamiania dla danej aplikacji będzie widoczne w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt;Aprowizacji** sekcji w dolnej części ekranu, w obszarze Ustawienia usługi. Ta sekcja zawiera podsumowanie, ilu użytkowników (i/lub grupy) są obecnie synchronizowane między dwoma systemami, a jeśli wystąpiły żadne błędy. Szczegóły błędu znajdować się w dziennikach inspekcji. Należy pamiętać, że stan inicjowania obsługi administracyjnej nie można wypełnić ukończenie jedną pełną wstępna synchronizacja między usługą Azure AD i aplikacji.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obszary ogólny problem z zainicjowania obsługi administracyjnej należy wziąć pod uwagę

Poniżej znajduje się lista obszarów ogólny problem, które można rozwinąć Jeśli masz pomysł gdzie zacząć.

* [Usługa aprowizacji nie rozpoczyna się](#provisioning-service-does-not-appear-to-start)
* [Nie można zapisać konfiguracji z powodu poświadczeń aplikacja nie działa](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Dzienniki inspekcji Załóżmy, że użytkownicy są "pominięto" i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa aprowizacji nie rozpoczyna się

Jeśli ustawisz **stanie aprowizacji** jako **na** w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt;Aprowizacji** części witryny Azure portal. Jednak inne szczegóły stanu są wyświetlane na tej stronie, po kolejnych ponowne załadowanie. Istnieje prawdopodobieństwo, że usługa działa, ale jeszcze synchronizacji początkowej nie zostało ukończone. Sprawdź **dzienniki inspekcji** opisanych powyżej, aby określić, jakie operacje usługa działa, a jeśli wystąpiły żadne błędy.

>[!NOTE]
>Początkowa synchronizacja może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. Kolejne synchronizacje po synchronizacji początkowej można szybciej, jak usługa aprowizowania magazyny znaki wodne, które reprezentują stanu obu systemów po początkowej synchronizacji, zwiększanie wydajności kolejne synchronizacje.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nie można zapisać konfiguracji z powodu poświadczeń aplikacja nie działa

Aby Inicjowanie obsługi administracyjnej do pracy usługa Azure AD wymaga prawidłowe poświadczenia umożliwiające nawiązać połączenia z interfejsem API przez tę aplikację zarządzania użytkownikami. Jeśli te poświadczenia nie działają lub nie znasz wat, w której są, sprawdź samouczek dotyczący konfigurowania tej aplikacji z wcześniejszym opisem.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji Załóżmy, że użytkownicy są pomijane i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane

Użytkownik jest wyświetlany jako "pominięto" w dziennikach inspekcji, jest bardzo ważne, aby przeczytać szczegółowe w wiadomości dziennika, aby ustalić przyczynę. Poniżej przedstawiono typowe przyczyny i rozwiązania:

-   **Skonfigurowano filtru określania zakresu** **, jest filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Użytkownik "nie jest skutecznie uprawnione".** Jeśli widzisz ten komunikat o błędzie określone, jest to, ponieważ wystąpił problem z rekordem przypisania użytkownika, które są przechowywane w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z poziomu aplikacji, a następnie ponownie przypisać. Aby uzyskać więcej informacji na temat przypisania, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Wymagany atrybut jest brak lub nie jest wypełnione dla użytkownika.** Ważne jest, aby uwzględnić podczas konfigurowania aprowizacji można przejrzeć i skonfigurować mapowania atrybutów i przepływów pracy, które określają, które użytkownik (lub grupy) właściwości przepływu z usługi Azure AD do aplikacji. Obejmuje to ustawienie "property pasującego" używany do jednoznacznego identyfikowania i dopasować użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego procesu ważne, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapowania atrybutów dla grup:** Inicjowanie obsługi nazwy grupy i szczegóły grupy, oprócz członków, jeśli jest obsługiwany dla niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję, włączając lub wyłączając **mapowanie** dla obiektów grupy wyświetlane w **aprowizacji** kartę. Jeśli Inicjowanie obsługi administracyjnej grupy jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest on używany do dopasowywania "ID". Może to być wyświetlaną nazwę lub alias e-mail), jak grupy i jej elementów członkowskich nie można zainicjować obsługi administracyjnej, jeśli właściwość dopasowania jest pusta lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)
