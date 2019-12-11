---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla platformy .NET (v4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 3322a9f2a7e09ee76c0fff94a2747c791ecf4804
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975384"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v4)

Wprowadzenie do biblioteki klienta Azure Key Vault dla platformy .NET. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla platformy .NET, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Upraszczanie i Automatyzowanie zadań związanych z certyfikatami SSL/TLS.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) [(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw .NET Core 2,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/overview)

W tym przewodniku szybki start założono, że używasz `dotnet`, [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)i poleceń systemu Windows w terminalu z systemem Windows (na przykład [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)lub [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-new-net-console-app"></a>Utwórz nową aplikację konsolową platformy .NET

W oknie konsoli Użyj `dotnet new` polecenie, aby utworzyć nową aplikację konsolową .NET o nazwie `akv-dotnet`.

```console
dotnet new console -n key-vault-console-app
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli programu Zainstaluj Azure Key Vaultą bibliotekę kliencką dla platformy .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

W tym przewodniku szybki start konieczne będzie zainstalowanie następujących pakietów:

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

Ten przewodnik Szybki Start używa wstępnie utworzonego magazynu kluczy platformy Azure. Magazyn kluczy można utworzyć, wykonując czynności opisane w [przewodniku szybki start dotyczącego interfejsu wiersza polecenia platformy Azure](quick-create-cli.md), [Azure PowerShell szybki start](quick-create-powershell.md)lub [Azure Portal przewodniku szybki start](quick-create-portal.md). Alternatywnie możesz po prostu uruchomić poniższe polecenia interfejsu CLI platformy Azure.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp < unikatowym identyfikatorem magazynu kluczy > nazwą magazynu klucza w poniższych przykładach.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Tworzenie jednostki usługi

Najprostszym sposobem uwierzytelniania aplikacji .NET opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz używanie Azure Key Vault tożsamości zarządzanej App Service](managed-identity.md) . W tym przewodniku szybki start można jednak utworzyć aplikację konsolową platformy .NET. Uwierzytelnianie aplikacji klasycznej przy użyciu platformy Azure wymaga użycia nazwy głównej usługi i zasad kontroli dostępu.

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

Zwróć uwagę na clientId, clientSecret i tenantId, ponieważ będziemy używać ich w poniższych krokach.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

Utwórz zasady dostępu dla magazynu kluczy, który przyznaje uprawnienia do nazwy głównej usługi przez przekazanie clientId do polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Nadaj jednostce usługi uprawnienia Get, list i Set dla kluczy i wpisów tajnych.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ustaw zmienne środowiskowe

Metoda DefaultAzureCredential w naszej aplikacji opiera się na trzech zmiennych środowiskowych: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`i `AZURE_TENANT_ID`. Użyj opcji Ustaw te zmienne do wartości clientId, clientSecret i tenantId zanotowanych w kroku [Create a Service Principal](#create-a-service-principal) .

Należy również zapisać nazwę magazynu kluczy jako zmienną środowiskową o nazwie `KEY_VAULT_NAME`;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Przy każdym wywołaniu `setx`należy uzyskać odpowiedź "sukces: określona wartość została zapisana".

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak utworzyć klienta, ustawić wpis tajny, pobrać klucz tajny i usunąć wpis tajny.

Cała Aplikacja konsolowa jest dostępna w https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

Uwierzytelnianie w magazynie kluczy i tworzenie klienta magazynu kluczy zależy od zmiennych środowiskowych w powyższym kroku [Ustaw zmienne środowiskowe](#set-environmental-variables) . Nazwa magazynu kluczy zostanie rozszerzona na identyfikator URI magazynu kluczy w formacie "https://\<nazwę magazynu kluczy\>. vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz tajny w magazynie kluczy przy użyciu [klienta. Setsecret — Metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) wymaga nazwy wpisu tajnego — w tym przykładzie jest używana wartość "My Secret".  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [klienta. Metoda getsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Wpis tajny jest teraz zapisywany jako `secret.Value`.

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec Usuń wpis tajny z magazynu kluczy z [klientem programu. DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Możesz sprawdzić, czy klucz tajny został usunięty za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano wpis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsolową w](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)serwisie GitHub.

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Implementowanie uwierzytelniania między usługami [w celu Azure Key Vault przy użyciu platformy .NET](service-to-service-authentication.md)
- Zapoznaj się [z omówieniem Azure Key Vault](key-vault-overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](key-vault-developers-guide.md)
- Informacje o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- Przegląd [Azure Key Vault najlepszych](key-vault-best-practices.md) rozwiązań
