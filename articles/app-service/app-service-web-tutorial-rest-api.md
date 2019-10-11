---
title: 'Samouczek: hostowanie interfejsu API RESTful z mechanizmem CORS-Azure App Service'
description: Dowiedz się, w jaki sposób usługa Azure App Service umożliwia hostowanie interfejsów API RESTful z obsługą mechanizmu CORS.
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f13b390047ea4d8280b106f3b02a8f18944a6f99
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255178"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Samouczek: hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Usługa App Service ma dodatkowo wbudowaną obsługę mechanizmu [współużytkowania zasobów między źródłami (CORS, Cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) dla interfejsów API RESTful. Ten samouczek pokazuje, w jaki sposób wdrożyć aplikację interfejsu API platformy ASP.NET Core w usłudze App Service z obsługą mechanizmu CORS. Aplikacja zostanie skonfigurowana przy użyciu narzędzi wiersza polecenia i wdrożona za pomocą narzędzia Git. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobów usługi App Service przy użyciu interfejsu wiersza polecenia platformy Azure
> * Wdrażanie interfejsu API RESTful na platformie Azure za pomocą narzędzia Git
> * Włączanie obsługi mechanizmu CORS w usłudze App Service

Kroki opisane w tym samouczku można wykonać w systemie macOS, Linux i Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj oprogramowanie Git](https://git-scm.com/).
* [Zainstaluj platformę .NET Core](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Tworzenie lokalnej aplikacji ASP.NET Core

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy ASP.NET Core. Usługa App Service obsługuje ten sam przepływ pracy w przypadku interfejsów API napisanych w innych językach.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W oknie terminalu dodaj element `cd` do katalogu roboczego.  

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

To repozytorium zawiera aplikację utworzoną na podstawie następującego samouczka: [ASP.NET Core Web API help pages using Swagger (Strony pomocy internetowego interfejsu API platformy ASP.NET Core korzystające ze struktury Swagger)](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Używa ona generatora struktury Swagger, aby obsłużyć [interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oraz punkt końcowy JSON struktury Swagger.

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom następujące polecenia, aby zainstalować wymagane pakiety, uruchom migracje baz danych i uruchom aplikację.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Przejdź do adresu `http://localhost:5000/swagger` w przeglądarce, aby przetestować interfejs użytkownika struktury Swagger.

![Interfejs API platformy ASP.NET Core uruchomiony lokalnie](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Przejdź do adresu `http://localhost:5000/api/todo`, aby wyświetlić listę zadań w formacie JSON.

Przejdź do adresu `http://localhost:5000`, aby przetestować aplikację przeglądarki. Później wskażesz aplikacji przeglądarki zdalny interfejs API w usłudze App Service, aby przetestować działanie mechanizmu CORS. Kod aplikacji przeglądarki znajduje się w katalogu _wwwroot_ repozytorium.

Aby zatrzymać platformę ASP.NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core połączoną z bazą danych SQL Database w usłudze App Service.

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Utwórz grupę zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do adresu `http://<app_name>.azurewebsites.net/swagger` w przeglądarce, aby przetestować interfejs użytkownika struktury Swagger.

![Interfejs API platformy ASP.NET Core uruchomiony w usłudze Azure App Service](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Przejdź do adresu `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json`, aby wyświetlić plik _swagger.json_ wdrożonego interfejsu API.

Przejdź do adresu `http://<app_name>.azurewebsites.net/api/todo`, aby wyświetlić działający wdrożony interfejs API.

## <a name="add-cors-functionality"></a>Dodawanie funkcji obsługi mechanizmu CORS

Następnie włącz wbudowaną obsługę mechanizmu CORS w usłudze App Service dla interfejsu API.

### <a name="test-cors-in-sample-app"></a>Testowanie mechanizmu CORS w aplikacji przykładowej

W lokalnym repozytorium otwórz plik _wwwroot/index.html_.

W wierszu 51 ustaw zmienną `apiEndpoint` na adres URL wdrożonego interfejsu API (`http://<app_name>.azurewebsites.net`). Zastąp zmienną _\<app_name>_ własną nazwą aplikacji w usłudze App Service.

W lokalnym oknie terminala ponownie uruchom aplikację przykładową.

```bash
dotnet run
```

Przejdź do aplikacji przeglądarki pod adresem `http://localhost:5000`. Otwórz okno narzędzia deweloperskie w przeglądarce (`Ctrl` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 w programie Chrome dla systemu Windows) i sprawdź kartę **konsola** . Powinien pojawić się komunikat o błędzie `No 'Access-Control-Allow-Origin' header is present on the requested resource`.

![Błąd mechanizmu CORS w kliencie przeglądarki](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Ze względu na niezgodność domeny aplikacji przeglądarki (`http://localhost:5000`) i zdalnego zasobu (`http://<app_name>.azurewebsites.net`) oraz z uwagi na fakt, że interfejs API usługi App Service nie wysyła nagłówka `Access-Control-Allow-Origin`, przeglądarka uniemożliwiła ładowanie zawartości między domenami w aplikacji przeglądarki.

W środowisku produkcyjnym aplikacja przeglądarki miałaby publiczny adres URL zamiast adresu URL hosta lokalnego, ale sposób włączania mechanizmu CORS dla adresu URL hosta lokalnego jest taki sam, jak dla publicznego adresu URL.

### <a name="enable-cors"></a>Włączanie mechanizmu CORS 

W usłudze Cloud Shell włącz mechanizm CORS dla adresu URL klienta przy użyciu polecenia [`az resource update`](/cli/azure/resource#az-resource-update). Zastąp symbol zastępczy _&lt;appname>_ .

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

W parametrze `properties.cors.allowedOrigins` możesz określić więcej niż jeden adres URL klienta (`"['URL1','URL2',...]"`). Możesz również włączyć adresy URL wszystkich klientów za pomocą wartości `"['*']"`.

> [!NOTE]
> Jeśli Twoja aplikacja wymaga wysyłania poświadczeń, takich jak pliki cookie lub tokeny uwierzytelniania, przeglądarka może wymagać nagłówka `ACCESS-CONTROL-ALLOW-CREDENTIALS` w odpowiedzi. Aby włączyć tę funkcję w App Service, ustaw `properties.cors.supportCredentials` na `true` w konfiguracji CORS. Nie można go włączyć, jeśli `allowedOrigins` zawiera `'*'`.

### <a name="test-cors-again"></a>Ponowne testowanie mechanizmu CORS

Odśwież aplikację przeglądarki pod adresem `http://localhost:5000`. Komunikat o błędzie w oknie **Konsola** zniknął i możesz wyświetlić dane z wdrożonego interfejsu API oraz z nich korzystać. Zdalny interfejs API obsługuje teraz mechanizm CORS w aplikacji przeglądarki uruchomionej lokalnie. 

![Powodzenie mechanizmu CORS w kliencie przeglądarki](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Gratulacje. Używasz interfejsu API w usłudze Azure App Service z obsługą mechanizmu CORS.

## <a name="app-service-cors-vs-your-cors"></a>Porównanie mechanizmu CORS usługi App Service z własnym mechanizmem CORS

W celu uzyskania większej elastyczności możesz korzystać z narzędzi własnego mechanizmu CORS zamiast mechanizmu CORS usługi App Service. Możesz na przykład potrzebować określenia różnych dozwolonych źródeł dla różnych tras lub metod. Ponieważ mechanizm CORS usługi App Service umożliwia określenie jednego zestawu zaakceptowanych źródeł dla wszystkich tras i metod interfejsu API, lepsze może okazać się użycie własnego kodu mechanizmu CORS. Zobacz, jak to działa na platformie ASP.NET Core, w temacie [Enabling Cross-Origin Requests (CORS) (Włączanie żądań między źródłami [CORS])](/aspnet/core/security/cors).

> [!NOTE]
> Nie próbuj używać jednocześnie mechanizmu CORS usługi App Service i własnego kodu mechanizmu CORS. W takim przypadku mechanizm CORS usługi App Service ma pierwszeństwo, a Twój kod mechanizmu CORS jest nieskuteczny.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie zasobów usługi App Service przy użyciu interfejsu wiersza polecenia platformy Azure
> * Wdrażanie interfejsu API RESTful na platformie Azure za pomocą narzędzia Git
> * Włączanie obsługi mechanizmu CORS w usłudze App Service

Przejdź do następnego samouczka, aby dowiedzieć się, jak uwierzytelniać i autoryzować użytkowników.

> [!div class="nextstepaction"]
> [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników](app-service-web-tutorial-auth-aad.md)
