---
title: "Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure | Microsoft Docs"
description: "Dowiedz się, jak dodać istniejącą niestandardową nazwę domeny DNS (domenę niestandardową) do aplikacji internetowej, zaplecza aplikacji mobilnej lub aplikacji interfejsu API w usłudze Azure App Service."
keywords: "app service, azure app service, mapowanie domeny, nazwa domeny, istniejąca domena, nazwa hosta"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9867cc2f8a8d484ca4bfb160c20a07df38790f4d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure

Usługa [Azure Web Apps](app-service-web-overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostowaną w Internecie. W tym samouczku przedstawiono, w jaki sposób można zmapować istniejącą niestandardową nazwę DNS na aplikacje internetowe platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny (na przykład `www.contoso.com`) przy użyciu rekordu CNAME
> * Mapowanie domeny katalogu głównego (na przykład `contoso.com`) przy użyciu rekordu A
> * Mapowanie domeny z symbolami wieloznacznymi (na przykład `*.contoso.com`) przy użyciu rekordu CNAME
> * Automatyzacja mapowania domen przy użyciu skryptów

Do mapowania niestandardowej nazwy DNS na usługę App Service możesz użyć **rekordu CNAME** lub **rekordu A**. 

> [!NOTE]
> Zalecane jest użycie rekordu CNAME dla wszystkich niestandardowych nazw DNS z wyjątkiem domeny katalogu głównego (na przykład `contoso.com`).

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service, zobacz [Migrate an active DNS name to Azure App Service](app-service-custom-domain-name-migrate.md) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.
* Kup nazwę domeny i upewnij się, że masz dostęp do rejestru DNS u swojego dostawcy domeny (na przykład GoDaddy).

  Aby na przykład dodać wpisy DNS dla domen `contoso.com` i `www.contoso.com`, musisz mieć możliwość skonfigurowania ustawień DNS dla domeny katalogu głównego `contoso.com`.

  > [!NOTE]
  > Jeśli nie masz istniejącej nazwy domeny, możesz [zakupić domenę za pośrednictwem witryny Azure Portal](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zamapować niestandardową nazwę DNS na aplikację internetową, dla tej aplikacji internetowej musisz mieć płatną warstwę [planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) (**Współdzielona**, **Podstawowa**, **Standardowa** lub  **Premium**). W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Nawigowanie do aplikacji w witrynie Azure Portal

W lewym menu wybierz pozycję **App Services**, a następnie wybierz nazwę swojej aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zostanie wyświetlona strona zarządzania aplikacji usługi App Service.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W lewym obszarze nawigacji na stronie aplikacji przewiń do sekcji **Ustawienia** i wybierz pozycję **Skaluj w górę (plan usługi App Service)**.

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Upewnij się, że aplikacja nie znajduje się w warstwie **Bezpłatna**. Niestandardowe nazwy DNS nie są obsługiwane w warstwie **Bezpłatna**. 

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Jeśli plan usługi App Service jest inny niż **Bezpłatna**, zamknij stronę **Wybieranie warstwy cenowej** i przejdź od razu do sekcji [Mapowanie rekordu CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Wybierz jedną z płatnych warstw (**Współdzielona**, **Podstawowa**, **Standardowa** lub **Premium**). 

Kliknij pozycję **Wybierz**.

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wyświetlenie następującego powiadomienia oznacza zakończenie operacji skalowania.

![Potwierdzenie operacji skalowania](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mapowanie rekordu CNAME

W przykładzie znajdującym się w tym samouczku dodasz rekord CNAME dla poddomeny `www` (na przykład `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Dodaj rekord CNAME, aby zmapować poddomenę na domyślną nazwę hosta aplikacji (`<app_name>.azurewebsites.net`, gdzie `<app_name>` to nazwa aplikacji).

W przypadku przykładowej domeny `www.contoso.com` dodaj rekord CNAME, który zmapuje nazwę `www` na `<app_name>.azurewebsites.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Włączanie mapowania rekordów CNAME na platformie Azure

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** aplikacji dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS (`www.contoso.com`).

Wybierz ikonę **+** obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowaną nazwę domeny, dla której dodano rekord CNAME, na przykład `www.contoso.com`. 

Wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **CNAME (www.example.com lub dowolna poddomena)**.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Jeśli pominięto jakiś krok lub popełniono gdzieś błąd w pisowni, w dolnej części strony zostanie wyświetlony komunikat o błędzie weryfikacji.

![Błąd weryfikacji](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapowanie rekordu A

W przykładzie znajdującym się w tym samouczku dodasz rekord A dla domeny katalogu głównego (na przykład `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Kopiowanie adresu IP aplikacji

Aby móc zamapować rekord A, musisz znać zewnętrzny adres IP aplikacji. Ten adres IP można znaleźć na stronie **Domeny niestandardowe** aplikacji w witrynie Azure Portal.

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** skopiuj adres IP aplikacji.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Tworzenie rekordu A

Aby móc zmapować rekord A na aplikację, usługa App Service wymaga **dwóch** rekordów DNS:

- Rekord **A** do zmapowania adresu IP aplikacji.
- Rekord **TXT** do zmapowania domyślnej nazwy hosta aplikacji `<app_name>.azurewebsites.net`. Usługa App Service używa tego rekordu tylko podczas konfiguracji, aby sprawdzić, czy jesteś właścicielem domeny niestandardowej. Po zweryfikowaniu i skonfigurowaniu domeny niestandardowej w usłudze App Service możesz usunąć ten rekord TXT. 

Dla przykładowej domeny `contoso.com` utwórz rekordy A i TXT zgodnie z wartościami z poniższej tabeli (`@` zazwyczaj reprezentuje domenę katalogu głównego). 

| Typ rekordu | Host | Wartość |
| - | - | - |
| A | `@` | Adres IP z sekcji [Kopiowanie adresu IP aplikacji](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Po dodaniu tych rekordów strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Strona rekordów DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Włączanie mapowania rekordu A w aplikacji

Ponownie na stronie **Domeny niestandardowe** aplikacji w witrynie Azure Portal dodaj do listy w pełni kwalifikowaną niestandardową nazwę DNS (na przykład `contoso.com`).

Wybierz ikonę **+** obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Wpisz w pełni kwalifikowaną nazwę domeny, dla której skonfigurowano rekord A, na przykład `contoso.com`.

Wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **Rekord A (example.com)**.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

![Dodany rekord A](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Jeśli pominięto jakiś krok lub popełniono gdzieś błąd w pisowni, w dolnej części strony zostanie wyświetlony komunikat o błędzie weryfikacji.

![Błąd weryfikacji](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapowanie domeny z symbolami wieloznacznymi

W przykładzie znajdującym się w tym samouczku zmapujesz [wieloznaczną nazwę DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (na przykład `*.contoso.com`) na aplikację usługi App Service przez dodanie rekordu CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Dodaj rekord CNAME, aby zmapować wieloznaczną nazwę na domyślną nazwę hosta aplikacji (`<app_name>.azurewebsites.net`).

Dla przykładowej domeny `*.contoso.com` rekord CNAME zmapuje nazwę `*` na nazwę `<app_name>.azurewebsites.net`.

Po dodaniu tego rekordu CNAME strona rekordów DNS wygląda podobnie jak w następującym przykładzie:

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Włączanie mapowania rekordów CNAME w aplikacji

Teraz możesz do aplikacji dodać dowolną poddomenę zgodną z wieloznaczną nazwą (na przykład nazwy `sub1.contoso.com` i `sub2.contoso.com` są zgodne z nazwą `*.contoso.com`). 

W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**. 

![Menu domen niestandardowych](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Wybierz ikonę **+** obok pozycji **Dodaj nazwę hosta**.

![Dodawanie nazwy hosta](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Wpisz w pełni kwalifikowany nazwę domeny, która pasuje do domeny z symbolami wieloznacznymi (na przykład `sub1.contoso.com`), a następnie wybierz przycisk **Weryfikuj**.

Zostanie aktywowany przycisk **Dodaj nazwę hosta**. 

Upewnij się, że opcja **Typ rekordu nazwy hosta** jest ustawiona na wartość **CNAME (www.example.com lub dowolna poddomena)**.

Wybierz przycisk **Dodaj nazwę hosta**.

![Dodawanie nazwy DNS do aplikacji](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Może upłynąć trochę czasu, zanim nowa nazwa hosta zostanie odzwierciedlona na stronie **Domeny niestandardowe** aplikacji. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.

Wybierz ponownie ikonę **+**, aby dodać inną nazwę hosta zgodną z domeną z symbolami wieloznacznymi. Na przykład dodaj nazwę `sub2.contoso.com`.

![Dodany rekord CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Testowanie w przeglądarce

Przejdź do nazw DNS, które zostały wcześniej skonfigurowane (na przykład `contoso.com`, `www.contoso.com`, `sub1.contoso.com` lub `sub2.contoso.com`).

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Naprawianie błędu 404 „Nie można odnaleźć witryny internetowej”

Jeśli podczas przechodzenia do adresu URL domeny niestandardowej występuje błąd HTTP 404 (Nie znaleziono), sprawdź, czy domena jest rozstrzygana na adres IP aplikacji, korzystając z witryny <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Jeśli nie, może to być spowodowane jedną z następujących przyczyn:

- W skonfigurowanej domenie niestandardowej brakuje rekordu A i/lub rekordu CNAME.
- Klient przeglądarki umieścił w pamięci podręcznej stary adres IP Twojej domeny. Wyczyść pamięć podręczną i ponownie przetestuj rozpoznawanie nazwy DNS. Do czyszczenia pamięci podręcznej na komputerze z systemem Windows należy użyć polecenia `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Kierowanie domyślnego adresu URL do katalogu niestandardowego

Domyślnie usługa App Service kieruje żądania internetowe do katalogu głównego w kodzie aplikacji. Jednak niektóre platformy internetowe nie uruchamiają się z poziomu katalogu głównego. Na przykład platforma [Laravel](https://laravel.com/) uruchamia się w podkatalogu `public`. W przypadku używanej tu przykładowej nazwy DNS `contoso.com` taka aplikacja byłaby dostępna w katalogu `http://contoso.com/public`, ale zamiast tego lepiej byłoby przekierować nazwę `http://contoso.com` do katalogu `public`. W tym kroku nie opisano rozpoznawania nazw DNS tylko dostosowywanie katalogu wirtualnego.

W tym celu na stronie aplikacji internetowej wybierz pozycję **Ustawienia aplikacji** w obszarze nawigacji po lewej stronie. 

W dolnej części strony widać, że główny katalog wirtualny `/` domyślnie wskazuje na katalog `site\wwwroot`, który jest katalogiem głównym kodu aplikacji. Zmień to ustawienie, aby główny katalog wirtualny wskazywał na przykład na katalog `site\wwwroot\public`, i zapisz zmiany. 

![Dostosowywanie katalogu wirtualnego](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Po zakończeniu tej operacji aplikacja powinna zwracać odpowiednią stronę w ścieżce głównej (na przykład http://contoso.com).

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie domenami niestandardowymi można zautomatyzować za pomocą skryptów [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Aby uzyskać więcej informacji, zobacz [Map a custom domain to a web app](scripts/app-service-cli-configure-custom-domain.md) (Mapowanie domeny niestandardowej na aplikację internetową). 

### <a name="azure-powershell"></a>Azure PowerShell 

Następujące polecenie dodaje skonfigurowaną niestandardową nazwę DNS do aplikacji usługi App Service. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Aby uzyskać więcej informacji, zobacz [Assign a custom domain to a web app](scripts/app-service-powershell-configure-custom-domain.md) (Przypisywanie domeny niestandardowej do aplikacji internetowej).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Mapowanie poddomeny przy użyciu rekordu CNAME
> * Mapowanie domeny katalogu głównego przy użyciu rekordu A
> * Mapowanie domeny z symbolami wieloznacznymi przy użyciu rekordu CNAME
> * Automatyzacja mapowania domen przy użyciu skryptów

Przejdź do następnego samouczka, aby dowiedzieć się, jak powiązać niestandardowy certyfikat SSL z aplikacją internetową.

> [!div class="nextstepaction"]
> [Wiązanie istniejącego niestandardowego certyfikatu SSL z usługą Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
