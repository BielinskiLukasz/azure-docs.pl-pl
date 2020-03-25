---
title: Zmienianie oferty subskrypcji platformy Azure
description: Dowiedz się, jak zmienić subskrypcję platformy Azure i przełączyć się do innej oferty przy użyciu Centrum konta platformy Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 4aa02dae521844f2654a50f77be9b2ee3c46a74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199521"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Zmienianie subskrypcji platformy Azure na inną ofertę

Jako klient z [indywidualną subskrypcją z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/) możesz przełączyć swoją subskrypcję platformy Azure na inną ofertę w [Centrum konta](https://account.windowsazure.com/Subscriptions). Na przykład możesz użyć tej funkcji, aby skorzystać z [miesięcznych środków dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Chcesz tylko podwyższyć poziom z bezpłatnej wersji próbnej?** Zobacz [Podwyższanie poziomu subskrypcji](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Jakie operacje są obsługiwane:

Możesz przełączać się z pojedynczej subskrypcji z płatnością zgodnie z rzeczywistym użyciem na następujące oferty:

- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Aby uzyskać informacje o innych zmianach ofert, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Przełączanie oferty subskrypcji

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions).
1. Wybierz pojedynczą subskrypcję z płatnością zgodnie z rzeczywistym użyciem.
1. Kliknij pozycję **Przełącz na inną ofertę**. Ta opcja jest dostępna tylko wtedy, gdy masz indywidualną subskrypcję z płatnością zgodnie z rzeczywistym użyciem i ukończono pierwszy okres rozliczeniowy.

   ![Zwróć uwagę na przycisk Przełącz ofertę po prawej stronie](./media/switch-azure-offer/switchbutton.png)
