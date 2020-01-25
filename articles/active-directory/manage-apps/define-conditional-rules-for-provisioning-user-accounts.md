---
title: Inicjowanie obsługi aplikacji przy użyciu filtrów określania zakresu | Microsoft Docs
description: Dowiedz się, jak używać filtrów zakresu, aby uniemożliwić obsługę administracyjną obiektów w aplikacjach obsługujących automatyczne Inicjowanie obsługi administracyjnej użytkowników, jeśli obiekt nie spełnia wymagań firmy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a82efda4cf53931dbf81b993b12a2927f02dfa0b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711704"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Inicjowanie obsługi aplikacji opartej na atrybutach przy użyciu filtrów zakresu
Celem tego artykułu jest wyjaśnienie, jak używać filtrów zakresu do definiowania reguł opartych na atrybutach, które określają, którzy użytkownicy są obsługiwani do aplikacji.

## <a name="scoping-filter-use-cases"></a>Przypadki użycia filtru określania zakresu

Filtr określania zakresu umożliwia usłudze aprowizacji Azure Active Directory (Azure AD) dołączenie lub wykluczenie wszystkich użytkowników, którzy mają atrybut pasujący do określonej wartości. Na przykład podczas aprowizacji użytkowników z usługi Azure AD do aplikacji SaaS używanej przez zespół ds. sprzedaży można określić, że tylko użytkownicy z atrybutem "dział" "Sales" muszą być w zakresie aprowizacji.

Filtry zakresu mogą być używane inaczej w zależności od typu łącznika aprowizacji:

* **Inicjowanie obsługi ruchu wychodzącego z usługi Azure AD do aplikacji SaaS**. Gdy usługa Azure AD jest systemem źródłowym, [przydziały użytkowników i grup](assign-user-or-group-access-portal.md) są najczęściej używanymi metodami określania, którzy użytkownicy znajdują się w zakresie aprowizacji. Te przypisania są również używane do włączania logowania jednokrotnego i zapewnienia pojedynczej metody zarządzania dostępem i aprowizacji. Filtry zakresu mogą być używane opcjonalnie, oprócz przypisań lub zamiast nich, do filtrowania użytkowników na podstawie wartości atrybutów.

    >[!TIP]
    > Można wyłączyć obsługę administracyjną na podstawie przypisań dla aplikacji przedsiębiorstwa przez zmianę ustawień w menu [zakres](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) w obszarze Ustawienia aprowizacji, aby **zsynchronizować wszystkich użytkowników i grupy**. Użycie tej opcji oraz filtrów określania zakresu atrybutów zapewnia większą wydajność niż przy użyciu przypisań opartych na grupach.  

* **Inicjowanie obsługi ruchu przychodzącego z aplikacji HCM do usługi Azure AD i Active Directory**. Gdy [aplikacja HCM, taka jak Workday](../saas-apps/workday-tutorial.md) , jest systemem źródłowym, filtry zakresu są podstawową metodą określania, którzy użytkownicy powinni być obsługiwani z aplikacji HCM do Active Directory lub Azure AD.

Domyślnie łączniki aprowizacji usługi Azure AD nie mają skonfigurowanych filtrów zakresów opartych na atrybutach. 

## <a name="scoping-filter-construction"></a>Konstrukcja filtru określania zakresu

Filtr określania zakresu składa się z co najmniej jednej *klauzuli*. Klauzule określają, którzy użytkownicy mogą przechodzić przez filtr określania zakresu, oceniając atrybuty poszczególnych użytkowników. Na przykład może istnieć jedna klauzula, która wymaga, aby atrybut "State" użytkownika miał wartość "New York", więc tylko użytkownicy z Nowym Jorku są obsługiwani do aplikacji. 

Pojedyncza klauzula definiuje pojedynczy warunek dla pojedynczej wartości atrybutu. Jeśli wiele klauzul jest tworzonych w ramach pojedynczego filtru określania zakresu, są one oceniane przy użyciu logiki "i". Oznacza to, że wszystkie klauzule muszą mieć wartość "true", aby użytkownik mógł zostać zainicjowany.

Na koniec można utworzyć wiele filtrów określania zakresu dla jednej aplikacji. Jeśli istnieją wiele filtrów określania zakresu, są one oceniane razem przy użyciu logiki "OR". Oznacza to, że jeśli wszystkie klauzule w dowolnym ze skonfigurowanych filtrów określania zakresu są oceniane na wartość "true", użytkownik jest zainicjowany.

Każdy użytkownik lub Grupa przetworzona przez usługę Azure AD Provisioning jest zawsze oceniana osobno dla każdego filtru określania zakresu.

Na przykład rozważmy następujący filtr określania zakresu:

