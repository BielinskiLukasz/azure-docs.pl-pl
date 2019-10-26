---
title: Tworzenie aplikacji funkcji platformy Azure przy użyciu języka Java i przezaćmienie | Microsoft Docs
description: Przewodnik po tworzeniu i publikowaniu prostej wyzwalanej przez protokół HTTP aplikacji bezserwerowej przy użyciu języka Java i przejściu do Azure Functions.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, obliczenia, architektura bezserwerowa, Java
ms.service: azure-functions
ms.topic: conceptual
ms.devlang: java
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 114d1298ec72dc0be23786664bf066fcbb7413f0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900154"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Tworzenie pierwszej funkcji przy użyciu języka Java i przezaćmienie 

W tym artykule pokazano, jak utworzyć projekt funkcji bez użycia [serwera](https://azure.microsoft.com/solutions/serverless/) za pomocą środowiska IDE i Apache Maven, przetestować go i debugować, a następnie wdrożyć go w Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska programistycznego

Aby opracować aplikację funkcji w języku Java i przezaćmieniu, musisz mieć zainstalowane następujące elementy:

-  Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
-  Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza.
-  [Przezaćmienie](https://www.eclipse.org/downloads/packages/)z obsługą języka Java i Maven.
-  [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

Zdecydowanie zaleca się także zainstalowanie [Azure Functions Core Tools w wersji 2](functions-run-local.md#v2), która zapewnia lokalne środowisko do uruchamiania i debugowania Azure Functions. 

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

1. W obszarze zaćmienie wybierz menu **plik** , a następnie wybierz polecenie **Nowy&gt; projekt Maven**. 
1. Zaakceptuj wartości domyślne w oknie dialogowym **Nowy projekt Maven** i wybierz pozycję **dalej**.
1. Wybierz pozycję **Dodaj Archetype** i Dodaj wpisy dla [funkcji Azure-Functions-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Identyfikator grupy Archetype: com. Microsoft. Azure
    - Identyfikator artefaktu Archetype: Azure-Functions-Archetype
    - Wersja: Użyj najnowszej wersji **1,22** z [centralnego repozytorium](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![zaćmienie Maven Utwórz](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Kliknij przycisk **OK** , a następnie kliknij przycisk **dalej**.  Należy pamiętać, aby wypełnić wartości dla wszystkich pól, w tym `resourceGroup`, `appName`i `appRegion` (Użyj innego argumentu inna niż **Fabrikam-Function-20170920120101928**) i ostatecznie **Zakończ**.
    ![zaćmienie](media/functions-create-first-java-eclipse/functions-create-eclipse2.png) Maven create2  

Narzędzie Maven tworzy pliki projektu w nowym folderze o nazwie wartości _artifactId_. Wygenerowany kod w projekcie jest prostą funkcją [wyzwalaną przez protokół http](/azure/azure-functions/functions-bindings-http-webhook) , która umożliwia echo treści wyzwalanego żądania HTTP.

## <a name="run-functions-locally-in-the-ide"></a>Uruchamianie funkcji lokalnie w środowisku IDE

> [!NOTE]
> [Azure Functions Core Tools należy zainstalować wersję 2,](functions-run-local.md#v2) aby uruchamiać i debugować funkcje lokalnie.

1. Kliknij prawym przyciskiem myszy wygenerowany projekt, a następnie wybierz polecenie **Uruchom jako** i **Maven kompilację**.
1. W oknie dialogowym **Edytowanie konfiguracji** wprowadź `package` w polach **cele** i **Nazwa** , a następnie wybierz pozycję **Uruchom**. Spowoduje to skompilowanie i spakowanie kodu funkcji.
1. Po zakończeniu kompilacji Utwórz inną konfigurację uruchomieniową jak powyżej, używając `azure-functions:run` jako celu i nazwy. Wybierz pozycję **Uruchom** , aby uruchomić funkcję w środowisku IDE.

Przerwij środowisko uruchomieniowe w oknie konsoli po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

### <a name="debug-the-function-in-eclipse"></a>Debuguj funkcję w programie zaćmienie

W ramach konfiguracji **Uruchom jako** skonfigurowane w poprzednim kroku Zmień `azure-functions:run` na `azure-functions:run -DenableDebug` i uruchom zaktualizowaną konfigurację, aby uruchomić aplikację funkcji w trybie debugowania.

Wybierz menu **Uruchom** i Otwórz aplet **konfiguracje debugowania**. Wybierz pozycję **zdalna aplikacja Java** i Utwórz nową. Nadaj nazwę konfiguracji i wprowadź ustawienia. Port powinien być zgodny z portem debugowania otwartym przez hosta funkcji, który domyślnie jest `5005`. Po zakończeniu instalacji kliknij pozycję `Debug`, aby rozpocząć debugowanie.

![Funkcje debugowania w programie zaćmienie](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ustaw punkty przerwania i sprawdź obiekty w funkcji przy użyciu środowiska IDE. Po zakończeniu Zatrzymaj debugera i działającego hosta funkcji. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) przed kontynuowaniem korzystania z wiersza poleceń komputera.

```azurecli
az login
```

Wdróż swój kod w nowej aplikacji funkcji przy użyciu `azure-functions:deploy` celu Maven w nowej konfiguracji **Uruchom jako** .

Po zakończeniu wdrażania zostanie wyświetlony adres URL umożliwiający uzyskanie dostępu do aplikacji funkcji platformy Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [Przewodnikiem dewelopera po funkcjach języka Java](functions-reference-java.md), aby uzyskać więcej informacji na temat tworzenia funkcji języka Java.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
