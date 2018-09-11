---
title: Uruchamianie usługi Azure Functions z pakietu | Dokumentacja firmy Microsoft
description: Ma uruchamiać swoje funkcje przez zainstalowanie plik pakietu wdrożeniowego, który zawiera pliki projektu aplikacji funkcji środowiska uruchomieniowego usługi Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: glenga
ms.openlocfilehash: a0e643397372e5b132119a7c23f251ecec876916
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346581"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uruchamianie usługi Azure Functions z pliku pakietu

> [!NOTE]
> Funkcje opisane w tym artykule nie jest dostępna dla funkcji w systemie Linux.

Na platformie Azure możesz uruchamiać swoje funkcje bezpośrednio z pliku pakietu wdrożenia w aplikacji funkcji. Inną możliwością jest wdrożenie plików w `d:\home\site\wwwroot` katalogu aplikacji funkcji.

W tym artykule opisano zalety uruchamiania funkcji z pakietu. Pokazano również, jak włączyć tę funkcję w aplikacji funkcji.

## <a name="benefits-of-running-from-a-package-file"></a>Korzyści wynikające z uruchamiania z pliku pakietu
  
Istnieje kilka korzyści z uruchamiania z pliku pakietu:

+ Zmniejsza ryzyko kopiowania plików na problemy z blokowaniem.
+ Można wdrożyć aplikacji produkcyjnej (z ponownym uruchomieniu).
+ Może być niektórych plików, które są uruchomione w swojej aplikacji.
+ Zwiększa wydajność [wdrożeń usługi Azure Resource Manager](functions-infrastructure-as-code.md).
+ Może zmniejszyć czasy zimnego startu, szczególnie w przypadku funkcji JavaScript z drzewa pakietu npm dużych.

Aby uzyskać więcej informacji, zobacz [tym ogłoszeniu](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Włączanie funkcji do uruchomienia z pakietu

Aby umożliwić zarządzanie aplikacją funkcji do uruchomienia z pakietu, wystarczy dodać `WEBSITE_RUN_FROM_PACKAGE` ustawienie w ustawieniach Twojej aplikacji funkcji. `WEBSITE_RUN_FROM_PACKAGE` Ustawienie może mieć jedną z następujących wartości:

| Wartość  | Opis  |
|---------|---------|
|**`<url>`**  | Lokalizacja pliku określonego pakietu, który chcesz uruchomić. Korzystając z usługi Blob storage, skorzystaj z kontenera prywatnych przy użyciu [sygnatury dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) Aby włączyć środowisko uruchomieniowe usługi Functions w celu uzyskania dostępu do pakietu. Możesz użyć [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) do przekazywania plików pakietu do konta usługi Blob storage.         |
| **`1`**  | Uruchom z pliku pakietu w `d:\home\data\SitePackages` folderu aplikacji funkcji. Ta opcja wymaga folder musi mieć również w pliku o nazwie `packagename.txt`. Ten plik zawiera tylko nazwę pliku pakietu w folderze, bez żadnych odstępów. |

Na poniższym obrazie przedstawiono aplikację funkcji skonfigurowane do uruchamiania z pliku zip, hostowana w usłudze Azure Blob storage:

![Ustawienia aplikacji WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu ZIP.

## <a name="integration-with-zip-deployment"></a>Integracja z wdrożeniem zip

[ZIP wdrożenia] [ Zip deployment for Azure Functions] to funkcja usługi Azure App Service, która umożliwia wdrażanie projektu aplikacji funkcji do `wwwroot` katalogu. Projekt jest spakowany jako plik zip wdrożenia. Tych samych interfejsów API mogą służyć do wdrożenia pakietu do `d:\home\data\SitePackages` folderu. Za pomocą `WEBSITE_RUN_FROM_PACKAGE` wartość ustawienia aplikacji `1`, wdrażanie pliku zip skopiuj interfejsów API pakietu do `d:\home\data\SitePackages` folderu zamiast wypakowywanie plików do `d:\home\site\wwwroot`. Tworzy również `packagename.txt` pliku. Aplikacja funkcji jest uruchamiany z pakietu po ponownym uruchomieniu, a `wwwroot` staje się tylko do odczytu. Aby uzyskać więcej informacji na temat wdrażania zip zobacz [Zip wdrożenia dla usługi Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Dodawanie ustawienia WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
