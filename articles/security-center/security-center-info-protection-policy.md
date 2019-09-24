---
title: Dostosowywanie zasad ochrony informacji SQL w programie Azure Security Center | Microsoft Docs
description: Dowiedz się, jak dostosować zasady ochrony informacji w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: edd415c330656d4cecc42a39d27598a88a1a8d2c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202098"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Dostosowywanie zasad ochrony informacji SQL w Azure Security Center (wersja zapoznawcza)
 
Zasady usługi SQL Information Protection można definiować i dostosowywać dla całej dzierżawy platformy Azure w Azure Security Center.

Information Protection to zaawansowana funkcja zabezpieczeń do odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w zasobach danych platformy Azure. Odkrywanie i klasyfikowanie najbardziej poufnych danych (biznes, finanse, opieka, dane osobowe itp.) może odgrywać rolę przestawną w usłudze Information Protection schemacie. Może to być infrastruktura dla:
- Pomaganie spełnić standardy prywatności danych i wymagania dotyczące zgodności z przepisami
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty dotyczące nietypowego dostępu do poufnych danych
- Kontrolowanie dostępu do i ograniczanie bezpieczeństwa magazynów danych zawierających wysoce poufne dane
 
[Program sql Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementuje ten model dla magazynów danych SQL, które są obecnie obsługiwane przez Azure SQL Database. Funkcja SQL Information Protection automatycznie wykrywa i klasyfikuje potencjalnie wrażliwe dane, zapewnia mechanizm etykietowania do trwałego tagowania poufnych danych przy użyciu atrybutów klasyfikacji i udostępnia szczegółowy pulpit nawigacyjny pokazujący stan klasyfikacji bazy danych. Ponadto oblicza on czułość zestawu wyników zapytań SQL, tak aby zapytania, które wyodrębniają poufne dane, mogły być jawnie poddane inspekcji, a dane mogą być chronione. Aby uzyskać więcej informacji na temat Information Protection SQL, zobacz [Azure SQL Database odnajdywania i klasyfikowania danych](../sql-database/sql-database-data-discovery-and-classification.md).
 
Mechanizm klasyfikacji opiera się na dwóch głównych konstrukcjach, które tworzą taksonomię klasyfikacji i **typy informacji**.
- **Etykiety** — główne atrybuty klasyfikacji używane do definiowania poziomu czułości danych przechowywanych w kolumnie. 
- **Typy informacji** — zapewnia dodatkowy poziom szczegółowości dla typu danych przechowywanych w kolumnie.
 
Information Protection jest dostarczany z wbudowanym zestawem etykiet i typów informacji, które są używane domyślnie. Aby je dostosować, można dostosować zasady ochrony informacji w Azure Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Dostosowywanie zasad ochrony informacji
Aby dostosować zasady ochrony informacji dla dzierżawy platformy Azure, musisz mieć [uprawnienia administracyjne w głównej grupie zarządzania dzierżawcy](security-center-management-groups.md). 
 
1. W menu głównym Security Center w obszarze **higiena zabezpieczeń zasobów** przejdź do pozycji **dane & magazyn** i kliknij przycisk **SQL Information Protection** .

   ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na stronie **Information Protection SQL** można wyświetlić bieżący zestaw etykiet. Są to główne atrybuty klasyfikacji, które są używane do kategoryzowania poziomu czułości danych. W tym miejscu możesz skonfigurować etykiety i **typy informacji** usługi **Information Protection** dla dzierżawy. 
 
### <a name="customizing-labels"></a>Dostosowywanie etykiet
 
1. Można edytować lub usunąć istniejącą etykietę lub dodać nową etykietę. Aby edytować istniejącą etykietę, wybierz tę etykietę, a następnie kliknij pozycję **Konfiguruj**, w górnej części lub w menu kontekstowym po prawej stronie. Aby dodać nową etykietę, kliknij przycisk **Utwórz etykietę** na górnym pasku menu lub u dołu tabeli etykiet.
2. Na ekranie **Konfigurowanie etykiety czułości** można utworzyć lub zmienić nazwę etykiety i opis. Można również określić, czy etykieta jest aktywna, czy wyłączona, włączając lub wyłączając **włączony** przełącznik. Na koniec można dodać lub usunąć typy informacji skojarzone z etykietą. Wszystkie wykryte dane, które pasują do tego typu informacji, będą automatycznie zawierały skojarzoną etykietę czułości w zaleceniach klasyfikacji.
3. Kliknij przycisk **OK**.
 
   ![Konfigurowanie etykiety czułości](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etykiety są wyświetlane w kolejności rosnącej. Aby zmienić klasyfikację między etykietami, przeciągnij etykiety, aby zmienić ich kolejność w tabeli, lub użyj przycisków **Przenieś w górę** i **Przenieś w dół** , aby zmodyfikować kolejność. 
 
    ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/move-up.png)
 
5. Pamiętaj, aby po zakończeniu kliknij pozycję **Zapisz** w górnej części ekranu.
 
 
## <a name="adding-and-customizing-information-types"></a>Dodawanie i Dostosowywanie typów informacji
 
1. Możesz zarządzać typami informacji i dostosowywać je, klikając pozycję **Zarządzaj typami informacji**.
2. Aby dodać nowy **Typ informacji**, wybierz opcję **Utwórz typ informacji** w górnym menu. Można skonfigurować nazwę, opis i ciągi wzorców wyszukiwania dla **typu informacji**. Ciągi wzorców wyszukiwania mogą opcjonalnie używać słów kluczowych z symbolami wieloznacznymi (za pomocą znaku "%"), który jest używany przez aparat automatycznego odnajdywania do identyfikowania poufnych danych w bazach danych na podstawie metadanych kolumn.
 
    ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/info-types.png)
 
