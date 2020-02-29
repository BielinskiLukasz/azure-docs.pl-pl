---
title: Uruchamianie aplikacji z pakietu ZIP
description: Wdróż pakiet ZIP aplikacji z niepodzielną. Popraw przewidywalność i niezawodność zachowania aplikacji podczas procesu wdrażania ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920726"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uruchom aplikację w Azure App Service bezpośrednio z pakietu ZIP

W [Azure App Service](overview.md)można uruchamiać aplikacje bezpośrednio z pliku pakietu zip wdrożenia. W tym artykule pokazano, jak włączyć tę funkcję w aplikacji.

Wszystkie inne metody wdrażania w App Service mają coś wspólnego: pliki są wdrażane do *D:\home\site\wwwroot* w aplikacji (lub */Home/site/wwwroot* dla aplikacji systemu Linux). Ponieważ ten sam katalog jest używany przez aplikację w czasie wykonywania, istnieje możliwość, że wdrożenie nie powiedzie się z powodu konfliktów blokady plików, a w przypadku aplikacji jest nieprzewidywalne, ponieważ niektóre pliki nie zostały jeszcze zaktualizowane.

Natomiast w przypadku uruchamiania bezpośrednio z pakietu pliki w pakiecie nie są kopiowane do katalogu *wwwroot* . Zamiast tego pakiet ZIP jest instalowany bezpośrednio jako katalog katalogu *wwwroot* tylko do odczytu. Istnieje kilka korzyści do uruchomienia bezpośrednio z pakietu:

- Eliminuje konflikty blokady plików między wdrożeniem i środowiskiem uruchomieniowym.
- Zapewnia, że w dowolnym momencie są uruchomione tylko aplikacje w pełni wdrożone.
- Program można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
- Zwiększa wydajność wdrożeń Azure Resource Manager.
- Może skrócić czas zimnego startu, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Włącz uruchamianie z pakietu

Ustawienie aplikacji `WEBSITE_RUN_FROM_PACKAGE` umożliwia uruchamianie z pakietu. Aby je ustawić, uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` umożliwia uruchamianie aplikacji z pakietu lokalnego dla aplikacji. Można również [uruchomić z pakietu zdalnego](#run-from-external-url-instead).

## <a name="run-the-package"></a>Uruchom pakiet

Najprostszym sposobem uruchomienia pakietu w App Service jest interfejs wiersza polecenia platformy Azure [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) . Na przykład:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Ponieważ ustawienie aplikacji `WEBSITE_RUN_FROM_PACKAGE` jest ustawione, to polecenie nie wyodrębnia zawartości pakietu do katalogu *D:\home\site\wwwroot* aplikacji. Zamiast tego przekazuje plik ZIP jako-is do *D:\home\data\SitePackages*i tworzy *pakietname. txt* w tym samym katalogu, który zawiera nazwę pakietu zip do załadowania w czasie wykonywania. Jeśli pakiet ZIP zostanie przekazany w inny sposób (na przykład [FTP](deploy-ftp.md)), należy ręcznie utworzyć katalog *D:\home\data\SitePackages* i plik *PackageName. txt* .

Polecenie spowoduje również ponowne uruchomienie aplikacji. Ponieważ `WEBSITE_RUN_FROM_PACKAGE` jest ustawiona, App Service instaluje przekazany pakiet jako katalog katalogu *wwwroot* tylko do odczytu i uruchamia aplikację bezpośrednio z tego zainstalowanego katalogu.

## <a name="run-from-external-url-instead"></a>Zamiast tego Uruchom z zewnętrznego adresu URL

Możesz również uruchomić pakiet z zewnętrznego adresu URL, takiego jak Azure Blob Storage. Aby przekazać pliki pakietu do konta magazynu obiektów blob, można użyć [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) . Należy użyć prywatnego kontenera magazynu z [sygnaturą dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby umożliwić środowisko uruchomieniowe App Service, aby bezpiecznie uzyskać dostęp do pakietu. 

Po przekazaniu pliku do usługi BLOB Storage i uzyskaniu adresu URL sygnatury dostępu współdzielonego dla pliku ustaw wartość ustawienia aplikacji `WEBSITE_RUN_FROM_PACKAGE` na adres URL. W poniższym przykładzie jest to możliwe za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Jeśli publikujesz zaktualizowany pakiet o tej samej nazwie w usłudze BLOB Storage, musisz ponownie uruchomić aplikację, aby zaktualizowany pakiet został załadowany do App Service.

### <a name="use-key-vault-references"></a>Użyj odwołań Key Vault

Aby zwiększyć bezpieczeństwo, można użyć odwołań Key Vault w połączeniu z zewnętrznym adresem URL. Dzięki temu adres URL jest szyfrowany i umożliwia korzystanie Key Vault na potrzeby zarządzania i rotacji tajnych. Zaleca się używanie usługi Azure Blob Storage, dzięki czemu można łatwo obrócić skojarzony klucz SAS. Magazyn obiektów blob platformy Azure jest szyfrowany w spoczynku, co zapewnia bezpieczeństwo danych aplikacji, gdy nie jest ona wdrożona w App Service.

1. Utwórz Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Dodaj zewnętrzny adres URL jako wpis tajny w Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Utwórz ustawienie aplikacji `WEBSITE_RUN_FROM_PACKAGE` i ustaw wartość jako odwołanie Key Vault do zewnętrznego adresu URL.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Zobacz następujące artykuły, aby uzyskać więcej informacji.

- [Key Vault odwołań dla App Service](app-service-key-vault-references.md)
- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Uruchamianie bezpośrednio z pakietu sprawia, że `wwwroot` tylko do odczytu. Aplikacja otrzyma błąd, jeśli spróbuje zapisać pliki w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja jest niezgodna z [lokalną pamięcią podręczną](overview-local-cache.md).
- W celu zwiększenia wydajności zimnej lokalizacji Użyj opcji zip (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="more-resources"></a>Więcej zasobów

- [Ciągłe wdrażanie dla Azure App Service](deploy-continuous-deployment.md)
- [Wdróż kod z plikiem ZIP lub WAR](deploy-zip.md)
