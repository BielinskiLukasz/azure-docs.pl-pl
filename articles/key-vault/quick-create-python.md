---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla języka Python
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej języka Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 3be246402c4acd63aee3518f2333d50ec307e9c0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648223"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla języka Python

Rozpocznij pracę z biblioteką klienta Azure Key Vault dla języka Python. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla języka Python, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Upraszczanie i Automatyzowanie zadań związanych z certyfikatami SSL/TLS.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[Dokumentacja referencyjna interfejsu API](/python/api/overview/azure/key-vault?view=azure-python) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) [(indeks pakietu języka Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 lub nowszy
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/overview)

W tym przewodniku szybki start założono, że uruchomiono [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) w oknie terminalu systemu Linux.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli Zainstaluj bibliotekę Azure Key Vault Secret dla języka Python.

```console
pip install azure-keyvault-secrets
```

W tym przewodniku szybki start konieczne będzie zainstalowanie pakietu Azure. Identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki Start używa wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując czynności opisane w [przewodniku szybki start dotyczącego interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [Azure PowerShell szybki start](quick-create-powershell.md)lub [Azure Portal przewodniku szybki start](quick-create-portal.md). Alternatywnie możesz po prostu uruchomić poniższe polecenia interfejsu CLI platformy Azure.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp < unikatowym identyfikatorem magazynu kluczy > nazwą magazynu klucza w poniższych przykładach.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Najprostszym sposobem uwierzytelniania aplikacji .NET opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz używanie Azure Key Vault tożsamości zarządzanej App Service](managed-identity.md) . W celu uproszczenia tego przewodnika Szybki Start tworzy aplikację konsolową platformy .NET. Uwierzytelnianie aplikacji klasycznej przy użyciu platformy Azure wymaga użycia nazwy głównej usługi i zasad kontroli dostępu.

Utwórz zasadę usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ta operacja zwróci serię par klucz/wartość. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zwróć uwagę na clientId i clientSecret, ponieważ będziemy z nich korzystać w kroku [Ustaw zmienną środowiskową](#set-environmental-variables) poniżej.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi przez przekazanie clientId do polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Nadaj jednostce usługi uprawnienia Get, list i Set dla kluczy i wpisów tajnych.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ustaw zmienne środowiskowe

Metoda DefaultAzureCredential w naszej aplikacji opiera się na trzech zmiennych środowiskowych: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`i `AZURE_TENANT_ID`. Ustaw te zmienne na wartości clientId, clientSecret i tenantId zanotowane w kroku [Tworzenie jednostki usługi](#create-a-service-principal) przy użyciu formatu `export VARNAME=VALUE`. (Ustawia to zmienne dla bieżącej powłoki i procesów utworzonych na podstawie powłoki; aby trwale dodać te zmienne do środowiska, edytuj plik `/etc/environment `). 

Należy również zapisać nazwę magazynu kluczy jako zmienną środowiskową o nazwie `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla języka Python umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić wpis tajny, pobrać klucz tajny i usunąć wpis tajny.

Cała Aplikacja konsolowa jest dostępna w https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

Uwierzytelnianie w magazynie kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych w powyższym kroku [Ustaw zmienne środowiskowe](#set-environmental-variables) . Nazwa magazynu kluczy jest rozszerzana na identyfikator URI magazynu kluczy w formacie "https://< nazwa magazynu kluczy >. magazyn. Azure. NET".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz tajny w magazynie kluczy przy użyciu [klienta. Setsecret — Metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) wymaga nazwy wpisu tajnego — w tym przykładzie jest używana wartość "My Secret".  

```python
client.set_secret(secretName, secretValue);
```

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [klienta. Metoda getsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Wpis tajny jest teraz zapisywany jako `retrieved_secret.value`.

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec Usuń wpis tajny z magazynu kluczy z [klientem programu. DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName);
```

Możesz sprawdzić, czy klucz tajny został usunięty za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

secretName = "mySecret";

keyVaultName = os.environ["KEY_VAULT_NAME"];
KVUri = "https://" + keyVaultName + ".vault.azure.net";

credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)

print("Input the value of your secret > ");
secretValue = raw_input();

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

client.set_secret(secretName, secretValue);

print(" done.");

print("Forgetting your secret.");
secretValue = "";
print("Your secret is '" + secretValue + "'.");

print("Retrieving your secret from " + keyVaultName + ".");

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.");
print("Deleting your secret from " + keyVaultName + " ...");

client.delete_secret(secretName);

print(" done.");
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano wpis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsolową w](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)serwisie GitHub.

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](key-vault-overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](key-vault-developers-guide.md)
- Informacje o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- Przegląd [Azure Key Vault najlepszych](key-vault-best-practices.md) rozwiązań