---
title: Aplikacje zarządzane platformy Azure w witrynie Marketplace | Microsoft Docs
description: W tym artykule opisano aplikacje zarządzane platformy Azure dostępne w witrynie Marketplace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 07/10/2018
ms.author: tomfitz
ms.openlocfilehash: 0bf1558f81f2936b4556ee658a235cd09dddf905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484018"
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Aplikacje zarządzane platformy Azure w witrynie Marketplace

Dostawcy mogą używać aplikacji zarządzanych platformy Azure w celu oferowania swoich rozwiązań wszystkim klientom witryny Azure Marketplace. Tymi dostawcami mogą być dostawcy usług zarządzanych (MSP, managed service provider), niezależni dostawcy oprogramowania (ISV, independent software vendor) oraz integratorzy systemów (SI, system integrator). Dzięki aplikacjom zarządzanym klienci mogą zmniejszyć narzut związany z konserwacją i serwisowaniem. Dostawcy sprzedają infrastrukturę i oprogramowanie za pośrednictwem witryny Marketplace. Do aplikacji zarządzanych mogą dołączyć usługi i operacyjną pomoc techniczną. Aby uzyskać więcej informacji, zobacz [Omówienie aplikacji zarządzanych](overview.md).

W tym artykule wyjaśniono, jak opublikować aplikację w witrynie Marketplace, aby była szeroko dostępna dla klientów.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Wymagania wstępne dotyczące publikowania aplikacji zarządzanej

Aby wykonać czynności opisane w tym artykule, trzeba mieć przygotowany plik zip definicji aplikacji zarządzanej. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji w katalogu usług](publish-service-catalog-app.md).

Ponadto istnieje kilka biznesowych wymagań wstępnych. Oto one:

* Siedziba firmy (lub firmy od niej zależnej) musi znajdować się w kraju, w którym jest obsługiwana sprzedaż w witrynie Marketplace.
* Produkt musi być licencjonowany w sposób, który jest zgodny z modelami rozliczeń obsługiwanymi przez witrynę Marketplace.
* Klienci muszą mieć dostęp do pomocy technicznej zorganizowanej w sposób rozsądny z komercyjnego punktu widzenia. Może być ona bezpłatna, płatna lub może działać na zasadzie wsparcia społeczności.
* Oprogramowanie dostawcy i wszelkie oprogramowanie podmiotów zależnych musi być licencjonowane.
* Należy dostarczyć zawartość spełniającą kryteria dla danej oferty, która zostanie umieszczona w witrynie Marketplace i w witrynie Azure Portal.
* Należy zaakceptować warunki opisane w dokumentach Zasady udziału w witrynie Azure Marketplace oraz Umowa wydawcy.
* Należy wyrazić zgodę na przestrzeganie Warunków użytkowania, Zasad zachowania poufności informacji firmy Microsoft i Umowy programu certyfikatu platformy Microsoft Azure.

## <a name="become-a-publisher"></a>Jak zostać wydawcą

Aby zostać wydawcą w witrynie Azure Marketplace, należy wykonać następujące czynności:

