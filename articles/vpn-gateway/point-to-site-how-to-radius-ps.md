---
title: 'Łączenie komputera z siecią wirtualną przy użyciu połączenia typu punkt-lokacja i usługi RADIUS: PowerShell | Azure'
description: Bezpieczne łączenie klientów systemu Windows i Mac OS X z siecią wirtualną przy użyciu uwierzytelniania P2S i usługi RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 1f55b8963ad9f940202816704c5818c6853ffcde
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353695"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania usługi RADIUS: PowerShell

W tym artykule opisano sposób tworzenia sieci wirtualnej z połączeniem typu punkt-lokacja korzystającego z uwierzytelniania usługi RADIUS. Ta konfiguracja jest dostępna tylko dla Menedżer zasobówgo modelu wdrażania.

Brama sieci VPN typu punkt-lokacja (P2S, Point-to-Site) pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN typu punkt-lokacja są przydatne, gdy chcesz nawiązać połączenie z siecią wirtualną z lokalizacji zdalnej, na przykład podczas komunikacji z domu lub z konferencji. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną.

Połączenie sieci VPN typu punkt-lokacja jest uruchamiane z urządzeń z systemem Windows i urządzeń Mac. Przy łączeniu klientów mogą być używane następujące metody uwierzytelniania: 

* Serwer RADIUS
* VPN Gateway natywne uwierzytelnianie certyfikatu

Ten artykuł ułatwia skonfigurowanie konfiguracji P2S z uwierzytelnianiem przy użyciu serwera RADIUS. Jeśli zamiast tego chcesz uwierzytelniać się przy użyciu wygenerowanych certyfikatów i natywnego uwierzytelniania certyfikatu bramy sieci VPN, zobacz [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatu bramy sieci VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram połączenia — promień](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. P2S tworzy połączenie sieci VPN za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol), OpenVPN lub IKEv2.

* Protokół SSTP to tunel sieci VPN bazujący na protokole SSL, który jest obsługiwany wyłącznie na platformach klienckich Windows. Może przechodzić przez zapory, co czyni go idealnym do nawiązywania połączenia z platformą Azure z dowolnego miejsca. Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2.

* OpenVPN® protokół sieci VPN oparty na protokole SSL/TLS. Rozwiązanie sieci VPN z protokołem SSL może przeniknąć zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, który jest używany przez protokół SSL. OpenVPN można użyć do nawiązania połączenia z urządzeń z systemami Android, iOS (wersja 11,0 i nowsze), Windows, Linux i Mac (OSX wersje 10,13 i nowsze).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

Dla połączeń punkt-lokacja wymagane są następujące elementy:

* Brama sieci VPN oparta na trasie. 
* Serwer RADIUS obsługujący uwierzytelnianie użytkowników. Serwer RADIUS można wdrożyć lokalnie lub w sieci wirtualnej platformy Azure.
* Pakiet konfiguracji klienta sieci VPN dla urządzeń z systemem Windows, które będą łączyć się z siecią wirtualną. Pakiet konfiguracji klienta sieci VPN udostępnia ustawienia wymagane przez klienta VPN do łączenia się za pośrednictwem usługi P2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>Informacje o uwierzytelnianiu domeny Active Directory (AD) dla sieci VPN P2S

Uwierzytelnianie domeny usługi AD umożliwia użytkownikom logowanie się na platformie Azure przy użyciu poświadczeń domeny organizacji. Wymaga serwera RADIUS, który integruje się z serwerem usługi AD. Organizacje mogą również korzystać z istniejącego wdrożenia usługi RADIUS.
 
Serwer RADIUS może znajdować się lokalnie lub w sieci wirtualnej platformy Azure. Podczas uwierzytelniania Brama sieci VPN działa jako przekazujący i przesyła dalej komunikaty uwierzytelniania między serwerem RADIUS a urządzeniem łączącym. Ważne jest, aby Brama sieci VPN mogła dotrzeć do serwera RADIUS. Jeśli serwer usługi RADIUS znajduje się w środowisku lokalnym, wymagane jest połączenie sieci VPN typu lokacja-lokacja z platformy Azure z lokacją lokalną.

Oprócz Active Directory serwer RADIUS można także zintegrować z innymi zewnętrznymi systemami tożsamości. Spowoduje to otwarcie dużej liczby opcji uwierzytelniania dla sieci VPN typu punkt-lokacja, w tym opcji MFA. Zapoznaj się z dokumentacją dostawcy serwera RADIUS, aby uzyskać listę systemów tożsamości, z którymi integruje się.