1. **Wybierz żądaną ofertę** na liście ofert, na które można przełączyć Twoją subskrypcję. Ta lista różni się w zależności od członkostwa, z którym jest skojarzone Twoje konto. Jeśli żadna oferta nie jest dostępna, sprawdź [listę dostępnych ofert, na które możesz się przełączyć](#whats-supported) i upewnij się, że masz właściwe członkostwo.

   ![Wybór oferty, do której ma nastąpić przełączenie](./media/switch-azure-offer/selectoffer.png)
1. W zależności od wybranej oferty możesz zobaczyć uwagę dotyczącą wpływu przełączenia. Przed kontynuowaniem przejrzyj tę listę uważnie i postępuj zgodnie z instrukcjami.

   ![Przeglądanie uwag](./media/switch-azure-offer/thingstonote.png)
1. Możesz zmienić nazwę subskrypcji. Domyślnie jest ustawiona nowa nazwa oferty. Kliknij przycisk **Przełącz ofertę**, aby zakończyć proces.

   ![Kliknięcie zielonego przycisku](./media/switch-azure-offer/confirmpage.png)
1. To wszystko! Twoja subskrypcja została przełączona do nowej oferty.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
W poniższych sekcjach zamieszczono odpowiedzi na często zadawane pytania.

### <a name="what-is-an-azure-offer"></a>Co to jest oferta platformy Azure?

Oferta platformy Azure to *typ* posiadanej subskrypcji platformy Azure. Ofertami platformy Azure są na przykład [subskrypcja ze stawkami płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p/) i [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/). Każda oferta ma inne [warunki](https://azure.microsoft.com/support/legal/offer-details/), a niektóre z nich mają specjalne korzyści. Ofertę Twojej subskrypcji możesz znaleźć na stronie subskrypcji w Centrum konta. Kliknij nazwę oferty, aby uzyskać więcej szczegółów.

   ![Kliknięcie linku oferty w Centrum konta w celu uzyskania dodatkowych szczegółów](./media/switch-azure-offer/offerlink01.png)

### <a name="why-dont-i-see-the-button"></a>Dlaczego nie widzę tego przycisku?

Opcja **Przełącz na inną ofertę** może nie być widoczna z następujących powodów:

* Nie masz [subskrypcji ze stawką płatności zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/). Obecnie tylko subskrypcje ze stawką płatności zgodnie z rzeczywistym użyciem mogą być konwertowane na inną ofertę.
  * Jeśli masz [bezpłatną wersję próbną](https://azure.microsoft.com/free/), dowiedz się, jak [podwyższyć poziom planu do subskrypcji rozliczanej zgodnie z rzeczywistym użyciem](upgrade-azure-subscription.md).
  * Aby przełączyć ofertę z innej subskrypcji, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Nadal jesteś w trakcie pierwszego okresu rozliczeniowego, więc musisz poczekać na jego zakończenie, aby można było przełączać oferty.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Dlaczego widzę komunikat „Obecnie w tym regionie lub kraju nie są dostępne żadne oferty”?

* Możesz nie kwalifikować się do żadnych przełączeń ofert. Zapoznaj się z [listą dostępnych ofert, na które możesz się przełączyć,](#whats-supported) i upewnij się, że aktywowano odpowiednie korzyści w programie Visual Studio lub BizSpark.
* Niektóre oferty mogą nie być dostępne we wszystkich krajach/regionach.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Jaki wpływ ma przełączanie ofert platformy Azure na moje usługi lub rozliczenia?

Poniżej znajdują się szczegółowe informacje o tym, co się dzieje po przełączeniu ofert platformy Azure w Centrum konta.

#### <a name="no-service-downtime"></a>Bez przestoju usługi

Usługa będą działać bez przestojów dla wszystkich użytkowników skojarzonych z subskrypcją. Jednak oferta, na którą się przełączysz, może mieć ograniczenia. Na przykład niektóre oferty zabraniają użycia produkcyjnego, dlatego konieczne jest przeniesienie zasobów produkcyjnych do innej subskrypcji.

#### <a name="quota-increases-are-reset"></a>Zwiększone limity przydziału są resetowane

Po przełączeniu ofert wszystkie [limity lub limity przydziału wykraczające poza domyślne limity](../../azure-portal/supportability/resource-manager-core-quotas-request.md) są resetowane. Działanie usługi nie zostanie przerwane, nawet jeśli masz więcej zasobów, niż na to pozwala limit domyślny. Jeśli na przykład w ramach subskrypcji korzystasz z 200 rdzeni, przełączenie ofert spowoduje zresetowanie limitu przydziału rdzeni do wartości domyślnej wynoszącej 20 rdzeni. Ta zmiana nie będzie miała wpływu na maszyny wirtualne korzystające z 200 rdzeni, które będą nadal działać. Jeśli jednak nie wprowadzisz kolejnego żądania zwiększenia limitu przydziału, nie możesz aprowizować większej liczby rdzeni.

#### <a name="billing"></a>Rozliczenia

W dniu przełączenia ofert zostanie wygenerowana faktura na wszystkie zaległe opłaty. Od tego momentu Twoja subskrypcja jest rozliczana według warunków cenowych nowej oferty. Rocznica rozliczenia Twojej subskrypcji zmieni się na datę, kiedy zostały zmienione oferty. Dane dotyczące użycia i rozliczeń przed zmianą oferty nie zostaną zachowane, dlatego zalecamy pobranie ich kopii przed przełączeniem.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Czy mogę przeprowadzić migrację z subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem do programu Cloud Solution Provider (CSP) lub umowy Enterprise Agreement (EA)?

* Aby przeprowadzić migrację do programu CSP, zobacz [Migracja subskrypcji z płatnością zgodnie z rzeczywistym użyciem do programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Aby przeprowadzić migrację do umowy EA, poproś administratora rejestracji o dodanie Twojego konta do umowy EA. Postępuj zgodnie z instrukcjami w wiadomości e-mail z zaproszeniem, aby przenieść swoje subskrypcje do rejestracji w ramach umowy EA. Aby dowiedzieć się więcej, zobacz [Kojarzenie istniejącego konta](https://ea.azure.com/helpdocs/associateExistingAccount) w witrynie EA Portal.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Czy mogę zmigrować dane i usługi do nowej subskrypcji?

* Zasoby można migrować bezpośrednio do nowej subskrypcji. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Aby przenieść własność subskrypcji platformy Azure i wszystkich jej zasobów na inną osobę, zobacz [Przenoszenie własności subskrypcji platformy Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Rozpoczęcie analizy kosztów](../costs/quick-acm-cost-analysis.md)
