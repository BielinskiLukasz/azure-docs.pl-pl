---
title: Korzystanie z tożsamości usługi AAD w usłudze sieci Web
titleSuffix: Azure Machine Learning
description: Użyj tożsamości usługi AAD w usłudze sieci Web w usłudze Azure Kubernetes Service, aby uzyskać dostęp do zasobów w chmurze podczas oceniania.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f76e149339e80ddeba8431afffbd677a4b595ec3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319477"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Używanie tożsamości usługi Azure AD z usługą sieci Web Machine Learning w usłudze Azure Kubernetes Service

W tym instruktażu dowiesz się, jak przypisać tożsamość usługi Azure Active Directory (AAD) do wdrożonego modelu uczenia maszynowego w usłudze Azure Kubernetes. Projekt [tożsamości usługi AAD pod](https://github.com/Azure/aad-pod-identity) jest umożliwia aplikacjom bezpieczne uzyskiwanie dostępu do zasobów w chmurze przy użyciu [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) i Kubernetes podstawowych. Dzięki temu usługa sieci Web może bezpiecznie uzyskiwać dostęp do zasobów platformy Azure bez konieczności osadzania poświadczeń ani zarządzania tokenami bezpośrednio w `score.py` skrypcie. W tym artykule opisano kroki umożliwiające utworzenie i zainstalowanie tożsamości platformy Azure w klastrze usługi Azure Kubernetes i przypisanie tożsamości do wdrożonej usługi sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Dostęp do klastra AKS przy użyciu `kubectl` polecenia. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z klastrem](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Usługa sieci Web Azure Machine Learning wdrożona w klastrze AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Tworzenie i Instalowanie tożsamości platformy Azure w klastrze AKS

1. Aby określić, czy klaster AKS jest włączony RBAC, użyj następującego polecenia:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    To polecenie zwraca wartość, `true` Jeśli RBAC jest włączona. Ta wartość Określa polecenie do użycia w następnym kroku.

1. Aby zainstalować [tożsamość usługi AAD pod](https://github.com/Azure/aad-pod-identity#getting-started) w klastrze AKS, użyj jednego z następujących poleceń:

    * Jeśli klaster AKS ma **włączoną funkcję RBAC** , użyj następującego polecenia:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Jeśli w klastrze AKS **nie włączono kontroli RBAC**, użyj następującego polecenia:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Dane wyjściowe polecenia są podobne do następującego tekstu:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Utwórz tożsamość platformy Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi AAD pod.

1. [Zainstaluj tożsamość platformy Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi AAD pod.

1. [Zainstaluj powiązanie tożsamości platformy Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi AAD pod.

1. Jeśli tożsamość platformy Azure utworzona w poprzednim kroku nie znajduje się w tej samej grupie zasobów co klaster AKS, postępuj zgodnie z instrukcjami [ustawionymi dla mikrofonu](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi AAD pod.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Przypisywanie tożsamości platformy Azure do usługi sieci Web Machine Learning

W poniższych krokach użyto tożsamości platformy Azure utworzonej w poprzedniej sekcji i przypiszesz ją do usługi sieci Web AKS za pomocą **etykiety selektora**.

Najpierw zidentyfikuj nazwę i przestrzeń nazw wdrożenia w klastrze AKS, do którego chcesz przypisać tożsamość platformy Azure. Te informacje można uzyskać, uruchamiając następujące polecenie. Przestrzenie nazw powinny być nazwami obszaru roboczego Azure Machine Learning, a nazwa wdrożenia powinna być nazwą punktu końcowego, jak pokazano w portalu.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Dodaj etykietę selektora tożsamości platformy Azure do wdrożenia, edytując specyfikację wdrażania. Wartość selektora powinna być taka, która została zdefiniowana w kroku 5 [instalacji powiązania tożsamości platformy Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edytuj wdrożenie, aby dodać etykietę selektora tożsamości platformy Azure. Przejdź do poniższej sekcji w temacie `/spec/template/metadata/labels` . Powinny być widoczne wartości, takie jak `isazuremlapp: “true”` . Dodaj etykietę usługi AAD-pod-Identity, jak pokazano poniżej.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Aby sprawdzić, czy etykieta została prawidłowo dodana, uruchom następujące polecenie.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Aby wyświetlić wszystkie Stany pod stanami, uruchom następujące polecenie.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Usługi sieci Web dla tego wdrożenia mogą teraz uzyskiwać dostęp do zasobów platformy Azure za pomocą tożsamości platformy Azure bez konieczności osadzania poświadczeń w kodzie. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Przypisywanie odpowiednich ról do tożsamości platformy Azure

[Przypisz swoją tożsamość zarządzaną platformy Azure z odpowiednimi rolami](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) , aby uzyskać dostęp do innych zasobów platformy Azure. Upewnij się, że przypisywane role mają poprawne **akcje dotyczące danych**. Na przykład [rola czytnika danych obiektów blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) będzie miała uprawnienia do odczytu obiektu blob magazynu, podczas gdy ogólna [rola czytnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) może nie być.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Używanie tożsamości platformy Azure z usługą sieci Web Machine Learning

Wdróż model w klastrze AKS. `score.py`Skrypt może zawierać operacje wskazujące zasoby platformy Azure, do których ma dostęp Twoja tożsamość platformy Azure. Upewnij się, że zainstalowano wymagane zależności biblioteki klienta dla zasobu, do którego próbujesz uzyskać dostęp. Poniżej przedstawiono kilka przykładów użycia tożsamości platformy Azure do uzyskiwania dostępu do różnych zasobów platformy Azure z usługi.

### <a name="access-key-vault-from-your-web-service"></a>Dostęp do Key Vault z usługi sieci Web

Jeśli masz uprawnienia do odczytu tożsamości platformy Azure do wpisu tajnego w ramach **Key Vault**, `score.py` możesz uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> Ten przykład używa DefaultAzureCredential. Aby udzielić dostępu do tożsamości przy użyciu określonych zasad dostępu, zobacz [część 4: Pobieranie klucza tajnego z Azure Key Vault](../key-vault/general/authentication.md#part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python).

### <a name="access-blob-from-your-web-service"></a>Dostęp do obiektu BLOB z usługi sieci Web

Jeśli masz dostęp do odczytu tożsamości platformy Azure do danych wewnątrz **obiektu blob magazynu**, `score.py` możesz uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat używania biblioteki klienta Azure Identity platform w języku Python, zobacz [repozytorium](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) w witrynie GitHub.
* Szczegółowy przewodnik dotyczący wdrażania modeli w klastrach usługi Azure Kubernetes Service znajduje się w temacie [how](how-to-deploy-azure-kubernetes-service.md)to.
