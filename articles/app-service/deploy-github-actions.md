---
title: Wdróż swój kod z potoku ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub — Azure App Service | Microsoft Docs
description: Dowiedz się, jak wdrożyć kod przy użyciu akcji usługi GitHub w App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 9842057a590b08f2207a1ea166e0ce0d457e4381
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620512"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Wdrażanie do App Service przy użyciu akcji usługi GitHub

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Za pomocą Azure App Service działań w witrynie GitHub można zautomatyzować przepływ pracy w celu wdrożenia w [Azure App Service](overview.md) za pomocą akcji usługi GitHub.

> [!IMPORTANT]
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (. yml) w ścieżce `/.github/workflows/` w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

W przypadku przepływu pracy Azure App Service plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Uwierzytelnianie** | 1. Zdefiniuj nazwę główną usługi <br /> 2. Utwórz wpis tajny usługi GitHub |
|**Utworzenia** | 1. Skonfiguruj środowisko <br /> 2. Tworzenie aplikacji sieci Web |
|**Wdrażanie** | 1. Wdróż aplikację sieci Web |

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](https://docs.microsoft.com/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

W tym przykładzie Zastąp symbole zastępcze w zasobie IDENTYFIKATORem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do aplikacji App Service. Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się na użycie profilu publikowania na potrzeby uwierzytelniania.

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

Można również użyć poświadczeń na poziomie aplikacji, takich jak profil publikowania dla wdrożenia. Postępuj zgodnie z instrukcjami, aby skonfigurować klucz tajny:

1. Pobierz profil publikowania dla aplikacji App Service z portalu przy użyciu opcji **Pobierz profil publikowania** .

2. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**

    ![wpisy tajne](media/app-service-github-actions/secrets.png)

3. Wklej zawartość pobranego pliku profilu publikowania w polu wartość klucza tajnego.

4. Teraz w pliku przepływu pracy w gałęzi: `.github/workflows/workflow.yml` Zastąp klucz tajny `publish-profile` danych wejściowych akcji Wdróż aplikację sieci Web platformy Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Zobaczysz wpis tajny, jak pokazano poniżej.

    ![wpisy tajne](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Konfigurowanie środowiska można wykonać przy użyciu jednej z akcji instalacji.

|**Język**  |**Akcja konfiguracji**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

W poniższych przykładach przedstawiono część przepływu pracy, który konfiguruje środowisko dla różnych obsługiwanych języków:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Kompilowanie aplikacji sieci Web

Jest to zależne od języka i języków obsługiwanych przez Azure App Service Ta sekcja powinna być standardowym etapem kompilacji każdego języka.

W poniższych przykładach przedstawiono część przepływu pracy, który kompiluje aplikację sieci Web, w różnych obsługiwanych językach.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Wdrażanie do usługi App Service

Aby wdrożyć kod w aplikacji App Service, użyj akcji `azure/webapps-deploy@v1 `. Ta akcja ma cztery parametry:

| **Konstruktora**  | **Wyjaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji App Service | 
| **Publikuj — profil** | Obowiązkowe Publikuj zawartość pliku profilu za pomocą wpisów tajnych Web Deploy |
| **Package** | Obowiązkowe Ścieżka do pakietu lub folderu. *. zip, *. War, *. jar lub folder do wdrożenia |
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza miejscem produkcyjnym |

### <a name="deploy-using-publish-profile"></a>Wdróż przy użyciu profilu publikowania

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji node. js na platformie Azure przy użyciu profilu publikowania.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Wdrażanie przy użyciu nazwy głównej usługi platformy Azure

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji node. js na platformie Azure przy użyciu jednostki usługi platformy Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Następne kroki

Zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Przepływ pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie do platformy Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywanie platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrażanie](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)
