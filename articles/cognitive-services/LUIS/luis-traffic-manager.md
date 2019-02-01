---
title: Zwiększ limit przydziału punktu końcowego
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) oferuje możliwość zwiększenia limitu przydziału żądania punktu końcowego, po przekroczeniu limitu przydziału jednego klucza. Polega to na tworzenie więcej kluczy dla usługi LUIS, a następnie dodanie ich do aplikacji usługi LUIS w **Publikuj** strony w **zasobów i klucze** sekcji.
author: diberry
manager: cgronlun
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 802a5cc629a467527c916c5a41a9c00d06e85600
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491726"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager umożliwia zarządzanie przydziału punktu końcowego za pośrednictwem kluczy
Language Understanding (LUIS) oferuje możliwość zwiększenia limitu przydziału żądania punktu końcowego, po przekroczeniu limitu przydziału jednego klucza. Polega to na tworzenie więcej kluczy dla usługi LUIS, a następnie dodanie ich do aplikacji usługi LUIS w **Publikuj** strony w **zasobów i klucze** sekcji. 

Aplikacja kliencka musi zarządzanie ruchem między kluczy. Usługa LUIS nie zrobić. 

W tym artykule wyjaśniono, jak zarządzanie ruchem między klucze za pomocą usługi Azure [usługi Traffic Manager][traffic-manager-marketing]. Musi już mieć aplikacją usługi LUIS wyszkolonych i opublikowane. Jeśli nie masz, postępuj zgodnie ze wstępnie utworzonych domen [Szybki Start](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>Łączenie do programu PowerShell w witrynie Azure portal
W [Azure] [ azure-portal] portal, Otwórz okno programu PowerShell. Ikona okno programu PowerShell jest **> _** w górnym pasku nawigacyjnym. Przy użyciu programu PowerShell z poziomu portalu, Pobierz najnowszą wersję programu PowerShell, a użytkownik jest uwierzytelniony. Wymaga programu PowerShell w witrynie portal [usługi Azure Storage](https://azure.microsoft.com/services/storage/) konta. 

![Otwórz portal zrzut ekranu z systemu Azure z okna programu Powershell](./media/traffic-manager/azure-portal-powershell.png)

W poniższych sekcjach użyto [poleceń cmdlet programu PowerShell usługi Traffic Manager](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Utwórz grupę zasobów platformy Azure przy użyciu programu PowerShell
Przed utworzeniem zasobów platformy Azure, Utwórz grupę zasobów zawierającą wszystkie zasoby. Określ nazwę grupy zasobów `luis-traffic-manager` użytkowania region jest `West US`. Region grupa zasobów przechowuje metadane dotyczące grupy. Go nie będzie zwolnić zasoby jeśli znajdują się w innym regionie. 

Utwórz grupę zasobów za pomocą **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** polecenia cmdlet:

```powerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Tworzenie kluczy, aby zwiększyć limit przydziału całkowita punktu końcowego usługi LUIS
1. W witrynie Azure portal, utworzysz dwa **Language Understanding** kluczy w `West US` i jeden we `East US`. Użyj istniejącej grupy zasobów, utworzony w poprzedniej sekcji o nazwie `luis-traffic-manager`. 

    ![Zrzut ekranu usługi Azure portal za pomocą dwóch kluczy usługi LUIS w grupie zasobów usługi luis — traffic-manager](./media/traffic-manager/luis-keys.png)

2. W [LUIS] [ LUIS] witryny sieci Web w **Zarządzaj** sekcji na **kluczy i punktów końcowych** strony, przypisywanie klawiszy do aplikacji i ponowne opublikowanie aplikacji przy użyciu Wybieranie **Publikuj** przycisk w prawym górnym menu. 

    Przykładowy adres URL w **punktu końcowego** kolumny za pomocą żądania GET klucza punktu końcowego jako parametr zapytania. Skopiuj adresy URL punktów końcowych dwóch nowych kluczy. Są one używane w ramach konfiguracji usługi Traffic Manager w dalszej części tego artykułu.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Zarządzaj żądaniami punktu końcowego usługi LUIS różnych kluczy z usługą Traffic Manager
Usługa Traffic Manager tworzy nowy punkt dostępu DNS dla punktów końcowych. Nie działa jako brama lub serwer proxy, ale wyłącznie na poziomie usługi DNS. W tym przykładzie nie zmienia żadnych rekordów DNS. Biblioteka DNS używa do komunikacji z usługą Traffic Manager można pobrać właściwego punktu końcowego dla tego konkretnego żądania. _Każdy_ żądania przeznaczone dla usługi LUIS wymaga najpierw żądanie usługi Traffic Manager, aby określić, które usługa LUIS punktu końcowego. 

### <a name="polling-uses-luis-endpoint"></a>Sondowanie korzysta z punktu końcowego usługi LUIS
Usługa Traffic Manager sonduje punktów końcowych, które okresowo, aby upewnić się, że punkt końcowy jest nadal dostępny. Adres URL usługi Traffic Manager sondowania musi być dostępny na żądanie GET i zwrócić 200. Adres URL punktu końcowego na **Publikuj** strony dzieje. Ponieważ każdy klucz punkt końcowy ma inną trasę i parametry ciągu zapytania, każdy klucz punktu końcowego musi ścieżką inną sondowania. Każdorazowo, gdy usługa Traffic Manager sonduje, kosztuje żądanie limitu przydziału. Parametr ciągu zapytania **q** usługi Luis punkt końcowy jest wypowiedź wysyłane do usługi LUIS. Tego parametru, zamiast wysyłać wypowiedź służy do dodania sondowania usługi Traffic Manager do dziennika punktu końcowego usługi LUIS jako techniki debugowania podczas pobierania Traffic Manager skonfigurowaną.

Ponieważ każdy punkt końcowy usługi LUIS potrzebuje własnej ścieżki, musi ona swój własny profil usługi Traffic Manager. Aby można było zarządzać w profilach, należy utworzyć [ _zagnieżdżonych_ usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architektury. Jeden profil nadrzędnego wskazuje profile elementów podrzędnych i zarządzania ruchem między nimi.

Po skonfigurowaniu usługi Traffic Manager, pamiętaj, aby zmienić ścieżkę do użycia rejestrowanie = parametr ciągu zapytania false, aby dany dziennik nie zapełnia się za pomocą sondowania.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurowanie usługi Traffic Manager przy użyciu profilów zagnieżdżonych
Poniższe sekcje, Utwórz dwa profile podrzędnych, jeden dla klucza usługi LUIS Wschód i jeden dla klucza usługi LUIS zachodnie. Następnie tworzony jest profil nadrzędnego i profile dwóch podrzędne są dodawane do profilu nadrzędnego. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu usługi Traffic Manager w regionie wschodnie stany USA przy użyciu programu PowerShell
Aby utworzyć profil usługi Traffic Manager w regionie wschodnie stany USA, istnieje kilka czynności: Tworzenie profilu, Dodaj punkt końcowy i ustaw punkt końcowy. Profil usługi Traffic Manager może mieć wiele punktów końcowych, ale każdy punkt końcowy ma taką samą ścieżkę sprawdzania poprawności. Ponieważ adresy URL punktów końcowych usługi LUIS w przypadku subskrypcji Wschodnią i zachodnią są różne ze względu na region i punktu końcowego klucza, każdy punkt końcowy usługi LUIS musi mieć jeden punkt końcowy w profilu. 

1. Tworzenie profilu za pomocą **[polecenia New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** polecenia cmdlet

    Użyj następującego polecenia cmdlet, aby utworzyć profil. Upewnij się, że zmiana `appIdLuis` i `subscriptionKeyLuis`. SubscriptionKey jest klucza wschodnie stany USA usługi LUIS. Jeśli ścieżka jest nieprawidłowy, LUIS identyfikator i punktu końcowego klucza aplikacji, w tym sondowania usługi Traffic Manager jest stan `degraded` ponieważ Traffic Manager nie może pomyślnie żądania punktu końcowego usługi LUIS. Upewnij się, że wartość `q` jest `traffic-manager-east` można wyświetlić tę wartość w dziennikach punktu końcowego usługi LUIS.

    ```powerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:
    
    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |— Nazwa|Usługa Luis-profile-eastus|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-ResourceGroupName|usługi Luis — traffic-manager|Utworzony w poprzedniej sekcji.|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu w usłudze Traffic Manager][routing-methods]. Jeśli używasz wydajności, na żądanie adresu URL do usługi Traffic Manager muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa czatbota lub innych aplikacji, odpowiada chatbot do naśladowania regionu w wywołaniu do usługi Traffic Manager. |
    |-RelativeDnsName|Usługa Luis-dns-eastus|Jest to poddomeny dla usługi: Usługa luis-dns-eastus.trafficmanager.net|
    |-Czas wygaśnięcia|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port oraz Protokół usługi LUIS jest 443 dla protokołu HTTPS|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Zastąp <appIdLuis> i <subscriptionKeyLuis> własnymi wartościami.|
    
    Żądania zakończonego powodzeniem ma bez odpowiedzi.

2. Dodaj punkt końcowy wschodnie stany USA, za pomocą **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** polecenia cmdlet

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwapunktukoncowego|Usługa Luis wschód endpoint|Nazwa punktu końcowego, wyświetlana w ramach profilu|
    |-TrafficManagerProfile|$eastprofile|Użyj obiektu profil utworzony w kroku 1|
    |-Type|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Traffic Manager][traffic-manager-endpoints] |
    |-Target|eastus.API.cognitive.microsoft.com|Jest to domeny dla punktu końcowego usługi LUIS.|
    |-EndpointLocation|"eastus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włączanie punktu końcowego, podczas jego tworzenia|

    Odpowiedź oznaczająca Powodzenie wygląda następująco:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Ustaw punkt końcowy wschodnie stany USA, z **[polecenia Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** polecenia cmdlet

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Odpowiedź oznaczająca Powodzenie będzie tę samą odpowiedź w kroku 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu usługi Traffic Manager dla regionu zachodnie stany USA przy użyciu programu PowerShell
Aby utworzyć profil usługi Traffic Manager dla regionu zachodnie stany USA, wykonaj te same kroki: Tworzenie profilu, Dodaj punkt końcowy i ustaw punkt końcowy.

1. Tworzenie profilu za pomocą **[polecenia New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    Użyj następującego polecenia cmdlet, aby utworzyć profil. Upewnij się, że zmiana `appIdLuis` i `subscriptionKeyLuis`. SubscriptionKey jest klucza wschodnie stany USA usługi LUIS. Jeśli ścieżka nie jest poprawny, w tym klucza identyfikator i punktu końcowego aplikacji usługi LUIS, sondowania usługi Traffic Manager jest stan `degraded` ponieważ Traffic Manager nie może pomyślnie żądania punktu końcowego usługi LUIS. Upewnij się, że wartość `q` jest `traffic-manager-west` można wyświetlić tę wartość w dziennikach punktu końcowego usługi LUIS.

    ```powerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:
    
    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |— Nazwa|Usługa Luis-profile-westus|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-ResourceGroupName|usługi Luis — traffic-manager|Utworzony w poprzedniej sekcji.|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu w usłudze Traffic Manager][routing-methods]. Jeśli używasz wydajności, na żądanie adresu URL do usługi Traffic Manager muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa czatbota lub innych aplikacji, odpowiada chatbot do naśladowania regionu w wywołaniu do usługi Traffic Manager. |
    |-RelativeDnsName|Usługa Luis-dns-westus|Jest to poddomeny dla usługi: Usługa luis-dns-westus.trafficmanager.net|
    |-Czas wygaśnięcia|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port oraz Protokół usługi LUIS jest 443 dla protokołu HTTPS|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Zastąp <appId> i <subscriptionKey> własnymi wartościami. Należy pamiętać, że ten klucz punktu końcowego różni się od klucza punktu końcowego wschód|
    
    Żądania zakończonego powodzeniem ma bez odpowiedzi.

2. Dodaj punkt końcowy zachodnie stany USA, za pomocą **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** polecenia cmdlet

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwapunktukoncowego|Usługa Luis zachód endpoint|Nazwa punktu końcowego, wyświetlana w ramach profilu|
    |-TrafficManagerProfile|$westprofile|Użyj obiektu profil utworzony w kroku 1|
    |-Type|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Traffic Manager][traffic-manager-endpoints] |
    |-Target|westus.API.cognitive.microsoft.com|Jest to domeny dla punktu końcowego usługi LUIS.|
    |-EndpointLocation|"westus"|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włączanie punktu końcowego, podczas jego tworzenia|

    Odpowiedź oznaczająca Powodzenie wygląda następująco:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Ustaw punkt końcowy zachodnie stany USA, z **[polecenia Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Odpowiedź oznaczająca Powodzenie to tę samą odpowiedź w kroku 2.

### <a name="create-parent-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager nadrzędnego
Utwórz element nadrzędny profilu usługi Traffic Manager i połączyć dwa profile usługi Traffic Manager podrzędne obiektu nadrzędnego.

1. Utwórz profil nadrzędnej z **[polecenia New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet

    ```powerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |— Nazwa|Usługa Luis profilu — relacji nadrzędny-|Nazwa usługi Traffic Manager w witrynie Azure portal|
    |-ResourceGroupName|usługi Luis — traffic-manager|Utworzony w poprzedniej sekcji.|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [metody routingu w usłudze Traffic Manager][routing-methods]. Jeśli używasz wydajności, na żądanie adresu URL do usługi Traffic Manager muszą pochodzić z regionu użytkownika. Jeśli pośrednictwa czatbota lub innych aplikacji, odpowiada chatbot do naśladowania regionu w wywołaniu do usługi Traffic Manager. |
    |-RelativeDnsName|Usługa Luis-dns nadrzędnego|Jest to poddomeny dla usługi: Usługa luis-dns-parent.trafficmanager.net|
    |-Czas wygaśnięcia|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port oraz Protokół usługi LUIS jest 443 dla protokołu HTTPS|
    |-MonitorPath|`/`|Ta ścieżka nie ma znaczenia, ponieważ ścieżki punktu końcowego podrzędne są używane zamiast tego.|

    Żądania zakończonego powodzeniem ma bez odpowiedzi.

2. Dodaj profil podrzędnych wschodnie stany USA do elementu nadrzędnego z **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** i **NestedEndpoints** typu

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwapunktukoncowego|podrzędne endpoint-useast|Profil wschód|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |-Type|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |Element TargetResourceId-|$eastprofile. Identyfikator|Identyfikator profilu podrzędne|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego, po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|"eastus"|[Nazwa regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/) zasobu|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Wygląd odpowiedź oznaczająca Powodzenie podobnie do następujących i zawiera nową `child-endpoint-useast` punktu końcowego:    

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Dodaj profil podrzędnych zachodnie stany USA do elementu nadrzędnego z **[Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** polecenia cmdlet i **NestedEndpoints** typu

    ```powerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    W następującej tabeli opisano każdej zmiennej w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Nazwapunktukoncowego|podrzędne endpoint-uswest|Profil zachodnie|
    |-TrafficManagerProfile|$parentprofile|Profil, aby przypisać ten punkt końcowy do|
    |-Type|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |Element TargetResourceId-|$westprofile. Identyfikator|Identyfikator profilu podrzędne|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego, po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|"westus"|[Nazwa regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/) zasobu|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Wygląd pomyślnej odpowiedzi, takie jak i obejmuje zarówno z poprzednich `child-endpoint-useast` punktu końcowego, a nowe `child-endpoint-uswest` punktu końcowego:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Ustaw punkty końcowe z **[polecenia Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** polecenia cmdlet 

    ```powerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Odpowiedź oznaczająca Powodzenie to tę samą odpowiedź w kroku 3.

### <a name="powershell-variables"></a>Zmienne programu PowerShell
W poprzednich sekcjach, zostały utworzone trzy zmienne programu PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Te zmienne są używane w kierunku końca Konfiguracja usługi Traffic Manager. Jeśli zrezygnował z tworzenia zmiennych lub zapomniał lub upłynie limit czasu okna programu PowerShell, możesz użyć polecenia cmdlet programu PowerShell  **[polecenia Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, aby ponownie pobrać profilu i przypisać ją do zmiennej. 

Zastąpienie elementów w nawiasy kątowe `<>`, za pomocą poprawne wartości dla każdego z trzech profilów, potrzebujesz. 

```powerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Sprawdź, czy działa usługa Traffic Manager
Aby sprawdzić, czy praca, profile usługi Traffic Manager, profile, które muszą mieć stan `Online` ten stan jest oparty na ścieżkę sondowania punktu końcowego. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Wyświetl profile nowy w witrynie Azure portal
Możesz sprawdzić, czy wszystkie trzy profile są tworzone, analizując zasoby w `luis-traffic-manager` grupy zasobów.

![Zrzut ekranu usługi Azure resource grupy usługi luis — traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Sprawdź, czy stan profilu jest w trybie Online
Traffic Manager sonduje ścieżkę każdego punktu końcowego, aby upewnić się, że jest w trybie online. Jeśli jest w trybie online, stan profile podrzędne są `Online`. Jest on wyświetlany na **Przegląd** dla każdego profilu. 

![Zrzut ekranu z usługi Azure Traffic Manager profil przedstawiający Monitor stanu elementu Online — omówienie](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Weryfikowanie usługi Traffic Manager działa sondowania
Innym sposobem, aby sprawdzić poprawność działania sondowania usługi traffic manager jest za pomocą dzienników punktu końcowego usługi LUIS. Na [LUIS] [ LUIS] listy aplikacji witryny sieci Web strony, Eksportuj Dziennik punktu końcowego dla aplikacji. Usługa Traffic Manager sonduje często pod kątem dwa punkty końcowe, dlatego są wpisy w dziennikach nawet wtedy, gdy zostały one tylko w ciągu kilku minut. Pamiętaj poszukać wpisów, gdzie zapytanie zaczyna się od `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Zweryfikować odpowiedź DNS z działania usługi Traffic Manager
Aby zweryfikować, że odpowiedź DNS zwraca punkt końcowy usługi LUIS, żądanie ruchu Zarządzanie profilem nadrzędnej DNS przy użyciu biblioteki klienta DNS. Nazwa DNS jest profil nadrzędnego `luis-dns-parent.trafficmanager.net`.

Poniższy kod Node.js sprawia, że żądanie dotyczące profilu nadrzędnego i zwraca punkt końcowy usługi LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Odpowiedź oznaczająca Powodzenie z punktem końcowym usługi LUIS jest:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Użyj profilu usługi Traffic Manager nadrzędnego
Aby można było zarządzać ruchem między punktami końcowymi, należy wstawić numer telefonu w systemie DNS usługi Traffic Manager, aby znaleźć punkt końcowy usługi LUIS. To wywołanie jest wykonywane dla każdego żądania punktu końcowego usługi LUIS i musi zostać symulować lokalizację geograficzną użytkownika aplikacji klienckiej usługi LUIS. Dodaj kod odpowiedzi DNS w zakresie od aplikacji klienckiej usługi LUIS do żądania do usługi LUIS do przewidywania punktu końcowego. 


## <a name="clean-up"></a>Czyszczenie
Usuń dwa klucze punktu końcowego usługi LUIS, trzy profile usługi Traffic Manager i grupę zasobów, która zawiera następujące pięć zasoby. Można to zrobić w witrynie Azure portal. Pięć zasoby możesz usunąć z listy zasobów. Następnie usuń grupę zasobów. 

## <a name="next-steps"></a>Kolejne kroki

Przegląd [oprogramowania pośredniczącego](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) opcje BotFramework v4, aby zrozumieć, jak ten kod zarządzania ruchem, można dodać do BotFramework bot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
