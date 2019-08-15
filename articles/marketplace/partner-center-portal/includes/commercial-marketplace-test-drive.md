---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 32bc5f76e0be19ae5adb24f10094494d94eeb4d6
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019579"
---
Na karcie **dysk testowy** można skonfigurować pokaz (lub "dysk testowy"), co umożliwi klientom wypróbowanie oferty przed zatwierdzeniem jej zakupu. Dowiedz się więcej w artykule [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Jeśli nie chcesz już podawać dysku testowego dla swojej oferty, Wróć do strony **Konfiguracja oferty** i usuń zaznaczenie opcji **Włącz dysk testowy**.

### <a name="technical-configuration"></a>Konfiguracja techniczna
Dostępne są następujące typy dysków testowych, z których każda ma własne wymagania dotyczące konfiguracji technicznej.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logiki](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Konfiguracja techniczna nie jest wymagana)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Konfiguracja techniczna dla Azure Resource Managergo dysku testowego

Szablon wdrożenia zawierający wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [Azure Resource Manager dysku testowego](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiony** (wymagane): Obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zwykle warto udostępnić dysk testowy w regionach, w których przewidujesz największą liczbę klientów, dzięki czemu można wybrać najbliższy region dla najlepszej wydajności. Musisz się upewnić, że Twoja subskrypcja może wdrażać wszystkie wymagane przez siebie regiony.

- **Wystąpienia**: Wybierz typ (gorąca lub zimna) oraz liczbę dostępnych wystąpień, które zostaną pomnożone przez liczbę regionów, w których oferta jest dostępna.

**Gorąca**: Ten typ wystąpienia jest wdrożony i oczekuje na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do gorącego wystąpienia dysku testowego, a nie muszą czekać na wdrożenie. Jego wadą jest to, że te wystąpienia są zawsze uruchamiane na Twojej subskrypcji platformy Azure, spowoduje naliczenie opłaty za większych przestojów, koszt. Zdecydowanie zaleca się, aby miało co najmniej jedno *aktywne* wystąpienie, ponieważ większość klientów nie chce czekać na pełne wdrożenia, co powoduje odrzucanie w przypadku użycia klienta w przypadku braku dostępnego wystąpienia.

**Zimne**: Ten typ wystąpienia reprezentuje łączną liczbę wystąpień, które mogą być wdrożone w poszczególnych regionach. Zimne wystąpienia wymagają, aby cały dysk testowy Menedżer zasobów szablon do wdrożenia, gdy klient zażąda dysku testowego, więc *zimne* wystąpienia są znacznie wolniejsze, aby można było ładować je od *aktywnych* wystąpień. Wadą jest to, że musisz tylko uregulować czas trwania testu, ale *nie* zawsze działa w ramach subskrypcji platformy Azure, tak jak w przypadku wystąpienia *aktywnego* .

- **Szablon Azure Resource Manager na dysku testowym**: Przekaż plik zip zawierający szablon Azure Resource Manager.  Dowiedz się więcej o tworzeniu szablonu Azure Resource Manager w artykule Szybki Start [Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Wersji testowej kończy się automatycznie po zakończeniu tego okresu. Ten czas trwania może być ustawiony tylko przez całą liczbę godzin (np. "2" godz. "1,5" jest nieprawidłowy).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla systemu Dynamics 365 Test Drive

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego, udostępniając i utrzymując obsługę administracyjną i wdrożenie usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców w biznesie, w ramach zaangażowania z klientami, czy z działu operacji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane): Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Każdy użytkownik współbieżny będzie korzystał z licencji Dynamics 365, gdy test jest aktywny, więc należy upewnić się, że masz wystarczającą liczbę dostępnych licencji do obsługi maksymalnego ustawionego limitu. Zalecana wartość 3-5.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego, określając liczbę godzin. Po tylu godzinach sesja zostanie zakończona i nie będzie już korzystać z jednej z Twoich licencji. Zalecamy użycie wartości 2-24 godzin w zależności od złożoności oferty. Ten czas trwania może być ustawiony tylko przez całą liczbę godzin (np. "2" godz. "1,5" jest nieprawidłowy).  Użytkownik może zażądać nowej sesji, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagane): Adres URL, pod którym klient rozpocznie swój dysk testowy. Zwykle jest to adres URL wystąpienia Dynamics 365, na którym działa Twoja aplikacja z zainstalowanymi przykładowymi danymi (np https://testdrive.crm.dynamics.com).

- **Adres URL internetowego interfejsu API wystąpienia** (wymagane): Pobierz adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365, logując się do konta Microsoft 365 i przechodząc do **ustawień** \&gt; **Dostosowanie** \&gt; **Zasoby dla deweloperów** \&gt; **Interfejs API sieci Web wystąpienia (główny adres URL usługi)** , skopiuj adres URL znaleziony tutaj https://testdrive.crm.dynamics.com/api/data/v9.0) (np.

- **Nazwa roli** (wymagane): Podaj nazwę roli zabezpieczeń zdefiniowaną w niestandardowym dysku testowym Dynamics 365. Ta wartość zostanie przypisana do użytkownika w trakcie jego dysku testowego (np. roli "Test-Drive-role").

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla dysku testowego aplikacji logiki

Wszystkie produkty niestandardowe powinny korzystać z tego typu szablonu wdrożenia dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź stronę [operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowanie klientów](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w serwisie GitHub.

- **Region** (wymagana, lista rozwijana o pojedynczym zaznaczeniu): Obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma jakieś zasoby niestandardowe przechowywane w określonym regionie, upewnij się, że w tym miejscu wybrano region. Najlepszym sposobem jest w pełni wdrażanie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa ona prawidłowo przed wybraniem tej opcji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane): Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Te stacje testowe są już wdrożone, umożliwiając klientom natychmiastowe uzyskiwanie dostępu do nich bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Po upływie tego czasu test kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagane): Wprowadź nazwę [grupy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) , w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagane): Wprowadź nazwę aplikacji logiki, która przypisuje dysk testowy do użytkownika. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

