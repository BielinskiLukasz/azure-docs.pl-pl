---
title: Tworzenie pakietów i wdrażanie aplikacji kontenerów usługi Service Fabric | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wygenerować definicję aplikacji usługi Azure Service Fabric przy użyciu narzędzia Yeoman i utworzyć pakiet aplikacji.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: fc589b79cf91dcbe24e6d99da44aeee883b58e5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365389"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application"></a>Samouczek: tworzenie pakietów kontenerów i wdrażanie ich jako aplikacji usługi Service Fabric

Niniejszy samouczek jest drugą częścią serii. W tym samouczku narzędzie generatora szablonów (Yeoman) jest używane do generowania definicji aplikacji usługi Service Fabric. Ta aplikacja może być następnie używana do wdrażania kontenerów w usłudze Service Fabric. Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Instalowanie narzędzia Yeoman  
> * Tworzenie pakietu aplikacji za pomocą narzędzia Yeoman
> * Konfigurowanie ustawień w pakiecie aplikacji do użycia z kontenerami
> * Kompilowanie aplikacji  
> * Wdrażanie i uruchamianie aplikacji 
> * Czyszczenie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- Używane są obrazy kontenerów wypychane do usługi Azure Container Registry utworzone w [części 1](service-fabric-tutorial-create-container-images.md) tej serii samouczków.
- Środowisko projektowe systemu Linux jest [skonfigurowane](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Instalowanie narzędzia Yeoman
Usługa Service Fabric udostępnia narzędzia do tworzenia szkieletów, które ułatwiają tworzenie aplikacji z poziomu terminalu przy użyciu generatora szablonów narzędzia Yeoman. Wykonaj poniższe kroki, aby upewnić się, że generator szablonów Yeoman jest zainstalowany. 

1. Zainstaluj środowisko Node.js i menedżera NPM na swojej maszynie. Należy pamiętać, że użytkownicy systemu Mac OSX będą musieli używać menedżera pakietów Homebrew

    ```bash
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash –
    sudo apt-get install -y nodejs 
    ```
2. Zainstaluj generator szablonów narzędzia Yeoman na swoim komputerze z poziomu narzędzia NPM 

    ```bash
    sudo npm install -g yo
    ```
3. Zainstaluj generator kontenerów narzędzia Yeoman usługi Service Fabric

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Pakowanie kontenera obrazu platformy Docker przy użyciu narzędzia Yeoman

1. Aby utworzyć aplikację kontenera usługi Service Fabric, w katalogu „container-tutorial” sklonowanego repozytorium uruchom poniższe polecenie.

    ```bash
    yo azuresfcontainer
    ```
2. Wpisz wartość „TestContainer”, aby nadać nazwę aplikacji
3. Wpisz wartość „azurevotefront”, aby nadać nazwę usłudze aplikacji.
4. Podaj ścieżkę obrazu kontenera w usłudze ACR dla repozytorium frontonu — na przykład „\<nazwaACR>.azurecr.io/azure-vote-front:v1”. Wartość pola \<nazwaACR> musi być taka sama jak wartość, której użyto w poprzednim samouczku.
5. Naciśnij klawisz Enter, aby pozostawić pustą sekcję Polecenia.
6. Określ liczbę wystąpień jako „1”.

Poniżej pokazano dane wejściowe i dane wyjściowe polecenia yo:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Aby dodać kolejną usługę kontenera do aplikacji już utworzonej przy użyciu narzędzia Yeoman, wykonaj następujące czynności:

1. Zmień katalog o jeden poziom na katalog **TestContainer**, na przykład *./TestContainer*
2. Uruchom polecenie `yo azuresfcontainer:AddService` 
3. Nazwij usługę „azurevoteback”
4. Podaj ścieżkę obrazu kontenera dla usługi Redis — „alpine:redis”
5. Naciśnij klawisz Enter, aby pozostawić sekcję Polecenia pustą
6. Określ liczbę wystąpień jako „1”.

Wszystkie wpisy używane do dodania tej usługi są podane poniżej:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

W pozostałej części tego samouczka pracujemy w katalogu **TestContainer**. Na przykład *./TestContainer/TestContainer*. Zawartość tego katalogu powinna być następująca.
```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Konfigurowanie manifestu aplikacji przy użyciu poświadczeń dla usługi Azure Container Registry
Aby usługa Service Fabric pobierała obrazy kontenerów z usługi Azure Container Registry, należy podać poświadczenia w pliku **ApplicationManifest.xml**. 

Zaloguj się do swojego wystąpienia usługi ACR. Aby wykonać tę operację, użyj polecenia **az acr login**. Podaj unikatową nazwę nadaną rejestrowi kontenerów podczas jego tworzenia.

```bash
az acr login --name <acrName>
```

Polecenie zwraca komunikat **Logowanie pomyślne** po ukończeniu.

Następnie uruchom następujące polecenie, aby pobrać hasło rejestru kontenerów. To hasło jest używane przez usługę Service Fabric do uwierzytelniania w usłudze ACR na potrzeby ściągania obrazów kontenera.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

W pliku **ApplicationManifest.xml** dodaj fragment kodu pod elementem **ServiceManifestImport** dla usługi frontonu. Wstaw wartość **nazwaACR** w polu **AccountName**, a hasło zwrócone przez poprzednie polecenie — w polu **Password**. Pełny plik **ApplicationManifest.xml** znajduje się na końcu tego dokumentu. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurowanie komunikacji i mapowania portów kontenera typu „port do hosta”

### <a name="configure-communication-port"></a>Konfigurowanie portu komunikacji

Skonfiguruj punkt końcowy HTTP, aby klienci mogli komunikować się z usługą. Otwórz plik *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* i zadeklaruj zasób punktu końcowego w elemencie **ServiceManifest**.  Dodaj protokół, port i nazwę. W tym samouczku usługa nasłuchuje na porcie 80. Poniższy fragment kodu jest umieszczony pod tagiem *ServiceManifest* w zasobie.
  
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
  
Podobnie zmodyfikuj manifest usługi dla usługi zaplecza. Otwórz plik *./TestContainer/azurevotebackPkg/ServiceManifest.xml* i zadeklaruj zasób punktu końcowego w elemencie **ServiceManifest**. W tym samouczku stosowana jest domyślna wartość usługi Redis — 6379. Poniższy fragment kodu jest umieszczony pod tagiem *ServiceManifest* w zasobie.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```
Jeśli zostanie określony parametr **UriScheme**, punkt końcowy kontenera zostanie automatycznie zarejestrowany w usłudze nazewnictwa Service Fabric, aby można go było odnaleźć. Pełny przykładowy plik ServiceManifest.xml dla usługi zaplecza znajduje się na końcu tego artykułu. 

### <a name="map-container-ports-to-a-service"></a>Mapowanie portów kontenerów na usługę
Aby udostępnić kontenery w klastrze, musimy również utworzyć powiązanie portu w pliku ApplicationManifest.xml. Zasady **PortBinding** odwołują się do punktów końcowych (**Endpoints**) zdefiniowanych w plikach **ServiceManifest.xml**. Żądania przychodzące do tych punktów końcowych są mapowane na porty kontenera, które są otwarte i ograniczone w tym miejscu. W pliku **ApplicationManifest.xml** dodaj następujący kod, aby powiązać porty 80 i 6379 z punktami końcowymi. Pełny plik **ApplicationManifest.xml** znajduje się na końcu tego dokumentu. 
  
```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Dodawanie nazwy DNS do usługi zaplecza
  
Aby usługa Service Fabric przypisała tę nazwę DNS do usługi zaplecza, nazwa musi być określona w pliku **ApplicationManifest.xml**. Dodaj atrybut **ServiceDnsName** do elementu **Service** w następujący sposób: 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Usługa frontonu odczytuje wartość zmiennej środowiskowej, aby poznać nazwę DNS wystąpienia usługi Redis. Ta zmienna środowiskowa jest już zdefiniowana w pliku Dockerfile użytym do generowania obrazu Docker i nie trzeba wykonywać żadnych działań w tym miejscu.
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
Poniższy fragment kodu przedstawia sposób, w jaki kod Python frontonu przejmuje zmienną środowiskową opisaną w pliku Dockerfile. Nie trzeba tutaj wykonywać żadnego działania. 

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

W tym punkcie samouczka szablon dla aplikacji pakietu usługi jest dostępny do wdrożenia w klastrze. W kolejnym samouczku ta aplikacja zostanie wdrożona i uruchomiona w klastrze usługi Service Fabric.

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric
Aby wdrożyć aplikację w klastrze na platformie Azure, utwórz własny klaster.

Klastry testowe to bezpłatne, działające przez ograniczony czas klastry usługi Service Fabric hostowane na platformie Azure. Są one obsługiwane przez zespół usługi Service Fabric. Każdy może wdrażać w nich aplikacje i poznawać szczegółowo platformę. Aby uzyskać dostęp do klastra testowego, [postępuj zgodnie z instrukcjami](http://aka.ms/tryservicefabric). 

W celu wykonywania operacji zarządzania w zabezpieczonym klastrze testowym można użyć narzędzia Service Fabric Explorer, interfejsu wiersza polecenia lub programu PowerShell. Aby korzystać z narzędzia Service Fabric Explorer, należy załadować plik PFX z witryny internetowej klastra testowego i zaimportować certyfikat do magazynu certyfikatów (w systemie Windows lub Mac) lub do przeglądarki (w systemie Ubuntu). Nie ma żadnego hasła dla certyfikatów z podpisem własnym z klastra testowego. 

Aby wykonywać operacje zarządzania za pomocą programu PowerShell lub interfejsu wiersza polecenia, potrzebny będzie plik PFX (w przypadku programu PowerShell) lub PEM (w przypadku interfejsu wiersza polecenia). Aby przekonwertować plik PFX na plik PEM, uruchom następujące polecenie:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Aby uzyskać informacje na temat tworzenia własnego klastra, zobacz [Tworzenie klastra usługi Service Fabric na platformie Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Tworzenie i wdrażanie aplikacji w klastrze
Aplikację można wdrożyć w klastrze platformy Azure za pomocą interfejsu wiersza polecenia usługi Service Fabric. Jeśli interfejs wiersza polecenia usługi Service Fabric nie jest zainstalowany, wykonaj instrukcje podane [tutaj](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli), aby go zainstalować. 

Połącz się z klastrem usługi Service Fabric na platformie Azure. Zamień zastępczy punkt końcowy na własny. Punkt końcowy musi być pełnym adresem URL podobnym do przedstawionego poniżej.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

Użyj skryptu instalacji udostępnionego w katalogu **TestContainer**, aby skopiować pakiet aplikacji do magazynu obrazów klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

```bash
./install.sh
```

Otwórz przeglądarkę i przejdź do narzędzia Service Fabric Explorer na stronie http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer. Rozwiń węzeł Aplikacje i zwróć uwagę, że istnieje wpis dla danego typu aplikacji i inny wpis dla wystąpienia.

![Service Fabric Explorer][sfx]

W celu nawiązania połączenia z działającą aplikacją otwórz przeglądarkę internetową i przejdź do adresu URL klastra, na przykład http://lin0823ryf2he.cloudapp.azure.com:80. W przeglądarce powinien zostać wyświetlony internetowy interfejs użytkownika.

![votingapp][votingapp]

## <a name="clean-up"></a>Czyszczenie
Użyj skryptu dezinstalacji udostępnionego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji. To polecenie wymaga pewnego czasu, aby wyczyścić wystąpienie. Polecenia „install.sh” nie można uruchamiać natychmiast po tym skrypcie. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Przykłady ukończonych manifestów

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies> 
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies> 
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml 
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```
## <a name="next-steps"></a>Następne kroki

W tym samouczku wiele kontenerów zostało umieszczonych w aplikacji usługi Service Fabric przy użyciu narzędzia Yeoman. Ta aplikacja została następnie wdrożona i uruchomiona w klastrze usługi Service Fabric. Wykonano następujące czynności:

> [!div class="checklist"]
> * Instalowanie narzędzia Yeoman  
> * Tworzenie pakietu aplikacji za pomocą narzędzia Yeoman
> * Konfigurowanie ustawień w pakiecie aplikacji do użycia z kontenerami
> * Kompilowanie aplikacji  
> * Wdrażanie i uruchamianie aplikacji 
> * Czyszczenie aplikacji

Przejdź do następnego samouczka, aby uzyskać informacje na temat trybu failover i skalowania aplikacji Service Fabric.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o trybie failover i skalowaniu aplikacji](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