1. Utwórz identyfikator Microsoft — Utwórz konto Microsoft przy użyciu adresu e-mail, który należy do domeny firmy, ale nie do jednej osoby. Ten adres e-mail będzie używany zarówno w Centrum deweloperów firmy Microsoft, jak i w portalu Cloud Partner. Aby uzyskać więcej informacji, zobacz [Azure Marketplace — przewodnik dla wydawcy](https://aka.ms/sellerguide).
1. Prześlij [Formularz nominacji witryny Azure Marketplace](https://aka.ms/ampnomination) — W polu **Rozwiązanie, które chcesz opublikować** wybierz pozycję **Aplikacja zarządzana**. Po przesłaniu formularza zespół ds. dołączania do witryny Marketplace zapozna się z aplikacją i zweryfikuje Twoje żądanie. Proces zatwierdzania może potrwać od jednego do trzech dni. Po zatwierdzeniu nominacji otrzymasz kod promocyjny umożliwiający anulowanie opłaty rejestracyjnej w Centrum deweloperów. Jeśli **nie** wypełnisz formularza nominacji witryny Marketplace, zostanie Ci naliczona opłata rejestracyjna w wysokości 99 USD.
1. Zarejestruj się w [Centrum deweloperów](https://dev.windows.com/registration?accountprogram=azure) — firma Microsoft sprawdzi, czy Twoja organizacja jest podmiotem prawnym i posiada identyfikator podatkowy w kraju, w którym jest zarejestrowana. Proces zatwierdzania może potrwać od 5 do 10 dni. Aby uniknąć opłaty rejestracyjnej, użyj kodu promocyjnego otrzymanego w wiadomości e-mail dotyczącej procesu nominacji. Aby uzyskać więcej informacji, zobacz [Azure Marketplace — przewodnik dla wydawcy](https://aka.ms/sellerguide).
1. Zaloguj się w [portalu Cloud Partner](https://cloudpartner.azure.com) — w profilu wydawcy skojarz swoje konto Centrum deweloperów z profilem wydawcy witryny Marketplace. Aby uzyskać więcej informacji, zobacz [Azure Marketplace — przewodnik dla wydawcy](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Tworzenie nowej oferty aplikacji platformy Azure

Po utworzeniu konta w portalu dla partnerów możesz utworzyć ofertę aplikacji zarządzanej.

### <a name="set-up-an-offer"></a>Konfigurowanie oferty

Oferta aplikacji zarządzanej odpowiada klasie produktu oferowanego przez wydawcę. Jeśli w witrynie Marketplace chcesz udostępnić nowy typ aplikacji, możesz skonfigurować tę ofertę jako nową. Oferta jest kolekcją jednostek SKU. Każda oferta jest wyświetlana w witrynie Marketplace jako osobna jednostka.

1. Zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com/).

1. W okienku nawigacji po lewej stronie wybierz pozycję **+ Nowa oferta** > **Aplikacje platformy Azure**.

1. W widoku **Edytor** zobaczysz wymagane formularze. Każdy formularz został opisany w dalszej części tego artykułu.

## <a name="offer-settings-form"></a>Formularz ustawień oferty

Pola w formularzu **Ustawienia oferty** są następujące:

* **Identyfikator oferty**: Ten unikatowy identyfikator określa ofertę w obrębie profilu wydawcy. Jest on widoczny w adresach URL produktu, szablonach usługi Resource Manager i raportach rozliczeń. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się łącznikiem. Może zawierać maksymalnie 50 znaków. Po aktywowaniu oferty to pole jest zablokowane.
* **Identyfikator wydawcy**: Przy użyciu tej listy rozwijanej wybierz profil wydawcy, w ramach którego chcesz opublikować daną ofertę. Po aktywowaniu oferty to pole jest zablokowane.
* **Nazwa**: Ta nazwa wyświetlana oferty jest widoczna w witrynie Marketplace oraz w portalu. Może zawierać maksymalnie 50 znaków. Warto umieścić tu rozpoznawalną nazwę marki produktu. Nie umieszczaj tu nazwy swojej firmy, chyba że produkt jest pod taką nazwą oferowany. Jeśli ta sama oferta znajduje się także w Twojej witrynie internetowej, upewnij się, że nazwa jest identyczna z nazwą wyświetlaną w Twojej witrynie.

Po zakończeniu wybierz pozycję **Zapisz**, aby zapisać postęp.

## <a name="skus-form"></a>Formularz jednostek SKU

Następnym krokiem jest dodanie jednostek SKU dla oferty.

Jednostka SKU to najmniejsza możliwa do kupienia jednostka oferty. Przy użyciu jednostki SKU w obrębie tej samej klasy produktu (oferty) można rozróżnić:

* różne obsługiwane funkcje;
* czy oferta jest zarządzana, czy niezarządzana;
* obsługiwane modele rozliczeń.

Jednostka SKU jest widoczna poniżej oferty nadrzędnej w witrynie Marketplace. Jest ona wyświetlana jako oddzielny, możliwy do kupienia element w witrynie Azure Portal.

1. Wybierz pozycję **Jednostki SKU** > **Nowa jednostka SKU**.

1. Wprowadź wartość w polu **Identyfikator jednostki SKU**. Identyfikator jednostki SKU to unikatowy identyfikator dla danej jednostki SKU w obrębie oferty. Jest on widoczny w adresach URL produktu, szablonach usługi Resource Manager i raportach rozliczeń. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się łącznikiem i może zawierać maksymalnie 50 znaków. Po aktywowaniu oferty to pole jest zablokowane. W obrębie oferty możesz mieć wiele jednostek SKU. Każdy obraz, który planujesz opublikować, musi mieć własną jednostkę SKU.

1. Wypełnij sekcję **Szczegóły jednostki SKU** w następującym formularzu:

   Wypełnij następujące pola:

   * **Tytuł**: Wprowadź tytuł dla tej jednostki SKU. Ten tytuł będzie widoczny w galerii dla tego elementu.
   * **Podsumowanie**: Wprowadź krótkie podsumowanie dla tej jednostki SKU. Ten tekst jest wyświetlany pod tytułem.
   * **Opis**: Wprowadź szczegółowy opis jednostki SKU.
   * **Typ jednostki SKU**: Dozwolone wartości to *Aplikacja zarządzana* i *Szablony rozwiązań*. W tym przypadku wybierz pozycję *Aplikacja zarządzana*.
   * **Dostępność w kraju/regionie**: Wybierz kraje, w których dana aplikacja zarządzana jest dostępna.
   * **Cennik**: Podaj cenę za zarządzanie aplikacją. Przed ustawieniem ceny wybierz dostępne kraje.

1. Dodaj nowy pakiet. Wypełnij sekcję **Szczegóły pakietu** w następującym formularzu:

   Wypełnij następujące pola:

   * **Wersja**: Wprowadź wersję przekazywanego pakietu. Powinna ona mieć format `{number}.{number}.{number}{number}`.
   * **Plik pakietu (zip)**: Ten pakiet zawiera dwa wymagane pliki skompresowane do pakietu zip. Jeden plik to szablon usługi Resource Manager określający zasoby, które trzeba wdrożyć dla danej aplikacji zarządzanej. Drugi plik definiuje [interfejs użytkownika](create-uidefinition-overview.md) dla klientów wdrażających tę aplikację zarządzaną za pośrednictwem portalu. W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podanie wartości parametrów.
   * **PrincipalId**: Ta właściwość jest identyfikatorem usługi Azure Active Directory (Azure AD) użytkownika, grupy użytkowników lub aplikacji, której udzielono uprawnień dostępu do zasobów w subskrypcji klienta. Uprawnienia są opisywane za pomocą definicji roli.
   * **Definicja roli**: Ta właściwość jest listą wszystkich wbudowanych kontroli dostępu na podstawie ról (RBAC, Role-Based Access Control) zapewnianych przez usługę Azure AD. Możesz wybrać rolę, która najbardziej nadaje się do zarządzania zasobami w imieniu klienta.
   * **Ustawienia zasad**: Zastosuj [zasady Azure Policy](../governance/policy/overview.md) do aplikacji zarządzanej, aby określić wymagania dotyczące zgodności dla wdrożonych rozwiązań. Z dostępnych opcji wybierz zasady do zastosowania. W polu **Parametry zasad** podaj ciąg JSON z wartościami parametrów. Definicje zasad i format wartości parametrów podano w artykule [Przykłady dla usługi Azure Policy](../governance/policy/samples/index.md).

Dodać można kilka autoryzacji. Zalecamy utworzenie grupy użytkowników usługi AD i określenie jej identyfikatora w polu **PrincipalId**. Dzięki temu będzie można dodawać kolejnych użytkowników do grupy użytkowników bez konieczności aktualizowania jednostki SKU.

Aby uzyskać więcej informacji o kontroli dostępu na podstawie ról, zobacz [Wprowadzenie do kontroli dostępu na podstawie ról w witrynie Azure Portal](../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formularz witryny Marketplace

Formularz witryny Marketplace zawiera pola, które są widoczne w witrynach [Azure Marketplace](https://azuremarketplace.microsoft.com) i [Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Identyfikatory subskrypcji wersji zapoznawczej

Wprowadź listę identyfikatorów subskrypcji platformy Azure, które mogą uzyskać dostęp do oferty po jej opublikowaniu. Przy użyciu tych dozwolonych subskrypcji można przetestować ofertę w wersji zapoznawczej zanim zostanie aktywowana. Biała lista może składać się z maksymalnie 100 subskrypcji w portalu dla partnerów.

### <a name="suggested-categories"></a>Sugerowane kategorie

Wybierz z listy maksymalnie pięć kategorii, z którymi można powiązać Twoją ofertę. Te kategorie są używane w celu mapowania oferty na kategorie produktów, które są dostępne w witrynach [Azure Marketplace](https://azuremarketplace.microsoft.com) i [Azure Portal](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

W podsumowaniu aplikacji zarządzanej są wyświetlane następujące pola:

![Podsumowanie w witrynie Marketplace](./media/publish-marketplace-app/publishvm10.png)

Na karcie **Omówienie** aplikacji zarządzanej są wyświetlane następujące pola:

![Omówienie portalu Marketplace](./media/publish-marketplace-app/publishvm11.png)

Na karcie **Plany i cennik** aplikacji zarządzanej są wyświetlane następujące pola:

![Plany w witrynie Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

W podsumowaniu aplikacji zarządzanej są wyświetlane następujące pola:

![Podsumowanie w witrynie Portal](./media/publish-marketplace-app/publishvm12.png)

W omówieniu aplikacji zarządzanej są wyświetlane następujące pola:

![Omówienie w witrynie Portal](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

Skorzystaj z poniższych wskazówek w przypadku każdego logo, które przekazujesz do portalu Cloud Partner:

*   Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
*   Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe. *Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.*
*   Nie używaj w logo tła gradientowego.
*   Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki. Logo powinno być proste i bez gradientów.
*   Upewnij się, że logo nie jest rozciągnięte.

#### <a name="hero-logo"></a>Logo Duży obraz

Logo Duży obraz jest opcjonalne. Wydawca może zrezygnować z przekazywania logo Duży obraz. Gdy ikona Duży obraz zostanie przekazana, nie będzie można jej usunąć. Partner musi postępować zgodnie z wytycznymi witryny Marketplace dotyczącymi ikon Duży obraz.

Skorzystaj z poniższych wskazówek dla ikony logo Duży obraz:

*   Nazwa wyświetlana wydawcy, tytuł planu i długie podsumowanie oferty są wyświetlane w kolorze białym. Z tego względu nie należy używać jasnego koloru tła ikony Duży obraz. Czarne, białe lub przezroczyste tło nie jest dozwolone w przypadku ikon Duży obraz.
*   Po umieszczeniu oferty na liście elementy wewnątrz logo Duży obraz są osadzane programowo. Osadzone elementy obejmują nazwę wyświetlaną wydawcy, tytuł planu, długie podsumowanie oferty i przycisk **Utwórz**. Z tego powodu podczas projektowania logo Duży obraz nie należy wprowadzać żadnego tekstu. Pozostaw puste miejsce po prawej stronie, ponieważ tekst jest dołączany programowo w tym miejscu. Puste miejsce na tekst powinno mieć rozmiar 415 x 100 pikseli i znajdować się po prawej stronie. Jest ono przesunięte o 370 pikseli od lewej strony.

    ![Przykład logo Duży obraz](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formularz pomocy technicznej

Formularz **Pomoc techniczna** wypełnij za pomocą kontaktów pomocy technicznej Twojej firmy. Te informacje mogą obejmować kontakt z inżynierami lub kontakt z obsługą klienta.

## <a name="publish-an-offer"></a>Publikowanie oferty

Po wypełnieniu wszystkich sekcji wybierz pozycję **Opublikuj**, aby rozpocząć proces udostępniania Twojej oferty klientom.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Aby dowiedzieć się, jak opublikować aplikację zarządzaną w katalogu usług, zobacz [Tworzenie i publikowanie aplikacji zarządzanej katalogu usług](publish-service-catalog-app.md).