---
title: Tworzenie połączenia punkt-lokacja z platformą Azure w usłudze Azure Virtual WAN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć połączenie punkt-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 8a4c0c1426200e6c2d5041131fd0dd9cde4761cf
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409290"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Samouczek: tworzenie połączenia punkt-lokacja przy użyciu usługi Azure Virtual WAN (wersja zapoznawcza)

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta na komputerze klienckim. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie konfiguracji P2S
> * Tworzenie koncentratora
> * Stosowanie konfiguracji P2S do koncentratora
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji klienta sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Rejestrowanie tej funkcji

Kliknij pozycję **Wypróbuj**, aby łatwo zarejestrować tę funkcję za pomocą usługi Azure Cloud Shell.

>[!NOTE]
>Jeśli nie zarejestrujesz tej funkcji, nie będzie można jej używać ani wyświetlać w portalu.
>
>

Po kliknięciu pozycji **Wypróbuj** w celu otwarcia usługi Azure Cloud Shell skopiuj i wklej następujące polecenia:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Gdy funkcja będzie widoczna jako zarejestrowana, ponownie zarejestruj subskrypcję w przestrzeni nazw Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Tworzenie sieci wirtualnej

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](http://aka.ms/azurevirtualwanpreviewfeatures) (wersja zapoznawcza) i zaloguj się przy użyciu konta platformy Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Tworzenie koncentratora

> [!NOTE]
> W tym kroku nie należy wybierać ustawienia „Uwzględnij bramę punkt-lokacja”.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Tworzenie konfiguracji P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Przejdź do pozycji **Wszystkie zasoby**.
2. Kliknij utworzoną wirtualną sieć WAN.
3. W obszarze **Architektura wirtualnej sieci WAN**, kliknij przycisk **Konfiguracje punkt-lokacja**.
4. Kliknij przycisk **+ Dodaj konfigurację punkt-lokacja** w górnej części strony, aby otworzyć stronę **Utwórz nową konfigurację punkt-lokacja**.
5. Na stronie **Utwórz nową konfigurację punkt-lokacja**, wypełnij następujące pola:

  *  **Nazwa konfiguracji** — jest to nazwa używana w celu odwoływania się do konfiguracji.
  *  **Typ tunelu** — protokół używany w przypadku tunelu.
  *  **Pula adresów** — jest to pula adresów IP, z której będą przypisywani klienci.
  *  **Nazwa certyfikatu głównego** — opisowa nazwa certyfikatu.
  *  **Dane certyfikatu głównego** — dane certyfikatu X.509 szyfrowanego algorytmem Base-64.

5. Kliknij przycisk **Utwórz**, aby utworzyć konfigurację.

## <a name="hub"></a>5. Edytowanie przypisania koncentratora

1. Na stronie Twojej wirtualnej sieci WAN kliknij opcję **Konfiguracje punkt-lokacja**.
2. W obszarze **Koncentrator** zostanie wyświetlona lista konfiguracji, które jeszcze nie zostały podłączone do koncentratora.
3. Wybierz konfigurację, którą chcesz skojarzyć, a następnie kliknij przycisk **Edytuj przypisanie koncentratora**.
4. Z listy rozwijanej wybierz koncentratory, które chcesz skojarzyć z konfiguracją.
5. Kliknij przycisk **Przypisz**. 
6. Ukończenie operacji może potrwać maksymalnie 30 minut.

## <a name="vnet"></a>6. Łączenie sieci wirtualnej z koncentratorem

W tym kroku zostanie utworzone połączenie równorzędne pomiędzy koncentratorem i siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie usługi Virtual WAN kliknij pozycję **Połączenie sieci wirtualnej**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.

## <a name="device"></a>7. Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie Twojej wirtualnej sieci WAN kliknij opcję **Konfiguracje punkt-lokacja**.
2. W górnej części strony kliknij polecenie **Pobierz profil punkt-lokacja**. 
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Aby skonfigurować klientów punkt-lokacja, należy użyć pliku profilu.

## <a name="device"></a>8. Konfigurowanie klientów punkt-lokacja
Użyj pobranego profilu, aby skonfigurować klientów zdalnego dostępu. Procedury dla każdego systemu operacyjnego są różne, postępuj zgodnie z poniższymi instrukcjami:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Pobierz i zainstaluj klienta OpenVPN z oficjalnej witryny internetowej.
2.  Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracje punkt-lokacja w witrynie Azure Portal lub za pomocą polecenia New-AzureRmVpnClientConfiguration w programie PowerShell.
3.  Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w programie Notatnik.
4.  W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Sprawdź tutaj, w jaki sposób wyeksportować certyfikat, aby otrzymać zakodowany klucz publiczny.
5.  W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Zobacz tutaj, w jaki sposób wyodrębnić klucz prywatny.
6.  Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7.  Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
8.  Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i kliknij przycisk Połącz.

#### <a name="ikev2"></a>IKEv2

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”.
2. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zostanie wyświetlone okno podręczne SmartScreen, kliknij pozycję Więcej informacji, a następnie pozycję Uruchom mimo to.
3. Na komputerze klienckim przejdź do obszaru Ustawienia sieci i kliknij pozycję Sieć VPN. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.
4. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Aby uzyskać więcej informacji na temat generowania certyfikatów, zobacz Generowanie certyfikatów. Aby dowiedzieć się, jak zainstalować certyfikat klienta, zobacz Instalowanie certyfikatu klienta.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Pobierz i zainstaluj klienta OpenVPN, na przykład program TunnelBlik, dostępny na stronie https://tunnelblick.net/downloads.html 
2.  Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punkt-lokacja w witrynie Azure Portal lub za pomocą polecenia New-AzureRmVpnClientConfiguration w programie PowerShell.
3.  Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w programie Notatnik.
4.  W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Sprawdź tutaj, w jaki sposób wyeksportować certyfikat, aby otrzymać zakodowany klucz publiczny.
5.  W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Zobacz tutaj, w jaki sposób wyodrębnić klucz prywatny.
6.  Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7.  Kliknij dwukrotnie plik profilu w celu utworzenia profilu w programie Tunnelblik
8.  Uruchom program Tunnelblik z poziomu folderu aplikacji
9.  Kliknij ikonę programu Tunneblik na pasku zadań i kliknij przycisk Połącz

#### <a name="ikev2"></a>IKEv2

Platforma Azure nie zapewnia pliku mobileconfig dla uwierzytelniania natywnego certyfikatu platformy Azure. Należy ręcznie skonfigurować natywnego klienta sieci VPN z protokołem IKEv2 na każdym komputerze Mac, który jest połączony z platformą Azure. Folder Generic zawiera wszystkie informacje potrzebne do skonfigurowania go. Jeśli nie widzisz folderu Generic w elementach pobranych, prawdopodobnie nie wybrano protokołu IKEv2 jako typu tunelu. Po wybraniu protokołu IKEv2 należy ponownie wygenerować plik zip, aby pobrać folder Generic. Folder Generic zawiera następujące pliki:

- VpnSettings.xml, który zawiera ważne ustawienia, takie jak adres serwera i typ tunelu.
- VpnServerRoot.cer, który zawiera certyfikat główny wymagany do sprawdzania poprawności usługi Azure VPN Gateway podczas konfigurowania połączenia P2S.

## <a name="viewwan"></a>9. Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator. Umieść kursor na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="viewhealth"></a>10. Wyświetlanie kondycji zasobów

1. Przejdź do sieci WAN.
2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **Kondycja** i wyświetl zasób.

## <a name="connectmon"></a>11. Monitorowanie połączenia

Utwórz połączenie, aby monitorować komunikację pomiędzy maszyną wirtualną platformy Azure a zdalną lokacją. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło zawiera adres IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="cleanup"></a>12. Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie lokacji
> * Tworzenie koncentratora
> * Łączenie koncentratora z lokacją
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji urządzenia sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).