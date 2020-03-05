---
title: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web platformy Microsoft Identity platform Python | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji sieci Web w języku Python za pomocą OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 34f0fb57b4432a8153f2cbaa8cb60edbb9a6f494
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271087"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python

W tym przewodniku szybki start dowiesz się, jak zintegrować aplikację sieci Web w języku Python z platformą tożsamości firmy Microsoft. Twoja aplikacja zaloguje użytkownika, uzyskaj token dostępu, aby wywołać interfejs API Microsoft Graph i przetworzyć żądanie do interfejsu API Microsoft Graph.

Po ukończeniu tego przewodnika aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory. (Zobacz [, jak działa przykład](#how-the-sample-works) dla ilustracji).


## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) lub [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolba](http://flask.pocoo.org/), [podsesja](https://pythonhosted.org/Flask-Session/), [żądania](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
>
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `python-webapp`.
>      - W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
>      - W obszarze **Identyfikator URI przekierowania** , na liście rozwijanej Wybierz platformę **sieci Web** , a następnie ustaw wartość na `http://localhost:5000/getAToken`.
>      - Wybierz pozycję **Zarejestruj**. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W menu po lewej stronie wybierz pozycję **certyfikaty & wpisy tajne** i kliknij pozycję **Nowy wpis tajny klienta** w sekcji wpisy **tajne klienta** :
>
>      - Wpisz opis klucza (dla wpisu tajnego aplikacji wystąpienia).
>      - Wybierz kluczowy czas trwania wynoszący **1 rok**.
>      - Po kliknięciu przycisku **Dodaj**zostanie wyświetlona wartość klucza.
>      - Skopiuj wartość klucza. Będzie potrzebny później.
> 1. Wybierz sekcję **uprawnienia interfejsu API**
>
>      - Kliknij przycisk **Dodaj uprawnienia** , a następnie
>      - Upewnij się, że karta **Microsoft API** została wybrana
>      - W sekcji *najczęściej używane interfejsy API firmy Microsoft* kliknij pozycję **Microsoft Graph**
>      - W sekcji **uprawnienia delegowane** upewnij się, że są zaznaczone odpowiednie uprawnienia: **User. ReadBasic. All**. W razie potrzeby użyj pola wyszukiwania.
>      - Wybierz przycisk **Dodaj uprawnienia**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby uzyskać przykładowy kod dla tego przewodnika Szybki Start, należy wykonać następujące czynności:
>
> 1. Dodaj adres URL odpowiedzi jako `http://localhost:5000/getAToken`.
> 1. Utwórz klucz tajny klienta.
> 1. Microsoft Graph Dodaj uprawnienie ReadBasic użytkownika interfejsu API. wszystkie delegowane uprawnienia.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-aspnet-webapp/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tego atrybutu

#### <a name="step-2-download-your-project"></a>Krok 2. Pobieranie projektu
> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Pobierz projekt i wyodrębnij plik zip do folderu lokalnego bliżej folderu głównego — na przykład **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Krok 3. Konfigurowanie aplikacji
> 
> 1. Wyodrębnij plik zip do folderu lokalnego bliższego folderowi głównemu, na przykład **C:\Azure-Samples**
> 1. Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE (opcjonalnie).
> 1. Otwórz plik **app_config. PR** , który znajduje się w folderze głównym i Zamień na następujący fragment kodu:
> 
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Gdzie:
>
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here` — jest **kluczem tajnym klienta** utworzonym w **certyfikatach & wpisy tajne** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Name_Here` — jest wartością **identyfikatora katalogu (dzierżawy)** zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3. Uruchamianie przykładu kodu

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4. Uruchamianie przykładu kodu

1. Konieczne będzie zainstalowanie biblioteki języka Python MSAL, struktury kolby, sesji do zarządzania sesjami po stronie serwera i żądań przy użyciu funkcji PIP w następujący sposób:

    ```Shell
    pip install -r requirements.txt
    ```

2. Uruchom app.py z powłoki lub wiersza polecenia:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [te instrukcje](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Pobieranie MSAL
MSAL to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.
Możesz dodać MSAL Python do aplikacji przy użyciu narzędzia PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL
Można dodać odwołanie do MSAL Python, dodając następujący kod na początku pliku, w którym będzie używany MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat aplikacji sieci Web, które logują użytkowników, a następnie wywołujących interfejsy API sieci Web:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacje sieci Web, które logują użytkowników](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
