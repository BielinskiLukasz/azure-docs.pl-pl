---
title: Samouczek — Tworzenie bramy aplikacji, która hostuje wiele witryn sieci Web przy użyciu Azure Portal
description: W tym samouczku dowiesz się, jak utworzyć bramę aplikacji, która będzie hostować wiele witryn sieci Web przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: 3b6818d68b62b574e49817b574450625c231506c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833573"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Samouczek: Tworzenie i Konfigurowanie bramy aplikacji na potrzeby hostowania wielu witryn sieci Web przy użyciu Azure Portal

Azure Portal służy do [konfigurowania hostingu wielu witryn sieci Web](multiple-site-overview.md) podczas tworzenia [bramy aplikacji](overview.md). W tym samouczku zdefiniujesz pule adresów zaplecza przy użyciu maszyn wirtualnych. Następnie, bazując na należących do Ciebie domenach, skonfigurujesz odbiorniki i reguły, aby się upewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach. W tym samouczku przyjęto założenie, że jesteś właścicielem wielu domen, przykładami których są *www.contoso.com* i *www.fabrikam.com*.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy Application Gateway
> * Tworzenie maszyn wirtualnych dla serwerów zaplecza
> * Tworzenie pul zaplecza z serwerami zaplecza
> * Tworzenie odbiorników zaplecza
> * Tworzenie reguł routingu
> * Tworzenie rekordu CNAME w domenie

