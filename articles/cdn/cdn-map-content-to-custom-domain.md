---
title: Samouczek — dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN | Microsoft Docs
description: W tym samouczku będziesz mapować zawartość punktu końcowego usługi Azure CDN na domenę niestandardową.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b9bcba78600e90c28f95c4ea842bf4b25b1c0da7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722792"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Samouczek: Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN
W tym samouczku przedstawiono sposób dodawania domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN). W przypadku dostarczania zawartości przy użyciu punktu końcowego usługi CDN domena niestandardowa jest niezbędna, jeśli chcesz, aby nazwa Twojej domeny była widoczna w adresie URL usługi CDN. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem. 

Po utworzeniu punktu końcowego usługi CDN w profilu nazwa punktu końcowego, czyli domena podrzędna domeny azureedge.net, jest uwzględniana w adresie URL w celu domyślnego dostarczania zawartości usługi CDN (np. https:\//contoso.azureedge.net/photo.png). Dla Twojej wygody usługa Azure CDN udostępnia opcję kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy punktu końcowego (np. https:\//www.contoso.com/photo.png). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz temat [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

Jeśli nie masz jeszcze domeny niestandardowej, musisz ją najpierw kupić od dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Jeśli używasz platformy Azure do hostowania [domen DNS](https://docs.microsoft.com/azure/dns/dns-overview), musisz delegować system nazw domen (DNS) dostawcy domen do usługi Azure DNS. Więcej informacji można znaleźć w temacie [Delegowanie domeny do usługi DNS platformy Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, przejdź do sekcji [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu DNS CNAME

Przed rozpoczęciem używania domeny niestandardowej z punktem końcowym usługi Azure CDN należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) przy użyciu dostawcy domeny, aby wskazać na punkt końcowy usługi CDN. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. W przypadku usługi Azure CDN nazwa domeny źródłowej to nazwa domeny niestandardowej, a nazwa domeny docelowej to nazwa hosta punktu końcowego usługi CDN. Gdy usługa Azure CDN zweryfikuje tworzony rekord CNAME, ruch przeznaczony dla domeny niestandardowej źródła (np. www.contoso.com) jest kierowany do wybranej nazwy hosta docelowego punktu końcowego usługi CDN (np. contoso.azureedge.net). 

Domenę niestandardową i jej domenę podrzędną można skojarzyć tylko z jednym punktem końcowym równocześnie. Można jednak używać różnych domen podrzędnych z tej samej domeny niestandardowej dla różnych punktów końcowych platformy Azure, korzystając z wielu rekordów CNAME. Można również mapować domenę niestandardową z różnymi domenami podrzędnymi do tego samego punktu końcowego usługi CDN.


## <a name="map-the-temporary-cdnverify-subdomain"></a>Mapowanie tymczasowej domeny podrzędnej cdnverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. W przypadku rejestrowania domeny niestandardowej w witrynie Azure Portal może wystąpić krótki przestój domeny. Aby uniknąć zakłóceń ruchu internetowego, należy najpierw przeprowadzić mapowanie domeny niestandardowej do nazwy hosta punktu końcowego usługi CDN przy użyciu domeny podrzędnej cdnverify platformy Azure w celu utworzenia tymczasowego mapowania CNAME. Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS. 

W przeciwnym razie, jeśli używasz domeny niestandardowej po raz pierwszy i nie ma w niej żadnego ruchu związanego ze środowiskiem produkcyjnym, domenę niestandardową można mapować bezpośrednio do punktu końcowego usługi CDN. Przejdź do sekcji [Mapowanie trwałej domeny niestandardowej](#map-the-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu poddomeny cdnverify:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Źródło: Wprowadź nazwę domeny niestandardowej, w tym domeny podrzędnej cdnverify w następującym formacie: cdnverify._&lt;nazwa domeny niestandardowej&gt;. Na przykład: cdnverify.www.contoso.com.

    - Wpisz: Wprowadź wartość *CNAME*.

    - Miejsce docelowe: Wprowadź nazwę hosta punktu końcowego usługi CDN, w tym domeny podrzędnej cdnverify, w następującym formacie: cdnverify._&lt;nazwa punktu końcowego&gt;_.azureedge.net. Na przykład: cdnverify.contoso.azureedge.net.

4. Zapisz zmiany.

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij następujące pola wpisu CNAME:

    ![Wpis CNAME](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Wpisz: Pozostaw pozycję *CNAME* wybraną.

    - Host: Wprowadź domenę podrzędną domeny niestandardowej do użycia, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.www.

    - Wskazuje na: Wprowadź nazwę hosta punktu końcowego usługi CDN, z uwzględnieniem nazwy domeny podrzędnej cdnverify. Na przykład: cdnverify.contoso.azureedge.net. 

    - Czas wygaśnięcia: Pozostaw wartość *1 godzina* wybraną.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Tabela rekordów DNS](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Tworzenie skojarzenia domeny niestandardowej z punktem końcowym usługi CDN

Po zarejestrowaniu domeny niestandardowej można dodać ją do punktu końcowego usługi CDN. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do profilu usługi CDN zawierającego punkt końcowy, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **Profil CDN** wybierz punkt końcowy usługi CDN do skojarzenia z domeną niestandardową.

   Zostanie otwarta strona **Punkt końcowy**.
    
3. Wybierz pozycję **Domena niestandardowa**. 

   ![Przycisk domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Zostanie otwarta strona **Dodawanie domeny niestandardowej**.

4. W obszarze **Nazwa hosta punktu końcowego** wstępnie wypełniono nazwę hosta punktu końcowego używanego jako domena docelowa rekordu CNAME. Ta nazwa pochodzi z adresu URL punktu końcowego usługi CDN: *&lt;nazwa hosta punktu końcowego&gt;*.azureedge.net. Nie można zmienić tej nazwy.

5. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową (razem z poddomeną), która ma być używana jako domena źródłowa rekordu CNAME. Na przykład: www.contoso.com lub cdn.contoso.com. Nie używaj nazwy domeny podrzędnej cdnverify.

   ![Okno dialogowe domeny niestandardowej usługi CDN](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana. 

   Propagacja ustawień nowej domeny niestandardowej do wszystkich węzłów brzegowych usługi CDN może zająć trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut.   


## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej sprawdź, czy odwołuje się ona do punktu końcowego usługi CDN.
 
1. Upewnij się, że masz publiczną zawartość buforowaną w punkcie końcowym. Jeśli na przykład punkt końcowy usługi CDN został skojarzony z kontem magazynu, usługa Azure CDN będzie buforować zawartość w kontenerze publicznym. Aby przetestować domenę niestandardową, sprawdź, czy kontener został ustawiony tak, aby zezwalać na publiczny dostęp, i czy zawiera co najmniej jeden plik.

2. W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Jeśli na przykład domena niestandardowa to cdn.contoso.com, adres URL buforowanego pliku powinien być podobny do następującego: http:\//cdn.contoso.com/my-public-container/my-file.jpg. Sprawdź, czy wynik jest to taki sam, kiedy bezpośrednio uzyskujesz dostęp do punktu końcowego usługi CDN, korzystając z adresu *&lt;nazwa hosta punktu końcowego&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

Jeśli sprawdzono, czy domena podrzędna cdnverify została pomyślnie mapowana do punktu końcowego (lub jeśli używasz nowej domeny niestandardowej, która nie znajduje się w środowisku produkcyjnym), można mapować domenę niestandardową bezpośrednio do nazwy hosta punktu końcowego usługi CDN.

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Źródło: Wprowadź nazwę domeny niestandardowej (np. www.contoso.com).

    - Wpisz: Wprowadź wartość *CNAME*.

    - Miejsce docelowe: Wprowadź nazwę hosta punktu końcowego usługi CDN. Jej wymagany format to: _&lt;nazwa punktu końcowego&gt;_. azureedge.net. Na przykład: contoso.azureedge.net.

4. Zapisz zmiany.

5. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej cdnverify, usuń go. 

6. Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, wykonaj kroki z sekcji [Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN](#associate-the-custom-domain-with-your-cdn-endpoint) i [Weryfikowanie domeny niestandardowej](#verify-the-custom-domain).

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij pola wpisu CNAME:

    ![Wpis CNAME](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Wpisz: Pozostaw pozycję *CNAME* wybraną.

    - Host: Wprowadź domenę podrzędną domeny niestandardowej do użycia. Na przykład: www lub cdn.

    - Wskazuje na: Wprowadź nazwę hosta punktu końcowego usługi CDN. Na przykład: contoso.azureedge.net. 

    - Czas wygaśnięcia: Pozostaw wartość *1 godzina* wybraną.

6. Wybierz pozycję **Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

    ![Tabela rekordów DNS](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Jeśli masz rekord CNAME cdnverify, wybierz ikonę ołówka obok niej, a następnie wybierz ikonę kosza.

8. Wybierz pozycję **Usuń**, aby usunąć rekord CNAME.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach dodano domenę niestandardową do punktu końcowego usługi CDN. Jeśli nie chcesz skojarzyć punktu końcowego z domeną niestandardową, możesz usunąć domenę niestandardową, wykonując następujące kroki:
 
1. W profilu usługi CDN wybierz punkt końcowy z domeną niestandardową, która ma zostać usunięta.

2. Na stronie **Punkt końcowy** w obszarze domen niestandardowych kliknij prawym przyciskiem myszy domenę niestandardową, którą chcesz usunąć, a następnie wybierz polecenie **Usuń** z menu kontekstowego.  

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z punktem końcowym usługi CDN.
> - Weryfikowanie domeny niestandardowej.

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować protokół HTTPS w domenie niestandardowej usługi Azure CDN.

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md)


