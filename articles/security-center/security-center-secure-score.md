---
title: Zabezpieczanie oceny w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Ustaw priorytet swoje zalecenia dotyczące zabezpieczeń przy użyciu bezpiecznego oceny w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 4bc426e291a47ae42e7628ad911e2960e654819f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261302"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Poprawa bezpiecznego ocenę w usłudze Azure Security Center


W przypadku tak wiele usług oferuje korzyści w zakresie zabezpieczeń jest często wiadomo, jakie czynności umożliwiające najpierw bezpieczny i wzmocnione obciążenia. Wynik bezpiecznej platformy Azure monitoruje zalecenia w zakresie zabezpieczeń i ich priorytetem, aby było wiadomo, które zalecenia, aby najpierw wykonać. Dzięki temu można znaleźć najpoważniejsze luk w zabezpieczeniach, dzięki czemu można określić priorytet badania. Bezpieczne wynik jest narzędziem, które pomaga ocenić poziomu bezpieczeństwa obciążenia.

## <a name="secure-score-calculation"></a>Zabezpieczanie obliczania wyniku

Usługa Security Center naśladuje pracy przez analityka zabezpieczeń, przeglądania zalecenia w zakresie zabezpieczeń i stosowanie zaawansowanych algorytmów do ustalenia, jak niezwykle istotne jest poszczególne zalecenia.
Usługi Azure Security center stale monitoruje możesz aktywne zalecenia i oblicza bezpiecznego ocenę na ich podstawie, wynik zalecenie jest tworzony na podstawie jego ważności i najlepszych rozwiązań dotyczących zabezpieczeń, które będzie miało wpływ na bezpieczeństwo obciążenia najbardziej.

Usługa Security Center zapewnia także **secure ogólną ocenę**. 

**Zabezpieczanie ogólny wynik** jest nagromadzenia wyniki zalecenia. Możesz wyświetlić ogólną bezpiecznego ocenę w Twojej subskrypcji lub grupy zarządzania, w zależności od tego, możesz wybrać. Wynik będzie zależeć od wybrano subskrypcji i aktywne zalecenia w ramach tych subskrypcji.

 
Zalecenia dotyczące największy trzy pierwsze aby sprawdzić, które zalecenia dotyczące najbardziej wpływ na ocenę bezpieczny, mogą być wyświetlane na pulpicie nawigacyjnym usługi Security Center lub można sortować zalecenia za pomocą bloku listę zaleceń dotyczących **bezpiecznego wynik wpływ** kolumny.


Aby wyświetlić ocenę ogólną bezpiecznego:

1. Na pulpicie nawigacyjnym platformy Azure kliknij **usługi Security Center** a następnie kliknij przycisk **bezpiecznego wynik**.
2. U góry możesz zobaczyć bezpiecznego ocena najważniejsze funkcje:
   - **Secure ogólną ocenę** reprezentuje wynik na zasady dla wybranej subskrypcji
   - **Zabezpieczanie wynik według kategorii** dowiesz się, które zasoby wymagają uwagi najbardziej
   - **Najlepszych zaleceń zabezpieczenia przez ocenę wpływu** zawiera listę zaleceń, które poprawią bezpiecznego ocenę, najlepiej, jeśli je skorygować.
 
   ![Zabezpieczanie wynik](./media/security-center-secure-score/secure-score-dashboard.png)

3. W poniższej tabeli widać wszystkich subskrypcji i wynik ogólny bezpieczny dla każdego.

   > [!NOTE]
   > Suma bezpiecznego wynik każdej subskrypcji nie jest równa wynik ogólny bezpieczne. Bezpieczne wynik jest obliczenia oparte na stosunek między zasoby w dobrej kondycji i łączna liczba zasobów na zalecenia nie sumę bezpiecznego wyniki wszystkich subskrypcji. 
   >
4. Kliknij przycisk **Wyświetl zalecenia** Aby wyświetlić zalecenia dla tej subskrypcji, która może rozwiązać w celu bezpiecznego ocenę.
4. Na liście zaleceń widać, że każde zalecenie, aby uzyskać to kolumna, która reprezentuje **Secure ocenę wpływu**. Ten numer reprezentuje ile zwiększyć ogólną bezpiecznego ocenę w sytuacji, jeśli należy postępować zgodnie z zaleceniami. Na przykład na ekranie poniżej, jeśli użytkownik **korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń w kontenerze**, bezpieczne ocenę ceny wzrosną o 35 punktów.

   ![Zabezpieczanie wynik](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Indywidualne bezpieczne wynik

Ponadto aby wyświetlić poszczególne wyniki bezpieczne, można znaleźć je w bloku poszczególne zalecenia.  

**Zalecenie secure wynik** jest obliczenia oparte na stosunek między zasoby w dobrej kondycji i łączna liczba zasobów. Liczba zasoby w dobrej kondycji jest równa łączna liczba zasobów, otrzymują maksymalna wynik bezpiecznego zalecenia 50. Spróbuj pobrać ocenę bezpiecznego bliżej max wynik, napraw zasoby w złej kondycji, postępując zgodnie z zaleceniami.

**Wpływ rekomendacji** umożliwia sprawdzenie, ile zwiększa ocenę bezpieczne w przypadku zastosowania kroków zaleceń. Na przykład, jeśli bezpieczne ocenę 42 i **wpływ rekomendacji** jest + 3, wykonując czynności opisane w zaleceniu, poprawić ocenę przestanie 45.

Zalecenie przedstawia zagrożenia, które obciążenia jest narażony na, jeśli nie zostaną podjęte kroki korygowania.

![wynik bezpiecznego poszczególne zalecenia](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Kolejne kroki
Ten artykuł pokazuje, jak zwiększyć, używając poziom zabezpieczeń **bezpiecznego wynik** w usłudze Azure Security Center. Aby dowiedzieć się więcej o usłudze Security Center, zobacz:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.


