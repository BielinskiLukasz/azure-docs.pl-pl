---
title: Jak badać ryzykownych użytkowników i logowania usługi Azure Active Directory identity Protection (odświeżane) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak badania ryzykownych użytkowników i logowania usługi Azure Active Directory identity Protection (odświeżane).
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.author: markvi
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: raluthra
ms.openlocfilehash: 25482d9cfc634b63b05e944cf7ca91f90b24f5f1
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913930"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Jak: Badanie ryzykownych użytkowników i logowania 


Korzystanie z raportów ryzykownych logowań i ryzykownych użytkowników możesz zbadać i uzyskiwanie szczegółowych informacji o podwyższonym ryzyku w danym środowisku. Z możliwością filtrowania i sortowania ryzykowne logowania i użytkowników możesz lepiej zrozumieć potencjalnych nieautoryzowanego dostępu w Twojej organizacji. 


## <a name="risky-users-report"></a>Raport ryzykownych użytkowników

Z informacjami w raporcie o ryzykownych użytkowników można uzyskać odpowiedzi na pytania takie jak:

- Użytkowników, którzy są wysokie ryzyko?
- Użytkowników, którzy mają stan ryzyka skorygowane?



Pierwszym punktem wejścia do tego raportu jest **zbadaj** sekcji na stronie zabezpieczenia.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/01.png)


Raport ryzykownych użytkowników ma domyślny widok, który pokazuje:

- Name (Nazwa)

- Stan ryzyka

- Poziom ryzyka

- Szczegóły ryzyka

- Ostatnia aktualizacja ryzyka

- Typ

- Stan
 

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/03.png)


Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/04.png)

Okno dialogowe kolumny pozwala na wyświetlanie dodatkowych pól lub usunąć pola, które są już wyświetlane.

Klikając pozycję w widoku listy, możesz wyświetlić jej wszystkie dostępne szczegóły w widoku poziomym.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/05.png)


Przedstawia widok szczegółów:

- Informacje podstawowe

- Ostatnie ryzykowne logowania

- Ryzykowne zdarzenia niepowiązane z logowaniem

- Historia ryzyka



Ponadto możesz wykonywać następujące czynności:

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/08.png)

- Wyświetl wszystkie skrótów logowania, aby wyświetlić raport dotyczący logowań dla tego użytkownika.

- Wyświetl wszystkie ryzykowne logowania do wyświetlenia wszystkich logowań dla danego użytkownika, które zostały oznaczone jako ryzykowne.

- Resetowanie hasła użytkownika, jeśli uważasz, że tożsamość użytkownika został złamany.

- Odrzuć ryzyka związanego z użytkownikiem, jeśli uważasz, że zdarzenia aktywnego ryzyka użytkownika czy wyników fałszywie dodatnich. Aby uzyskać więcej informacji, zobacz [sposób zwiększania dokładności wykrywania](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Filtruj ryzykownych użytkowników

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, który Ci odpowiada, można filtrować dane ryzykowny użytkownik przy użyciu następujących pól domyślnych:

- Name (Nazwa)

- Nazwa użytkownika

- Stan ryzyka

- Poziom ryzyka

- Typ

- Stan

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/06.png)



**Nazwa** filtr umożliwia określenie nazwy lub głównej nazwy użytkownika (UPN) użytkownika, interesujące Cię.


**Ryzyka stanu** filtr umożliwia wybranie:

- Narażeni
- Skorygowane
- Odrzucone


**Poziom ryzyka** filtr umożliwia wybranie:

- Wysoka
- Medium
- Małe


**Typu** filtr umożliwia wybranie:

- Gość
- Członek

**Stan** filtr umożliwia wybranie:

- Usunięte
- Aktywne


### <a name="download-risky-users-data"></a>Pobierz dane ryzykownych użytkowników

Jeśli chcesz pracować z nim poza witryny Azure portal można pobrać danych ryzykownych użytkowników. Klikając przycisk Pobierz tworzy plik CSV ostatnich rekordów 5K. 

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/07.png)


Możesz dostosować widok listy, klikając pozycję kolumny na pasku narzędzi.
 
Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.
 
Aby dowiedzieć się więcej na temat ryzykowny użytkownik, kliknij w szufladzie szczegóły, aby go rozwinąć

 



## <a name="risky-sign-ins-report"></a>Raport dotyczący ryzykownych logowań

Informacje dostarczone przez raport dotyczący ryzykownych logowań można uzyskać odpowiedzi na pytania takie jak:

- Ile pomyślnych logowaniach pojawili się tam, ma zdarzenia o podwyższonym ryzyku w anonimowych adresów IP w ostatnim tygodniu?

- Potwierdzono użytkowników, którzy zostaną naruszone w ciągu ostatniego miesiąca?

- Którzy użytkownicy mieli ryzykownych logowań w portalu Office 365?




Pierwszym punktem wejścia do tego raportu jest **zbadaj** sekcji na stronie zabezpieczenia.

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/02.png)

Raport dotyczący ryzykownych logowań zawiera domyślny widok, który pokazuje:

- Date

- Użytkownik

- Aplikacja

- Stan logowania

- Stan ryzyka

- Poziom ryzyka (agregacji)

- Poziom ryzyka (w czasie rzeczywistym)

- Dostęp warunkowy

- Wymagana usługa MFA  
 

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/09.png)


Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/11.png)

Okno dialogowe kolumny pozwala na wyświetlanie dodatkowych pól lub usunąć pola, które są już wyświetlane.

Klikając pozycję w widoku listy, możesz wyświetlić jej wszystkie dostępne szczegóły w widoku poziomym.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/12.png)


Przedstawia widok szczegółów:

- Informacje podstawowe

- Informacje o urządzeniu

- Informacje o ryzyku

- Informacje o uwierzytelnianiu wieloskładnikowym

- Dostęp warunkowy





Ponadto możesz wykonywać następujące czynności:

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/13.png)

- Potwierdź naruszenie bezpieczeństwa 

- Potwierdź bezpieczeństwo

Aby uzyskać więcej informacji, zobacz [sposób zwiększania dokładności wykrywania](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Filtr dotyczący ryzykownych logowań

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, który Ci odpowiada, można filtrować dane ryzykowny użytkownik przy użyciu następujących pól domyślnych:

- Użytkownik
- Aplikacja
- Stan logowania
- Stan ryzyka
- Poziom ryzyka (agregacji)
- Poziom ryzyka (w czasie rzeczywistym)
- Dostęp warunkowy
- Date
- Typ poziomu ryzyka

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/14.png)



**Nazwa** filtr umożliwia określenie nazwy lub głównej nazwy użytkownika (UPN) użytkownika, interesujące Cię.

**Aplikacji** Filtr pozwala na określenie użytkownik próbował uzyskać dostęp do aplikacji w chmurze.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Powodzenie
- Niepowodzenie


**Ryzyka stanu** filtr umożliwia wybranie:

- Narażeni
- Potwierdzono naruszenie bezpieczeństwa
- Potwierdzono bezpieczeństwo
- Odrzucone
- Skorygowane


**(Agregacji) poziom ryzyka** filtr umożliwia wybranie:

- Wysoka
- Medium
- Małe

**(W czasie rzeczywistym) poziom ryzyka** filtr umożliwia wybranie:

- Wysoka
- Medium
- Małe


**Dostępu warunkowego** filtr umożliwia wybranie:

- Wszyscy
- Nie zastosowano
- Powodzenie
- Niepowodzenie


Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.
Możliwe wartości:

- Ostatni 1 miesiąc
- Ostatnie 7 dni
- Ostatnie 24 godziny
- Niestandardowy zakres czasu





### <a name="download-risky-sign-ins-data"></a>Pobierz dane ryzykowne logowania

Jeśli chcesz pracować z nim poza portalem Azure, można pobrać danych dotyczący ryzykownych logowań. Klikając przycisk Pobierz tworzy plik CSV ostatnich rekordów 5K. 

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview-v2.md).