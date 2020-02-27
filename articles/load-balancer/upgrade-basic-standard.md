---
title: Uaktualnij z warstwy Podstawowa publicznie do standardowej Azure Load Balancer publicznej
description: W tym artykule opisano sposób uaktualniania Load Balancer publicznej platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 83cac961eb3cd700451f16c684c64185b35e9bd3
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616750"
---
# <a name="upgrade-azure-public-load-balancer"></a>Uaktualnij Load Balancer publicznej platformy Azure
[Usługa Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysokiej dostępności za pomocą nadmiarowości stref. Aby dowiedzieć się więcej na temat Load Balancer SKU, zobacz [tabela porównania](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Uaktualnienie obejmuje dwa etapy:

1. Migrowanie konfiguracji
2. Dodawanie maszyn wirtualnych do pul zaplecza usługa Load Balancer w warstwie Standardowa

W tym artykule opisano migrację konfiguracji. Dodawanie maszyn wirtualnych do pul zaplecza może się różnić w zależności od konkretnego środowiska. [Dostępne są](#add-vms-to-backend-pools-of-standard-load-balancer)jednak pewne ogólne zalecenia dotyczące wysokiego poziomu.

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardową jednostkę SKU Load Balancer w grupie zasobów i lokalizacji określonej przez użytkownika.
* Bezproblemowo kopiuje konfiguracje podstawowej jednostki SKU Load Balancer do nowo utworzonej usługa Load Balancer w warstwie Standardowa.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skrypt obsługuje tylko publiczne uaktualnienie Load Balancer. W przypadku wewnętrznego uaktualnienia Load Balancer należy utworzyć standardowy Load Balancer wewnętrzny, jeśli łączność wychodząca nie jest wymagana, i utworzyć standardową Load Balancer wewnętrzną i standardową Load Balancer, jeśli wymagana jest łączność wychodząca.
* Usługa Load Balancer w warstwie Standardowa ma nowy adres publiczny. Nie można przenieść adresów IP skojarzonych z istniejącymi podstawowymi Load Balancer bezproblemowo do usługa Load Balancer w warstwie Standardowa, ponieważ mają różne jednostki SKU.
* Jeśli w innym regionie zostanie utworzony standardowy moduł równoważenia obciążenia, nie będzie możliwe skojarzenie maszyn wirtualnych istniejących w starym regionie z nowo utworzonymi usługa Load Balancer w warstwie Standardowa. Aby obejść to ograniczenie, należy utworzyć nową maszynę wirtualną w nowym regionie.
* Jeśli Load Balancer nie ma żadnej konfiguracji adresu IP frontonu lub puli zaplecza, można napotkać błąd podczas uruchamiania skryptu. Upewnij się, że nie są puste.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0).
## <a name="use-the-script"></a>Używanie skryptu

W zależności od konfiguracji i preferencji lokalnego środowiska programu PowerShell dostępne są dwie opcje:

* Jeśli nie masz zainstalowanych modułów platformy Azure AZ lub nie chcesz odinstalować modułów Azure AZ modules, najlepszym rozwiązaniem jest użycie opcji `Install-Script`, aby uruchomić skrypt.
* Jeśli zachodzi potrzeba zachowania usługi Azure AZ modules, najlepszym trafieniem jest pobranie skryptu i uruchomienie go bezpośrednio.

Aby określić, czy masz zainstalowane moduły Azure AZ, uruchom `Get-InstalledModule -Name az`. Jeśli nie widzisz żadnych zainstalowanych modułów AZ, możesz użyć metody `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody install-Script

Aby można było użyć tej opcji, na komputerze nie trzeba mieć zainstalowanych modułów platformy Azure. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Możesz odinstalować usługę Azure AZ module lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzurePublicLBUpgrade`

To polecenie instaluje również wymagane AZ modules.  

### <a name="install-using-the-script-directly"></a>Instalowanie przy użyciu skryptu bezpośrednio

Jeśli zainstalowano kilka modułów na platformie Azure i nie można ich odinstalować (lub nie chcesz ich odinstalować), możesz ręcznie pobrać skrypt, korzystając z karty **pobranej ręcznie** w linku pobierania skryptu. Skrypt zostanie pobrany jako plik pierwotny NUPKG. Aby zainstalować skrypt z tego pliku NUPKG, zobacz [ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount`, aby nawiązać połączenie z platformą Azure.

1. Użyj `Import-Module Az` do zaimportowania AZ modules.

1. Badanie wymaganych parametrów:

   * **oldRgName: [ciąg]: wymagane** — jest to grupa zasobów dla istniejących Load Balancer podstawowych, które mają zostać uaktualnione. Aby znaleźć tę wartość ciągu, przejdź do Azure Portal, wybierz podstawowe źródło Load Balancer i kliknij **Przegląd** usługi równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [ciąg]: wymagane** — jest to nazwa istniejącego modułu, który ma zostać uaktualniony. 
   * **newrgName: [ciąg]: wymagane** — jest to grupa zasobów, w której zostanie utworzony usługa Load Balancer w warstwie Standardowa. Może to być Nowa grupa zasobów lub istniejąca. W przypadku wybrania istniejącej grupy zasobów należy pamiętać, że nazwa Load Balancer musi być unikatowa w ramach grupy zasobów. 
   * **newLocation: [ciąg]: wymagane** — lokalizacja, w której zostanie utworzony usługa Load Balancer w warstwie Standardowa. Zaleca się, aby dziedziczyć tę samą lokalizację wybranych Load Balancer podstawowych do usługa Load Balancer w warstwie Standardowa w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [ciąg]: wymagane** — jest to nazwa usługa Load Balancer w warstwie Standardowa, która ma zostać utworzona.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Ukończenie tego procesu może potrwać od 5 do siedmiu minut.

    **Przykład**

   ```azurepowershell
   ./AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Dodawanie maszyn wirtualnych do pul zaplecza usługa Load Balancer w warstwie Standardowa

Najpierw należy sprawdzić, czy skrypt pomyślnie utworzył nową standardową Load Balancer publiczną z dokładną konfiguracją zmigrowane z poziomu podstawowej Load Balancer publicznego. Można to sprawdzić z poziomu Azure Portal.

Należy pamiętać, aby wysłać mały ruch przez usługa Load Balancer w warstwie Standardowa jako test ręczny.
  
Poniżej przedstawiono kilka scenariuszy dodawania maszyn wirtualnych do pul zaplecza dla nowo utworzonych standardowych Load Balancer publicznych, które można skonfigurować, i zaleceń dotyczących każdego z nich:

* **Przeniesienie istniejących maszyn wirtualnych z puli zaplecza starych podstawowych Load Balancer publicznych do pul zaplecza nowo utworzonych standardowych Load Balancer publicznych**.
    1. Aby wykonać zadania z tego przewodnika Szybki start, zaloguj się do witryny [Azure Portal](https://portal.azure.com).
 
    1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **nowo utworzoną usługa Load Balancer w warstwie Standardowa** z listy zasobów.
   
    1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**.
   
    1. Wybierz pulę zaplecza, która pasuje do puli zaplecza podstawowego Load Balancer, wybierz następującą wartość: 
      - **Maszyna wirtualna**: Lista rozwijana i wybieranie maszyn wirtualnych z dopasowanej puli zaplecza Load Balancer podstawowej.
    1. Wybierz pozycję **Zapisz**.
    >[!NOTE]
    >W przypadku maszyn wirtualnych, które mają publiczne adresy IP, konieczne będzie utworzenie standardowych adresów w pierwszej kolejności, w której ten sam adres IP nie jest gwarantowany. Usuń skojarzenie maszyn wirtualnych z podstawowymi adresami IP i skojarz je z nowo utworzonymi standardowymi adresami. Następnie będzie można wykonać instrukcje dotyczące dodawania maszyn wirtualnych do puli zaplecza usługa Load Balancer w warstwie Standardowa. 

* **Tworzenie nowych maszyn wirtualnych do dodania do pul zaplecza dla nowo utworzonej Load Balancer publicznej**.
    * Więcej instrukcji dotyczących tworzenia maszyn wirtualnych i kojarzenia ich z usługa Load Balancer w warstwie Standardowa można znaleźć [tutaj](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia dotyczące skryptu Azure PowerShell w celu migrowania konfiguracji z wersji od 1 do v2?

Tak. Zobacz [zastrzeżenia/ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt Azure PowerShell przełączany jest również ruch z mojego podstawowego Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa?

Nie. Skrypt Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest odpowiedzialna i w Twoim formancie.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wystąpił problem z używaniem tego skryptu. Jak uzyskać pomoc?
  
Możesz wysłać wiadomość e-mail do slbupgradesupport@microsoft.com, otworzyć sprawę pomocy technicznej z pomocą techniczną platformy Azure lub wykonać obie czynności.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
