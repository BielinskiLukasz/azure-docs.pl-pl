---
title: Tworzenie aplikacji Ruby on Rails i wdrażanie jej w usłudze App Service w systemie Linux | Microsoft Docs
description: Informacje na temat tworzenia aplikacji Ruby on Rails w usłudze App Service w systemie Linux.
keywords: usługa azure app, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: cfowler
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 04025998f78843b1cc69acd663681b9563a531bc
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431231"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Informacje na temat tworzenia aplikacji Ruby on Rails w usłudze App Service w systemie Linux

Usługa [Azure App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten przewodnik szybkiego startu pokazuje, jak utworzyć podstawową aplikację [Ruby on Rails](https://rubyonrails.org/), którą można wdrożyć na platformie Azure jako aplikację internetową w systemie Linux.

> [!NOTE]
> Obecnie stos programistyczny Ruby obsługuje jedynie aplikację Ruby on Rails. Jeśli chcesz użyć innej platformy, np. Sinatra, zobacz Przewodnik Szybki start dotyczący funkcji [Web App for Containers](https://docs.microsoft.com/azure/app-service/containers/).

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Zainstaluj oprogramowanie Ruby 2.3 lub nowsze</a>
* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Uruchamianie aplikacji lokalnie

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala, zmień katalog na `hello-world` i użyj polecenia `rails server`, aby uruchomić serwer.

Pierwszym krokiem jest zainstalowanie wymaganych rozwiązań Gem. W próbce znajduje się `Gemfile`, więc nie trzeba określać rozwiązań Gem do zainstalowania. W tym celu użyjemy programu instalującego niezamówione pakiety oprogramowania:

```
bundle install
```

Po zainstalowaniu rozwiązań Gem użyjemy do uruchomienia aplikacji programu instalującego niezamówione pakiety oprogramowania:

```bash
bundle exec rails server
```

Przy użyciu przeglądarki internetowej przejdź pod adres `http://localhost:3000`, aby przetestować aplikację lokalnie.

![Skonfigurowana aplikacja Hello World](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Przejdź do witryny, aby zobaczyć nowo utworzoną aplikację internetową z wbudowanym obrazem. Zastąp ciąg _&lt;nazwa aplikacji>_ nazwą swojej aplikacji internetowej.

```bash
http://<app_name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Strona powitalna](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Wdrażanie aplikacji

Uruchom następujące polecenia w celu wdrożenia lokalnej aplikacji w witrynie internetowej platformy Azure:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Upewnij się, że operacje zdalnego wdrażania raportują powodzenie. Polecenia tworzą dane wyjściowe podobne do następującego tekstu:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Po zakończeniu wdrożenia uruchom ponownie swoją aplikację internetową, aby wdrożenie zostało zastosowane, korzystając z polecenia [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart), jak pokazano poniżej:

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Przejdź do swojej witryny i sprawdź wyniki.

```bash
http://<app name>.azurewebsites.net
```

![uaktualniona aplikacja internetowa](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Podczas ponownego uruchamiania aplikacji próba przeglądania wyników witryny powoduje wyświetlenie kodu stanu HTTP `Error 503 Server unavailable`. Proces ponownego uruchamiania może potrwać kilka minut.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Środowisko Ruby on Rails z bazą danych MySQL](tutorial-ruby-postgres-app.md)