- Anulowanie aprowizacji **nazwy aplikacji logiki** (wymagane): Wprowadź nazwę aplikacji logiki, która powoduje odłożenie dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna dla Power BI dysków testowych nie jest wymagana

Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, mogą użyć osadzonego linku, aby udostępnić pulpit nawigacyjny, który jest wbudowanym niestandardowym jako dysk testowy, nie jest wymagana żadna dodatkowa konfiguracja techniczna. Dowiedz się więcej o konfigurowaniu aplikacji[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) Template.

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć dysk testowy w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure. (Niewymagane w przypadku Power BI dysków testowych).

- **Identyfikator subskrypcji platformy Azure** (wymagane dla Azure Resource Manager i aplikacji logiki): Wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (np. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagane): Wprowadź [Identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **Właściwości** , a następnie wyszukaj numer **identyfikatora katalogu** na liście (np. 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.

- **Nazwa dzierżawy usługi Azure AD** (wymagane w przypadku dynamicznego 365): Wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagane): Wprowadź [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Wpis tajny klienta aplikacji usługi Azure AD** (wymagane): Wprowadź [wpis tajny klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i**wpisy tajne, wybierz pozycję **Nowy wpis tajny klienta**, wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**. Należy pamiętać o skopiowaniu wartości. (Nie opuszczaj strony przed wykonaniem tej czynności lub nie będziesz mieć dostępu do wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

### <a name="test-drive-listings-optional"></a>Aukcje na dysku testowym (opcjonalnie)

Opcja **aukcje na dysku testowym** znajduje się na karcie **dysk testowy** zawiera języki (i rynki), w których jest dostępny dysk testowy. obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Należy zdefiniować szczegóły stacji testowej (opis, Podręcznik użytkownika, wideo itp.) dla każdego języka/rynku.

- **Opis** (wymagane): Opisz swoją wersję testową, która zostanie wykazana, jakie są cele dla użytkownika w celu poeksperymentowania z programem, funkcje do eksplorowania i wszelkie istotne informacje pomocne w ustaleniu, czy chcesz uzyskać ofertę. W tym polu można wprowadzić do 3 000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych): Wyjaśnij, co musi znać klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.

- **Ręczne użytkownika** (wymagane): Szczegółowy przewodnik dotyczący środowiska testowego. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.

- **Wideo Dodaj filmy** wideo (opcjonalnie): Filmy wideo można przekazać do usługi YouTube lub Vimeo i przywoływane w tym miejscu za pomocą linku i miniatury (533 x 324 pikseli), aby klient mógł wyświetlić szczegółowe informacje ułatwiające lepsze zrozumienie dysku testowego, w tym sposób pomyślnego użycia funkcji Zaproponuj i zrozumieć scenariusze, które wyróżnią swoje korzyści.
  - **Nazwa** potrzeb
  - **Adres URL (tylko w serwisie YouTube lub Vimeo)** potrzeb
  - **Miniatura (533 x 324px)** : Plik obrazu musi być w formacie PNG.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .
