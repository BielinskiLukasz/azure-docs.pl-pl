---
title: Połączenia hybrydowe — Azure App Service | Microsoft Docs
description: Jak utworzyć i używać Połączenia hybrydowe do uzyskiwania dostępu do zasobów w różnych sieciach
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 72874e7b96e2ec8909a325b5ae598b900ebe8079
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791895"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Połączenia hybrydowe #

Połączenia hybrydowe to usługa na platformie Azure i funkcja w Azure App Service. Jako usługa, ma ona użycie i możliwości poza tymi, które są używane w App Service. Aby dowiedzieć się więcej na temat Połączenia hybrydowe i ich użycia poza App Service, zobacz [Azure Relay połączenia hybrydowe][HCService].

W App Service Połączenia hybrydowe może służyć do uzyskiwania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie umożliwia alternatywnej możliwości uzyskiwania dostępu do aplikacji. Jak w App Service każde połączenie hybrydowe jest skorelowane z pojedynczym hostem TCP i kombinacją portów. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, pod warunkiem, że uzyskujesz dostęp do portu nasłuchiwania protokołu TCP. Funkcja Połączenia hybrydowe nie wie ani nie posługuje się z protokołem aplikacji lub dostępem do niego. Zapewnia to po prostu dostęp do sieci.  


## <a name="how-it-works"></a>Zasady działania ##
Funkcja Połączenia hybrydowe obejmuje dwa wywołania wychodzące do przekaźnika Azure Service Bus. Istnieje połączenie z biblioteki na hoście, na którym aplikacja działa w App Service. Istnieje również połączenie z Menedżer połączeń hybrydowych (HCM) do Service Bus Relay. HCM to usługa przekaźnikowa, która jest wdrażana w sieci obsługującej zasób, do którego próbujesz uzyskać dostęp. 

Za pośrednictwem dwóch dołączonych połączeń aplikacja ma tunel TCP do stałego hosta: kombinacja portów po drugiej stronie HCM. W ramach uwierzytelniania i autoryzacji połączenie używa protokołu TLS 1,2 dla zabezpieczeń i kluczy sygnatury dostępu współdzielonego (SAS).    

![Diagram przepływu wysokiego poziomu połączenia hybrydowego][1]

Gdy aplikacja wysyła żądanie DNS pasujące do skonfigurowanego punktu końcowego połączenia hybrydowego, ruch wychodzący TCP zostanie przekierowany przez połączenie hybrydowe.  

> [!NOTE]
> Oznacza to, że należy próbować zawsze używać nazwy DNS dla połączenia hybrydowego. Niektóre oprogramowanie klienckie nie wykonuje wyszukiwania DNS, jeśli punkt końcowy używa adresu IP. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service korzyści z połączeń hybrydowych ###

Istnieje szereg korzyści dla Połączenia hybrydowe możliwości, w tym:

- Aplikacje mogą bezpiecznie uzyskać dostęp do lokalnych systemów i usług.
- Ta funkcja nie wymaga punktu końcowego dostępnego z Internetu.
- Konfiguracja jest szybka i łatwa. 
- Każde połączenie hybrydowe jest zgodne z pojedynczym hostem: kombinacją portów, przydatną dla zabezpieczeń.
- Zwykle nie wymaga to otworów zapory. Połączenia są wychodzące przez standardowe porty sieci Web.
- Ponieważ ta funkcja jest poziomem sieci, jest niezależny od do języka używanego przez aplikację i technologii używanej przez punkt końcowy.
- Może służyć do zapewnienia dostępu w wielu sieciach z pojedynczej aplikacji. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Elementy, których nie można wykonać za pomocą Połączenia hybrydowe ###

Elementy, które nie są dostępne w Połączenia hybrydowe obejmują:

- Zainstaluj dysk.
- Użyj protokołu UDP.
- Uzyskaj dostęp do usług opartych na protokole TCP, które używają portów dynamicznych, takich jak tryb pasywny FTP lub rozszerzony tryb pasywny.
- Obsługa protokołu LDAP, ponieważ może to wymagać protokołu UDP.
- Active Directory obsługi, ponieważ nie można przyłączyć się do domeny App Service Worker.

