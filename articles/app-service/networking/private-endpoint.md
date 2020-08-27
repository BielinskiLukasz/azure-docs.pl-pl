---
title: Połącz się prywatnie z aplikacją internetową platformy Azure przy użyciu prywatnego punktu końcowego
description: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 08/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 16ce03ad8d6af1908a9ea4d3b7759bb5eb3c1139
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961554"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Używanie prywatnych punktów końcowych dla usługi Azure Web App (wersja zapoznawcza)

> [!Note]
> Po odświeżeniu wersji zapoznawczej wydano funkcję ochrony danych eksfiltracji.
>
> Wersja zapoznawcza jest dostępna we wszystkich regionach publicznych dla PremiumV2 Windows i Linux Web Apps i elastycznych funkcji Premium. 

Możesz użyć prywatnego punktu końcowego dla aplikacji sieci Web platformy Azure, aby umożliwić klientom znajdującym się w sieci prywatnej bezpieczny dostęp do aplikacji za pośrednictwem prywatnego linku. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ruch sieciowy między klientem w sieci prywatnej a aplikacją sieci Web przechodzi przez sieć wirtualną oraz prywatny link w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Korzystanie z prywatnego punktu końcowego dla aplikacji sieci Web umożliwia:

- Zabezpiecz swoją aplikację sieci Web, konfigurując prywatny punkt końcowy, eliminując ujawnianie opinii publicznej.
- Bezpiecznie łącz się z aplikacją internetową z sieci lokalnych, które łączą się z siecią wirtualną za pomocą sieci VPN lub prywatnej komunikacji równorzędnej ExpressRoute.
- Unikaj eksfiltracji danych z sieci wirtualnej. 

Jeśli potrzebujesz bezpiecznego połączenia między siecią wirtualną a aplikacją sieci Web, punkt końcowy usługi jest najprostszym rozwiązaniem. Jeśli trzeba również skontaktować się z aplikacją internetową ze strony lokalnej za pośrednictwem bramy platformy Azure, sieci równorzędnej z regionalną lub wirtualną siecią równorzędną, jest to rozwiązanie.  

Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi][serviceendpoint].

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy to specjalny interfejs sieciowy (NIC) dla aplikacji sieci Web platformy Azure w podsieci w Virtual Network (VNet).
Utworzenie prywatnego punktu końcowego dla aplikacji sieci Web zapewnia bezpieczną łączność między klientami w sieci prywatnej a aplikacją sieci Web. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej.
Połączenie między prywatnym punktem końcowym a aplikacją sieci Web używa bezpiecznego [linku prywatnego][privatelink]. Prywatny punkt końcowy jest używany tylko w przypadku przepływów przychodzących do aplikacji sieci Web. Przepływy wychodzące nie będą używać tego prywatnego punktu końcowego, ale można wstrzyknąć przepływy wychodzące do sieci w innej podsieci za pomocą [funkcji integracji sieci wirtualnej][vnetintegrationfeature].

Podsieć, w której jest podłączony prywatny punkt końcowy, może zawierać inne zasoby, nie jest potrzebna dedykowana pusta podsieć.
Możesz również wdrożyć prywatny punkt końcowy w innym regionie niż aplikacja internetowa. 

> [!Note]
>Funkcja integracji sieci wirtualnej nie może używać tej samej podsieci co prywatny punkt końcowy. jest to ograniczenie funkcji integracji sieci wirtualnej.

Z perspektywy zabezpieczeń:

- Po włączeniu prywatnych punktów końcowych w aplikacji sieci Web należy wyłączyć wszystkie prawa dostępu publicznego.
- Można włączyć wiele prywatnych punktów końcowych w innych sieci wirtualnych i podsieciach, w tym sieci wirtualnych w innych regionach.
- Adres IP karty sieciowej prywatnego punktu końcowego musi być dynamiczny, ale pozostanie taki sam do momentu usunięcia prywatnego punktu końcowego.
- Karta sieciowa prywatnego punktu końcowego nie może mieć skojarzonej sieciowej grupy zabezpieczeń.
- W podsieci, w której znajduje się prywatny punkt końcowy, może być skojarzona sieciowej grupy zabezpieczeń, ale należy wyłączyć wymuszanie zasad sieciowych dla prywatnego punktu końcowego: zobacz [wyłączanie zasad sieciowych dla prywatnych punktów końcowych][disablesecuritype]. W związku z tym nie można filtrować według sieciowej grupy zabezpieczeń dostępu do prywatnego punktu końcowego.
- Po włączeniu prywatnego punktu końcowego w aplikacji sieci Web konfiguracja [ograniczeń dostępu][accessrestrictions] aplikacji sieci Web nie jest szacowana.
- Możesz wyeliminować ryzyko związane z eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły sieciowej grupy zabezpieczeń, w których miejsce docelowe jest znacznikiem internetowym lub usług platformy Azure. Po wdrożeniu prywatnego punktu końcowego dla aplikacji sieci Web można uzyskać dostęp tylko do tej konkretnej aplikacji sieci Web za pomocą prywatnego punktu końcowego. Jeśli masz inną aplikację sieci Web, musisz wdrożyć inny dedykowany prywatny punkt końcowy dla tej innej aplikacji sieci Web.