![Diagram połączenia — promień](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Tylko połączenie sieci VPN typu lokacja-lokacja może służyć do łączenia się z lokalnym serwerem RADIUS. Nie można użyć połączenia ExpressRoute.
>
>

## <a name="before"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>Przykładowe wartości

Wartości przykładowych możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule. Można postępować zgodnie z opisanymi krokami i użyć przedstawionych wartości bez ich zmieniania lub zmienić je, aby odzwierciedlały dane środowisko.

* **Nazwa: VNet1**
* **Przestrzeń adresowa: 192.168.0.0/16** i **10.254.0.0/16**<br>W tym przykładzie używamy więcej niż jednej przestrzeni adresowej, aby zilustrować, że ta konfiguracja współpracuje z wieloma przestrzeniami adresowymi. Jednak ta konfiguracja nie wymaga wielu przestrzeni adresowych.
* **Nazwa podsieci: FrontEnd**
  * **Zakres adresów podsieci: 192.168.1.0/24**
* **Nazwa podsieci: BackEnd**
  * **Zakres adresów podsieci: 10.254.1.0/24**
* **Nazwa podsieci: GatewaySubnet**<br>Nazwa podsieci *GatewaySubnet* jest obowiązkowa, aby brama VPN mogła działać.
  * **Zakres adresów podsieci bramy: 192.168.200.0/24** 
* **Pula adresów klienta sieci VPN: 172.16.201.0/24**<br>Klienci sieci VPN łączący się z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów sieci VPN.
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów: TestRG**
* **Lokalizacja: Wschodnie stany USA**
* **Serwer DNS: adres IP** serwera DNS, który ma być używany do rozpoznawania nazw dla sieci wirtualnej. (opcjonalnie)
* **Nazwa bramy: Vnet1GW**
* **Nazwa publicznego adresu IP: VNet1GWPIP**
* **VpnType: RouteBased**


## <a name="signin"></a>Logowanie i Ustawianie zmiennych

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Deklarowanie zmiennych

Zadeklaruj zmienne, których chcesz użyć. Użyj poniższego przykładu, podstawiając własne wartości tam, gdzie to konieczne. Jeśli zamkniesz sesję programu PowerShell/Cloud Shell w dowolnym momencie podczas wykonywania, po prostu skopiuj i wklej ponownie wartości, aby ponownie zadeklarować zmienne.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## 1. <a name="vnet"> </a>Tworzenie grupy zasobów, sieci wirtualnej i publicznego adresu IP

Poniższe kroki tworzą grupę zasobów i sieć wirtualną w grupie zasobów z trzema podsieciami. Podczas podstawiania wartości ważne jest, aby nazwa podsieci bramy była zawsze określana jako "GatewaySubnet". Jeśli nastąpi inna nazwa, tworzenie bramy nie powiedzie się.

1. Utwórz grupę zasobów.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Utwórz konfiguracje podsieci dla sieci wirtualnej, nadając im nazwy *FrontEnd*, *BackEnd* i *GatewaySubnet*. Prefiksy te muszą być częścią zadeklarowanej przestrzeni adresowej sieci wirtualnej.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Utwórz sieć wirtualną.

   W tym przykładzie parametr serwera -DnsServer jest opcjonalny. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie z Twojej sieci wirtualnej. W tym przykładzie użyto prywatnego adresu IP, ale może to nie być adres IP Twojego serwera DNS. Pamiętaj, aby użyć własnych wartości. Określona wartość jest używana przez zasoby wdrażane w sieci wirtualnej, a nie przez połączenie P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

   Określ zmienne, aby zażądać dynamicznie przypisanego publicznego adresu IP.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## 2. <a name="radius"> </a>Skonfiguruj serwer RADIUS

Przed utworzeniem i skonfigurowaniem bramy sieci wirtualnej należy prawidłowo skonfigurować serwer RADIUS na potrzeby uwierzytelniania.

1. Jeśli nie masz wdrożonego serwera usługi RADIUS, wdróż go. Procedurę wdrażania można znaleźć w przewodniku instalacji dostarczonym przez dostawcę usługi RADIUS.  
2. Skonfiguruj bramę sieci VPN jako klienta RADIUS na serwerze RADIUS. Podczas dodawania tego klienta usługi RADIUS należy określić utworzoną sieć wirtualną GatewaySubnet. 
3. Po skonfigurowaniu serwera RADIUS Pobierz adres IP serwera RADIUS i wspólny klucz tajny, który powinni używać klienci usługi RADIUS do komunikowania się z serwerem RADIUS. Jeśli serwer RADIUS należy do sieci wirtualnej platformy Azure, użyj adresu IP urzędu certyfikacji serwera usługi RADIUS.

Artykuł [serwera zasad sieciowych (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) zawiera wskazówki dotyczące konfigurowania serwera usługi Windows RADIUS (NPS) na potrzeby uwierzytelniania domeny usługi AD.

## 3. <a name="creategw"> </a>tworzenie bramy sieci VPN

Skonfiguruj i Utwórz bramę sieci VPN dla sieci wirtualnej.

* -Gatewaytype musi mieć wartość "VPN", a-VpnType musi mieć wartość "RouteBased".
* Ukończenie bramy sieci VPN może potrwać do 45 minut, w zależności od wybranej [jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku) .

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"> </a>Dodaj serwer RADIUS i pulę adresów klienta
 
* Wartość parametru-RadiusServer można określić według nazwy lub adresu IP. Jeśli określisz, że nazwa i serwer znajdują się lokalnie, Brama sieci VPN może nie być w stanie rozpoznać nazwy. W takim przypadku lepiej jest określić adres IP serwera programu. 
* Parametr-RadiusSecret powinien być zgodny z konfiguracją na serwerze RADIUS.
* -VpnClientAddressPool jest zakresem, z którego łączący się klienci sieci VPN otrzymują adres IP. Użyj prywatnego zakresu adresów IP, który nie nakłada się na lokalizację lokalną, z której będziesz się łączyć, lub z siecią wirtualną, z którą chcesz nawiązać połączenie. Upewnij się, że skonfigurowano wystarczającą dla siebie pulę adresów.  

1. Utwórz bezpieczny ciąg dla wpisu tajnego usługi RADIUS.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Zostanie wyświetlony monit o wprowadzenie klucza tajnego usługi RADIUS. Wprowadzone znaki nie będą wyświetlane, a zamiast tego zostaną zastąpione znakiem "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Dodaj pulę adresów klienta sieci VPN i informacje o serwerze RADIUS.

   W przypadku konfiguracji protokołu SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   W przypadku konfiguracji® OpenVPN:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   W przypadku konfiguracji protokołu IKEv2:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   W przypadku protokołu SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"> </a>Pobierz pakiet konfiguracji klienta sieci VPN i Skonfiguruj klienta sieci VPN

Konfiguracja klienta sieci VPN umożliwia urządzeniom łączenie się z siecią wirtualną za pośrednictwem połączenia P2S. Aby wygenerować pakiet konfiguracji klienta sieci VPN i skonfigurować klienta sieci VPN, zobacz [Tworzenie konfiguracji klienta sieci VPN na potrzeby uwierzytelniania usługi RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Nawiązywanie połączenia z platformą Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Aby połączyć się z klienta sieci VPN w systemie Windows

1. Aby nawiązać połączenie z siecią wirtualną na komputerze klienckim, przejdź do połączeń sieci VPN i wyszukaj wcześniej utworzone połączenie sieci VPN. Połączenie będzie miało taką samą nazwę jak sieć wirtualna. Wprowadź poświadczenia domeny i kliknij przycisk "Połącz". Zostanie wyświetlony komunikat podręczny z żądaniem podwyższonego poziomu uprawnień. Zaakceptuj ją i wprowadź poświadczenia.

   ![Łączenie klienta sieci VPN z platformą Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Połączenie zostało ustanowione.

   ![Ustanowiono połączenie](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Nawiązywanie połączenia z klienta sieci VPN dla komputerów Mac

W oknie dialogowym Sieć znajdź profil klienta, którego chcesz użyć, a następnie kliknij polecenie **Połącz**.

  ![Połączenie z komputerem Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Aby zweryfikować połączenie

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP należy do określonej podczas konfiguracji połączenia punkt-lokacja puli adresów klienta sieci VPN. Wyniki są podobne, jak w następującym przykładzie:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Aby rozwiązać problem z połączeniem P2S, zobacz [Rozwiązywanie problemów z połączeniami typu punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczą P2S przy użyciu uwierzytelniania usługi RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).
