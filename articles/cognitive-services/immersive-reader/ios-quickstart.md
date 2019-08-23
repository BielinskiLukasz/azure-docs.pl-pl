---
title: 'Szybki start: Tworzenie aplikacji dla systemu iOS, która uruchamia czytnik immersyjny (SWIFT)'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację dla systemu iOS od podstaw i dodasz funkcję czytnika immersyjny.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899528"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Szybki start: Tworzenie aplikacji dla systemu iOS, która uruchamia czytnik immersyjny (SWIFT)

[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności.

W tym przewodniku szybki start utworzysz aplikację dla systemu iOS od podstaw i zintegrujesz czytnik immersyjny przy użyciu zestawu SDK czytnika immersyjny. Pełny przykład pracy tego przewodnika Szybki Start jest dostępny [tutaj](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Zasób czytnika immersyjny skonfigurowany do uwierzytelniania Azure Active Directory (Azure AD). Postępuj zgodnie z [tymi instrukcjami](./azure-active-directory-authentication.md) , aby rozpocząć konfigurację. W przypadku konfigurowania przykładowych właściwości projektu konieczne będzie utworzenie niektórych wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.

## <a name="create-an-xcode-project"></a>Tworzenie projektu Xcode

Utwórz nowy projekt w Xcode.

![Nowy projekt](./media/ios/xcode-create-project.png)

Wybierz **aplikację z pojedynczym widokiem**.

![Nowa aplikacja z pojedynczym widokiem](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Pobierz zestaw SDK CocoaPod
Najprostszym sposobem korzystania z zestawu SDK czytnika immersyjny jest za pośrednictwem CocoaPods. Aby zainstalować za pośrednictwem Cocoapods:
1. [Install Cocoapods](http://guides.cocoapods.org/using/getting-started.html) — postępuj zgodnie z przewodnikiem wprowadzenie, aby zainstalować Cocoapods.
2. Utwórz element plik podfile, uruchamiając `pod init` w katalogu głównym projektu Xcode.
3.  Dodaj CocoaPod do plik podfile przez dodanie `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Twoja Pofile powinna wyglądać podobnie do poniższego, przy użyciu nazwy elementu docelowego z zastępowaniem szybkiego startu:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. W terminalu, w katalogu projektu Xcode, uruchom polecenie `pod install` , aby zainstalować zestaw SDK programu immersyjny pod.
5. Dodaj `import immersive_reader_sdk` do wszystkich plików, które muszą odwoływać się do zestawu SDK.
6. Upewnij się, że otwarto projekt, `.xcworkspace` otwierając plik, a `.xcodeproj` nie plik.

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Wymagane są pewne wartości z kroku wymagań wstępnych konfiguracji uwierzytelniania usługi Azure AD dla tej części. Odwołaj się do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

W głównym folderze projektu, który zawiera plik plik viewcontroller. Swift, Utwórz plik klasy SWIFT o nazwie stałych. Swift. Zastąp klasę następującym kodem, dodając je do wartości, o ile ma to zastosowanie. Zachowaj ten plik jako plik lokalny, który istnieje tylko na komputerze i upewnij się, że nie zatwierdzisz tego pliku w kontroli źródła, ponieważ zawiera on klucze tajne, które nie powinny być publiczne. Zaleca się, aby nie przechowywać wpisów tajnych w aplikacji. Zamiast tego zalecamy użycie usługi wewnętrznej bazy danych w celu uzyskania tokenu, w którym wpisy tajne mogą być przechowywane poza aplikacją i poza urządzeniem. Punkt końcowy interfejsu API zaplecza powinien być zabezpieczony za pomocą uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)), aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów, które mają być używane z usługą czytnika danych immersyjny i rozliczeniami. Ta praca wykracza poza zakres tego przewodnika Szybki Start.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Skonfiguruj aplikację do uruchamiania bez scenorysu

Otwórz plik AppDelegate. Swift i zastąp go następującym kodem.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Utwórz kontroler widoku uruchamiania i Dodaj przykładową zawartość

Zmień nazwę plik viewcontroller. Swift na LaunchViewController. Swift i Zastąp plik poniższym kodem.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Ustaw schemat Archiwum w Xcode, wybierając symulator lub urządzenie docelowe.
![Schemat Archiwum](./media/ios/xcode-archive-scheme.png)<br/>
![Wybierz element docelowy](./media/ios/xcode-select-target.png)

W Xcode naciśnij klawisze CTRL + R lub kliknij przycisk Odtwórz, aby uruchomić projekt, a aplikacja powinna zostać uruchomiona w określonym symulatorze lub urządzeniu.

W aplikacji powinny zostać wyświetlone następujące tematy:

![Przykładowa aplikacja](./media/ios/sample-app-ipad.png)

Po kliknięciu przycisku "czytnik immersyjny" zobaczysz czytnik immersyjny uruchamiany z zawartością w aplikacji.

![Czytnik immersyjny](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [zestawem SDK programu immersyjny dla systemu iOS](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) i [Kompendium zestawu SDK systemu iOS dla czytnika immersyjny](./ios-reference.md)