![Przykład routingu obejmujący wiele witryn](./media/create-multiple-sites-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Tworzenie bramy Application Gateway

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Application Gateway** z listy **Polecane**.

### <a name="basics-tab"></a>Karta podstawy

1. Na karcie **podstawowe** wprowadź następujące wartości następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.

     ![Utwórz nową bramę aplikacji: podstawowe](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**wybierz pozycję **Utwórz nową** , aby utworzyć nową sieć wirtualną. W otwartym oknie **Tworzenie sieci wirtualnej** wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (Application Gateway podsieć): w siatce **podsieci** zostanie wyświetlona podsieć o nazwie *default*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci** .

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** Wprowadź zakres adresów, który nie nakłada się na zakres adresów *myAGSubnet*. Na przykład jeśli zakres adresów *myAGSubnet* jest równy 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK** , aby zamknąć okno **Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

     ![Utwórz nową bramę aplikacji: Sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie **podstawy** zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Dalej: frontony**.

### <a name="frontends-tab"></a>Karta frontonów

1. Na karcie **frontony** Sprawdź, czy **Typ adresu IP frontonu** jest ustawiony na wartość **Public**. <br>Adres IP frontonu można skonfigurować tak, aby był publiczny lub prywatny jak w przypadku użycia. W tym przykładzie wybrano publiczny adres IP frontonu.
   > [!NOTE]
   > W przypadku jednostki SKU Application Gateway v2 można wybrać tylko **publiczną** konfigurację adresu IP frontonu. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz opcję **Utwórz nowy** dla **publicznego adresu IP** i wprowadź *MYAGPUBLICIPADDRESS* dla nazwy publicznego adresu IP, a następnie wybierz przycisk **OK**. 

     ![Utwórz nową bramę aplikacji: frontony](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz pozycję **Dalej: nadkończenie**.

### <a name="backends-tab"></a>Karta zakończyła się

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą być kartami sieciowymi, zestawami skalowania maszyn wirtualnych, publicznymi adresami IP, wewnętrznymi adresami IP, w pełni kwalifikowanymi nazwami domen (FQDN) i wielodostępnymi zaplecami, jak Azure App Service W tym przykładzie utworzysz pustą pulę zaplecza z bramą aplikacji, a następnie dodasz cele zaplecza do puli zaplecza.

1. Na karcie **nadkończenie** wybierz pozycję **+ Dodaj pulę zaplecza**.

2. W otwartym oknie **Dodawanie puli zaplecza** wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa**: wprowadź *contosoPool* jako nazwę puli zaplecza.
    - **Dodaj pulę zaplecza bez elementów docelowych**: wybierz opcję **tak** , aby utworzyć pulę zaplecza, która nie ma elementów docelowych. Po utworzeniu bramy aplikacji należy dodać cele zaplecza.

3. W oknie **Dodawanie puli zaplecza** wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza i powrócić **do karty** zaplecze.
4. Teraz Dodaj kolejną pulę zaplecza o nazwie *fabrikamPool*.

     ![Utwórz nową bramę aplikacji: zafrontony](./media/create-multiple-sites-portal/backend-pools.png)

4. Na karcie **zakończyło** się wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** zostanie nawiązane połączenie frontonu i pul zaplecza utworzonych przy użyciu reguły routingu.

1. Wybierz pozycję **Dodaj regułę** w kolumnie **reguły routingu** .

2. W otwartym oknie **Dodawanie reguły routingu** wpisz *ContosoRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: wprowadź *contosoListener* dla nazwy odbiornika.
    - **Adres IP frontonu**: wybierz opcję **publiczny** , aby wybrać publiczny adres IP utworzony dla frontonu.

   W obszarze **Ustawienia dodatkowe**:
   - **Typ odbiornika**: wiele lokacji
   - **Nazwa hosta**: **www.contoso.com**

   Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.

   ![Utwórz nową bramę aplikacji: odbiornik](./media/create-multiple-sites-portal/routing-rule.png)

4. Na karcie **cele zaplecza** wybierz pozycję **contosoPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http**wybierz pozycję **Utwórz nowy** , aby utworzyć nowe ustawienie http. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **Dodawanie ustawienia protokołu HTTP** , które zostanie otwarte, wprowadź *contosoHTTPSetting* dla **nazwy ustawienia http**. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** . 

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj** , aby zapisać regułę routingu, i wróć do karty **Konfiguracja** .
7. Wybierz pozycję **Dodaj regułę** i Dodaj podobną regułę, odbiornik, cel zaplecza i ustawienie protokołu HTTP dla firmy Fabrikam.

     ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Wybierz pozycję **Dalej: Tagi** , a następnie kliknij przycisk **Dalej: przegląd + Utwórz**.

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

Przejrzyj ustawienia na karcie **Przegląd + tworzenie** , a następnie wybierz pozycję **Utwórz** , aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut.

Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodaj cele zaplecza

W tym przykładzie użyjesz maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne używane przez platformę Azure jako serwery zaplecza dla bramy aplikacji.

Aby dodać cele zaplecza, można:

1. Utwórz dwie nowe maszyny wirtualne, *contosoVM* i *fabrikamVM*, które będą używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do pul zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz pozycję **obliczenia** , a następnie wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Application Gateway może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używane jest centrum danych systemu Windows Server 2016.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: wprowadź *contosoVM* dla nazwy maszyny wirtualnej.
    - **Nazwa**użytkownika: wprowadź *azureuser* dla nazwy administratora.
    - **Hasło**: wprowadź *Azure123456!* jako hasło administratora.
4. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: dyski**.  
5. Zaakceptuj ustawienia domyślne karty **dyski** , a następnie wybierz pozycję **Dalej: sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.<br>Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, w której znajduje się, ale należy upewnić się, że połączenie IP jest nawiązywane.
7. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Zainstaluj usługi IIS do testowania

W tym przykładzie należy zainstalować usługi IIS tylko na maszynach wirtualnych, aby sprawdzić, czy platforma Azure utworzyła bramę aplikacji pomyślnie.

1. Otwórz program [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, używając wcześniej ukończonych kroków. Użyj *fabrikamVM* dla nazwy maszyny wirtualnej i ustawienia **VMName** polecenia cmdlet **Set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pools"></a>Dodawanie serwerów zaplecza do pul zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz pozycję **contosoPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarze interfejsy **maszyn wirtualnych** i **sieciowych**wybierz maszynę wirtualną **contosoVM** , a jej interfejs sieciowy jest skojarzony z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Wybierz pozycję **Zapisz**.
7. Powtarzaj, aby dodać *fabrikamVM* i interfejs do *fabrikamPool*.

Przed przejściem do następnego kroku poczekaj na zakończenie wdrożenia.

## <a name="create-a-www-a-record-in-your-domains"></a>Tworzenie rekordu A www w domenach

Po utworzeniu bramy aplikacji przy użyciu publicznego adresu IP można uzyskać adres IP i użyć go do utworzenia rekordu A w domenach. 

1. Kliknij pozycję **wszystkie zasoby**, a następnie kliknij pozycję **myAGPublicIPAddress**.

    ![Rejestruj adres DNS bramy aplikacji](./media/create-multiple-sites-portal/public-ip.png)

2. Skopiuj adres IP i użyj go jako wartości nowego rekordu *www* a w domenach.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wpisz nazwę swojej domeny na pasku adresu przeglądarki. Na przykład `http://www.contoso.com`.

    ![Testowanie witryny contoso w bramie aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Zmień adres na drugą domenę. Powinny zostać wyświetlone informacje, jak w następującym przykładzie:

    ![Testowanie witryny fabrikam w bramy aplikacji](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Po usunięciu grupy zasobów należy również usunąć bramę aplikacji i wszystkie powiązane z nią zasoby.

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź ciąg *myResourceGroupAG* w polu **WPISZ NAZWĘ GRUPY ZASOBÓW**, a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat tego, co możesz zrobić za pomocą usługi Azure Application Gateway](application-gateway-introduction.md)