---
title: "Integrowanie usługi Azure Service Fabric z usługą API Management | Microsoft Docs"
description: "Dowiedz się, jak szybko rozpocząć pracę z usługami Azure API Management i Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7265af62adf8f380a3a5bd5d9950fb70e9ad6172
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-api-management-with-service-fabric"></a>Wdrażanie usługi API Management z usługą Service Fabric
Niniejszy samouczek jest czwartą częścią serii.  Wdrażanie usługi Azure API Management z usługą Service Fabric odbywa się w ramach zaawansowanego scenariusza.  Usługa API Management jest przydatna, gdy trzeba opublikować interfejsy API z obszernym zestawem reguł rozsyłania dla usług zaplecza Service Fabric. Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. W usłudze Service Fabric bramą może być dowolna usługa bezstanowa przeznaczona dla ruchu przychodzącego, na przykład aplikacja ASP.NET Core albo usługa Event Hubs, IoT Hub lub Azure API Management. 

W tym samouczku przedstawiono sposób konfigurowania usługi [Azure API Management](../api-management/api-management-key-concepts.md) z usługą Service Fabric pod kątem kierowania ruchu do usługi zaplecza w usłudze Service Fabric.  Wykonanie podanych instrukcji pozwoli wdrożyć usługę API Management w sieci wirtualnej i skonfigurować kierowanie ruchu do bezstanowych usług zaplecza za pomocą operacji interfejsu API. Aby dowiedzieć się więcej o scenariuszach użycia usługi Azure API Management z usługą Service Fabric, zobacz artykuł z [omówieniem](service-fabric-api-management-overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie usługi API Management
> * Konfigurowanie usługi API Management
> * Tworzenie operacji interfejsu API
> * Konfigurowanie zasad zaplecza
> * Dodawanie interfejsu API do produktu

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) na platformie Azure przy użyciu szablonu
> * [Skalowanie klastra na zewnątrz lub do wewnątrz](service-fabric-tutorial-scale-cluster.md)
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * Wdrażanie usługi API Management z usługą Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka:
- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zainstaluj [moduł Azure PowerShell w wersji 4.1 lub nowszej](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) albo [interfejs wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).
- Utwórz bezpieczny [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [klaster systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) na platformie Azure.
- W przypadku wdrażania klastra systemu Windows skonfiguruj środowisko deweloperskie w systemie Windows. Zainstaluj program [Visual Studio 2017](http://www.visualstudio.com), a następnie zainstaluj obciążenia **Programowanie na platformie Azure**, **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych** oraz **Programowanie dla wielu platform w środowisku .NET Core**.  Następnie skonfiguruj [środowisko deweloperskie platformy .NET](service-fabric-get-started.md).
- W przypadku wdrażania klastra systemu Linux skonfiguruj środowisko projektowe Java w systemie [Linux](service-fabric-get-started-linux.md) lub [MacOS](service-fabric-get-started-mac.md).  Zainstaluj [interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md). 

## <a name="network-topology"></a>Topologia sieci
Gdy już masz bezpieczny [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [klaster systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) na platformie Azure, wdróż usługę API Management w sieci wirtualnej (VNET) należącej do podsieci oraz sieciową grupę zabezpieczeń przeznaczoną dla usługi API Management. Wstępna konfiguracja szablonu usługi Resource Manager dla usługi API Management używanego w tym samouczku obejmuje użycie nazw sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń, które skonfigurowano w poprzednim [samouczku dotyczącym klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) lub [samouczku dotyczącym klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). W tym samouczku na platformie Azure zostanie wdrożona następująca topologia. Na ilustracji widać, że usługi API Management i Service Fabric znajdują się w podsieciach tej samej sieci wirtualnej:

 ![Podpis pod obrazem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Logowanie do platformy Azure i wybieranie subskrypcji
Przed wykonaniem poleceń platformy Azure zaloguj się na konto platformy Azure i wybierz subskrypcję.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Wdrażanie usługi zaplecza Service Fabric

Do skonfigurowania usługi API Management pod kątem kierowania ruchu do usługi zaplecza Service Fabric potrzebna jest uruchomiona usługa, umożliwiająca akceptowanie żądań.  Jeśli wcześniej utworzono [klaster systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), wdróż usługę Service Fabric platformy .NET.  Jeśli wcześniej utworzono [klaster systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), wdróż usługę Service Fabric platformy Java.

### <a name="deploy-a-net-service-fabric-service"></a>Wdrażanie usługi Service Fabric platformy .NET

W tym samouczku zostanie utworzona podstawowa niezawodna usługa bezstanowa ASP.NET Core przy użyciu szablonu projektu domyślnego internetowego interfejsu API. Spowoduje to utworzenie punktu końcowego HTTP dla usługi, który należy udostępnić za pośrednictwem usługi Azure API Management.

Uruchom program Visual Studio jako administrator i utwórz usługę ASP.NET Core:

 1. W programie Visual Studio wybierz pozycję Plik -> Nowy projekt.
 2. W obszarze Chmura wybierz szablon aplikacji Service Fabric i nadaj mu nazwę **„ApiApplication”**.
 3. Wybierz szablon bezstanowej usługi ASP.NET Core i nazwij projekt **„WebApiService”**.
 4. Wybierz szablon projektu Web API ASP.NET Core 2.0.
 5. Po utworzeniu projektu otwórz plik `PackageRoot\ServiceManifest.xml` i usuń atrybut `Port` z konfiguracji zasobu punktu końcowego:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Usuwanie portu pozwala usłudze Service Fabric dynamicznie wskazać port z zakresu portów aplikacji, otwartego za pomocą sieciowej grupy zabezpieczeń sieci w szablonie usługi Resource Manager klastra, co umożliwia przepływ ruchu z usługi API Management do tego portu.
 
 6. W programie Visual Studio naciśnij klawisz F5, aby sprawdzić, czy internetowy interfejs API jest dostępny lokalnie. 

    Otwórz narzędzie Service Fabric Explorer i przejdź do konkretnego wystąpienia usługi ASP.NET Core, aby wyświetlić podstawowy adres, na jakim nasłuchuje ta usługa. Dodaj ciąg `/api/values` do podstawowego adresu, a następnie otwórz ten adres w przeglądarce. Spowoduje to wywołanie metody Get klasy ValuesController w szablonie internetowego interfejsu API. Szablon zwróci domyślną odpowiedź — tablicę JSON, zawierającą dwa ciągi:

    ```json
    ["value1", "value2"]`
    ```

    Jest to punkt końcowy, który należy udostępnić za pomocą usługi API Management na platformie Azure.

 7. Teraz wdróż aplikację w klastrze na platformie Azure. W programie Visual Studio kliknij prawym przyciskiem myszy projekt aplikacji, a następnie wybierz polecenie **Opublikuj**. Podaj punkt końcowy klastra (na przykład `mycluster.southcentralus.cloudapp.azure.com:19000`), aby wdrożyć aplikację w klastrze usługi Service Fabric na platformie Azure.

W klastrze powinna teraz być uruchomiona bezstanowa usługa ASP.NET Core o nazwie `fabric:/ApiApplication/WebApiService`.

### <a name="create-a-java-service-fabric-service"></a>Tworzenie usługi Service Fabric platformy Java
W tym samouczku zostanie wdrożony podstawowy serwer sieci Web, która powtarza komunikaty, kierując je z powrotem do użytkownika. Przykładowa aplikacja serwera zawiera punkt końcowy HTTP dla usługi, który należy udostępnić za pośrednictwem usługi Azure API Management.

1. Sklonuj przykłady umożliwiające rozpoczęcie pracy z językiem Java.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Zmodyfikuj plik *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Zaktualizuj punkt końcowy, tak aby usługa nasłuchiwała na porcie 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Zapisz plik *ServiceManifest.xml*, a następnie skompiluj aplikację EchoServer 1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Wdróż aplikację w klastrze.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   W klastrze powinna teraz być uruchomiona bezstanowa usługa Java o nazwie `fabric:/EchoServerApplication/EchoServerService`.

5. Otwórz przeglądarkę i wpisz ciąg http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Powinien pojawić się komunikat „[version 1.0]Hello World!!!”

## <a name="download-and-understand-the-resource-manager-templates"></a>Pobieranie szablonów usługi Resource Manager i zapoznawanie się z nimi
Pobierz i zapisz plik parametrów oraz następujące szablony usługi Resource Manager:
 
- [network-apim.json][network-arm]
- [network-apim.parameters.json][network-parameters-arm]
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Szablon *network-apim.json* umożliwia wdrożenie nowej podsieci i sieciowej grupy zabezpieczeń w sieci wirtualnej, w której wdrożono klaster usługi Service Fabric.

W poniższych sekcjach opisano zasoby definiowane za pomocą szablonu *apim.json*. Poszczególne sekcje zawierają linki do dokumentacji szablonów, umożliwiające uzyskanie dodatkowych informacji. Konfigurowalne parametry, zdefiniowane w pliku *apim.parameters.json*, zostaną ustawione w dalszej części tego artykułu.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
Szablon [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) opisuje wystąpienie usługi API Management i zawiera następujące informacje: nazwę, jednostkę SKU lub warstwę, lokalizację grupy zasobów oraz informacje o wydawcy i sieci wirtualnej.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
Szablon [Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) umożliwia konfigurowanie zabezpieczeń usługi API Management. W ramach odnajdywania usługi usługa API Management musi zostać uwierzytelniona w klastrze usługi Service Fabric przy użyciu certyfikatu klienta, który ma dostęp do klastra. W tym samouczku jest używany certyfikat określony wcześniej podczas tworzenia [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) lub [klastra systemu Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor) — domyślnie można go użyć w celu uzyskania dostępu do klastra. 

Ten sam certyfikat służy do uwierzytelniania klienta i zapewnienia zabezpieczeń między węzłami klastra. Jeśli masz skonfigurowany osobny certyfikat klienta, możesz go używać do korzystania z klastra usługi Service Fabric. Podaj **nazwę**, **hasło** i **dane** (szyfrowany ciąg base64) dotyczące pliku klucza prywatnego (pfx) certyfikatu klastra, który został określony podczas tworzenia klastra usługi Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
Szablon [Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) opisuje usługę zaplecza, do której jest przekazywany ruch. 

W przypadku usługi Service Fabric zapleczem jest klaster usługi Service Fabric, a nie określona usługa Service Fabric. Dzięki temu za pomocą jednej zasady można kierować ruch do więcej niż jednej usługi w klastrze. Pole **url** zawiera w pełni kwalifikowaną nazwę usługi w klastrze, do której są domyślnie kierowane wszystkie żądania, jeśli w zasadach zaplecza nie podano nazwy usługi. Jeśli nie potrzebujesz usługi rezerwowej, możesz użyć wymyślonej nazwy, takiej jak „fabric:/fake/service”. Pole **resourceId** określa punkt końcowy zarządzania klastrem.  Pola **clientCertificateThumbprint** i **serverCertificateThumbprints** identyfikują certyfikaty używane do uwierzytelniania w klastrze.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
Szablon [Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) umożliwia utworzenie produktu. W usłudze Azure API Management produkt zawiera co najmniej jeden interfejs API oraz limit przydziału użycia i warunki użytkowania. Po opublikowaniu produktu deweloperzy mogą go zasubskrybować i zacząć korzystać z jego interfejsów API. 

W polu **displayName** wpisz opisową nazwę produktu, a w polu **description** — opis. W tym samouczku wymagana jest subskrypcja, ale administrator nie musi jej zatwierdzać.  **Stan** tego produktu ma wartość „opublikowany” i jest widoczny dla subskrybentów. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
Szablon [Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) umożliwia utworzenie interfejsu API. Interfejs API usługi API Management reprezentuje zestaw operacji, które mogą być wywoływane przez aplikacje klienckie. Po dodaniu operacji do produktu jest dodawany interfejs API, który można opublikować. Opublikowany interfejs API można zasubskrybować, a deweloperzy mogą go używać.

- Pole **displayName** może zawierać dowolną nazwę interfejsu API. W tym samouczku jest używana nazwa „Service Fabric App”.
- Pole **name** zawiera unikatową i opisową nazwę interfejsu API, taką jak „service-fabric-app”. Jest ona wyświetlana w portalach dewelopera i wydawcy. 
- Pole **serviceUrl** zawiera odwołanie do usługi HTTP implementującej interfejs API. Usługa API Management przekazuje żądania na ten adres. W przypadku zapleczy usługi Service Fabric ta wartość adresu URL nie jest używana. Można tu wpisać dowolną wartość. Na przykład w tym samouczku jest to „http://servicefabric”. 
- Wartość pola **path** jest dołączana do podstawowego adresu URL usługi API Management. Podstawowy adres URL jest wspólny dla wszystkich interfejsów API hostowanych przez wystąpienie usługi API Management. W usłudze API Management interfejsy API są rozróżniane na podstawie sufiksów, dlatego sufiksy poszczególnych interfejsów API dla danego wydawcy muszą być unikatowe. 
- Pole **protocols** określa, których protokołów można używać w celu uzyskania dostępu do interfejsu API. W tym samouczku lista ta ma zawierać pozycje **http** i **https**.
- Pole **path** jest sufiksem interfejsu API. W tym samouczku należy użyć wartości „myapp”.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Przed użyciem interfejsu API usługi API Management należy dodać do niego operacje.  Klienci zewnętrzni używają operacji do komunikowania się z bezstanową usługą ASP.NET Core uruchomioną w klastrze usługi Service Fabric.

Aby dodać operację interfejsu API frontonu, podaj następujące wartości:

- Pola **displayName** i **description** opisują operację. W tym samouczku użyj ciągu „Values”.
- Pole **method** określa polecenie HTTP.  W tym samouczku podaj polecenie **GET**.
- Wartość pola **urlTemplate** jest dołączana do podstawowego adresu URL interfejsu API i określa pojedynczą operację HTTP.  Użyj wartości `/api/values`, jeśli dodano usługę zaplecza platformy .NET, lub wartości `getMessage`, jeśli dodano usługę zaplecza platformy Java.  Określona tutaj ścieżka URL jest domyślnie wysyłana do usługi Service Fabric zaplecza. W przypadku podania ścieżki URL używanej przez usługę, takiej jak „/api/values”, operacja działa bez dodatkowych modyfikacji. Można również podać ścieżkę URL, która różni się od ścieżki URL używanej przez usługę Service Fabric zaplecza. W takim przypadku należy później określić nadpisanie ścieżki w zasadach operacji.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
Szablon [Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) umożliwia utworzenie zasad zaplecza, które pozwalają zintegrować wszystkie elementy. Zasady te umożliwiają skonfigurowanie usługi Service Fabric zaplecza, do której są kierowane żądania. Zasady można zastosować do dowolnej operacji interfejsu API.  Aby uzyskać więcej informacji, zobacz [Omówienie zasad](/azure/api-management/api-management-howto-policies). 

[Konfiguracja zaplecza dla usługi Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) udostępnia następujące możliwości sterowania routingiem żądań: 
 - Wybieranie wystąpienia usługi przez podanie nazwy wystąpienia usługi Service Fabric — zakodowanej (na przykład `"fabric:/myapp/myservice"`) lub wygenerowanej na podstawie żądania HTTP (na przykład `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Rozpoznawanie partycji przez wygenerowanie klucza partycji przy użyciu dowolnego schematu partycjonowania usługi Service Fabric.
 - Wybieranie repliki dla usług stanowych.
 - Warunki ponownego rozpoznawania, które umożliwiają określenie warunków ponownego rozpoznawania lokalizacji usługi i wysyłania żądania.

**policyContent** to zawartość XML zasad, ujęta w znaki Json zmieniające znaczenie.  W tym samouczku utwórz zasady zaplecza kierujące żądania bezpośrednio do wcześniej wdrożonej usługi bezstanowej .NET lub Java. Dodaj zasady `set-backend-service` w obszarze zasad ruchu przychodzącego.  Wartość *sf-service-instance-name* zastąp ciągiem `fabric:/ApiApplication/WebApiService`, jeśli wcześniej wdrożono usługę zaplecza platformy .NET, lub ciągiem `fabric:/EchoServerApplication/EchoServerService`, jeśli wdrożono usługę platformy Java.  Pole *backend-id* zawiera odwołanie do zasobu zaplecza, w tym przypadku zasobu `Microsoft.ApiManagement/service/backends` zdefiniowanego w szablonie *apim.json*. Pole *backend-id* może także odwoływać się do innego zasobu zaplecza, utworzonego za pomocą interfejsów API usługi API Management. W tym samouczku ustaw pole *backend-id* na wartość parametru *service_fabric_backend_name*.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Aby uzyskać pełny zestaw atrybutów zasad zaplecza usługi Service Fabric, zobacz[API Management back-end documentation (Dokumentacja zaplecza usługi API Management)](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Ustawianie parametrów i wdrażanie usługi API Management
Wypełnij następujące puste parametry szablonu *apim.parameters.json* dla danego wdrożenia. 

|Parametr|Wartość|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;ciąg zakodowany w formacie Base-64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Ciąg XML&gt;|

Parametry *certificatePassword* i *serviceFabricCertificateThumbprint* muszą być zgodne z certyfikatem klastra użytym podczas konfigurowania klastra.  

Parametr *serviceFabricCertificate* zawiera certyfikat w postaci ciągu zakodowanego w formacie Base-64, który można wygenerować za pomocą następującego skryptu:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

W parametrze *inbound_policy* wartość *sf-service-instance-name* zastąp ciągiem `fabric:/ApiApplication/WebApiService`, jeśli wcześniej wdrożono usługę zaplecza platformy .NET, lub ciągiem `fabric:/EchoServerApplication/EchoServerService`, jeśli wdrożono usługę platformy Java. Pole *backend-id* zawiera odwołanie do zasobu zaplecza, w tym przypadku zasobu `Microsoft.ApiManagement/service/backends` zdefiniowanego w szablonie *apim.json*. Pole *backend-id* może także odwoływać się do innego zasobu zaplecza, utworzonego za pomocą interfejsów API usługi API Management. W tym samouczku ustaw pole *backend-id* na wartość parametru *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Następujący skrypt umożliwia wdrożenie szablonu i plików parametrów usługi Resource Manager dla usługi API Management:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Testowanie

Teraz możesz wysłać żądanie do usługi zaplecza Service Fabric za pośrednictwem usługi API Management bezpośrednio z witryny [Azure Portal](https://portal.azure.com).

 1. W usłudze API Management wybierz pozycję **Interfejs API**.
 2. W interfejsie API **aplikacji usługi Service Fabric** utworzonym w poprzednich krokach wybierz kartę **Test**, a następnie operację **Values**.
 3. Kliknij przycisk **Wyślij**, aby wysłać żądanie testowe do usługi zaplecza.  Powinna zostać wyświetlona mniej więcej taka odpowiedź HTTP:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT

    
    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Klaster składa się z innych zasobów platformy Azure poza samym zasobem klastra. Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Zaloguj się do platformy Azure i wybierz identyfikator subskrypcji, za pomocą którego chcesz usunąć klaster.  Identyfikator subskrypcji można uzyskać po zalogowaniu się do [witryny Azure Portal](http://portal.azure.com). Usuń grupę zasobów i wszystkie zasoby klastra, korzystając z polecenia cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie usługi API Management
> * Konfigurowanie usługi API Management
> * Tworzenie operacji interfejsu API
> * Konfigurowanie zasad zaplecza
> * Dodawanie interfejsu API do produktu

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
