---
title: Sieci wirtualne
titleSuffix: Azure Cognitive Services
description: Skonfiguruj zabezpieczenia warstwowe sieci dla zasobów Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371226"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurowanie sieci wirtualnych Cognitive Services platformy Azure

Usługa Azure Cognitive Services udostępnia model zabezpieczeń warstwowych. Ten model umożliwia Zabezpieczanie kont Cognitive Services w określonym podzestawie sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do konta. Można ograniczyć dostęp do zasobów za pomocą filtrowania żądań. Zezwalanie tylko na żądania pochodzące z określonych adresów IP, zakresów adresów IP lub z listy podsieci w [sieciach wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli interesuje Cię ofertę, musisz [zażądać dostępu do wersji zapoznawczej](https://aka.ms/cog-svc-vnet-signup).

Aplikacja, która uzyskuje dostęp do zasobu Cognitive Services, gdy reguły sieciowe są stosowane, wymagają autoryzacji. Autoryzacja jest obsługiwana z poświadczeniami [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) lub z prawidłowym kluczem interfejsu API.

> [!IMPORTANT]
> Włączenie reguł zapory dla konta Cognitive Services domyślnie blokuje przychodzące żądania dla danych. Aby można było zezwolić na żądania w programie, należy spełnić jeden z następujących warunków:
> * Żądanie powinno pochodzić z usługi działającej w ramach platformy Azure Virtual Network (VNet) na liście dozwolonych podsieci docelowego konta Cognitive Services. Punkt końcowy w żądaniach pochodzących z sieci wirtualnej musi być ustawiony jako [niestandardową poddomeną](cognitive-services-custom-subdomains.md) konta Cognitive Services.
> * Lub żądanie powinno pochodzić z listy dozwolonych adresów IP.
>
> Żądania, które są blokowane obejmują z innymi usługami platformy Azure w witrynie Azure portal, rejestrowania i metryk usług i tak dalej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenariusze

Aby zabezpieczyć zasób Cognitive Services, należy najpierw skonfigurować regułę, aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji. Możesz również skonfigurować reguły, aby udzielić dostępu do ruchu z wybranych zakresów publicznych adresów IP w Internecie, włączając połączenia z określonych klientów internetowych lub lokalnych.

Reguły sieciowe są wymuszane dla wszystkich protokołów sieciowych na platformie Azure Cognitive Services, w tym REST i WebSocket. Aby uzyskać dostęp do danych przy użyciu narzędzi, takich jak konsole testów platformy Azure, należy skonfigurować jawne reguły sieciowe. Reguły sieci można stosować do istniejących zasobów Cognitive Services lub podczas tworzenia nowych zasobów Cognitive Services. Po reguły sieci są stosowane, są one wymuszane dla wszystkich żądań.

## <a name="supported-regions-and-service-offerings"></a>Obsługiwane regiony i oferty usług

Obsługa sieci wirtualnych dla Cognitive Services wymienionych poniżej jest ograniczona do *centralnych stanów USA*, *Południowo-środkowe*stany USA, *wschodniej USA*, *zachodnie*stany USA 2, *Europa Północna*, *Północna Republika Południowej Afryki*, *Europa zachodnia*, *Indie Środkowe*, *Australia Wschodnia*, *zachodnie stany USA*i *US gov Wirginiae* regiony platformy Azure. Jeśli oferta usługi nie jest wymieniona w tym miejscu, nie obsługuje sieci wirtualnych.

> [!div class="checklist"]
> * [Wykrywacz anomalii](./anomaly-detector/index.yml)
> * [Przetwarzanie obrazów](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Stoi](./face/index.yml)
> * [Aparat rozpoznawania formularzy](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizacja](./personalizer/index.yml)
> * [Analiza tekstu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Obsługa sieci wirtualnych dla Cognitive Services wymienionych poniżej jest ograniczona do regionu *środkowe stany USA*, *Południowo-środkowe*stany USA, *Wschodnie stany*US USA, *zachodnie stany USA 2*, *globalne*i *US gov Wirginia* regiony platformy Azure.
> [!div class="checklist"]
> * [Tłumaczenie tekstu w usłudze Translator](./translator/index.yml)

## <a name="service-tags"></a>Tagi usług
Oprócz obsługi punktów końcowych usługi sieci wirtualnej dla powyższych usług Cognitive Services obsługuje także tag usługi dla konfiguracji reguł sieci wychodzącej. Poniższe usługi są zawarte w tagu usługi CognitiveServicesManagement.
> [!div class="checklist"]
> * [Wykrywacz anomalii](./anomaly-detector/index.yml)
> * [Przetwarzanie obrazów](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Stoi](./face/index.yml)
> * [Aparat rozpoznawania formularzy](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizacja](./personalizer/index.yml)
> * [Analiza tekstu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Tłumaczenie tekstu w usłudze Translator](./translator/index.yml)
> * [Speech Service](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Zmień domyślną regułę dostępu do sieci

Domyślnie zasoby Cognitive Services akceptują połączenia od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, należy najpierw zmienić domyślnej akcji.

> [!WARNING]
> Wprowadzanie zmian w regułach sieci może mieć wpływ na możliwość łączenia się aplikacji z platformą Azure Cognitive Services. Ustawienie domyślnej reguły sieci na **Odmów** uniemożliwia dostęp do danych, chyba że stosowane są również określone reguły **sieciowe zezwalające na dostęp.** Pamiętaj udzielić dostępu do żadnych dozwolonych sieci przy użyciu reguł sieci, zanim będzie można zmienić domyślną regułę, aby odmówić dostępu. Jeśli zezwolisz na wyświetlanie listy adresów IP dla sieci lokalnej, pamiętaj, aby dodać wszystkie możliwe wychodzące publiczne adresy IP z sieci lokalnej.

### <a name="managing-default-network-access-rules"></a>Reguły dostępu do sieci w usłudze zarządzania domyślne

Można zarządzać domyślnymi regułami dostępu do sieci dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

   ![Opcja Sieć wirtualna](media/vnet/virtual-network-blade.png)

1. Aby domyślnie zablokować dostęp, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. Z ustawieniem **wybrane sieci** , niezależnie od skonfigurowanych **sieci wirtualnych** lub **zakresów adresów** — wszystkie dostępy są skutecznie odrzucane. W przypadku odmowy dostępu, żądania próbujące korzystać z zasobu Cognitive Services nie są dozwolone. Azure Portal, Azure PowerShell lub interfejs wiersza polecenia platformy Azure nadal może służyć do konfigurowania zasobu Cognitive Services.
1. Aby zezwolić na ruch ze wszystkich sieci, wybierz opcję zezwolenia na dostęp ze **wszystkich sieci**.

   ![Odmowa sieci wirtualnych](media/vnet/virtual-network-deny.png)

1. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Wyświetl stan domyślnej reguły dla zasobu Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Wyświetl stan domyślnej reguły dla zasobu Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ustaw domyślną regułę w celu blokowania dostępu do sieci domyślnie.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ustaw domyślną regułę, aby zezwolić na dostęp do sieci domyślnie.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Udzielanie dostępu z sieci wirtualnej

Można skonfigurować zasoby Cognitive Services w taki sposób, aby zezwalały na dostęp tylko z określonych podsieci. Dozwolone podsieci mogą należeć do sieci wirtualnej w tej samej subskrypcji lub w innej subskrypcji, łącznie z subskrypcjami należącymi do innej dzierżawy Azure Active Directory.

Włącz [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Cognitive Services w sieci wirtualnej. Punkt końcowy usługi kieruje ruch z sieci wirtualnej przez optymalną ścieżkę do usługi Azure Cognitive Services. Tożsamości podsieci i sieci wirtualnej są również przesyłane z każdym żądaniem. Administratorzy mogą następnie skonfigurować reguły sieci dla zasobu Cognitive Services, które zezwalają na odbieranie żądań z określonych podsieci w sieci wirtualnej. Klienci, którym udzielono dostępu za pomocą tych reguł sieci, muszą nadal spełnić wymagania dotyczące autoryzacji Cognitive Services zasobu, aby uzyskać dostęp do danych.

Każdy zasób Cognitive Services obsługuje do 100 reguł sieci wirtualnej, które mogą być połączone z [regułami sieci IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Wymagane uprawnienia

Aby zastosować regułę sieci wirtualnej do zasobu Cognitive Services, użytkownik musi mieć odpowiednie uprawnienia do dodawanych podsieci. Wymagane uprawnienie jest domyślną rolą *współautor* lub *Cognitive Services rolą współautor* . Wymagane uprawnienia można także dodać do niestandardowych definicji ról.

Zasób Cognitive Services i sieci wirtualne z udzielonym dostępem mogą znajdować się w różnych subskrypcjach, w tym w przypadku subskrypcji należących do innej dzierżawy usługi Azure AD.

> [!NOTE]
> Konfiguracja reguł, które udzielają dostępu do podsieci w sieciach wirtualnych, które są częścią innej dzierżawy Azure Active Directory, są obecnie obsługiwane tylko za pomocą programu PowerShell, interfejsu wiersza polecenia i interfejsów API REST. Takich reguł nie można skonfigurować za pomocą Azure Portal, ale mogą one być wyświetlane w portalu.

### <a name="managing-virtual-network-rules"></a>Zarządzanie regułami sieci wirtualnej

Można zarządzać regułami sieci wirtualnej dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do sieci wirtualnej przy użyciu istniejącej reguły sieci, w obszarze **sieci wirtualne**wybierz pozycję **Dodaj istniejącą sieć wirtualną**.

   ![Dodaj istniejącą sieć wirtualną](media/vnet/virtual-network-add-existing.png)

1. Wybierz opcje **sieci wirtualne** i **podsieci** , a następnie wybierz pozycję **Włącz**.

   ![Dodaj szczegóły istniejącej sieci wirtualnej](media/vnet/virtual-network-add-existing-details.png)

1. Aby utworzyć nową sieć wirtualną i udzielić jej dostępu, wybierz pozycję **Dodaj nową sieć wirtualną**.

   ![Dodaj nową sieć wirtualną](media/vnet/virtual-network-add-new.png)

1. Podaj informacje niezbędne do utworzenia nowej sieci wirtualnej, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Jeśli punkt końcowy usługi dla usługi Azure Cognitive Services nie był wcześniej skonfigurowany dla wybranej sieci wirtualnej i podsieci, możesz go skonfigurować w ramach tej operacji.
    >
    > Obecnie do wyboru podczas tworzenia reguły są wyświetlane tylko sieci wirtualne należące do tej samej dzierżawy Azure Active Directory. Aby udzielić dostępu do podsieci w sieci wirtualnej należącej do innej dzierżawy, użyj programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.

1. Aby usunąć regułę sieci wirtualnej lub podsieci, wybierz pozycję **...** , aby otworzyć menu kontekstowe dla sieci wirtualnej lub podsieci, a następnie wybierz pozycję **Usuń**.

   ![Usuń sieć wirtualną](media/vnet/virtual-network-remove.png)

1. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Lista reguł sieci wirtualnej.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Aby dodać regułę sieciową dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, należy użyć w pełni kwalifikowanego parametru **VirtualNetworkResourceId** w postaci "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-name".

1. Usuń regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Lista reguł sieci wirtualnej.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Włącz punkt końcowy usługi dla usługi Azure Cognitive Services w istniejącej sieci wirtualnej i podsieci.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Dodaj regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Aby dodać regułę dla podsieci w sieci wirtualnej należącej do innej dzierżawy usługi Azure AD, użyj w pełni kwalifikowanego identyfikatora podsieci w postaci "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Możesz użyć parametru **subskrypcji** , aby pobrać identyfikator podsieci dla sieci wirtualnej należącej do innej dzierżawy usługi Azure AD.

1. Usuń regułę sieciowej dla sieci wirtualnej i podsieci.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="grant-access-from-an-internet-ip-range"></a>Udzielanie dostępu z internetowego zakresu adresów IP

Można skonfigurować zasoby Cognitive Services, aby zezwolić na dostęp z określonych publicznych zakresów adresów IP. Ta konfiguracja zapewnia dostęp do określonych usług i sieci lokalnych, co skutecznie blokuje ogólny ruch internetowy.

Podaj dozwolone zakresy adresów internetowych przy użyciu [notacji CIDR](https://tools.ietf.org/html/rfc4632) w formularzu `16.17.18.0/24` lub jako pojedyncze adresy IP, takie jak `16.17.18.19`.

   > [!Tip]
   > Zakresy adresów małe, za pomocą "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane. Tych zakresów powinny być skonfigurowane przy użyciu reguły dotyczące poszczególnych adresów IP.

Reguły sieci IP są dozwolone tylko dla **publicznych** adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od `10.*`, `172.16.*` - `172.31.*`i `192.168.*`.

   > [!NOTE]
   > Reguły sieci IP nie mają wpływu na żądania pochodzące z tego samego regionu świadczenia usługi Azure co zasób Cognitive Services. Użyj [reguł sieci wirtualnej](#grant-access-from-a-virtual-network) , aby zezwolić na żądania tego samego regionu.

W tej chwili obsługiwane są tylko adresy IPV4. Każdy zasób Cognitive Services obsługuje do 100 reguł sieci adresów IP, które mogą być połączone z [regułami sieci wirtualnej](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurowanie dostępu do sieci lokalnej

Aby udzielić dostępu z sieci lokalnych do zasobu Cognitive Services przy użyciu reguły sieci adresu IP, należy zidentyfikować adresy IP, które są używane przez sieć. Aby uzyskać pomoc, skontaktuj się z administratorem sieci.

Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md) lokalnie do publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft, musisz zidentyfikować adresy IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych. Każda z nich jest stosowana do ruchu usługi platformy Azure, gdy ruch przechodzi do Microsoft Azure sieci szkieletowej. W przypadku komunikacji równorzędnej firmy Microsoft używane adresy IP translatora adresów sieciowych są dostarczane przez klienta lub przez dostawcę usług. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Dowiedz się więcej o [translatorze adresów sieciowych publicznej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft dla usługi ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Zarządzanie regułami sieci IP

Można zarządzać regułami sieci IP dla zasobów Cognitive Services za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Przejdź do zasobu Cognitive Services, który chcesz zabezpieczyć.

1. Wybierz menu **Zarządzanie zasobami** o nazwie **Sieć wirtualna**.

1. Sprawdź, czy wybrano opcję zezwalania na dostęp z **wybranych sieci**.

1. Aby udzielić dostępu do zakresu internetowego adresu IP, wprowadź adres IP lub zakres adresów (w [formacie CIDR](https://tools.ietf.org/html/rfc4632)) w obszarze **Firewall** > zapory **zakres adresów**. Akceptowane są tylko prawidłowe publiczne adresy IP (niezarezerwowane).

   ![Dodaj zakres adresów IP](media/vnet/virtual-network-add-ip-range.png)

1. Aby usunąć regułę sieci IP, wybierz <span class="docon docon-delete x-hidden-focus"></span> ikonę kosza obok zakresu adresów.

   ![Usuń zakres adresów IP](media/vnet/virtual-network-delete-ip-range.png)

1. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) i [Zaloguj się](/powershell/azure/authenticate-azureps)lub wybierz opcję **Wypróbuj**.

1. Lista reguł sieci IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Dodaj regułę sieciowej dla indywidualnego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Dodaj regułę sieciowej dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieci dla indywidualnego adresu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Usuń regułę sieci dla zakresu adresów IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli)lub wybierz opcję **Wypróbuj**.

1. Lista reguł sieci IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Dodaj regułę sieciowej dla indywidualnego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Dodaj regułę sieciowej dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Usuń regułę sieci dla indywidualnego adresu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Usuń regułę sieci dla zakresu adresów IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Pamiętaj, aby [ustawić regułę domyślną](#change-the-default-network-access-rule) na **odrzucanie**, lub reguły sieciowe nie mają żadnego efektu.

## <a name="next-steps"></a>Następne kroki

* Poznaj różne [Cognitive Services platformy Azure](welcome.md)
* Dowiedz się więcej o [punktach końcowych usługi Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)