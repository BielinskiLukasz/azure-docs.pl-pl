---
title: 'Konfigurowanie komunikacji równorzędnej dla obwodu ExpressRoute: Azure | Microsoft Docs'
description: W tym artykule opisano kroki tworzenia i inicjowania obsługi ExpressRoute prywatnych i komunikacji równorzędnej firmy Microsoft. W tym artykule przedstawiono również sposób sprawdzania stanu, aktualizacji lub usuwania komunikacji równorzędnej dla obwodu.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 30b330d60d75896406cbdf2d1eb41537960c5be5
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965286"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute

W tym artykule opisano tworzenie i zarządzanie konfiguracją routingu Azure Resource Manager dla obwodu ExpressRoute (ARM) przy użyciu Azure Portal. Możesz również sprawdzić stan, aktualizowanie i usuwanie komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — publiczna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Usługę Azure Private i Komunikacja równorzędna firmy Microsoft można skonfigurować dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Możesz skonfigurować komunikację równorzędną w dowolnej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [Informacje o obwodach i komunikacji równorzędnej](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji

* Pamiętaj, aby przed rozpoczęciem konfiguracji przejrzeć strony z [wymaganiami wstępnymi](expressroute-prerequisites.md), [wymaganiami routingu](expressroute-routing.md) oraz [przepływami pracy](expressroute-workflows.md).
* Musisz mieć aktywny obwód usługi ExpressRoute. Zanim przejdziesz dalej, postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md), który powinien zostać włączony przez dostawcę połączenia. Aby można było skonfigurować komunikację równorzędną, obwód ExpressRoute musi być w stanie aprowizacji i włączony. 
* Jeśli planujesz użycie skrótu klucza wspólnego/MD5, pamiętaj, aby użyć go na obu stronach tunelu i ograniczyć liczbę znaków alfanumerycznych do maksymalnie 25. Znaki specjalne nie są obsługiwane. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zwykle IPVPN, takie jak MPLS), dostawca łączności konfiguruje i zarządza routingiem. 

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Przed skonfigurowaniem komunikacji równorzędnej BGP skontaktuj się z dostawcą usług.
> 
> 

## <a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Skonfiguruj obwód usługi ExpressRoute. Sprawdź **stan dostawcy** , aby upewnić się, że obwód jest w pełni obsługiwany przez dostawcę łączności przed dalszym kontynuowaniem.

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć komunikację równorzędną firmy Microsoft. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu należy wykonać poniższe kroki.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Obwód — stan dostawcy: zainicjowano**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Zanim przejdziesz dalej, upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4, którego jesteś właścicielem, zarejestrowany w RIR/IRR. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. W przypadku łączy podstawowych i pomocniczych należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: musisz podać listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Możesz wybrać komunikację równorzędną, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   [![Wybierz wiersz komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Wybierz wiersz komunikacji równorzędnej firmy Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Skonfiguruj komunikację równorzędną firmy Microsoft. **Zapisz** konfigurację po określeniu wszystkich parametrów. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "weryfikacja nie jest konieczna". 
>
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez Otwieranie biletu pomocy technicznej, jak pokazano poniżej. 
>

   Jeśli obwód jest w stanie "wymagana Walidacja", musisz otworzyć bilet pomocy technicznej, aby wyświetlić dowód własności prefiksów dla naszego zespołu pomocy technicznej. Możesz otworzyć bilet pomocy technicznej bezpośrednio z portalu, jak pokazano w następującym przykładzie:

   ![Wymagana weryfikacja — bilet pomocy technicznej](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższej ilustracji:

   ![Stan komunikacji równorzędnej: skonfigurowany](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stan komunikacji równorzędnej: skonfigurowano")]

### <a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić właściwości komunikacji równorzędnej firmy Microsoft, wybierając wiersz dla komunikacji równorzędnej.

[![Wyświetl właściwości komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Wyświetl właściwości")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz wybrać wiersz dla komunikacji równorzędnej, którą chcesz zmodyfikować, a następnie zmodyfikować właściwości komunikacji równorzędnej i zapisać modyfikacje.

![Wybierz wiersz komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Aby usunąć konfigurację komunikacji równorzędnej, kliknij ikonę Usuń, jak pokazano na poniższej ilustracji:

![Usuwanie komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Prywatna Komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. 

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć prywatną komunikację równorzędną Azure. W takim przypadku nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca połączenia nie zarządza usługą routingu, po utworzeniu obwodu przejdź do następnych kroków.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Obwód — stan dostawcy: zainicjowano**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Zanim przejdziesz do następnych kroków, upewnij się, czy masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci zostanie przypisany pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft użyje drugiego adresu IP dla jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. W przypadku łączy podstawowych i pomocniczych należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć numeru prywatnego jako dla tej komunikacji równorzędnej z wyjątkiem liczby od 65515 do 65520 włącznie.
   * Trasy należy anonsować od lokalnego routera brzegowego do platformy Azure za pośrednictwem protokołu BGP podczas konfigurowania prywatnej komunikacji równorzędnej.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Wybierz wiersz prywatnej komunikacji równorzędnej Azure, jak pokazano w następującym przykładzie:

   [![Wybierz wiersz prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Wybierz wiersz prywatnej komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Skonfiguruj prywatną komunikację równorzędną. **Zapisz** konfigurację po określeniu wszystkich parametrów.

   ![Skonfiguruj prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższego przykładu:

   ![Zapisano prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Aby wyświetlić szczegóły prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

[![Wyświetl właściwości prywatnych komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Wyświetl właściwości prywatnych komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości. Po uaktualnieniu Zapisz zmiany.

![Aktualizowanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, wybierając ikonę Usuń, jak pokazano na poniższej ilustracji:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i ExpressRoute Global Reach są usuwane. 
> 
> 

![Usuwanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Publiczna Komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji publicznej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

> [!Note]
> Publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure

Wyświetl właściwości publicznej komunikacji równorzędnej Azure, wybierając komunikację równorzędną.

### <a name="updatepublic"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure

Wybierz wiersz dla komunikacji równorzędnej, a następnie zmodyfikuj właściwości komunikacji równorzędnej.

### <a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Usuń konfigurację komunikacji równorzędnej, wybierając ikonę Usuń.

## <a name="next-steps"></a>Następne kroki

Następny krok, [łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).
