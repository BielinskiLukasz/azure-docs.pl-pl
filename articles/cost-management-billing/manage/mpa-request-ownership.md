---
title: Uzyskiwanie własności rozliczeń subskrypcji platformy Azure dla umowy partnerskiej firmy Microsoft
description: Dowiedz się, jak uzyskiwać własność rozliczeń subskrypcji platformy Azure od innych użytkowników.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: c4366b272aab160828afca927f6432c98a8fecd7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370733"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Uzyskiwanie własności rozliczeń subskrypcji platformy Azure dla konta umowy partnerskiej firmy Microsoft

Aby zapewnić pojedynczą skonsolidowaną fakturę dla usług zarządzanych i użycia platformy Azure, dostawca rozwiązań w chmurze może przejąć własność rozliczeń subskrypcji platformy Azure od klientów z bezpośrednią umową Enterprise Agreement (EA).

Ta funkcja jest dostępna tylko dla partnerów rozliczających się bezpośrednio, którzy mają certyfikat [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Podlega to ładowi i zasadom firmy Microsoft i może wymagać weryfikacji oraz zatwierdzenia w przypadku niektórych klientów.

Aby zawnioskować o własność rozliczeń, użytkownik musi mieć rolę **administratora globalnego** lub **agenta administratora**. Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — Przypisywanie ról i uprawnień użytkowników](https://docs.microsoft.com/partner-center/permissions-overview).

Ten artykuł dotyczy kont rozliczeniowych dla umów partnerskich firmy Microsoft Te konta są tworzone dla dostawców rozwiązań w chmurze w celu zarządzania rozliczeniami dla klientów w nowym środowisku handlowym. To nowe środowisko jest dostępne tylko dla partnerów mających co najmniej jednego klienta, który zaakceptował umowę klienta firmy Microsoft i ma plan platformy Azure. [Sprawdź, czy masz dostęp do umowy partnerskiej firmy Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Wymagania wstępne

1. Ustanów [relację odsprzedawcy](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) z klientem. Sprawdź [Omówienie uwierzytelniania regionalnego dostawców usług w chmurze](https://docs.microsoft.com/partner-center/regional-authorization-overview), aby upewnić się, że klient i dzierżawca partnera znajdują się w tym samym regionie autoryzowanym.  

2. [Potwierdź, że klient zaakceptował umowę klienta firmy Microsoft](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Skonfiguruj [plan platformy Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) dla klienta. Jeśli klient dokonuje zakupu za pośrednictwem wielu odsprzedawców, należy skonfigurować plan platformy Azure dla każdej kombinacji klienta i odsprzedawcy.

## <a name="request-billing-ownership"></a>Żądanie własności rozliczeń

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu poświadczeń agenta administratora dostawcy usług w chmurze.

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/mpa-request-ownership/search-cmb.png)

3. Wybierz pozycję **Klienci** po lewej stronie, a następnie wybierz klienta z listy.

   ![Zrzut ekranu pokazujący wybieranie klientów](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Wybierz pozycję **Żądania przeniesienia** w lewym dolnym rogu, a następnie wybierz pozycję **Dodaj nowe żądanie**.

   ![Zrzut ekranu przedstawiający wybieranie żądań przeniesienia](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Wprowadź adres e-mail użytkownika w organizacji klienta, który będzie akceptował żądanie przeniesienia. Użytkownik musi być właścicielem konta umowy Enterprise Agreement. Wybierz pozycję **Wyślij żądanie przeniesienia**.

   ![Zrzut ekranu przedstawiający wysyłanie żądania przeniesienia](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. Użytkownik otrzymuje wiadomość e-mail z instrukcjami dotyczącymi przeglądania żądania transferu.

   ![Zrzut ekranu przedstawiający wiadomość e-mail z prośbą o przejrzenie żądania przeniesienia](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Aby zatwierdzić żądanie przeniesienia, użytkownik wybiera link w wiadomości e-mail i postępuje zgodnie z instrukcjami.

    ![Zrzut ekranu przedstawiający wiadomość e-mail z prośbą o przejrzenie żądania przeniesienia](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Sprawdzanie stanu żądania przeniesienia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.

   ![Zrzut ekranu przedstawiający wyszukiwanie zarządzania kosztami i rozliczeń w witrynie Azure Portal](./media/mpa-request-ownership/billing-search-cost-management-billing.png)

3. Wybierz pozycję **Klienci** po lewej stronie.

   ![Zrzut ekranu pokazujący wybieranie klientów](./media/mpa-request-ownership/mpa-select-customers.png)

4. Wybierz z listy klienta, dla którego wysłano żądanie przeniesienia.

5. Wybierz pozycję **Żądania przeniesienia** w lewym dolnym rogu. Strona Żądania przeniesienia zawiera następujące informacje:

    ![Zrzut ekranu przedstawiający listę żądań przeniesienia](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Kolumna|Definicja|
   |---------|---------|
   |Data żądania|Data wysłania żądania przeniesienia|
   |Adresat|Adres e-mail użytkownika, do którego wysłano żądanie przeniesienia własności rozliczeń|
   |Data wygaśnięcia|Data wygaśnięcia żądania|
   |Stan|Stan żądania przeniesienia|

    Żądanie przeniesienia może mieć jeden z następujących stanów:

   |Stan|Definicja|
   |---------|---------|
   |W toku|Użytkownik nie zaakceptował żądania przeniesienia|
   |Przetwarzanie|Użytkownik zatwierdził żądanie przeniesienia. Rozliczanie subskrypcji wybranych przez użytkownika jest przenoszone na konto|
   |Zakończone| Rozliczanie subskrypcji wybranych przez użytkownika jest przenoszone na konto|
   |Zakończone z błędami|Żądanie zostało zakończone, ale nie można było przenieść rozliczeń dla niektórych subskrypcji wybranych przez użytkownika|
   |Wygasłe|Użytkownik nie zaakceptował żądania na czas i żądanie wygasło|
   |Anulowane|Ktoś z dostępem do żądania przeniesienia anulował żądanie|
   |Odrzucone|Użytkownik odrzucił żądanie przeniesienia|

6. Wybierz żądanie przeniesienia, aby wyświetlić szczegóły. Strona szczegółów przeniesienia zawiera następujące informacje: ![Zrzut ekranu przedstawiający listę przeniesionych subskrypcji](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Kolumna  |Definicja|
   |---------|---------|
   |Identyfikator żądania przeniesienia|Unikatowy identyfikator żądania przeniesienia. Jeśli przesyłasz wniosek o pomoc techniczną, przekaż ten identyfikator do działu pomocy technicznej platformy Azure|
   |Przeniesienia zażądano w dniu|Data wysłania żądania przeniesienia|
   |Żądanie przeniesienia zgłoszone przez|Adres e-mail użytkownika, który wysłał żądanie przeniesienia|
   |Żądanie przeniesienia wygasa| Data wygaśnięcia żądania przeniesienia|
   |Adres e-mail adresata|Adres e-mail użytkownika, do którego wysłano żądanie przeniesienia własności rozliczeń|
   |Link do przenoszenia został wysłany do adresata|Adres URL, który został wysłany do użytkownika w celu przejrzenia żądania przeniesienia|

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Możesz zażądać własności rozliczeń wymienionych poniżej typów subskrypcji.

- [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* Subskrypcję Tworzenie i testowanie należy najpierw przekonwertować na ofertę dla przedsiębiorstw z umową EA za pośrednictwem biletu pomocy technicznej. Po przeniesieniu subskrypcja Enterprise — tworzenie i testowanie będzie rozliczania przy użyciu stawek płatności zgodnie z rzeczywistym użyciem. Żaden rabat oferowany za pośrednictwem oferty Enterprise — tworzenie i testowanie w ramach umowy EA klienta nie będzie dostępny dla partnera CSP.

## <a name="additional-information"></a>Dodatkowe informacje

W poniższej sekcji przedstawiono dodatkowe informacje na temat przenoszenia subskrypcji.

### <a name="no-service-downtime"></a>Bez przestoju usługi

Usługi platformy Azure w ramach subskrypcji będą działały bez jakiejkolwiek przerwy. W przypadku subskrypcji platformy Azure, które użytkownik wybierze do przeniesienia, przeniesiemy tylko relację rozliczeń.

### <a name="disabled-subscriptions"></a>Wyłączone subskrypcje

Wyłączonych subskrypcji nie można przenosić. Aby przeniesienie własności rozliczeń było możliwe, subskrypcje muszą być w stanie aktywnym.

### <a name="azure-resources-transfer"></a>Przenoszenie zasobów platformy Azure

Wszystkie zasoby z subskrypcji, takie jak maszyny wirtualne, dyski i witryny internetowe, są przenoszone.

### <a name="azure-marketplace-products-transfer"></a>Przenoszenie produktów z witryny Azure Marketplace

Produkty z witryny Azure Marketplace, które są dostępne dla subskrypcji zarządzanych przez dostawców rozwiązań w chmurze, są przenoszone wraz z ich odpowiednimi subskrypcjami. Nie można przenieść subskrypcji mających produkty z witryny Azure Marketplace, które nie są włączone dla dostawców usług w chmurze.

### <a name="azure-reservations-transfer"></a>Przenoszenie rezerwacji platformy Azure

Rezerwacje platformy Azure nie są automatycznie przenoszone wraz z subskrypcjami. Możesz zachować rezerwację w ramach umowy EA dla innych subskrypcji lub [anulować rezerwację](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) i umożliwić partnerowi ponowny zakup w programie CSP. 

### <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Przejście nie wpływa na dostęp do istniejących użytkowników, grup lub jednostek usługi, które zostały przypisane przy użyciu usługi [Azure RBAC (kontroli dostępu opartej na rolach)](../../role-based-access-control/overview.md). Partner nie uzyska nowego dostępu RBAC do subskrypcji.  

Partnerzy powinni skontaktować się z klientem w celu uzyskania dostępu do subskrypcji.  Partnerzy muszą uzyskać [Dostęp administratora w imieniu — AOBO (Admin on Behalf Of)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) lub [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider), aby otwierać bilety pomocy technicznej.

### <a name="azure-support-plan"></a>Plan pomocy technicznej platformy Azure

Pomoc techniczna platformy Azure nie jest przenoszona razem z subskrypcjami. Jeśli użytkownik przeniesie wszystkie subskrypcje platformy Azure, poproś go o anulowanie planu pomocy technicznej. Po przeniesieniu partner będący dostawcą usług w chmurze jest odpowiedzialny za pomoc techniczną. W sprawie wniosków o pomoc techniczną klient powinien kontaktować się z partnerem będącym dostawcą usług w chmurze.  

### <a name="charges-for-transferred-subscription"></a>Opłaty za przeniesioną subskrypcję

Oryginalny właściciel rozliczeń subskrypcji jest odpowiedzialny za wszelkie opłaty, które zostały zgłoszone do momentu ukończenia przenoszenia. Użytkownik jest odpowiedzialny za opłaty zgłoszone od momentu przeniesienia. Pewne opłaty, które miały miejsce przed przeniesieniem, mogły zostać zgłoszona dopiero po nim. Te opłaty są widoczne na fakturze.

### <a name="cancel-a-transfer-request"></a>Anulowanie żądania przeniesienia

Żądanie przeniesienia można anulować, dopóki nie zostanie zatwierdzone lub odrzucone. Aby anulować żądanie przeniesienia, przejdź do [strony szczegółów przeniesienia](#check-the-transfer-request-status) i wybierz pozycję Anuluj w dolnej części strony.

### <a name="software-as-a-service-saas-transfer"></a>Przenoszenie oprogramowania jako usługi (SaaS)

Produkty SaaS nie są przenoszone razem z subskrypcjami. Poproś użytkownika o [skontaktowanie się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) w celu przeniesienia własności rozliczeń produktów SaaS. Oprócz własności rozliczeń użytkownik może również przenieść własność zasobów. Własność zasobów umożliwia wykonywanie operacji zarządzania, takich jak usuwanie i wyświetlanie szczegółów produktu. Użytkownik musi być właścicielem zasobu w ramach produktu SaaS, aby przenieść własność zasobów.

### <a name="additional-approval-for-certain-customers"></a>Dodatkowe zatwierdzenie dla niektórych klientów

Niektóre żądania przeniesienia klientów mogą wymagać dodatkowego procesu weryfikacji przez firmę Microsoft ze względu na charakter obecnej struktury rejestracji przedsiębiorstwa klienta. Partner zostanie powiadomiony o tych wymaganiach podczas próby wysłania zaproszenia do klientów. Partnerzy są proszeni o współpracę z zespołem ds. rozwoju partnerów i kierownikiem konta klienta w celu ukończenia tego procesu weryfikacji.

### <a name="azure-subscription-directory"></a>Katalog subskrypcji platformy Azure
Katalog subskrypcji platformy Azure, które są przenoszone, musi być zgodny z katalogiem klienta wybranego podczas ustanawiania relacji z dostawcą usług w chmurze.

Jeśli te dwa katalogi nie są zgodne, przeniesienie subskrypcji jest niemożliwe. Należy ustanowić nową relację z odsprzedawcą dostawcy usług w chmurze, wybierając katalog subskrypcji platformy Azure, lub zmienić katalog subskrypcji platformy Azure, aby był zgodny z katalogiem relacji klienta z dostawcą usług w chmurze. Aby uzyskać więcej informacji, zobacz [Kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Sprawdzanie dostępu do umowy partnerskiej firmy Microsoft
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

## <a name="next-steps"></a>Następne kroki

- Własność rozliczeń subskrypcji platformy Azure zostaje przeniesiona na użytkownika. Śledź opłaty za te subskrypcje w witrynie [Azure Portal](https://portal.azure.com).

- Skontaktuj się z klientem, aby uzyskać dostęp do przeniesionych subskrypcji platformy Azure. [Zarządzaj dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
