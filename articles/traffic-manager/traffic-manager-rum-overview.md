---
title: Pomiary rzeczywistego użytkownika na platformie Azure Traffic Manager
description: Wprowadzenie do Pomiary rzeczywistego użytkownika w Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4aa2649ba2e49e1fec1b9b124a9b82313280cee9
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333731"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Omówienie Pomiary rzeczywistego użytkownika Traffic Manager

Po skonfigurowaniu profilu Traffic Manager do korzystania z metody routingu wydajności usługa sprawdza, czy żądania zapytań DNS pochodzą z i podejmuje decyzje dotyczące routingu, aby kierować tych podmiotów żądających do regionu platformy Azure, który zapewnia im najmniejsze opóźnienia. Jest to realizowane przy użyciu analizy opóźnienia sieci, która Traffic Manager utrzymywana dla różnych sieci użytkowników końcowych.

Pomiary rzeczywistego użytkownika pozwala mierzyć pomiary opóźnienia sieci w regionach platformy Azure, od aplikacji klienckich używanych przez użytkowników końcowych, a także mieć Traffic Manager uwzględniania tych informacji podczas podejmowania decyzji dotyczących routingu. Korzystając z Pomiary rzeczywistego użytkownika, można zwiększyć dokładność routingu dla żądań pochodzących z tych sieci, w których znajdują się użytkownicy końcowi. 

## <a name="how-real-user-measurements-work"></a>Jak działa Pomiary rzeczywistego użytkownika

Pomiary rzeczywistego użytkownika pracy, ponieważ aplikacje klienckie mierzą opóźnienia w regionach platformy Azure, ponieważ są widoczne z sieci użytkowników końcowych, w których są używane. Jeśli na przykład masz stronę sieci Web, do której użytkownicy uzyskują dostęp w różnych lokalizacjach (na przykład w regionach Ameryki Północnej), możesz użyć Pomiary rzeczywistego użytkownika z metodą routingu wydajności, aby uzyskać dostęp do najlepszego regionu platformy Azure, w którym serwer Aplikacja jest hostowana.

Zaczyna od osadzania języka JavaScript dostarczonego przez platformę Azure (z unikatowym kluczem) na stronach sieci Web. Gdy to zrobisz, za każdym razem, gdy użytkownik odwiedzi tę stronę sieci Web, kwerendy języka JavaScript Traffic Manager, aby uzyskać informacje na temat regionów świadczenia usługi Azure, które powinny być mierzone. Usługa zwraca zestaw punktów końcowych do skryptu, który następnie mierzy te regiony po kolei przez pobranie obrazu pojedynczego piksela hostowanego w tych regionach świadczenia usługi Azure i zanotowanie opóźnienia między czasem wysłania żądania a czasem odebrania pierwszego bajtu . Te pomiary są następnie raportowane z powrotem do usługi Traffic Manager.

W miarę upływu czasu zdarza się to wiele razy i między wieloma sieciami, co prowadzi do Traffic Manager uzyskiwania dokładniejszych informacji na temat właściwości opóźnienia sieci, w których znajdują się użytkownicy końcowi. Te informacje zaczynają być uwzględniane w decyzjach dotyczących routingu podejmowanych przez Traffic Manager. W efekcie prowadzi to do zwiększonej dokładności tych decyzji na podstawie wysłanych Pomiary rzeczywistego użytkownika.

Gdy używasz Pomiary rzeczywistego użytkownika, opłaty są naliczane na podstawie liczby pomiarów wysłanych do Traffic Manager. Aby uzyskać więcej informacji na temat cen, odwiedź [stronę z cennikiem Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Często zadawane pytania

* [Jakie korzyści wynikają z używania Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Czy mogę używać Pomiary rzeczywistego użytkownika z regionami nienależącymi do platformy Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Która metoda routingu przynosi korzyści z Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Czy muszę włączyć Pomiary rzeczywistego użytkownika każdy profil osobno?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Jak mogę wyłączyć Pomiary rzeczywistego użytkownika dla mojej subskrypcji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Czy mogę używać Pomiary rzeczywistego użytkownika z aplikacjami klienckimi innymi niż strony sieci Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Ile pomiarów jest wykonywanych za każdym razem, gdy jest renderowana Strona sieci Web z włączonymi Pomiary rzeczywistego użytkownikaami?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Czy istnieje opóźnienie przed uruchomieniem skryptu Pomiary rzeczywistego użytkownika na mojej stronie sieci Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Czy mogę używać Pomiary rzeczywistego użytkownika tylko z regionami platformy Azure, które mają być mierzone?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Czy mogę ograniczyć liczbę pomiarów wprowadzonych do określonego numeru?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Czy mogę zobaczyć pomiary wykonywane przez aplikację kliencką w ramach Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Czy mogę zmodyfikować skrypt pomiarowy dostarczony przez Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Czy będzie możliwe, aby inni mogli zobaczyć klucz, z którego korzystam z Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Czy inne osoby mogą używać klucza rumu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Czy muszę umieścić miarę JavaScript na wszystkich moich stronach sieci Web?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Czy informacje o użytkownikach końcowych są identyfikowane przez Traffic Manager, jeśli używam Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Czy funkcja mierzenia Pomiary rzeczywistego użytkownika musi używać Traffic Manager do routingu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Czy muszę hostować każdą usługę w regionach platformy Azure, która ma być używana z Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Czy moja przepustowość platformy Azure zwiększa się, gdy używam Pomiary rzeczywistego użytkownika?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak używać [pomiary rzeczywistego użytkownika ze stronami sieci Web](traffic-manager-create-rum-web-pages.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o usłudze [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](traffic-manager-create-profile.md)

