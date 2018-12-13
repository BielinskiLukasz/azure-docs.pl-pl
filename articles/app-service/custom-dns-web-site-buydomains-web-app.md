---
title: Kupowanie niestandardowej nazwy domeny na platformie Azure — App Service
description: Dowiedz się, jak kupić domenę niestandardową nazwą aplikacji sieci web w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3f587b7e1b051b28d11ad84fcb2d02bd01315de5
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256438"
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Kupowanie niestandardowej nazwy domeny dla usługi Azure Web Apps

Domeny usługi App Service (wersja zapoznawcza) to domeny najwyższego poziomu, które są zarządzane bezpośrednio na platformie Azure. Ułatwiają one Zarządzanie domenami niestandardowymi dla [Azure Web Apps](app-service-web-overview.md). W tym samouczku dowiesz się, jak kupić domenę usługi App Service i przypisać nazwy DNS do usługi Azure Web Apps.

Ten artykuł jest przeznaczony dla usługi Azure App Service (aplikacje sieci Web, aplikacje API Apps, Mobile Apps, Logic Apps). Dla maszyny Wirtualnej platformy Azure lub usługi Azure Storage, zobacz [domeny Przypisz usługi App Service do maszyny Wirtualnej platformy Azure lub usługi Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Dla usług w chmurze, zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.
* [Usuń limit wydatków w ramach subskrypcji](../billing/billing-spending-limit.md#remove). Możesz dokonać zakupu domen usługi App Service z bezpłatnych środków.

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Używania domen niestandardowych w usłudze Azure Web Apps, aplikacji sieci web firmy [planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) musi mieć płatną warstwę (**Shared**, **podstawowe**, **standardowa**, lub  **Premium**). W tym kroku, należy upewnić się, że aplikacja sieci web jest w obsługiwanej warstwie cenowej.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Nawigowanie do aplikacji w witrynie Azure Portal

W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.  

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **F1**. Niestandardowe nazwy DNS nie są obsługiwane w warstwie **F1**. 

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Jeśli plan usługi App Service nie znajduje się w **F1** warstwy Zamknij **skalowanie w górę** stronie, a następnie przejdź do [kupować domeny](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**D1**, **B1**, **B2**, **B3** lub dowolną warstwę z kategorii **Produkcja**). Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij przycisk **Zastosuj**.

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Kupowanie domeny

### <a name="pricing-information"></a>Informacje o cenach
Aby poznać ceny informacji na temat domen usługi Azure App Service, odwiedź [stronie cennika usługi App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) i przewiń w dół do domeny usługi App Service.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Otwórz witrynę [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.

### <a name="launch-buy-domains"></a>Uruchom zakup domen
W **aplikacji sieci Web** kliknij nazwę aplikacji sieci web, wybierz pozycję **ustawienia**, a następnie wybierz pozycję **domen niestandardowych**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

W **domen niestandardowych** kliknij **kupować domeny**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Jeśli nie widzisz **domen usługi App Service** sekcji, którą chcesz usunąć limit wydatków dla Twojego konta platformy Azure (zobacz [wymagania wstępne](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurowanie zakupu domen

W **domeny usługi App Service** stronie **wyszukiwanie domeny** wpisz nazwę domeny, aby kupić, a następnie wpisz `Enter`. Sugerowane dostępnych domen są wyświetlane poniżej pola tekstowego. Wybierz co najmniej jednej domeny, który chcesz kupić.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Następujące [domen najwyższego poziomu](https://wikipedia.org/wiki/Top-level_domain) są obsługiwane przez domeny usługi App Service: _com_, _net_, _co.uk_, _organizacji_, _nl_, _w_, _biz_, _org.uk_, i _co.in_.
>
>

Kliknij przycisk **informacje kontaktowe** i wypełnij formularz informacji kontaktowych w domenie. Po zakończeniu kliknij przycisk **OK** aby wrócić do strony domeny usługi App Service.
   
Jest ważne, wypełnij wszystkie wymagane pola, o tyle dokładności, jak to możliwe. Nie można kupować domeny może spowodować nieprawidłowe dane, aby uzyskać więcej informacji. 

Następnie wybierz odpowiednie opcje dla swojej domeny. Zobacz poniższą tabelę objaśnienia dotyczące:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Ochrona prywatności | Włączanie | Zgódź się na "Ochrona prywatności", która jest ujęta w cenie zakupu _za darmo_. Niektóre domeny najwyższego poziomu są zarządzane przez rejestratorów, które nie obsługują ochrony prywatności i są one wyświetlane na **ochrony prywatności** strony. |
| Przypisz domyślne nazwy hostów | **www** i **@** | Wybierz żądaną nazwą hosta powiązania, w razie potrzeby. Po zakończeniu operacji zakupu domeny aplikacji sieci web są dostępne w wybranej nazwy hostów. Jeśli aplikacja sieci web znajduje się za zaporą [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), widzisz opcję, aby przypisać domeny katalogu głównego (@), ponieważ usługa Traffic Manager jest nie rekordów A pomocy technicznej. Można zmienić przypisania nazwy hosta, po zakończeniu zakupu domen. |

### <a name="accept-terms-and-purchase"></a>Zaakceptuj warunki i zakupu

Kliknij przycisk **postanowienia prawne** Przejrzyj warunki i opłaty, a następnie kliknij pozycję **Kup**.

> [!NOTE]
> Domeny usługi App Service umożliwia hostowanie domen DNS platformy Azure. Oprócz opłaty rejestracji domeny użycia dla usługi Azure DNS opłaty. Aby uzyskać informacje, zobacz [cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

Ponownie **domeny usługi App Service** kliknij **OK**. Gdy trwa operacja, zostaną wyświetlone następujące powiadomienia:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testowanie nazwy hostów

Jeśli domyślne nazwy hostów zostały przypisane do aplikacji sieci web, zobaczysz również powiadomienie o powodzeniu, dla każdej wybranej nazwy hosta. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Zobacz też wybranej nazwy hostów w **domen niestandardowych** strony w **niestandardowe nazwy hostów** sekcji. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Aby przetestować nazwy hostów, przejdź do listy nazw hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu, spróbuj przejść do _kontoso.net_ i _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Przypisywanie nazw hostów do aplikacji sieci web

Jeśli zrezygnujesz z Przypisz co najmniej jeden domyślne nazwy hostów do aplikacji sieci web w procesie zakupu lub musisz przypisać nazwy hosta nie jest wyświetlane, można przypisać nazwy hosta w dowolnym czasie.

Można także przypisać nazwy hostów w domeny usługi App Service do dowolnej aplikacji sieci web. Kroki zależą od tego, czy domeny usługi App Service i aplikację sieci web należą do tej samej subskrypcji.

- Inną subskrypcję: Mapy niestandardowe rekordy DNS z domeny usługi App Service do aplikacji sieci web, takich jak zakupionego zewnętrznie domeny. Aby uzyskać informacje na temat dodawania niestandardowych nazw DNS do domeny usługi App Service, zobacz [Zarządzanie rekordami systemu DNS niestandardowej](#custom). Aby zamapować domeny zewnętrznej zakupionych aplikacji sieci web, zobacz [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure Web Apps](app-service-web-tutorial-custom-domain.md). 
- Tej samej subskrypcji: Wykonaj następujące kroki.

### <a name="launch-add-hostname"></a>Uruchamianie Dodaj nazwę hosta
W **App Services** stronie, wybierz nazwę aplikacji sieci web, którą chcesz przypisać nazwy hostów, aby wybrać **ustawienia**, a następnie wybierz pozycję **domen niestandardowych**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Upewnij się, że zakupiona domeny jest wymieniony w **domen usługi App Service** sekcji, ale nie wybieraj go. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Wszystkich domen usługi App Service w tej samej subskrypcji są wyświetlane w aplikacji sieci web **domen niestandardowych** strony. Jeśli Twoja domena to w subskrypcji aplikacji sieci web, ale nie jest widoczna w aplikacji sieci web **domen niestandardowych** strony, spróbuj otworzyć **domen niestandardowych** strony lub Odśwież stronę sieci Web. Należy także sprawdzić dzwonek powiadomień w górnej części witryny Azure portal do błędów tworzenia lub postępu.
>
>

Wybierz przycisk **Dodaj nazwę hosta**.

### <a name="configure-hostname"></a>Konfigurowanie nazwy hosta
W **Dodaj nazwę hosta** okno dialogowe, wpisz w pełni kwalifikowana nazwa domeny Twojej domeny usługi App Service lub dowolna poddomena. Na przykład:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Po zakończeniu wybierz pozycję **weryfikacji**. Typ rekordu nazwy hosta jest automatycznie wybrana.

Wybierz przycisk **Dodaj nazwę hosta**.

Po zakończeniu operacji zostanie wyświetlone powiadomienie o powodzeniu, przypisanej nazwy hosta.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zamknij Dodaj nazwę hosta
W **Dodaj nazwę hosta** strony, należy przypisać inne nazwy hosta do aplikacji sieci web, zgodnie z potrzebami. Po zakończeniu zamknij **Dodaj nazwę hosta** strony.

W przypadku nowo przypisanych hostname(s) powinien zostać wyświetlony w swojej aplikacji **domen niestandardowych** strony.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testowanie nazwy hostów

Przejdź do listy nazw hostów w przeglądarce. W przykładzie na poprzednim zrzucie ekranu, spróbuj przejść do _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Odnów domenę

Domena usługi App Service, które zakupiono jest ważny przez jeden rok od daty zakupu. Domyślnie domena jest konfigurowana automatycznie odnowić pobierając metodę płatności dla następnego roku. Jeśli chcesz wyłączyć funkcję automatycznego odnawiania lub jeśli chcesz ręcznie odnowić domenę, postępuj zgodnie z instrukcjami w tym miejscu.

W **aplikacji sieci Web** kliknij nazwę aplikacji sieci web, wybierz pozycję **ustawienia**, a następnie wybierz pozycję **domen niestandardowych**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

W **domen usługi App Service** sekcji, wybierz domenę, którą chcesz skonfigurować.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Na lewym pasku nawigacyjnym domeny wybierz **odnowienie domeny**. Aby zatrzymać automatyczne odnawianie domeny, wybierz **poza**, a następnie **Zapisz**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Aby ręcznie odnowić domenę, wybierz **odnawianie domeny**. Jednak ten przycisk jest aktywny aż do 90 dni przed wygaśnięciem domeny.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Zarządzanie rekordami systemu DNS niestandardowe

Na platformie Azure rekordy DNS dla domeny usługi App Service są zarządzane przy użyciu [system DNS Azure](https://azure.microsoft.com/services/dns/). Możesz dodać, usunąć i aktualizowania rekordów DNS, podobnie jak w przypadku domeny zakupionego zewnętrznie.

### <a name="open-app-service-domain"></a>Domena Otwórz usługi App Service

W witrynie Azure portal w menu po lewej stronie wybierz **wszystkich usług** > **domen usługi App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę, do zarządzania. 

### <a name="access-dns-zone"></a>Dostęp do strefy DNS

W menu po lewej stronie w domenie, wybierz **strefy DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Ta akcja powoduje otwarcie [strefy DNS](../dns/dns-zones-records.md) strony Twojej domeny usługi App Service w usłudze Azure DNS. Aby uzyskać informacje na temat edytowania rekordów DNS, zobacz [jak zarządzać strefami systemu DNS w witrynie Azure portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Anulowanie zakupu (usuwania domeny)

Po zakupie domeny usługi App Service, masz pięć dni w celu anulowania zakupu w taki sposób, aby uzyskać pełny zwrot kosztów. Po upływie pięciu dni można usunąć domeny usługi App Service, ale nie może odbierać zwrot pieniędzy.

### <a name="open-app-service-domain"></a>Domena Otwórz usługi App Service

W witrynie Azure portal w menu po lewej stronie wybierz **wszystkich usług** > **domen usługi App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wybierz domenę dla Ciebie chcesz anulować lub usunąć. 

### <a name="delete-hostname-bindings"></a>Usuwanie powiązania nazwy hosta

W menu po lewej stronie w domenie, wybierz **powiązania nazwy hosta**. Poniżej przedstawiono powiązania nazwy hosta z wszystkich usług platformy Azure.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Nie można usunąć domeny usługi App Service, dopóki nie zostaną usunięte wszystkie powiązania nazwy hosta.

Usunięcie każdego powiązania nazwy hosta przez wybranie symbolu **...**   >  **Usuń**. Po usunięciu wszystkich powiązań wybierz **Zapisz**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Anuluj lub delete

W menu po lewej stronie w domenie, wybierz **Przegląd**. 

Jeśli anulowania w domenie zakupionych nie upłynął, zaznaczyć **anulowania zakupu**. W przeciwnym razie zostanie wyświetlony **Usuń** zamiast tego przycisku. Aby usunąć domeny bez zwrotu kosztów, wybierz **Usuń**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Aby potwierdzić operację, wybierz pozycję **tak**.

Po zakończeniu operacji domena jest wydanej z subskrypcji i dostępne dla każdego, kto zakupu ponownie. 

## <a name="direct-default-url-to-a-custom-directory"></a>Kierowanie domyślnego adresu URL do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Aby kierować do podkatalogu, takich jak `public`, zobacz [kierowanie domyślnego adresu URL do katalogu niestandardowego](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Więcej zasobów

[FAQ: Domeny usługi App Service (wersja zapoznawcza) i domen niestandardowych](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