3. Możesz również skonfigurować wbudowane **typy informacji** przez dodanie dodatkowych ciągów wzorca wyszukiwania, wyłączenie niektórych istniejących ciągów lub zmianę opisu. Nie można usunąć wbudowanych **typów informacji** ani edytować ich nazw. 
4. **Typy informacji** są wymienione w kolejności rosnącej klasyfikacji odnajdywania, co oznacza, że typy wyższe na liście będą podejmować próby dopasowania najpierw. Aby zmienić klasyfikację między typami informacji, przeciągnij typy do prawego miejsca w tabeli lub użyj przycisków **Przenieś w górę** i **Przenieś w dół** , aby zmienić kolejność. 
5. Po zakończeniu kliknij przycisk **OK** .
6. Po zakończeniu zarządzania typami informacji Pamiętaj o skojarzeniu odpowiednich typów z odpowiednimi etykietami, klikając opcję **Konfiguruj** dla określonej etykiety i dodając lub usuwając typy informacji zgodnie z potrzebami.
7. Kliknij przycisk **Zapisz** w bloku głównych **etykiet** , aby zastosować wszystkie zmiany.
 
Po pełnym zdefiniowaniu i zapisaniu zasad usługi Information Protection będzie ona miała zastosowanie do klasyfikacji danych w ramach wszystkich baz danych usługi Azure SQL w dzierżawie.
 
 
## <a name="next-steps"></a>Następne kroki
 
Ten artykuł zawiera informacje na temat definiowania zasad Information Protection SQL w programie Azure Security Center. Aby dowiedzieć się więcej o używaniu programu SQL Information Protection do klasyfikowania i ochrony poufnych danych w bazach danych SQL, zobacz [Azure SQL Database odnajdywania i klasyfikowania danych](../sql-database/sql-database-data-discovery-and-classification.md). 

Aby uzyskać więcej informacji na temat zasad zabezpieczeń i zabezpieczeń danych w Azure Security Center, zobacz następujące artykuły:
 
- [Przegląd zasad zabezpieczeń](security-center-policies-overview.md): Zapoznaj się z omówieniem zasad zabezpieczeń w Security Center
- [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji i grup zasobów platformy Azure
- [Azure Security Center zabezpieczenia danych](security-center-data-security.md): Informacje o tym, jak Security Center zarządza i zabezpiecza dane


