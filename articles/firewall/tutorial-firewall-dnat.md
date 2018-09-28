---
title: Filtrowanie ruchu przychodzącego za pomocą funkcji DNAT usługi Azure Firewall przy użyciu witryny Azure Portal
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować funkcję DNAT usługi Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982053"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Samouczek: filtrowanie ruchu przychodzącego za pomocą funkcji DNAT usługi Azure Firewall przy użyciu witryny Azure Portal

Możesz skonfigurować funkcję translacji docelowych adresów sieciowych (DNAT) usługi Azure Firewall do wykonywania translacji i filtrowania ruchu przychodzącego do podsieci. W usłudze Azure Firewall nie istnieje pojęcie reguł ruchu przychodzącego i wychodzącego. Istnieją zasady aplikacji i zasady sieci, które są stosowane do wszelkiego ruchu przechodzącego przez zaporę. Najpierw stosowane są reguły sieci, a następnie reguły aplikacji, a reguły powodują zakończenie.

>[!NOTE]
>Funkcja DNAT usługi Firewall jest obecnie dostępna tylko w programie Azure PowerShell i architekturze REST.

Jeśli na przykład zostanie dopasowana reguła sieci, pakiet nie zostanie oceniony przez reguły aplikacji. Jeśli nie zostanie dopasowana reguła sieci i jeśli protokół pakietu to HTTP/HTTPS, pakiet zostanie oceniony przez reguły aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet zostanie oceniony względem [kolekcji reguł infrastruktury](infrastructure-fqdns.md). Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

Podczas konfigurowania funkcji DNAT akcja kolekcji reguł NAT jest ustawiana na wartość **Translacja docelowych adresów sieciowych (DNAT)**. Publiczny adres IP zapory i port są tłumaczone na prywatny adres IP i port. Następnie reguły są stosowane jak zwykle: najpierw reguły sieci, a potem reguły aplikacji. Możesz na przykład skonfigurować regułę sieci zezwalającą na ruch pulpitu zdalnego na porcie TCP 3389. Najpierw jest wykonywana translacja adresów, a następnie przy użyciu przetłumaczonych adresów są stosowane reguły sieci i reguły aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Konfigurowanie reguły sieci
> * Testowanie zapory

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W tym samouczku utworzysz dwie sieci wirtualne połączone przy użyciu komunikacji równorzędnej:
- **VN-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VN-Spoke** — w tej sieci wirtualnej znajduje się serwer obciążeń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
1. Na stronie głównej witryny Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **Dodaj**.
2. W polu **Nazwa grupy zasobów** wpisz **RG-DNAT-Test**.
3. W polu **Subskrypcja** wybierz subskrypcję.
4. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie kolejne zasoby, które utworzysz, muszą znajdować się w tej samej lokalizacji.
5. Kliknij pozycję **Utwórz**.

## <a name="set-up-the-network-environment"></a>Konfigurowanie środowiska sieciowego
Najpierw utwórz sieci wirtualne, a następnie połącz je przy użyciu komunikacji równorzędnej.

### <a name="create-the-hub-vnet"></a>Tworzenie koncentratora sieci wirtualnej
1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Sieci wirtualne**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **VN-Hub**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
7. W polu **Subskrypcja** wybierz subskrypcję.
8. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
9. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
10. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**.

     Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
     > [!NOTE]
     > Minimalny rozmiar podsieci AzureFirewallSubnet to /25.
11. W polu **Zakres adresów** wpisz wartość **10.0.1.0/24**.
12. Użyj innych domyślnych ustawień, a następnie kliknij przycisk **Utwórz**.

### <a name="create-a-spoke-vnet"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Sieci wirtualne**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz wartość **VN-Spoke**.
5. W polu **Przestrzeń adresowa** wpisz wartość **192.168.0.0/16**.
7. W polu **Subskrypcja** wybierz subskrypcję.
8. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
9. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
10. W obszarze **Podsieć** w polu **Nazwa** wpisz **SN-Workload**.

    Serwer będzie znajdował się w tej podsieci.
1. W polu **Zakres adresów** wpisz wartość **192.168.1.0/24**.
2. Użyj innych domyślnych ustawień, a następnie kliknij przycisk **Utwórz**.

### <a name="peer-the-vnets"></a>Łączenie sieci wirtualnych przy użyciu komunikacji równorzędnej

Teraz połącz sieci wirtualne przy użyciu komunikacji równorzędnej.

#### <a name="hub-to-spoke"></a>Piasta do szprychy

1. Kliknij sieć wirtualną **VN-Hub**.
2. W obszarze **Ustawienia** kliknij przycisk **Komunikacje równorzędne**.
3. Kliknij pozycję **Add** (Dodaj).
4. Jako nazwę wpisz **Peer-HubSpoke**.
5. Jako sieć wirtualną wybierz **VN-Spoke**.
7. Kliknij przycisk **OK**.

#### <a name="spoke-to-hub"></a>Szprycha do piasty

1. Kliknij sieć wirtualną **VN-Spoke**.
2. W obszarze **Ustawienia** kliknij przycisk **Komunikacje równorzędne**.
3. Kliknij pozycję **Add** (Dodaj).
4. Jako nazwę wpisz **Peer-SpokeHub**.
5. Jako sieć wirtualną wybierz **VN-Hub**.
6. Kliknij pozycję **Zezwalaj na ruch przesłany dalej**.
7. Kliknij przycisk **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną obciążenia i umieść ją w podsieci **SN-Workload**.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Obliczanie** kliknij pozycję **Maszyny wirtualne**.
3. Kliknij przycisk **Dodaj** i kliknij pozycję **system Windows Server**, kliknij pozycję **Windows Server 2016 Datacenter**, a następnie kliknij pozycję **Utwórz**.