### <a name="prerequisites"></a>Wymagania wstępne ###
 - Wymagana jest usługa aplikacji systemu Windows. Jest on dostępny tylko w systemie Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Dodawanie i tworzenie Połączenia hybrydowe w aplikacji ##

Aby utworzyć połączenie hybrydowe, przejdź do [Azure Portal][portal] i wybierz aplikację. Wybierz pozycję **sieć** > **Skonfiguruj punkty końcowe połączenia hybrydowego**. Tutaj możesz zobaczyć Połączenia hybrydowe, które są skonfigurowane dla Twojej aplikacji.  

![Zrzut ekranu listy połączeń hybrydowych][2]

Aby dodać nowe połączenie hybrydowe, wybierz pozycję **[+] Dodaj połączenie hybrydowe**.  Zostanie wyświetlona lista Połączenia hybrydowe, które zostały już utworzone. Aby dodać jeden lub więcej z nich do aplikacji, wybierz odpowiednie opcje, a następnie wybierz pozycję **Dodaj wybrane połączenie hybrydowe**.  

![Zrzut ekranu portalu połączenia hybrydowego][3]

Jeśli chcesz utworzyć nowe połączenie hybrydowe, wybierz pozycję **Utwórz nowe połączenie hybrydowe**. Określ: 

- Nazwa połączenia hybrydowego.
- Nazwa hosta punktu końcowego.
- Port punktu końcowego.
- Service Bus przestrzeń nazw, której chcesz użyć.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowego połączenia hybrydowego][4]

Każde połączenie hybrydowe jest powiązane z przestrzenią nazw Service Bus, a każda Service Bus przestrzeń nazw znajduje się w regionie świadczenia usługi Azure. Ważne jest, aby próbować użyć przestrzeni nazw Service Bus w tym samym regionie, w którym znajduje się aplikacja, aby uniknąć opóźnień w sieci.

Jeśli chcesz usunąć połączenie hybrydowe z aplikacji, kliknij ją prawym przyciskiem myszy, a następnie wybierz polecenie **Rozłącz**.  

Po dodaniu połączenia hybrydowego do aplikacji możesz wyświetlić szczegółowe informacje na jego temat, zaznaczając je. 

