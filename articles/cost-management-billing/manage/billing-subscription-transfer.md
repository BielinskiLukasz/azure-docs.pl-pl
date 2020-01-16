---
title: Przeniesienie własności rozliczeń subskrypcji platformy Azure
description: Opisuje sposób przeniesienia własności rozliczeń subskrypcji platformy Azure na inne konto. Zawiera odpowiedzi na często zadawane pytania dotyczące tego procesu
keywords: przeniesienie subskrypcji platformy Azure, przeniesienie subskrypcji platformy Azure na inne konto, zmiana właściciela subskrypcji platformy Azure, transfer subskrypcji platformy Azure na inne konto, transfer rozliczeń na platformie Azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f43f7424cddae8659301be0ddc4842412a62952
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993546"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure na inne konto

Możesz chcieć przenieść własność rozliczeń subskrypcji platformy Azure, jeśli opuszczasz organizację lub chcesz, aby Twoja subskrypcja była rozliczana na inne konto. Przeniesienie własności rozliczeń na inne konto zapewnia administratorom nowego konta uprawnienia do wykonywania zadań rozliczeniowych, takich jak zmiana formy płatności, wyświetlanie opłat i anulowanie subskrypcji.

Jeśli chcesz zachować własność rozliczeń, ale zmienić typ subskrypcji, zobacz [Przełączanie subskrypcji platformy Azure na inną ofertę](switch-azure-offer.md). Jeśli chcesz kontrolować, kto może zarządzać zasobami w ramach subskrypcji, zobacz [Wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Jeśli jesteś klientem z Umową Enterprise (EA), administratorzy w Twoim przedsiębiorstwie mogą przenosić własność rozliczeń subskrypcji między kontami. Więcej informacji — zobacz [Przenoszenie własności rozliczeń subskrypcji w ramach Umowy Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Przeniesienie własności rozliczeń subskrypcji platformy Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta rozliczeniowego z subskrypcją, którą chcesz przenieść. Aby dowiedzieć się, czy jesteś administratorem, przeczytaj odpowiedzi na [często zadawane pytania](#faq).

1. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Wybierz pozycję **Subskrypcje** w okienku po lewej stronie. W zależności od dostępu może być konieczne wybranie zakresu rozliczeniowego, a następnie **subskrypcji** lub **subskrypcji platformy Azure**.

1. Wybierz pozycję **Przenieś własność rozliczeń subskrypcji** dla subskrypcji, którą chcesz przenieść.

   ![Wybierz subskrypcję do przeniesienia](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Wprowadź adres e-mail użytkownika, który jest administratorem rozliczeń konta i będzie nowym właścicielem subskrypcji.

1. Jeśli przenosisz subskrypcję do konta w innej dzierżawie usługi Azure AD, zaznacz, że chcesz przenieść subskrypcję do dzierżawy nowego konta. Więcej informacji — zobacz [Przenoszenie subskrypcji do konta w innej dzierżawie usługi Azure AD](#transferring-subscription-to-an-account-in-another-azure-ad-tenant).

    > [!IMPORTANT]
    >
    > Jeśli przeniesiesz subskrypcję do konta w innej dzierżawie usługi Azure AD, wszystkie [przypisania kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji zostaną trwale usunięte. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami w ramach subskrypcji. Więcej informacji — zobacz [Przenoszenie subskrypcji do użytkownika w innej dzierżawie usługi Azure AD](../../cognitive-services/acoustics/known-issues.md). Alternatywnie możesz usunąć zaznaczenie pola dzierżawy usługi Azure AD subskrypcji w celu przeniesienia własności rozliczeń bez przeniesienia subskrypcji do dzierżawy nowego konta. W takim przypadku istniejące uprawnienia RBAC do zarządzania zasobami platformy Azure zostaną zachowane.

    ![Strona wysyłania przeniesienia](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Wybierz opcję **Wyślij żądanie przeniesienia**.

1. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

   ![Wiadomość e-mail dotycząca przeniesienia subskrypcji wysłana do adresata](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Aby zatwierdzić żądanie przeniesienia, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami. Użytkownik musi wybrać formę płatności, która będzie używana do płacenia za subskrypcję. Ponadto, jeśli użytkownik nie ma konta platformy Azure, może być konieczne zarejestrowanie się w celu uzyskania nowego konta.

   ![Pierwsza strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Druga strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Druga strona sieci Web przeniesienia subskrypcji](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. To wszystko! Subskrypcja została przeniesiona.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Przenoszenie subskrypcji do konta w innej dzierżawie usługi Azure AD

Dzierżawa usługi Azure Active Directory (AD) jest tworzona podczas tworzenia konta na platformie Azure. Dzierżawa reprezentuje Twoje konto. Dzierżawa służy do zarządzania dostępem do Twoich subskrypcji i zasobów.

Nowo utworzona subskrypcja jest hostowana w dzierżawie usługi Azure AD konta. Jeśli chcesz zapewnić innym użytkownikom dostęp do swojej subskrypcji lub jej zasobów, musisz zaprosić ich do dołączenia do dzierżawy. Ułatwia to kontrolowanie dostępu do subskrypcji i zasobów.

Jeśli przenosisz własność rozliczeń subskrypcji do konta w innej dzierżawie usługi Azure AD, możesz przenieść subskrypcję do dzierżawy nowego konta. W takim przypadku wszyscy użytkownicy, grupy lub jednostki usługi [mający dostęp oparty na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) do zarządzania subskrypcjami i ich zasobami utracą ten dostęp. Tylko użytkownik nowego konta, akceptujący żądanie przeniesienia, będzie miał dostęp do zarządzania zasobami. Aby zapewnić dostęp użytkownikom, którzy mieli go pierwotnie, nowy właściciel musi [ręcznie dodać tych użytkowników do subskrypcji](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Przenoszenie subskrypcji tworzenia i testowania programu Visual Studio, Microsoft Partner Network (MPN) i płatności zgodnie z rzeczywistym użyciem

Subskrypcje programu Visual Studio i Microsoft Partner Network zawierają comiesięczne, powiązane z nimi środki na korzystanie z platformy Azure. Po przeniesieniu tych subskrypcji środki nie są dostępne na docelowym koncie rozliczeniowym. Subskrypcja używa kredytu na docelowym koncie rozliczeniowym. Jeśli na przykład Robert przeniesie subskrypcję Visual Studio Enterprise na konto Janiny 9 września, a Janina zaakceptuje przeniesienie, po zakończeniu przeniesienia subskrypcja zacznie korzystać z kredytu na koncie Janiny. Środki będą resetowane 9 dnia każdego miesiąca.


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Przenoszenie własności rozliczeń subskrypcji w ramach Umowy Enterprise (EA)

Administrator przedsiębiorstwa może przenieść własność subskrypcji między kontami w ramach rejestracji. Więcej informacji — zobacz [Przenoszenie własności konta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w portalu EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Kroki po zaakceptowaniu własności rozliczeń

Jeśli zaakceptujesz własność rozliczeń subskrypcji platformy Azure, zalecamy zapoznanie się z następującymi krokami:

1. Przejrzyj i zaktualizuj role administrator usługi, współadministratorzy i inne role RBAC. Więcej informacji — zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Zarządzanie dostępem przy użyciu RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
1. Zaktualizuj poświadczenia skojarzone z usługami tej subskrypcji, w tym:
   1. Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Klucze dostępu dla usług, takich jak Storage. Więcej informacji — zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).
   1. Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.
1. Jeśli pracujesz z partnerem, rozważ zaktualizowanie identyfikatora partnera w ramach tej subskrypcji. Identyfikator partnera można zaktualizować w witrynie [Azure Portal](https://portal.azure.com). Więcej informacji — zobacz [Łączenie identyfikatora partnera z kontami platformy Azure](link-partner-id.md).

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Przenoszenie subskrypcji w witrynie Azure Portal jest dostępne dla typów subskrypcji wymienionych poniżej. Obecnie przenoszenie nie jest obsługiwane w przypadku [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p/) ani [subskrypcji platformy Azure w ramach programu licencjonowania Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Więcej informacji — zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Aby przenieść inne subskrypcje, takie jak plany [dostępu sponsorowanego](https://azure.microsoft.com/offers/ms-azr-0036p/) lub pomocy technicznej, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [za pośrednictwem portalu EA](#EA).

\*\* Obsługiwane tylko w przypadku kont utworzonych podczas tworzenia konta w witrynie sieci Web platformy Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Często zadawane pytania dotyczące nadawców

Te pytania dotyczą użytkowników przenoszących własność rozliczeń subskrypcji platformy Azure na inne konto.

### <a name="whoisaa"></a> Kto jest administratorem rozliczeń konta?

Administrator rozliczeń to osoba, która ma uprawnienia do zarządzania rozliczeniami konta. Administratorzy mogą uzyskiwać dostęp do rozliczeń w witrynie [Azure Portal](https://portal.azure.com) i wykonywać różne zadania rozliczeniowe, takie jak tworzenie subskrypcji, wyświetlanie i płacenie faktur lub aktualizowanie form płatności.

Aby zidentyfikować konta, dla których jesteś administratorem rozliczeń, wykonaj następujące czynności:

1. Przejdź do strony [Zarządzanie kosztami i rozliczenia w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Zaznacz **Wszystkie zakresy rozliczeniowe** w okienku po lewej stronie.
1. Na stronie Subskrypcje są wyświetlane wszystkie subskrypcje, dla których jesteś administratorem rozliczeń.

Jeśli nie wiesz, kto jest administratorem konta dla subskrypcji, wykonaj poniższe kroki, aby to sprawdzić.

1. Odwiedź [stronę Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie przejrzyj **Ustawienia**.
1. Wybierz pozycję **Właściwości**. Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Czy wszystko jest przenoszone? Również grupy zasobów, maszyny wirtualne, dyski i inne uruchomione usługi?

Wszystkie zasoby, takie jak maszyny wirtualne, dyski i witryny internetowe, są przenoszone na nowe konto. Jeśli jednak przeniesiesz subskrypcję na konto w innej dzierżawie usługi Azure AD, wszystkie[role administratora](add-change-subscription-administrator.md) i przypisania [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji [nie zostaną przeniesione](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Ponadto [rejestracje aplikacji](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) ani inne usługi specyficzne dla dzierżawy nie są przenoszone razem z subskrypcją.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Czy mogę przenieść własność na konto w innym kraju?
Niestety, w witrynie Azure Portal nie można wykonywać przeniesień między krajami. Aby przenieść swoją subskrypcję między krajami [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jestem administratorem na dwóch kontach. Czy mogę przenieść subskrypcję z jednego ze swoich kont na inne?
Tak, możesz przenieść subskrypcję między kontami. Twoje konta są koncepcyjnie uznawane za konta dwóch różnych użytkowników, możesz zatem wykonać powyższe kroki w celu przeniesienia subskrypcji między kontami.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Czy przeniesienie subskrypcji skutkuje przestojem usługi?

Przeniesienie subskrypcji na konto w tej samej dzierżawie usługi Azure Active Directory nie ma wpływu na zasoby działające w ramach subskrypcji. Jeśli jednak subskrypcja zostanie przeniesiona na konto w innej dzierżawie, wszyscy użytkownicy, wszystkie grupy i wszystkie jednostki usługi z [dostępem opartym na rolach (RBAC)](../../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji utracą dostęp. Może to skutkować przestojem usługi.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Czy użytkownicy na nowym koncie mają dostęp do historii użytkowania i rozliczeń?

Jedyne informacje dostępne dla użytkowników na nowym koncie to koszt subskrypcji z ostatniego miesiąca. Pozostała część historii użycia i rozliczeń nie jest przenoszona z subskrypcją.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak mogę przeprowadzić migrację danych i usług mojej subskrypcji platformy Azure do nowej subskrypcji?

Jeśli nie możesz przenieść własności subskrypcji, możesz ręcznie migrować zasoby. Zobacz artykuł [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Czy jeśli przeniosę subskrypcję programu Visual Studio lub Microsoft Partner Network, moje środki są przenoszone wraz z subskrypcją na nowe konto?

Nie, Twoje środki nie są dostępne na nowym koncie. Użytkownik akceptujący żądanie przeniesienia musi mieć licencję programu Visual Studio, aby zaakceptować żądanie przeniesienia. Subskrypcja korzysta z kredytu programu Visual Studio, który jest dostępny na koncie użytkownika. Więcej informacji — zobacz [Przenoszenie subskrypcji tworzenia i testowania programu Visual Studio, Microsoft Partner Network (MPN) i płatności zgodnie z rzeczywistym użyciem](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Często zadawane pytania dotyczące adresatów

Te pytania dotyczą użytkowników przejmujących własność rozliczeń subskrypcji platformy Azure z innego konta.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Jeśli przejmuję własność rozliczeń subskrypcji z innego konta, czy użytkownicy tego konta nadal mają dostęp do moich zasobów?

Tak. Jeśli jednak Twoje konto znajduje się w dzierżawie usługi Azure AD innej niż dzierżawa subskrypcji, a użytkownik, który wysłał żądanie przeniesienia, przenosi subskrypcję do dzierżawy Twojego konta, wszystkie [role administratorów](add-change-subscription-administrator.md) i przypisania [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) są usuwane. Aby wyświetlić użytkowników, którzy [mają dostęp oparty na rolach (RBAC)](../../role-based-access-control/overview.md) do zarządzania zasobami w ramach subskrypcji, wykonaj następujące czynności:

1. Odwiedź [stronę subskrypcji w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz wyświetlić, a następnie w lewym okienku wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** .
1. Wybierz pozycję **Przypisania ról** w górnej części strony. Na stronie przypisań ról są wyświetleni wszyscy użytkownicy z dostępem RBAC do subskrypcji.

Nawet jeśli [przypisania kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/role-assignments-portal.md) zostaną usunięte podczas przenoszenia, użytkownicy pierwotnego konta właściciela mogą nadal mieć dostęp do subskrypcji za pośrednictwem niektórych mechanizmów zabezpieczeń, w takich jak:

* Certyfikaty zarządzania, które przyznają użytkownikowi uprawnienia administratora do zasobów subskrypcji. Więcej informacji — zobacz [Tworzenie i przekazywanie certyfikatu zarządzania dla platformy Azure](../../cloud-services/cloud-services-certs-create.md).
* Klucze dostępu dla usług, takich jak Storage. Aby uzyskać więcej informacji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-create-storage-account.md).
* Poświadczenia dostępu zdalnego dla usług, takich jak Azure Virtual Machines.

Jeśli odbiorca musi ograniczyć dostęp do swoich zasobów platformy Azure, powinien rozważyć zaktualizowanie wszystkich wpisów tajnych skojarzonych z usługą. Większość zasobów można zaktualizować, wykonując następujące czynności:

  1. Zaloguj się do [portalu Azure](https://portal.azure.com).
  2. W menu Centrum wybierz pozycję **Wszystkie zasoby**.
  3. Wybierz zasób.
  4. Na stronie zasobu kliknij opcję **Ustawienia**. Możesz tu wyświetlać i aktualizować istniejące wpisy tajne.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Jeśli przejmę własność rozliczeń subskrypcji w trakcie cyklu rozliczeniowego, czy muszę płacić za cały cykl?

Twoje konto jest odpowiedzialne za opłacenie użycia zgłoszonego od momentu przeniesienia. Pewna część użycia, która miała miejsce przed przeniesieniem, mogła zostać zgłoszona dopiero po nim. Jest ona ujęta na rachunku konta.

### <a name="can-i-use-a-different-payment-method"></a>Czy mogę użyć innej formy płatności?

Tak. Akceptując żądanie przeniesienia, możesz wybrać istniejącą formę płatności połączoną z Twoim kontem lub wybrać nową.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Jak mogę przenieść własność mojego konta subskrypcji umowy Enterprise Agreement (EA), jeśli pierwotny właściciel konta nie należy już do organizacji?

Administrator przedsiębiorstwa może zaktualizować własność dowolnego konta nawet wtedy, gdy pierwotny właściciel nie należy już do organizacji. Może to zrobić, postępując zgodnie z instrukcjami dotyczącymi [przenoszenia własności konta dla wszystkich subskrypcji](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) w portalu EA.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a id="no-button"></a> Czemu nie widzę przycisku „Przenieś subskrypcję”?

Samoobsługowe przeniesienie subskrypcji nie jest dostępne w przypadku Twojego konta rozliczeniowego. Obecnie nie obsługujemy przenoszenia własności rozliczeń subskrypcji na kontach Umowy Enterprise (EA) w witrynie Portalu Azure. Również konta Umowy klienta firmy Microsoft utworzone podczas pracy z przedstawicielem Microsoft nie obsługują przenoszenia własności rozliczeń.

### <a id="no-button"></a> Dlaczego mój typ subskrypcji nie obsługuje przenoszenia?

Nie wszystkie typy subskrypcji obsługują przenoszenie własności rozliczeń. Aby zobaczyć listę typów subskrypcji obsługujących przenoszenie, zobacz [Obsługiwane typy subskrypcji](#supported-subscription-types).

### <a id="no-button"></a> Dlaczego podczas próby przeniesienia własności rozliczeń subskrypcji widzę komunikat o błędzie odmowy dostępu?

Ten komunikat jest wyświetlany, jeśli użytkownik próbuje przenieść subskrypcję planu platformy Microsoft Azure bez wymaganych uprawnień. Aby przenieść subskrypcję planu platformy Microsoft Azure, musisz być właścicielem lub współautorem w sekcji faktury dotyczącej subskrypcji. Więcej informacji — zobacz [Sekcja Zarządzanie subskrypcjami dla celów rozliczeniowych](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i zaktualizuj role administrator usługi, współadministratorzy i inne role RBAC. Więcej informacji — zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md) i [Zarządzanie dostępem przy użyciu RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).
