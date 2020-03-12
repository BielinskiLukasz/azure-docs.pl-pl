---
title: Zresetuj poświadczenia dla klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zaktualizować lub zresetować poświadczenia nazwy głównej usługi lub aplikacji AAD dla klastra usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096101"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizowanie lub obracanie poświadczeń usługi Azure Kubernetes Service (AKS)

Domyślnie klastry AKS są tworzone za pomocą jednostki usługi, która ma okres ważności jednoletniej. Gdy zbliżasz się do daty wygaśnięcia, możesz zresetować poświadczenia, aby zwiększyć jednostkę usługi przez dodatkowy okres czasu. Możesz również zaktualizować lub obrócić poświadczenia w ramach zdefiniowanych zasad zabezpieczeń. W tym artykule szczegółowo opisano, jak zaktualizować te poświadczenia dla klastra AKS.

Możesz również [zintegrować klaster AKS z Azure Active Directory][aad-integration]i używać go jako dostawcy uwierzytelniania dla klastra. W takim przypadku dla klastra zostanie utworzonych 2 więcej tożsamości, aplikacja serwera usługi AAD i aplikacja kliencka usługi AAD mogą również zresetować te poświadczenia. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizowanie lub tworzenie nowej nazwy głównej usługi dla klastra AKS

Jeśli chcesz zaktualizować poświadczenia dla klastra AKS, możesz wybrać następujące opcje:

* Zaktualizuj poświadczenia istniejącej nazwy głównej usługi używanej przez klaster lub
* Utwórz nazwę główną usługi i zaktualizuj klaster, aby użyć tych nowych poświadczeń.

### <a name="reset-existing-service-principal-credential"></a>Zresetuj istniejące poświadczenia jednostki usługi

Aby zaktualizować poświadczenia dla istniejącej nazwy głównej usługi, Pobierz identyfikator jednostki usługi klastra za pomocą polecenia [AZ AKS show][az-aks-show] . Poniższy przykład pobiera identyfikator klastra o nazwie *myAKSCluster* w *grupie zasobów zasobu* . Identyfikator jednostki usługi jest ustawiany jako zmienna o nazwie *SP_ID* do użycia w dodatkowym poleceniu.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

W przypadku zestawu zmiennych, który zawiera identyfikator jednostki usługi, teraz Zresetuj poświadczenia przy użyciu polecenia [AZ AD Sp Credential Reset][az-ad-sp-credential-reset]. Poniższy przykład umożliwia wygenerowanie nowego bezpiecznego wpisu dla jednostki usługi przez platformę Azure. Ten nowy bezpieczny klucz tajny jest również przechowywany jako zmienna.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teraz Kontynuuj, aby [zaktualizować klaster AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny w przypadku zmiany nazwy głównej usługi w klastrze AKS.

### <a name="create-a-new-service-principal"></a>Utwórz nową nazwę główną usługi

Jeśli wybrano opcję zaktualizowania istniejących poświadczeń jednostki usługi w poprzedniej sekcji, Pomiń ten krok. Kontynuuj [Aktualizowanie klastra AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials).

Aby utworzyć nazwę główną usługi, a następnie zaktualizować klaster AKS, aby korzystał z tych nowych poświadczeń, użyj polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] . W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości są używane w następnym kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Teraz Zdefiniuj zmienne dla identyfikatora jednostki usługi i wpisu tajnego klienta przy użyciu danych wyjściowych polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] , jak pokazano w poniższym przykładzie. *SP_ID* jest *identyfikatorem aplikacji*, a *SP_SECRET* to *hasło*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Teraz Kontynuuj, aby [zaktualizować klaster AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny w przypadku zmiany nazwy głównej usługi w klastrze AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń jednostki usługi

Niezależnie od tego, czy wybrano aktualizację poświadczeń dla istniejącej nazwy głównej usługi, czy też utworzyć nazwę główną usługi, można teraz zaktualizować klaster AKS przy użyciu nowych poświadczeń za pomocą polecenia [AZ AKS Update-Credentials][az-aks-update-credentials] . Zmienne dla *--Service-Principal* i *--Client-Secret* są używane:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Aktualizacja nazwy głównej usługi w AKS może chwilę potrwać.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń aplikacji usługi AAD

Nowe aplikacje serwera i klienta usługi AAD można utworzyć, wykonując czynności związane z [integracją usługi AAD][create-aad-app]. Lub zresetuj istniejące aplikacje usługi AAD, postępując zgodnie z [tą samą metodą jak w przypadku resetowania nazwy głównej](#reset-existing-service-principal-credential). Po wykonaniu tej opcji wystarczy zaktualizować poświadczenia aplikacji usługi AAD klastra przy użyciu tego samego polecenia [AZ AKS Update-Credentials][az-aks-update-credentials] , ale przy użyciu zmiennych *--Reset-AAD* .

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano nazwę główną usługi dla klastra AKS oraz aplikacje integracji w usłudze AAD. Aby uzyskać więcej informacji na temat zarządzania tożsamością dla obciążeń w klastrze, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