**Podstawy**

1. W polu **Nazwa** wpisz wartość **Srv-Workload**.
5. Wpisz nazwę użytkownika i hasło.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W obszarze **Grupa zasobów** kliknij pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. Kliknij przycisk **OK**.

**Rozmiar**

1. Wybierz odpowiedni rozmiar dla testowej maszyny wirtualnej z systemem Windows Server. Na przykład **B2ms** (8 GB pamięci RAM, 16 GB magazynu).
2. Kliknij pozycję **Wybierz**.

**Ustawienia**

1. W obszarze **Sieć** w polu **Sieć wirtualna** wybierz pozycję **VN-Spoke**.
2. W polu **Podsieć** wybierz pozycję **SN-Workload**.
3. Kliknij pozycję **Publiczny adres IP**, a następnie kliknij pozycję **Brak**.
4. W obszarze **Wybierz publiczne porty wejściowe** wybierz pozycję **Brak publicznych portów wejściowych**. 
2. Pozostaw pozostałe ustawienia domyślne i kliknij przycisk **OK**.

**Podsumowanie**

Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz**. Ukończenie tej operacji potrwa kilka minut.

Po zakończeniu wdrożenia zanotuj prywatny adres IP maszyny wirtualnej. Posłuży on później do skonfigurowania zapory. Kliknij nazwę maszyny wirtualnej, a następnie w obszarze **Ustawienia**, kliknij pozycję **Sieć** i znajdź prywatny adres IP.


## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. Na stronie głównej portalu kliknij pozycję **Utwórz zasób**.
2. Kliknij pozycję **Sieć**, a po liście **Polecane** kliknij pozycję **Zobacz wszystko**.
3. Kliknij pozycję **Zapora**, a następnie kliknij pozycję **Utwórz**. 
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Name (Nazwa)     |FW-DNAT-test|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Użyj istniejącej**: RG-DNAT-Test |
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: VN-Hub|
   |Publiczny adres IP     |**Utwórz nową**. Publiczny adres IP musi mieć typ Standardowa jednostka SKU.|

2. Kliknij pozycję **Przegląd + utwórz**.
3. Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz**, aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
4. Po zakończeniu wdrażania przejdź do grupy zasobów **RG-DNAT-Test**, a następnie kliknij zaporę **FW-DNAT-test**.
6. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.


## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **SN-Workload** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. Na stronie głównej witryny Azure Portal kliknij pozycję **Wszystkie usługi**.
2. W obszarze **Sieć** kliknij pozycję **Tabele tras**.
3. Kliknij pozycję **Add** (Dodaj).
4. W polu **Nazwa** wpisz **RT-FWroute**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Kliknij pozycję **Utwórz**.
9. Kliknij pozycję **Odśwież**, a następnie kliknij tabelę tras **RT-FWroute**.
10. Kliknij pozycję **Podsieci**, a następnie kliknij pozycję **Skojarz**.
11. Kliknij pozycję **Sieć wirtualna**, a następnie wybierz pozycję **VN-Spoke**.
12. W obszarze **Podsieć** kliknij pozycję **SN-Workload**.
13. Kliknij przycisk **OK**.
14. Kliknij pozycję **Trasy**, a następnie kliknij pozycję **Dodaj**.
15. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
1. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
2. Kliknij przycisk **OK**.


## <a name="configure-a-dnat-rule"></a>Konfigurowanie reguły DNAT

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

1. Otwórz grupę zasobów**RG DNAT Test** i kliknij zaporę **FW-DNAT-test**.
1. Na stronie **FW-DNAT-test** w obszarze **Ustawienia**, kliknij pozycję **Reguły**.
2. Kliknij pozycję **Dodaj kolekcję reguł sieci**.

Skonfiguruj reguły, używając poniższej tabeli, a następnie kliknij pozycję **Dodaj**:


|Parametr  |Wartość  |
|---------|---------|
|Name (Nazwa)     |**RC-Net-01**|
|Priorytet     |**200**|
|Akcja     |**Zezwalaj**|

W obszarze **Reguły**:

|Parametr  |Ustawienie  |
|---------|---------|
|Name (Nazwa)     |**RL-RDP**|
|Protokół     |**TCP**|
|Adresy źródłowe     |*|
|Adresy docelowe     |Prywatny adres IP maszyny wirtualnej **Srv-Workload**|
|Porty docelowe|**3389**|


## <a name="test-the-firewall"></a>Testowanie zapory

1. Połącz pulpit zdalny z publicznym adresem IP zapory. Powinno zostać nawiązane połączenie z maszyną wirtualną **Srv-Workload**.
3. Zamknij pulpit zdalny.
4. Zmień akcję kolekcji reguł sieci **RC-Net-01** na wartość **Odmawiaj**.
5. Spróbuj ponownie połączyć się z publicznym adresem IP zapory. Tym razem nie powinno się to powieść z powodu reguły **Odmawiaj**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **RG-DNAT-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Konfigurowanie reguły sieci
> * Testowanie zapory

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
