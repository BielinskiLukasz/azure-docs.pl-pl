---
title: Wdrażanie aplikacji .NET w kontenerze w usłudze Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak konteneryzować istniejącą aplikację platformy .NET przy użyciu programu Visual Studio i jak debugować kontenery lokalnie w usłudze Service Fabric. Konteneryzowana aplikacja jest wypychana do usługi Azure Container Registry i wdrażana w klastrze usługi Service Fabric. Po wdrożeniu na platformie Azure aplikacja utrwala dane za pomocą usługi Azure SQL DB.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 36b9a2e710a2a7f34ee9374e89f3fb19cc591ac3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429596"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Samouczek: wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric

W tym samouczku przedstawiono sposób konteneryzacji istniejącej aplikacji ASP.NET i tworzenia z niej pakietu będącego aplikacją usługi Service Fabric.  Uruchom kontenery lokalnie w klastrze programistycznym usługi Service Fabric, a następnie wdróż aplikację na platformie Azure.  Aplikacja będzie utrwalać dane w usłudze [Azure SQL Database](/azure/sql-database/sql-database-technical-overview). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konteneryzowanie istniejącej aplikacji za pomocą programu Visual Studio
> * Tworzenie bazy danych SQL Azure
> * Tworzenie usługi Azure Container Registry
> * Wdrażanie aplikacji usługi Service Fabric na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Zainstaluj program [Docker CE dla systemu Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), który umożliwia uruchamianie kontenerów w systemie Windows 10.
3. Zainstaluj [środowisko uruchomieniowe usługi Service Fabric w wersji 6.2 lub nowszej](service-fabric-get-started.md) i [zestaw SDK usługi Service Fabric w wersji 3.1](service-fabric-get-started.md) lub nowszej.
4. [Zainstaluj program Visual Studio 2017 w wersji 15.7](https://www.visualstudio.com/) lub nowszej z pakietami roboczymi **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
5. Zainstaluj program [Azure PowerShell][link-azure-powershell-install].
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Pobieranie i uruchamianie aplikacji Fabrikam Fiber CallCenter
Pobierz aplikację przykładową [Fabrikam Fiber CallCenter][link-fabrikam-github].  Kliknij link **pobierania archiwum**.  Z katalogu *sourceCode* w pliku *fabrikam.zip* wyodrębnij plik *sourceCode.zip*, a następnie katalog *VS2015* na komputerze.

Sprawdź, czy aplikację Fabrikam Fiber CallCenter można skompilować i uruchomić bez błędów.  Uruchom program Visual Studio jako **administrator**, a następnie otwórz plik [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Naciśnij klawisz F5, aby debugować i uruchomić aplikację.

![Przykład internetowy dla firmy Fabrikam][fabrikam-web-page]

## <a name="containerize-the-application"></a>Konteneryzowanie aplikacji
Kliknij prawym przyciskiem myszy projekt **FabrikamFiber.Web** i wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów**.  Wybierz pozycję **Service Fabric** jako orkiestratora kontenerów i kliknij pozycję **OK**.

Kliknij przycisk **Tak**, aby przełączyć serwer Docker na kontenery systemu Windows.

Nowy projekt aplikacji usługi Service Fabric **FabrikamFiber.CallCenterApplication** zostanie utworzony w rozwiązaniu.  Plik Dockerfile zostanie dodany do istniejącego projektu **FabrikamFiber.Web**.  Katalog **PackageRoot** także zostanie dodany do projektu **FabrikamFiber.Web**. Zawiera on manifest usługi i ustawienia dla nowej usługi FabrikamFiber.Web. 

Kontener jest teraz gotowy do skompilowania i spakowania w aplikację usługi Service Fabric. Po skompilowaniu obrazu kontenera na komputerze możesz wypchnąć go do dowolnego rejestru kontenerów, a następnie ściągnąć na dowolny host w celu uruchomienia.

## <a name="create-an-azure-sql-db"></a>Tworzenie bazy danych usługi Azure SQL DB
Gdy aplikacja Fabrikam Fiber CallCenter działa w środowisku produkcyjnym, dane muszą być utrwalane w bazie danych. Obecnie nie ma możliwości zagwarantowania trwałości danych w kontenerze, dlatego nie można zapisywać danych produkcyjnych w programie SQL Server w kontenerze.

Zalecamy usługę [Azure SQL Database](/azure/sql-database/sql-database-get-started-powershell). Aby skonfigurować i uruchomić zarządzaną bazę danych SQL Server na platformie Azure, uruchom następujący skrypt.  Zmodyfikuj zmienne skryptu odpowiednio do potrzeb. Element *clientIP* określa adres IP Twojego komputera programistycznego.

Jeśli jesteś za firmową zaporą, adres IP komputera programistycznego może nie być adresem IP ujawnianym w Internecie. Aby sprawdzić, czy baza danych ma poprawny adres IP dla reguły zapory, przejdź do witryny [Azure Portal](https://portal.azure.com) i znajdź swoją bazę danych w sekcji Bazy danych SQL. Kliknij jej nazwę, a następnie w sekcji Przegląd kliknij pozycję „Ustaw zaporę serwera”. Parametr „Adres IP klienta” określa adres IP Twojej maszyny programistycznej. Upewnij się, że jest on zgodny z adresem IP w regule „AllowClient”.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Aktualizowanie konfiguracji internetowej
W projekcie **FabrikamFiber.Web** zaktualizuj parametry połączenia w pliku **web.config**, aby wskazywały na program SQL Server w kontenerze.  Zaktualizuj część *Server* parametrów połączenia dla serwera utworzonego przez poprzedni skrypt. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Na potrzeby lokalnego debugowania można używać dowolnego programu SQL Server, ale musi on być dostępny z hosta. Baza danych **localdb** nie obsługuje jednak komunikacji typu `container -> host`. Jeśli podczas tworzenia kompilacji wydania aplikacji internetowej ma zostać użyta inna usługa SQL Database, dodaj kolejne parametry połączenia do pliku *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Uruchamianie aplikacji konteneryzowanych lokalnie
Naciśnij klawisz **F5**, aby uruchomić i debugować aplikację w kontenerze w lokalnym klastrze programistycznym usługi Service Fabric. Kliknij przycisk **Tak**, jeśli zostanie wyświetlone pole komunikatu z prośbą o nadanie grupie „ServiceFabricAllowedUsers” uprawnień do odczytu i wykonywania do katalogu projektu programu Visual Studio.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
Teraz, gdy aplikacja działa lokalnie, rozpocznij przygotowania do wdrożenia na platformie Azure.  Obrazy kontenerów muszą być przechowywane w rejestrze kontenerów.  Utwórz usługę [Azure Container Registry](/azure/container-registry/container-registry-intro) za pomocą następującego skryptu. Nazwa rejestru kontenera jest widoczna w innych subskrypcjach platformy Azure, dlatego musi być unikatowa.
Przed wdrożeniem aplikacji na platformie Azure wypchnij obraz kontenera do tego rejestru.  Gdy aplikacja jest wdrażana na klastrze platformy Azure, obraz kontenera jest ściągany z tego rejestru.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Tworzenie klastra usługi Service Fabric na platformie Azure
Aplikacje usługi Service Fabric działają w klastrze — połączonym z siecią zestawie maszyn wirtualnych lub fizycznych.  Przed wdrożeniem aplikacji na platformie Azure utwórz klaster usługi Service Fabric na platformie Azure.

Możesz:
- Utworzyć klaster testowy z poziomu programu Visual Studio. Ta opcja służy do tworzenia bezpiecznego klastra bezpośrednio z poziomu programu Visual Studio z preferowaną konfiguracją. 
- [Utworzyć zabezpieczony klaster na podstawie szablonu](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

W ramach tego samouczka klaster jest tworzony za pomocą programu Visual Studio, co jest bardzo przydatne w przypadku scenariuszy testowych. Jeśli utworzono klaster w inny sposób lub jest używany istniejący klaster, możesz skopiować i wkleić punkt końcowy połączenia lub wybrać go z subskrypcji. 

Podczas tworzenia klastra wybierz jednostkę SKU, która obsługuje uruchomione kontenery. System operacyjny Windows Server na węzłach klastra musi być zgodny z systemem operacyjnym Windows Server kontenera. Aby dowiedzieć się więcej, zobacz temat [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) (Zgodność między systemami operacyjnymi Windows Server kontenera i hosta). Domyślnie w tym samouczku zostanie utworzony obraz platformy Docker opartej na systemie Windows Server 2016 LTSC. Kontenery oparte na tym obrazie zostaną uruchomione na klastrach utworzonych za pomocą Windows Server 2016 Datacenter with Containers. Jednak jeśli utworzysz klaster lub użyjesz istniejącego klastra w oparciu o Windows Server Datacenter Core 1709 with Containers, zmień obraz systemu operacyjnego Windows Server, na którym jest oparty kontener. Otwórz plik **Dockerfile** w projekcie **FabrikamFiber.Web**, usuń komentarz dotyczący istniejącej instrukcji akcji `FROM` (na podstawie `windowsservercore-ltsc`) i usuń komentarz dotyczący instrukcji akcji `FROM` na podstawie `windowsservercore-1709`. 

1. Kliknij prawym przyciskiem myszy projekt aplikacji **FabrikamFiber.CallCenterApplication** w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

2. Zaloguj się przy użyciu konta platformy Azure, aby uzyskać dostęp do subskrypcji. 

3. Wybierz listę rozwijaną **Punkt końcowy połączenia**, a następnie wybierz polecenie **Utwórz nowy klaster...**.    
        
4. W oknie dialogowym **Tworzenie klastra** zmodyfikuj następujące ustawienia:

    1. Określ nazwę klastra w polu **Nazwa klastra**, a także subskrypcję i lokalizację, których chcesz użyć.
    2. Opcjonalnie możesz zmodyfikować liczbę węzłów. Domyślne ustawienie to trzy węzły, czyli minimalna liczba wymagana do testowania scenariuszy usługi Service Fabric.
    3. Wybierz kartę **Certyfikat**. Na tej karcie wpisz hasło zabezpieczające certyfikat klastra. Ten certyfikat pomaga zabezpieczyć klaster. Możesz również zmodyfikować ścieżkę, w której ma zostać zapisany certyfikat. Program Visual Studio może również automatycznie zaimportować certyfikat, ponieważ jest to krok wymagany do opublikowania aplikacji w klastrze.
    4. Wybierz kartę **Szczegóły maszyny wirtualnej**. Określ hasło, którego chcesz używać dla maszyn wirtualnych wchodzących w skład klastra. Za pomocą nazwy użytkownika i hasła można zdalnie łączyć się z maszynami wirtualnymi. Należy również wybrać rozmiar maszyny wirtualnej oraz zmienić obraz maszyny wirtualnej, jeśli jest to konieczne.
    5. Na karcie **Zaawansowane** wyświetl port aplikacji otwierany w module równoważenia obciążenia przy wdrażaniu klastra. W Eksploratorze rozwiązań otwórz plik FabrikamFiber.Web -> PackageRoot -> ServiceManifest.xml.  Numer portu frontonu internetowego znajduje się w polu **Punkt końcowy**.  Można również dodać istniejący klucz usługi Application Insights, do którego mają być kierowane pliki dziennika aplikacji.
    6. Po zakończeniu modyfikowania ustawień wybierz przycisk **Utwórz**. 
5. Tworzenie klastra może zająć kilka minut. Informacja o ukończeniu tworzenia klastra pojawi się w oknie danych wyjściowych.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Zezwalanie aplikacji uruchomionej na platformie Azure na dostęp do usługi SQL DB
Wcześniej dla usługi SQL została utworzona reguła zapory, która udziela dostępu aplikacji uruchomionej lokalnie.  W następnej kolejności należy włączyć dostęp do usługi SQL DB dla aplikacji uruchomionej na platformie Azure.  Utwórz [punkt końcowy usługi Virtual Network](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) dla klastra usługi Service Fabric, a następnie utwórz regułę, która umożliwia dostęp do usługi SQL DB temu punktowi końcowemu.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Wdrażanie aplikacji na platformie Azure
Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze platformy Azure bezpośrednio z programu Visual Studio.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt aplikacji **FabrikamFiber.CallCenterApplication** i wybierz polecenie **Publikuj**.  W polu **Punkt końcowy połączenia** wybierz punkt końcowy klastra, który został utworzony wcześniej.  W polu **Azure Container Registry** wybierz rejestr kontenerów utworzony wcześniej.  Kliknij polecenie **Publikuj**, aby wdrożyć aplikację w klastrze platformy Azure.

![Publikowanie aplikacji][publish-app]

Śledzenie postępu wdrażania w oknie danych wyjściowych.  Gdy aplikacja jest wdrażana, otwórz przeglądarkę, a następnie wpisz adres klastra i port aplikacji. Na przykład http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Przykład internetowy dla firmy Fabrikam][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Konfigurowanie ciągłej integracji i ciągłego wdrażania (CI/CD) przy użyciu klastra usługi Service Fabric
Aby dowiedzieć się, jak skonfigurować wdrożenie aplikacji ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric za pomocą usługi Azure DevOps, zobacz [Samouczek: wdrażanie aplikacji przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Proces opisany w tym samouczku jest taki sam dla tego projektu (FabrikamFiber) — wystarczy po prostu pominąć pobieranie przykładu Voting i zastąpić nazwę repozytorium Voting nazwą FabrikamFiber.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu pracy usuń wszystkie utworzone zasoby.  Najprostszym sposobem jest usunięcie grup zasobów, które zawierają klaster usługi Service Fabric, usługę Azure SQL DB i usługę Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konteneryzowanie istniejącej aplikacji za pomocą programu Visual Studio
> * Tworzenie bazy danych SQL Azure
> * Tworzenie usługi Azure Container Registry
> * Wdrażanie aplikacji usługi Service Fabric na platformie Azure

Z następnej części samouczka dowiesz się, jak [wdrażać aplikację kontenera przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
