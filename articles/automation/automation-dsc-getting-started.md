---
title: Wprowadzenie do konfiguracji stanu Azure Automation
description: Wyjaśnienie i przykłady najczęstszych zadań w Azure Automation konfiguracji stanu (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f943aac4a91217983963fac6f8d0b2b3ba6895a1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243625"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Wprowadzenie do konfiguracji stanu Azure Automation

W tym artykule opisano sposób wykonywania najczęstszych zadań z konfiguracją stanu Azure Automation, takich jak tworzenie, importowanie i kompilowanie konfiguracji, dołączanie maszyn w celu zarządzania i wyświetlania raportów. Aby zapoznać się z omówieniem konfiguracji stanu Azure Automation, zobacz [Konfiguracja stanu Azure Automation — Omówienie](automation-dsc-overview.md). Aby zapoznać się z dokumentacją konfiguracji żądanego stanu (DSC), zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Ten artykuł zawiera przewodnik krok po kroku dotyczący korzystania z konfiguracji stanu Azure Automation. Jeśli chcesz, aby przykładowe środowisko zostało już skonfigurowane, nie wykonując kroków opisanych w tym artykule, możesz użyć następującego szablonu Menedżer zasobów: [Azure Automation szablonu węzła zarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ten szablon konfiguruje Azure Automation środowisko konfiguracji stanu, w tym maszynę wirtualną platformy Azure, która jest zarządzana przez Azure Automationą konfigurację stanu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać przykłady opisane w tym artykule, wymagane są następujące elementy:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna Azure Resource Manager (nieklasyczna) z [obsługiwanym systemem operacyjnym](automation-dsc-overview.md#operating-system-requirements). Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Można utworzyć prostą [konfigurację DSC](/powershell/scripting/dsc/configurations/configurations) , która zapewnia obecność lub brak funkcji systemu Windows **serwera sieci Web** (IIS), w zależności od sposobu przypisywania węzłów.

1. Uruchom [programu vscode](https://code.visualstudio.com/docs) (lub dowolny edytor tekstu).
1. Wpisz następujący tekst:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Zapisz plik jako `TestConfig.ps1`.

Ta konfiguracja wywołuje jeden zasób w każdym bloku węzła, [zasób WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource), który zapewnia obecność lub brak funkcji **serwera sieci Web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importowanie konfiguracji do Azure Automation

Następnie należy zaimportować konfigurację do konta usługi Automation.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie **konto usługi Automation** wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** , a następnie kliknij pozycję **+ Dodaj**.
1. Na stronie **Konfiguracja importowania** przejdź do pliku `TestConfig.ps1` na komputerze.

   ![Zrzut ekranu przedstawiający blok programu * * konfiguracja importowania * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Kliknij przycisk **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Wyświetlanie konfiguracji w Azure Automation

Po zaimportowaniu konfiguracji można wyświetlić ją w Azure Portal.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie **konto usługi Automation** wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig** (jest to nazwa konfiguracji zaimportowanej w poprzedniej procedurze).
1. Na stronie **Konfiguracja TestConfig** kliknij pozycję **Wyświetl źródło konfiguracji**.

   ![Zrzut ekranu przedstawiający blok konfiguracji TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Zostanie otwarta strona **Źródło konfiguracji TestConfig** , w której zostanie wyświetlony kod programu PowerShell dla konfiguracji.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilowanie konfiguracji w Azure Automation

Aby można było zastosować żądany stan do węzła, Konfiguracja DSC definiująca ten stan musi być skompilowana do co najmniej jednej konfiguracji węzła (dokument MOF) i umieszczona na serwerze Automation DSC ściągania. Aby uzyskać bardziej szczegółowy opis kompilowania konfiguracji w konfiguracji stanu Azure Automation, zobacz [Kompilowanie konfiguracji w Azure Automation konfiguracja stanu](automation-dsc-compile.md).
Aby uzyskać więcej informacji na temat kompilowania konfiguracji, zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig** (nazwę wcześniej zaimportowanej konfiguracji).
1. Na stronie **Konfiguracja TestConfig** kliknij pozycję **Kompiluj**, a następnie kliknij przycisk **tak**. Spowoduje to uruchomienie zadania kompilacji.

   ![Zrzut ekranu przedstawiający przycisk Kompiluj wyróżnianie strony konfiguracji TestConfig](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w programie Azure Automation automatycznie wdraża wszystkie utworzone konfiguracje węzłów pliki MOF na serwerze ściągania.

## <a name="viewing-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po rozpoczęciu kompilacji można wyświetlić ją na kafelku **zadania kompilacji** na stronie **Konfiguracja** . Na kafelku **zadania kompilacji** są wyświetlane zadania uruchomione, ukończone i zakończone niepowodzeniem. Po otwarciu strony zadania kompilacji zostaną wyświetlone informacje o tym zadaniu, w tym o napotkanych błędach lub ostrzeżeniach, parametrach wejściowych używanych w konfiguracji i dziennikach kompilacji.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **konfiguracje** , a następnie kliknij pozycję **TestConfig** (nazwę wcześniej zaimportowanej konfiguracji).
1. W obszarze **zadania kompilacji**wybierz zadanie kompilacji, które chcesz wyświetlić. Zostanie otwarta strona **zadanie kompilacji** z etykietą z datą rozpoczęcia zadania kompilacji.

   ![Zrzut ekranu strony zadania kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknij dowolny kafelek na stronie **zadanie kompilacji** , aby zobaczyć dalsze szczegóły dotyczące zadania.

## <a name="viewing-node-configurations"></a>Wyświetlanie konfiguracji węzłów

Pomyślne zakończenie zadania kompilacji powoduje utworzenie co najmniej jednej nowej konfiguracji węzła. Konfiguracja węzła to dokument MOF, który jest wdrażany na serwerze ściągania i gotowy do ściągnięcia i zastosowania przez co najmniej jeden węzeł. Konfiguracje węzłów można wyświetlić na koncie usługi Automation na stronie **Konfiguracja stanu (DSC)** . Konfiguracja węzła ma nazwę z formą *ConfigurationName*. *Węzełname*.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **skompilowane konfiguracje** .

   ![Zrzut ekranu przedstawiający kartę skompilowane konfiguracje](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Dołączanie maszyny wirtualnej platformy Azure w celu zarządzania za pomocą konfiguracji stanu Azure Automation

Konfiguracja stanu Azure Automation służy do zarządzania maszynami wirtualnymi platformy Azure (klasycznymi i Menedżer zasobów), lokalnymi maszynami wirtualnymi, maszynami z systemem Linux, AWS maszynami wirtualnymi i lokalnymi maszynami fizycznymi. W tym artykule dowiesz się, jak dołączyć tylko Azure Resource Manager maszyny wirtualne. Aby uzyskać informacje na temat dołączania innych typów maszyn, zobacz sekcję dołączanie [maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Aby dołączyć maszynę wirtualną Azure Resource Manager do zarządzania według konfiguracji stanu Azure Automation

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** na karcie **węzły** kliknij pozycję **+ Dodaj**.

   ![Zrzut ekranu przedstawiający stronę węzły DSC z wyróżnionym przyciskiem Dodaj maszynę wirtualną platformy Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na stronie **Virtual Machines** wybierz maszynę wirtualną.
1. Na stronie Szczegóły **maszyny wirtualnej** kliknij pozycję **+ Połącz**.

   > [!IMPORTANT]
   > Musi to być Azure Resource Manager maszyna wirtualna z [obsługiwanym systemem operacyjnym](automation-dsc-overview.md#operating-system-requirements).

2. Na stronie **rejestracja** wybierz nazwę konfiguracji węzła, która ma zostać zastosowana do maszyny wirtualnej w polu **nazwa konfiguracji węzła** . Podanie nazwy w tym momencie jest opcjonalne. Po dołączeniu węzła można zmienić konfigurację przypisanego węzła.
   W **razie konieczności Sprawdź węzeł ponownego uruchomienia**, a następnie kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający blok rejestracji](./media/automation-dsc-getting-started/RegisterVM.png)

   Określona konfiguracja węzła jest stosowana do maszyny wirtualnej w odstępach czasu określonych przez **częstotliwość trybu konfiguracji**, a maszyna wirtualna sprawdza dostępność aktualizacji konfiguracji węzła w odstępach czasu określonych przez **częstotliwość odświeżania**. Aby uzyskać więcej informacji na temat sposobu używania tych wartości, zobacz [Konfigurowanie lokalnego Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Platforma Azure uruchamia proces dołączania maszyny wirtualnej. Po zakończeniu ta maszyna wirtualna będzie wyświetlana na karcie **węzły** na stronie **Konfiguracja stanu (DSC)** na koncie usługi Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Wyświetlanie listy węzłów zarządzanych

Listę wszystkich komputerów, które zostały dołączone do zarządzania na koncie usługi Automation, można wyświetlić na karcie **węzły** na stronie **Konfiguracja stanu (DSC)** .

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **węzły** .

## <a name="viewing-reports-for-managed-nodes"></a>Wyświetlanie raportów dotyczących węzłów zarządzanych

Za każdym razem, gdy Azure Automation konfiguracja stanu wykonuje kontrolę spójności w zarządzanym węźle, węzeł wysyła raport o stanie z powrotem do serwera ściągania. Te raporty można wyświetlić na stronie dla tego węzła.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **węzły** . W tym miejscu można zobaczyć przegląd stanu konfiguracji i szczegółów dla każdego węzła.

   ![Zrzut ekranu strony węzła](./media/automation-dsc-getting-started/NodesTab.png)

1. Na karcie **węzły** kliknij rekord węzła, aby otworzyć raport. Kliknij raport, który chcesz wyświetlić dodatkowe szczegóły raportowania.

   ![Zrzut ekranu przedstawiający blok raportu](./media/automation-dsc-getting-started/NodeReport.png)

W bloku dla pojedynczego raportu można zobaczyć następujące informacje o stanie dla odpowiedniego sprawdzenia spójności:

- Stan raportu — czy węzeł jest zgodny, konfiguracja "Niepowodzenie" lub węzeł jest "niezgodne" (gdy węzeł jest w trybie **ApplyandMonitor** , a maszyna nie jest w żądanym stanie).
- Godzina rozpoczęcia kontroli spójności.
- Łączny czas wykonywania kontroli spójności.
- Typ kontroli spójności.
- Wszelkie błędy, w tym kod błędu i komunikat o błędzie.
- Wszystkie zasoby DSC używane w konfiguracji oraz stan poszczególnych zasobów (czy węzeł jest w żądanym stanie dla tego zasobu) — możesz kliknąć każdy zasób, aby uzyskać bardziej szczegółowe informacje dotyczące tego zasobu.
- Nazwa, adres IP i tryb konfiguracji węzła.

Możesz również kliknąć pozycję **Wyświetl raport nieprzetworzony** , aby wyświetlić rzeczywiste dane wysyłane przez węzeł do serwera programu.
Aby uzyskać więcej informacji na temat korzystania z tych danych, zobacz [Korzystanie z serwera raportów DSC](/powershell/scripting/dsc/pull-server/reportserver).

Po dołączeniu węzła do pierwszego raportu może upłynąć trochę czasu. Po dołączeniu węzła może być konieczne odczekanie do 30 minut na pierwszy raport.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Ponowne przypisywanie węzła do innej konfiguracji węzła

Węzeł można przypisać do innej konfiguracji węzła niż ta, która została początkowo przypisana.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **węzły** .
1. Na karcie **węzły** kliknij nazwę węzła, który chcesz ponownie przypisać.
1. Na stronie tego węzła kliknij pozycję **Przypisz konfigurację węzła**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnionym przyciskiem Przypisz konfigurację węzła](./media/automation-dsc-getting-started/AssignNode.png)

1. Na stronie **Konfigurowanie konfiguracji węzła** wybierz konfigurację węzła, do której chcesz przypisać węzeł, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu strony Konfiguracja przypisywania węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Wyrejestrowywanie węzła

Jeśli nie chcesz już zarządzać węzłem Azure Automation DSC, możesz go wyrejestrować.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. W bloku **konto usługi Automation** kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie **Konfiguracja stanu (DSC)** kliknij kartę **węzły** .
1. Na karcie **węzły** kliknij nazwę węzła, który chcesz wyrejestrować.
1. Na stronie tego węzła kliknij pozycję **Wyrejestruj**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła z wyróżnionym przyciskiem wyrejestrowywania](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Powiązane artykuły

- [Przegląd konfiguracji stanu Azure Automation](automation-dsc-overview.md)
- [Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md)
- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/azurerm.automation/#automation)
- [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
