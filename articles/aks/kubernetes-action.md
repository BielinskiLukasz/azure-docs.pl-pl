---
title: Kompiluj, Testuj i wdrażaj kontenery w usłudze Azure Kubernetes Service przy użyciu akcji GitHub
description: Dowiedz się, jak wdrożyć kontener w usłudze Kubernetes przy użyciu akcji usługi GitHub
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: cc2d6df952b2e0aa9b9f4d4e1dcb4859a5bb3790
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130532"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akcje GitHub dotyczące wdrażania w usłudze Kubernetes Service

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Akcja Kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) ułatwia wdrażanie klastrów usługi Azure Kubernetes. Akcja ustawia docelowy kontekst klastra AKS, który może być używany przez inne akcje, takie jak [Azure/k8s-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/k8s-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) itp. lub uruchomić dowolne polecenia polecenia kubectl.

> [!IMPORTANT]
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (. yml) w ścieżce `/.github/workflows/` w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Dla przepływu pracy AKS, plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Uwierzytelnianie** | Zaloguj się do prywatnego rejestru kontenerów (ACR) |
|**Kompilacja** | Kompilowanie & wypychanie obrazu kontenera  |
|**Wdrażanie** | 1. Ustaw docelowy klaster AKS |
| |2. tworzenie wpisu tajnego rejestru ogólnego/Docker w klastrze Kubernetes  |
||3. Wdróż w klastrze Kubernetes|

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](https://docs.microsoft.com/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

W powyższym poleceniu Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji i grupą zasobów. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do zasobu. Polecenie powinno spowodować wyjście obiektu JSON podobnego do tego.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

Postępuj zgodnie z instrukcjami, aby skonfigurować wpisy tajne:

1. W witrynie [GitHub](https://github.com/)przejdź do repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

    ![wpisy tajne](media/kubernetes-action/secrets.png)

2. Wklej zawartość powyższego `az cli` polecenia jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

3. Podobnie należy zdefiniować następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenera i ustawić je w akcji logowania Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Zostaną wyświetlone wpisy tajne, jak pokazano poniżej.

    ![Kubernetes — wpisy tajne](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tworzenie obrazu kontenera i wdrażanie go w klastrze usługi Azure Kubernetes Service

Kompilacja i wypychanie obrazów kontenera odbywa się przy użyciu akcji `Azure/docker-login@v1`. Aby wdrożyć obraz kontenera w AKS, musisz użyć akcji `Azure/k8s-deploy@v1`. Ta akcja ma pięć parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **namespace** | Obowiązkowe Wybierz docelową przestrzeń nazw Kubernetes. Jeśli przestrzeń nazw nie zostanie podana, polecenia zostaną uruchomione w domyślnej przestrzeni nazw | 
| **manifesty** |  Potrzeb Ścieżka do plików manifestu, która będzie używana do wdrażania |
| **images** | Obowiązkowe W pełni kwalifikowany adres URL zasobów przeznaczonych do podstawiania plików manifestu |
| **imagepullsecrets** | Obowiązkowe Nazwa wpisu tajnego rejestru platformy Docker, który został już skonfigurowany w klastrze. Każda z tych nazw tajnych jest dodawana w polu imagePullSecrets dla obciążeń znalezionych w wejściowych plikach manifestu |
| **polecenia kubectl — wersja** | Obowiązkowe Instaluje określoną wersję pliku binarnego polecenia kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Wdróż w klastrze usługi Azure Kubernetes Service

Kompleksowy przepływ pracy tworzenia obrazów kontenerów i wdrażania go w klastrze usługi Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Następne kroki

Zestaw akcji można znaleźć w różnych repozytoriach usługi GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Konfiguracja — polecenia kubectl](https://github.com/Azure/setup-kubectl)

- [k8s — Ustawianie kontekstu](https://github.com/Azure/k8s-set-context)

- [AKS — Ustawianie kontekstu](https://github.com/Azure/aks-set-context)

- [k8s-Create-Secret](https://github.com/Azure/k8s-create-secret)

- [k8s — Wdróż](https://github.com/Azure/k8s-deploy)

- [aplikacje webapps — kontener — wdrażanie](https://github.com/Azure/webapps-container-deploy)

- [akcje — przykłady dla przepływu pracy](https://github.com/Azure/actions-workflow-samples)
