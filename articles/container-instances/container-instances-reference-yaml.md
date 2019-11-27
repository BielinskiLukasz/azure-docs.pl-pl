---
title: YAML odwołanie do grupy kontenerów
description: Odwołanie do pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania grupy kontenerów
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533484"
---
# <a name="yaml-reference-azure-container-instances"></a>Odwołanie YAML: Azure Container Instances

W tym artykule opisano składnię i właściwości pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania [grupy kontenerów](container-instances-container-groups.md). Użyj pliku YAML, aby wprowadzić konfigurację grupy do polecenia [AZ Container Create][az-container-create] w interfejsie CLI platformy Azure. 

Plik YAML jest wygodnym sposobem konfigurowania grupy kontenerów dla powtarzalnych wdrożeń. Jest to zwięzła alternatywa dla używania [szablonu Menedżer zasobów](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) lub zestawów SDK Azure Container Instances do tworzenia lub aktualizowania grupy kontenerów.

> [!NOTE]
> To odwołanie dotyczy plików YAML Azure Container Instances `2018-10-01`interfejsu API REST.

## <a name="schema"></a>Schemat 

Schemat pliku YAML jest następujący, w tym komentarze do wyróżniania właściwości klucza. Aby uzyskać opis właściwości w tym schemacie, zapoznaj się z sekcją [wartości właściwości](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Wartości właściwości

W poniższych tabelach opisano wartości, które należy ustawić w schemacie.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Obiekt Microsoft. ContainerInstance/containerGroups

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Yes | Nazwa grupy kontenerów. |
|  apiVersion | Wyliczenia | Yes | 2018-10-01 |
|  location | ciąg | Nie | Lokalizacja zasobu. |
|  tagów | obiekt | Nie | Tagi zasobów. |
|  identity | obiekt | Nie | Tożsamość grupy kontenerów, jeśli została skonfigurowana. - [obiekt ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | obiekt | Yes | [Obiekt ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Obiekt ContainerGroupIdentity

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  type | Wyliczenia | Nie | Typ tożsamości używany dla grupy kontenerów. Typ "SystemAssigned, UserAssigned" obejmuje zarówno niejawnie utworzoną tożsamość, jak i zestaw tożsamości przypisanych do użytkownika. Typ "none" usunie wszystkie tożsamości z grupy kontenerów. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, brak |
|  Resourceidentity | obiekt | Nie | Lista tożsamości użytkowników skojarzonych z grupą kontenerów. Odwołania do klucza słownika tożsamości użytkownika będą Azure Resource Manager identyfikatorów zasobów w postaci: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Obiekt ContainerGroupProperties

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  containers | tablica | Yes | Kontenery w obrębie grupy kontenerów.[obiekt kontenera](#Container)  -  |
|  imageRegistryCredentials | tablica | Nie | Poświadczenia rejestru obrazu, na podstawie których zostanie utworzona grupa kontenerów. - [obiekt ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Wyliczenia | Nie | Zasady ponownego uruchamiania dla wszystkich kontenerów w grupie kontenerów. - `Always` zawsze uruchamiaj ponownie `OnFailure` ponownego uruchomienia po awarii — `Never` nigdy nie uruchamiaj ponownie. -Zawsze, OnFailure, nigdy |
|  Adresu | obiekt | Nie | Typ adresu IP grupy kontenerów. - [obiekt IPAddress](#IpAddress) |
|  osType | Wyliczenia | Yes | Typ systemu operacyjnego wymagany przez kontenery w grupie kontenerów. — Windows lub Linux |
|  volumes | tablica | Nie | Lista woluminów, które mogą być instalowane przez kontenery w tej grupie kontenerów. - [obiektu woluminu](#Volume) |
|  Diagnostyka | obiekt | Nie | Informacje diagnostyczne dla grupy kontenerów. - [obiekt ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | obiekt | Nie | Informacje o profilu sieciowym dla grupy kontenerów. - [obiekt ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  Powodzenie | obiekt | Nie | Informacje o konfiguracji DNS dla grupy kontenerów. - [obiekt DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Obiekt kontenera

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Yes | Nazwa podana przez użytkownika wystąpienia kontenera. |
|  properties | obiekt | Yes | Właściwości wystąpienia kontenera. - [obiekt ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Obiekt ImageRegistryCredential

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  serwer | ciąg | Yes | Serwer rejestru platformy Docker bez protokołu, takiego jak "http" i "https". |
|  nazwa użytkownika | ciąg | Yes | Nazwa użytkownika dla rejestru prywatnego. |
|  hasło | ciąg | Nie | Hasło do rejestru prywatnego. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress — obiekt

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  ports | tablica | Yes | Lista portów uwidocznionych w grupie kontenerów.[obiekt portu](#Port)  -  |
|  type | Wyliczenia | Yes | Określa, czy adres IP jest narażony na publiczny Internet lub prywatną sieć wirtualną. — Publiczne lub prywatne |
|  przegląd | ciąg | Nie | Adres IP uwidoczniony dla publicznego Internetu. |
|  dnsNameLabel | ciąg | Nie | Etykieta nazwy DNS dla adresu IP. |


<a id="Volume" />

### <a name="volume-object"></a>Obiekt woluminu

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Yes | Nazwa woluminu. |
|  azureFile | obiekt | Nie | Wolumin plików platformy Azure. - [obiekt AzureFileVolume](#AzureFileVolume) |
|  emptyDir | obiekt | Nie | Pusty wolumin katalogu. |
|  wpis tajny | obiekt | Nie | Wolumin tajny. |
|  gitRepo | obiekt | Nie | Wolumin repozytorium git. - [obiekt GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Obiekt ContainerGroupDiagnostics

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | obiekt | Nie | Informacje o usłudze log Analytics grupy kontenerów. - [obiekt LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Obiekt ContainerGroupNetworkProfile

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  id | ciąg | Yes | Identyfikator profilu sieciowego. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Obiekt DnsConfiguration

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Serwery nazw | tablica | Yes | Serwery DNS dla grupy kontenerów. -String |
|  searchDomains | ciąg | Nie | Domeny wyszukiwania DNS dla wyszukiwania nazw hostów w grupie kontenerów. |
|  options | ciąg | Nie | Opcje usługi DNS dla grupy kontenerów. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Obiekt ContainerProperties

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  image | ciąg | Yes | Nazwa obrazu użytego do utworzenia wystąpienia kontenera. |
|  command | tablica | Nie | Polecenia do wykonania w ramach wystąpienia kontenera w formularzu exec. -String |
|  ports | tablica | Nie | Uwidocznione porty w wystąpieniu kontenera. - [obiekt ContainerPort](#ContainerPort) |
|  environmentVariables | tablica | Nie | Zmienne środowiskowe do ustawienia w wystąpieniu kontenera. - [obiekt zmiennych środowiskowych](#EnvironmentVariable) |
|  produkcyjnych | obiekt | Yes | Wymagania dotyczące zasobów wystąpienia kontenera. - [obiekt ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | tablica | Nie | Zainstaluj woluminy dostępne dla wystąpienia kontenera. - [obiekt VolumeMount](#VolumeMount) |
|  livenessProbe | obiekt | Nie | Sonda na żywo. - [obiekt ContainerProbe](#ContainerProbe) |
|  readinessProbe | obiekt | Nie | Sonda gotowości. - [obiekt ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Obiekt portu

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  protocol | Wyliczenia | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | liczba całkowita | Yes | Numer portu. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Obiekt AzureFileVolume

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  shareName | ciąg | Yes | Nazwa udziału plików platformy Azure, który ma zostać zainstalowany jako wolumin. |
|  Trybie | wartość logiczna | Nie | Flaga wskazująca, czy plik platformy Azure udostępniony jako wolumin jest tylko do odczytu. |
|  storageAccountName | ciąg | Yes | Nazwa konta magazynu zawierającego udział plików platformy Azure. |
|  storageAccountKey | ciąg | Nie | Klucz dostępu konta magazynu używany do uzyskiwania dostępu do udziału plików platformy Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Obiekt GitRepoVolume

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  katalogi | ciąg | Nie | Nazwa katalogu docelowego. Nie może zawierać ani zaczynać się od "..".  Jeśli zostanie podana wartość ".", katalog woluminu będzie repozytorium git.  W przeciwnym razie wolumin będzie zawierać repozytorium Git w podkatalogu o podanej nazwie. |
|  repozytorium | ciąg | Yes | Adres URL repozytorium |
|  skorygowan | ciąg | Nie | Skrót zatwierdzenia dla określonej poprawki. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Obiekt LogAnalytics

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  workspaceId | ciąg | Yes | Identyfikator obszaru roboczego dla usługi log Analytics |
|  workspaceKey | ciąg | Yes | Klucz obszaru roboczego dla usługi log Analytics |
|  logType | Wyliczenia | Nie | Typ dziennika, który ma być używany. -ContainerInsights lub ContainerInstanceLogs |
|  metadane | obiekt | Nie | Metadane usługi log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Obiekt ContainerPort

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  protocol | Wyliczenia | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | liczba całkowita | Yes | Numer portu uwidoczniony w obrębie grupy kontenerów. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Obiekt zmiennych środowiskowych

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Yes | Nazwa zmiennej środowiskowej. |
|  wartość | ciąg | Nie | Wartość zmiennej środowiskowej. |
|  secureValue | ciąg | Nie | Wartość zmiennej środowiskowej Secure. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Obiekt ResourceRequirements

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  Żądania | obiekt | Yes | Żądania zasobów tego wystąpienia kontenera. - [obiekt ResourceRequests](#ResourceRequests) |
|  ograniczeń | obiekt | Nie | Limity zasobów tego wystąpienia kontenera. - [obiekt ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Obiekt VolumeMount

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Yes | Nazwa instalacji woluminu. |
|  mountPath | ciąg | Yes | Ścieżka wewnątrz kontenera, w którym należy zainstalować wolumin. Nie może zawierać dwukropka (:). |
|  Trybie | wartość logiczna | Nie | Flaga wskazująca, czy instalacja woluminu jest tylko do odczytu. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Obiekt ContainerProbe

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  szef | obiekt | Nie | Wykonywanie polecenia do sondowania — [obiekt ContainerExec](#ContainerExec) |
|  Narzędzia HttpGet | obiekt | Nie | Ustawienia http get do Probe- [ContainerHttpGet Object](#ContainerHttpGet) |
|  initialDelaySeconds | liczba całkowita | Nie | Początkowe opóźnienie w sekundach. |
|  periodSeconds | liczba całkowita | Nie | Okres w sekundach. |
|  failureThreshold | liczba całkowita | Nie | Próg błędu. |
|  successThreshold | liczba całkowita | Nie | Próg sukcesu. |
|  timeoutSeconds | liczba całkowita | Nie | Limit czasu w sekundach. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Obiekt ResourceRequests

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | numer | Yes | Żądanie pamięci w GB tego wystąpienia kontenera. |
|  cpu | numer | Yes | Żądanie procesora CPU tego wystąpienia kontenera. |
|  gpu | obiekt | Nie | Żądanie procesora GPU tego wystąpienia kontenera. - [obiekt GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Obiekt ResourceLimits

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | numer | Nie | Limit pamięci w GB tego wystąpienia kontenera. |
|  cpu | numer | Nie | Limit procesorów dla tego wystąpienia kontenera. |
|  gpu | obiekt | Nie | Limit procesora GPU dla tego wystąpienia kontenera. - [obiekt GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Obiekt ContainerExec

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  command | tablica | Nie | Polecenia do wykonania w kontenerze. -String |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Obiekt ContainerHttpGet

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  path | ciąg | Nie | Ścieżka do sondy. |
|  port | liczba całkowita | Yes | Numer portu do sondowania. |
|  równaniu | Wyliczenia | Nie | Schemat. -http lub https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Obiekt GpuResource

|  Nazwa | Typ | Wymagany | Wartość |
|  ---- | ---- | ---- | ---- |
|  count | liczba całkowita | Yes | Liczba zasobów procesora GPU. |
|  sku | Wyliczenia | Yes | Jednostka SKU zasobu GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem [wdrażanie grupy wielokontenerowej przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

Zobacz przykłady użycia pliku YAML do wdrażania grup kontenerów w [sieci wirtualnej](container-instances-vnet.md) lub [instalowania woluminu zewnętrznego](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