W dziennikach HTTP sieci Web aplikacji sieci Web znajduje się adres IP źródła klienta. Ta funkcja jest implementowana przy użyciu protokołu proxy TCP, przekazując Właściwość IP klienta do aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie informacji o połączeniu przy użyciu protokołu TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globalny punkt końcowy punktu końcowego aplikacji sieci Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

W przypadku korzystania z prywatnego punktu końcowego dla aplikacji internetowej żądany adres URL musi być zgodny z nazwą aplikacji sieci Web. Domyślnie mywebappname.azurewebsites.net.

Domyślnie, bez prywatnego punktu końcowego, publiczna nazwa aplikacji sieci Web jest nazwą kanoniczną klastra.
Na przykład rozpoznawanie nazw będzie:

|Nazwa |Typ |Wartość |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Podczas wdrażania prywatnego punktu końcowego aktualizujemy wpis DNS w taki sposób, aby wskazywał nazwę kanoniczną mywebapp.privatelink.azurewebsites.net.
Na przykład rozpoznawanie nazw będzie:

|Nazwa |Typ |Wartość |Dyskusji |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|< — ten publiczny adres IP nie jest prywatnym punktem końcowym, zostanie wyświetlony błąd 403|

Należy skonfigurować prywatny serwer DNS lub Azure DNS strefę prywatną, w przypadku testów można zmodyfikować wpis hosta maszyny testowej.
Należy utworzyć strefę DNS: **privatelink.azurewebsites.NET**. Zarejestruj rekord aplikacji sieci Web przy użyciu rekordu a i prywatnego adresu IP punktu końcowego.
Na przykład rozpoznawanie nazw będzie:

|Nazwa |Typ |Wartość |Dyskusji |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|< — zarządzanie tym wpisem w systemie DNS w celu wskazywania prywatnego adresu IP punktu końcowego|

Po tej konfiguracji DNS można skontaktować się z Twoją aplikacją internetową, korzystając z nazwy domyślnej mywebappname.azurewebsites.net.


Jeśli musisz użyć niestandardowej nazwy DNS, musisz dodać nazwę niestandardową w aplikacji sieci Web. W trakcie korzystania z wersji zapoznawczej Nazwa niestandardowa musi być zweryfikowana, podobnie jak nazwa niestandardowa, przy użyciu publicznego rozpoznawania DNS. Aby uzyskać więcej informacji, zobacz [niestandardowe sprawdzanie poprawności nazw DNS][dnsvalidation].

W przypadku konsoli usługi kudu lub interfejsu API REST (na przykład wdrożenia z użyciem platformy Azure DevOps dla agentów) należy utworzyć dwa rekordy w strefie prywatnej Azure DNS lub na niestandardowym serwerze DNS. 

| Nazwa | Typ | Wartość |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link][pricing].

## <a name="limitations"></a>Ograniczenia

Gdy korzystasz z funkcji platformy Azure w elastycznym planie Premium z prywatnym punktem końcowym, aby uruchomić lub wykonać funkcję w portalu internetowym platformy Azure, musisz mieć bezpośredni dostęp do sieci lub otrzymać błąd HTTP 403. Innymi słowy, przeglądarka musi mieć możliwość nawiązania połączenia z prywatnym punktem końcowym w celu wykonania funkcji w portalu internetowym platformy Azure. 

W trakcie okresu zapoznawczego tylko miejsce produkcyjne jest widoczne za prywatnym punktem końcowym, a inne gniazda muszą być osiągalne przez publiczny punkt końcowy.

Regularnie udoskonalamy funkcję prywatnego linku i prywatnego punktu końcowego. Zapoznaj się z [tym artykułem][pllimitations] , aby uzyskać aktualne informacje o ograniczeniach.

## <a name="next-steps"></a>Kolejne kroki

- Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web za pomocą portalu, zobacz [jak połączyć się prywatnie z aplikacją sieci Web za pomocą portalu][howtoguide1]
- Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [jak połączyć się prywatnie z aplikacją internetową przy użyciu interfejsu wiersza polecenia platformy Azure][howtoguide2]
- Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu programu PowerShell, zobacz [jak połączyć się prywatnie z aplikacją sieci Web za pomocą programu PowerShell][howtoguide3]
- Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu szablonu platformy Azure, zobacz [jak połączyć się prywatnie z aplikacją internetową przy użyciu szablonu platformy Azure][howtoguide4]
- Przykład kompleksowego sposobu łączenia aplikacji sieci Web frontonu z zabezpieczoną aplikacją sieci Web zaplecza przy użyciu iniekcji wirtualnej i prywatnego punktu końcowego z szablonem ARM, zobacz ten [Przewodnik Szybki Start][howtoguide5]
- Przykład kompleksowego sposobu łączenia aplikacji sieci Web frontonu z zabezpieczoną aplikacją sieci Web zaplecza przy użyciu iniekcji wirtualnej i prywatnego punktu końcowego z Terraform, zobacz ten [przykład][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/create-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md