![Zrzut ekranu przedstawiający szczegóły połączeń hybrydowych][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Tworzenie połączenia hybrydowego w portalu Azure Relay ###

Oprócz środowiska portalu z poziomu aplikacji można utworzyć Połączenia hybrydowe z poziomu portalu Azure Relay. Aby połączenie hybrydowe było używane przez App Service, musi:

* Wymagaj autoryzacji klienta.
* Posiadanie elementu metadanych o nazwie Endpoint, który zawiera wartość parametru Host: port.

## <a name="hybrid-connections-and-app-service-plans"></a>Plany Połączenia hybrydowe i App Service ##

App Service Połączenia hybrydowe są dostępne tylko w jednostkach SKU w warstwach Podstawowa, standardowa, Premium i izolowanych. Istnieją ograniczenia związane z planem cenowym.  

| Plan cenowy | Liczba Połączenia hybrydowe użytecznych w planie |
|----|----|
| Basic | 5 |
| Standardowa (Standard) | 25 |
| Premium | 200 |
| Izolowane | 200 |

Interfejs użytkownika planu App Service pokazuje, ile Połączenia hybrydowe są używane, a także aplikacji.  

![Zrzut ekranu przedstawiający właściwości planu App Service][6]

Wybierz połączenie hybrydowe, aby wyświetlić szczegóły. Możesz zobaczyć wszystkie informacje, które zostały wyświetlone w widoku aplikacji. Możesz również sprawdzić, ile innych aplikacji w tym samym planie używa tego połączenia hybrydowego.

Istnieje limit liczby punktów końcowych połączenia hybrydowego, które mogą być używane w planie App Service. Można jednak używać poszczególnych połączeń hybrydowych w ramach dowolnej liczby aplikacji w tym planie. Na przykład pojedyncze połączenie hybrydowe używane w pięciu oddzielnych aplikacjach w planie App Service jest traktowane jako jedno połączenie hybrydowe.

### <a name="pricing"></a>Cennik ###

Oprócz tego, że jest wymagane App Service planu SKU, istnieje dodatkowy koszt korzystania z Połączenia hybrydowe. Jest naliczana opłata za każdy odbiornik używany przez połączenie hybrydowe. Odbiornik jest Menedżer połączeń hybrydowych. Jeśli masz pięć Połączenia hybrydowe obsługiwanych przez dwóch menedżerów połączeń hybrydowych, które byłyby 10 odbiorników. Aby uzyskać więcej informacji, zobacz [Cennik usługi Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Menedżer połączeń hybrydowych ##

Funkcja Połączenia hybrydowe wymaga agenta przekazywania w sieci, który hostuje punkt końcowy połączenia hybrydowego. Agent przekazywania ma nazwę Menedżer połączeń hybrydowych (HCM). Aby pobrać HCM, z aplikacji w [Azure Portal][portal]wybierz pozycję **Sieć** > **skonfigurować punkty końcowe połączenia hybrydowego**.  

To narzędzie działa w systemie Windows Server 2012 lub nowszym. HCM działa jako usługa i łączy ruch wychodzący do Azure Relay na porcie 443.  

Po zainstalowaniu HCM można uruchomić program HybridConnectionManagerUi. exe, aby użyć interfejsu użytkownika dla narzędzia. Ten plik znajduje się w katalogu instalacyjnym programu Menedżer połączeń hybrydowych. W systemie Windows 10 można również wyszukać *Menedżer połączeń hybrydowych interfejs użytkownika* w polu wyszukiwania.  

![Zrzut ekranu przedstawiający Menedżer połączeń hybrydowych][7]

Po uruchomieniu interfejsu użytkownika HCM pierwszy element jest widoczny w tabeli, która zawiera listę wszystkich Połączenia hybrydowe, które są skonfigurowane za pomocą tego wystąpienia HCM. Jeśli chcesz wprowadzić zmiany, najpierw Uwierzytelnij się na platformie Azure. 

Aby dodać jeden lub więcej Połączenia hybrydowe do HCM:

1. Uruchom interfejs użytkownika HCM.
2. Wybierz opcję **skonfiguruj inne połączenie hybrydowe**.
Zrzut ekranu ![konfigurowania nowych Połączenia hybrydowe][8]

1. Zaloguj się przy użyciu konta platformy Azure, aby uzyskać Połączenia hybrydowe dostęp do Twoich subskrypcji. HCM nie będzie nadal korzystać z konta platformy Azure poza tym. 
1. wybierz subskrypcję.
1. Wybierz Połączenia hybrydowe, które mają być przekazywane przez HCM.
![zrzut ekranu Połączenia hybrydowe][9]

1. Wybierz pozycję **Zapisz**.

Teraz możesz zobaczyć dodaną Połączenia hybrydowe. Możesz również wybrać skonfigurowane połączenie hybrydowe, aby wyświetlić szczegóły.

![Zrzut ekranu przedstawiający szczegóły połączenia hybrydowego][10]

Aby można było obsługiwać Połączenia hybrydowe, które jest skonfigurowane z programem, HCM wymaga:

- Dostęp TCP do platformy Azure przez port 443.
- Dostęp TCP do punktu końcowego połączenia hybrydowego.
- Możliwość wyszukiwania w systemie DNS na hoście punktu końcowego i przestrzeni nazw Service Bus.

> [!NOTE]
> Azure Relay korzysta z gniazd sieci Web na potrzeby łączności. Ta funkcja jest dostępna tylko w systemie Windows Server 2012 lub nowszym. Z tego powodu HCM nie jest obsługiwany w żadnym systemie Windows Server 2012.
>

### <a name="redundancy"></a>Nadmiarowość ###

Każdy HCM może obsługiwać wiele Połączenia hybrydowe. Ponadto każde połączenie hybrydowe może być obsługiwane przez wiele HCMs. Domyślne zachowanie polega na kierowaniu ruchu sieciowego przez skonfigurowany HCMs dla danego punktu końcowego. Jeśli potrzebujesz wysokiej dostępności na Połączenia hybrydowe z sieci, uruchom wiele HCMs na oddzielnych komputerach. Algorytm dystrybucji obciążenia używany przez usługę przekazywania do dystrybucji ruchu do HCMs jest przypisaniem losowym. 

### <a name="manually-add-a-hybrid-connection"></a>Ręczne dodawanie połączenia hybrydowego ###

Aby umożliwić komuś spoza subskrypcji hostowanie wystąpienia HCM dla danego połączenia hybrydowego, należy udostępnić parametry połączenia bramy dla połączenia hybrydowego. Parametry połączenia bramy są widoczne we właściwościach połączenia hybrydowego w [Azure Portal][portal]. Aby użyć tego ciągu, wybierz opcję **wprowadź ręcznie** w HCM i wklej w parametrach połączenia bramy.

![Ręczne dodawanie połączenia hybrydowego][11]

### <a name="upgrade"></a>Uaktualnienie ###

Istnieją okresowe aktualizacje Menedżer połączeń hybrydowych w celu rozwiązania problemów lub zapewnienia ulepszeń. Po wydaniu uaktualnień zostanie wyświetlone okno podręczne w interfejsie użytkownika HCM. Zastosowanie uaktualnienia spowoduje zastosowanie zmian i ponowne uruchomienie HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programistyczne Dodawanie połączenia hybrydowego do aplikacji ##

Wymienione poniżej interfejsy API mogą być używane bezpośrednio do zarządzania Połączenia hybrydowe połączonymi z aplikacjami. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Obiekt JSON skojarzony z połączeniem hybrydowym wygląda następująco:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Jednym ze sposobów korzystania z tych informacji jest armclient, które można uzyskać z projektu usługi GitHub [armclient][armclient] . Oto przykład na temat dołączania istniejącego połączenia hybrydowego do aplikacji. Utwórz plik JSON na powyższym schemacie, taki jak:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Aby użyć tego interfejsu API, wymagany jest identyfikator wysyłania klucza i zasobu przekaźnika. Jeśli Zapisano informacje w pliku hctest. JSON, wydaj to polecenie, aby dołączyć połączenie hybrydowe do swojej aplikacji: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Rozwiązywanie problemów ##

Stan "połączone" oznacza, że co najmniej jeden HCM jest skonfigurowany przy użyciu tego połączenia hybrydowego i jest w stanie uzyskać dostęp do platformy Azure. Jeśli stan połączenia hybrydowego nie jest **połączony**, połączenie hybrydowe nie zostanie skonfigurowane na żadnym HCM, który ma dostęp do platformy Azure.

Głównym powodem, że klienci nie mogą połączyć się z punktem końcowym, jest fakt, że punkt końcowy został określony przy użyciu adresu IP zamiast nazwy DNS. Jeśli aplikacja nie może nawiązać połączenia z żądanym punktem końcowym i został użyty adres IP, przełącz się do korzystania z nazwy DNS, która jest prawidłowa na hoście, na którym działa HCM. Sprawdź również, czy nazwa DNS jest rozpoznawana poprawnie na hoście, na którym działa HCM. Upewnij się, że nawiązano połączenie z hostem, na którym uruchomiono HCM, do punktu końcowego połączenia hybrydowego.  

W App Service narzędzie wiersza polecenia **tcpping** można wywołać z poziomu konsoli narzędzia zaawansowane (kudu). To narzędzie może powiedzieć, czy masz dostęp do punktu końcowego TCP, ale nie informuje Cię o tym, czy masz dostęp do punktu końcowego połączenia hybrydowego. Gdy korzystasz z narzędzia w konsoli programu względem punktu końcowego połączenia hybrydowego, potwierdzasz, że używa kombinacji hosta: port.  

Jeśli masz klienta wiersza polecenia dla punktu końcowego, możesz przetestować łączność z poziomu konsoli aplikacji. Na przykład można testować dostęp do punktów końcowych serwera sieci Web za pomocą zwinięcia.

## <a name="biztalk-hybrid-connections"></a>Połączenia hybrydowe BizTalk ##

Wczesna forma tej funkcji była nazywana Połączenia hybrydowe BizTalk. Ta możliwość przestała obowiązywać z dniem 31 maja 2018 i zaprzestanie operacji. Połączenia hybrydowe BizTalk zostały usunięte ze wszystkich aplikacji i nie są dostępne za pośrednictwem portalu lub interfejsu API. Jeśli nadal masz te starsze połączenia skonfigurowane w Menedżer połączeń hybrydowych, zobaczysz stan wycofane i wyświetlona zostanie instrukcja końca okresu ważności u dołu.

![Połączenia hybrydowe BizTalk w HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