![Filtr zakresu](media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Zgodnie z tym filtrem określania zakresu użytkownicy muszą spełniać następujące kryteria, aby zapewnić ich zainicjowanie:

* Muszą one znajdować się w Nowym Jorku.
* Muszą one współpracować z działem inżynieryjnym.
* IDENTYFIKATOR pracownika firmy musi zawierać się w zakresie od 1 000 000 do 2 000 000.
* Ich tytuł zadania nie może mieć wartości null ani być pusty.

## <a name="create-scoping-filters"></a>Tworzenie filtrów zakresu
Filtry zakresu są konfigurowane jako część mapowań atrybutów dla każdego łącznika aprowizacji użytkowników usługi Azure AD. W poniższej procedurze przyjęto założenie, że skonfigurowano automatyczną obsługę administracyjną dla [jednej z obsługiwanych aplikacji](../saas-apps/tutorial-list.md) i dodano do niej filtr określania zakresu.

### <a name="create-a-scoping-filter"></a>Tworzenie filtru określania zakresu
1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory** > aplikacje dla **przedsiębiorstw** > **wszystkie aplikacje** .

2. Wybierz aplikację, dla której skonfigurowano automatyczną obsługę administracyjną: na przykład "usługi ServiceNow".

3. Wybierz kartę **aprowizacji** .

4. W sekcji **mapowania** wybierz mapowanie, dla którego chcesz skonfigurować filtr określania zakresu: na przykład "Synchronizuj Azure Active Directory użytkowników do usługi ServiceNow".

5. Wybierz menu **zakres obiektów źródłowych** .

6. Wybierz pozycję **Dodaj filtr określania zakresu**.

7. Zdefiniuj klauzulę, wybierając **nazwę atrybutu**źródłowego, **operatora**i **wartość atrybutu** do dopasowania. Obsługiwane są następujące operatory:

   a. **Równa**się. Klauzula zwraca wartość "true", jeśli obliczony atrybut jest zgodny z wartością ciągu wejściowego dokładnie (z uwzględnieniem wielkości liter).

   b. **nie równa**się. Klauzula zwraca wartość "true", jeśli oceniony atrybut nie jest zgodny z wartością ciągu wejściowego (z uwzględnieniem wielkości liter).

   d. **ma wartość true**. Klauzula zwraca wartość "true", jeśli obliczony atrybut zawiera wartości logicznej true.

   d. **ma wartość false**. Klauzula zwraca wartość "true", jeśli obliczony atrybut zawiera wartości logiczne false.

   e. **ma wartość null**. Klauzula zwraca wartość "true", jeśli oceniony atrybut jest pusty.

   f. **nie ma wartości null**. Klauzula zwraca wartość "true", jeśli oceniony atrybut nie jest pusty.

   g. **dopasowanie wyrażenia regularnego**. Klauzula zwraca wartość "true", jeśli oceniony atrybut pasuje do wzorca wyrażenia regularnego. Na przykład: ([1-9] [0-9]) dopasowuje dowolną liczbę z zakresu od 10 do 99.

   h. **nie pasuje do wyrażenia regularnego**. Klauzula zwraca wartość "true", jeśli oceniony atrybut nie jest zgodny ze wzorcem wyrażenia regularnego.
 
>[!IMPORTANT] 
> Filtry include i IsMemberOf nie są obsługiwane. Wkrótce zostaną usunięte z interfejsu użytkownika.

9. Opcjonalnie powtórz kroki 7-8, aby dodać więcej klauzul określania zakresu.

10. W polu **tytuł filtru określania zakresu**Dodaj nazwę filtru określania zakresu.

11. Kliknij przycisk **OK**.

12. Ponownie wybierz **przycisk OK** na ekranie **filtry zakresu** . Opcjonalnie powtórz kroki 6-11, aby dodać inny filtr określania zakresu.

13. Na ekranie **Mapowanie atrybutu** wybierz pozycję **Zapisz** . 

>[!IMPORTANT] 
> Zapisanie nowego filtru określania zakresu wyzwala nową pełną synchronizację dla aplikacji, w której wszystkie użytkownicy w systemie źródłowym są oceniane ponownie dla nowego filtru określania zakresu. Jeśli użytkownik w aplikacji był wcześniej objęty zakresem aprowizacji, ale poza zakresem, jego konto jest wyłączone lub anulowane w aplikacji. Aby zastąpić to zachowanie domyślne, zapoznaj się z tematem [pomijanie usuwania dla kont użytkowników, które wykraczają poza zakres](skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Typowe filtry zakresu
| Atrybut docelowy| Operator | Wartość | Opis|
|----|----|----|----|
|userPrincipalName|DOPASOWANIE WYRAŻENIA REGULARNEGO|.\*@domain.com |Wszyscy użytkownicy z userPrincipalą z domeną @domain.com będą w zakresie aprowizacji|
|userPrincipalName|NIE PASUJE DO WYRAŻENIA REGULARNEGO|.\*@domain.com|Wszyscy użytkownicy z userPrincipalą z domeną @domain.com będą poza zakresem aprowizacji|
|department|UBIEGŁ|transakcje|Wszyscy użytkownicy z działu sprzedaży znajdują się w zakresie aprowizacji|
|workerID|DOPASOWANIE WYRAŻENIA REGULARNEGO|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Wszyscy pracownicy z workerIDsem od 1000000 do 2000000 są w zakresie aprowizacji.|

## <a name="related-articles"></a>Pokrewne artykuły:
* [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](user-provisioning.md)
* [Dostosuj mapowania atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md)
* [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
* [Powiadomienia o aprowizacji konta](user-provisioning.md)
* [Użyj Standard scim, aby włączyć automatyczną obsługę administracyjną użytkowników i grup z Azure Active Directory do aplikacji](use-scim-to-provision-users-and-groups.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

