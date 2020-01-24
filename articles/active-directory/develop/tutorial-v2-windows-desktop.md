---
title: Wprowadzenie do programu Microsoft Identity platform Windows Desktop | Microsoft Docs
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez platformę tożsamości firmy Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1864b4867b8e1367a2397c5ed2ed9a77001dadb4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701199"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Wywoływanie interfejsu API Microsoft Graph z aplikacji klasycznej systemu Windows

W tym przewodniku pokazano, jak Natywna aplikacja Windows Desktop .NET (XAML) używa tokenu dostępu do wywoływania interfejsu API Microsoft Graph. Aplikacja może również uzyskiwać dostęp do innych interfejsów API, które wymagają tokenów dostępu z platformy tożsamości firmy Microsoft dla deweloperów w wersji 2.0. Ta platforma była wcześniej nazywana usługą Azure AD.

Po zakończeniu przewodnika aplikacja będzie mogła wywołać chroniony interfejs API, który korzysta z kont osobistych (w tym outlook.com, live.com i innych). Aplikacja będzie również używać kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.  

> [!NOTE]
> Przewodnik wymaga programu Visual Studio 2015 Update 3, Visual Studio 2017 lub Visual Studio 2019. Nie masz żadnej z tych wersji? [Pobierz bezpłatnie program Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa Przykładowa aplikacja generowana przez ten przewodnik

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Przykładowa aplikacja utworzona za pomocą tego przewodnika umożliwia aplikacji klasycznych systemu Windows, która będzie wysyłać zapytania do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który akceptuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu należy dodać token do żądań HTTP za pośrednictwem nagłówka autoryzacji. Biblioteka Microsoft Authentication Library (MSAL) obsługuje pozyskiwanie i odnawianie tokenów.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa pozyskiwania tokenu do uzyskiwania dostępu do chronionych interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token, za pomocą którego można wysyłać zapytania do interfejsu API Microsoft Graph lub interfejsu API sieci Web, który jest zabezpieczony przez platformę tożsamości firmy Microsoft dla deweloperów.

Interfejsy API, takie jak Microsoft Graph wymagają tokenu, aby zezwolić na dostęp do określonych zasobów. Na przykład token jest wymagany do odczytywania profilu użytkownika, uzyskiwania dostępu do kalendarza użytkownika lub wysyłania wiadomości e-mail. Aplikacja może zażądać tokenu dostępu za pomocą MSAL, aby uzyskać dostęp do tych zasobów, określając zakresy interfejsów API. Ten token dostępu jest następnie dodawany do nagłówka autoryzacji HTTP dla każdego wywołania, które zostało wykonane względem chronionego zasobu.

MSAL zarządza buforowaniem i odświeża tokeny dostępu, dzięki czemu aplikacja nie musi.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku są stosowane następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft (MSAL.NET)|

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji utworzysz nowy projekt, aby zademonstrować, jak zintegrować aplikację .NET Desktop (XAML) z *logowaniem się z firmą Microsoft* , aby aplikacja mogła wysyłać zapytania do internetowych interfejsów API, które wymagają tokenu.

Aplikacja utworzona za pomocą tego przewodnika wyświetla przycisk, który służy do wywoływania grafu, obszar do wyświetlania wyników na ekranie i przycisk Wyloguj.

> [!NOTE]
> Wolisz pobrać projekt przykładowego programu Visual Studio? [Pobierz projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)i przejdź do [kroku konfiguracji](#register-your-application) , aby skonfigurować przykładowy kod przed jego uruchomieniem.
>

Aby utworzyć aplikację, wykonaj następujące czynności:

1. W programie Visual Studio wybierz pozycje **Plik** > **Nowy** > **Projekt**.
2. W obszarze **Szablony**wybierz **pozycję C#Wizualizacja** .
3. Wybierz pozycję **Aplikacja WPF (.NET Framework)** , w zależności od używanej wersji programu Visual Studio.

## <a name="add-msal-to-your-project"></a>Dodawanie MSAL do projektu

1. W programie Visual Studio wybierz pozycję **Narzędzia** > **Menedżer pakietów NuGet**> **Konsola menedżera pakietów**.
2. W oknie Konsola Menedżera pakietów Wklej następujące polecenie Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > To polecenie powoduje zainstalowanie biblioteki uwierzytelniania firmy Microsoft. MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkowników, które są używane w celu uzyskania dostępu do interfejsów API chronionych przez Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację możesz zarejestrować na dwa sposoby.

### <a name="option-1-express-mode"></a>Opcja 1: tryb ekspresowy

Możesz szybko zarejestrować aplikację, wykonując następujące czynności:
1. Przejdź do [Azure Portal — Rejestracja aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wybierz pozycję **Nowa rejestracja**.
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `Win-App-calling-MsGraph`.
   - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)** .
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
   1. W sekcji **identyfikatory URI przekierowania** na liście identyfikatorów URI przekierowania:
   1. W kolumnie **Typ** wybierz pozycję **publiczny Klient/natywny (Mobile & Desktop)** .
   1. W kolumnie **Identyfikator URI przekierowania** wprowadź `https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Wybierz pozycję **Zarejestruj**.
1. Przejdź do programu Visual Studio, Otwórz plik *App.XAML.cs* , a następnie zastąp `Enter_the_Application_Id_here` w poniższym FRAGMENCIE kodu identyfikatorem aplikacji, która została właśnie zarejestrowana i skopiowana.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Dodaj kod w celu zainicjowania MSAL

W tym kroku utworzysz klasę do obsługi interakcji z MSAL, taką jak obsługa tokenów.

1. Otwórz plik *App.XAML.cs* , a następnie Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Zaktualizuj klasę aplikacji do następujących:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Tworzenie interfejsu użytkownika aplikacji

W tej sekcji pokazano, jak aplikacja może wysyłać zapytania do chronionego serwera zaplecza, takiego jak Microsoft Graph. 

Plik *MainWindow. XAML* powinien być automatycznie tworzony jako część szablonu projektu. Otwórz ten plik, a następnie zastąp węzeł *\<siatki >* aplikacji, korzystając z następującego kodu:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Użyj MSAL, aby uzyskać token dla interfejsu API Microsoft Graph

W tej sekcji użyjesz MSAL, aby uzyskać token dla interfejsu API Microsoft Graph.

1. W pliku *MainWindow.XAML.cs* Dodaj odwołanie do MSAL do klasy:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Zastąp kod klasy `MainWindow` następującym:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

#### <a name="get-a-user-token-interactively"></a>Interaktywne pobieranie tokenu użytkownika

Wywołanie metody `AcquireTokenInteractive` powoduje pojawienie się okna z monitowaniem użytkowników o zalogowanie się. Aplikacje zwykle wymagają od użytkowników logowania się interaktywnie przy pierwszym dostępie do chronionego zasobu. Może być również konieczne zalogowanie się w przypadku niepowodzenia operacji dyskretnej uzyskania tokenu (na przykład po wygaśnięciu hasła przez użytkownika).

#### <a name="get-a-user-token-silently"></a>Dyskretne pobieranie tokenu użytkownika

Metoda `AcquireTokenSilent` obsługuje pozyskiwanie i odnawianie tokenów bez żadnej interakcji z użytkownikiem. Gdy `AcquireTokenInteractive` jest wykonywane po raz pierwszy, `AcquireTokenSilent` jest zazwyczaj stosowana do uzyskiwania tokenów, które uzyskują dostęp do chronionych zasobów dla kolejnych wywołań, ponieważ wywołania żądania lub odnowienia tokenów są wykonywane w trybie dyskretnym.

Wreszcie Metoda `AcquireTokenSilent` zakończy się niepowodzeniem. Przyczyną niepowodzenia może być wylogowanie lub zmiana hasła przez użytkownika na innym urządzeniu. Gdy MSAL wykryje, że problem może zostać rozwiązany przez wymaganie interaktywnej akcji, uruchamia wyjątek `MsalUiRequiredException`. Aplikacja może obsłużyć ten wyjątek na dwa sposoby:

* Może to spowodować wywołanie `AcquireTokenInteractive` od razu. To wywołanie powoduje wyświetlenie monitu o zalogowanie użytkownika. Ten wzorzec jest zwykle używany w aplikacjach online, w przypadku których użytkownik nie ma dostępnej zawartości w trybie offline. Przykład generowany przez tę konfigurację z przewodnikiem jest zgodny z tym wzorcem, który można zobaczyć w akcji przy pierwszym wykonaniu przykładu. 

* Ponieważ żaden użytkownik nie użył aplikacji, `PublicClientApp.Users.FirstOrDefault()` zawiera wartość null i zostanie zgłoszony wyjątek `MsalUiRequiredException`. 

* Kod w próbce następnie obsługuje wyjątek przez wywołanie `AcquireTokenInteractive`, które powoduje wyświetlenie monitu o zalogowanie użytkownika.

* Może zamiast tego przedstawić wizualne wskazanie użytkownikom, że jest wymagane logowanie interaktywne, aby umożliwić im wybranie odpowiedniego czasu do zalogowania. Lub aplikacja może ponowić próbę `AcquireTokenSilent` później. Ten wzorzec jest często używany, gdy użytkownicy mogą korzystać z innych funkcji aplikacji bez zakłóceń — na przykład gdy zawartość offline jest dostępna w aplikacji. W takim przypadku użytkownicy mogą zdecydować się na zalogowanie się w celu uzyskania dostępu do chronionego zasobu lub odświeżenia nieaktualnych informacji. Alternatywnie aplikacja może zdecydować się na ponowienie próby `AcquireTokenSilent` po przywróceniu sieci po jej tymczasowym udostępnieniu.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Wywoływanie interfejsu API Microsoft Graph przy użyciu właśnie uzyskanego tokenu

Dodaj następującą nową metodę do `MainWindow.xaml.cs`. Metoda służy do `GET` żądania interfejs API programu Graph przy użyciu autoryzowanego nagłówka:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Więcej informacji na temat tworzenia wywołania REST w ramach chronionego interfejsu API

W tej przykładowej aplikacji użyto metody `GetHttpContentWithToken`, aby wykonać żądanie HTTP `GET` względem chronionego zasobu, który wymaga tokenu, a następnie zwrócić zawartość do obiektu wywołującego. Ta metoda dodaje token uzyskany w nagłówku autoryzacji HTTP. Dla tego przykładu zasób *jest punktem* końcowym usługi Microsoft Graph API, który wyświetla informacje o profilu użytkownika.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Dodawanie metody do wylogowania użytkownika

Aby wylogować użytkownika, Dodaj następującą metodę do pliku `MainWindow.xaml.cs`:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Więcej informacji na temat wylogowywania użytkowników

Metoda `SignOutButton_Click` usuwa użytkowników z pamięci podręcznej użytkownika MSAL, co efektywnie informuje MSAL o tym, że bieżący użytkownik chce, aby przyszłe żądanie uzyskania tokenu zakończyło się powodzeniem tylko wtedy, gdy zostanie wykonane interaktywnie.

Mimo że aplikacja w tym przykładzie obsługuje pojedynczych użytkowników, MSAL obsługuje scenariusze, w których może być zalogowanych wiele kont w tym samym czasie. Przykładem jest aplikacja poczty e-mail, w której użytkownik ma wiele kont.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Wyświetl podstawowe informacje o tokenie

Aby wyświetlić podstawowe informacje o tokenie, Dodaj następującą metodę do pliku *MainWindow.XAML.cs* :

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Oprócz tokenu dostępu, który jest używany do wywoływania interfejsu API Microsoft Graph, po zalogowaniu się użytkownika MSAL również uzyskuje token identyfikatora. Ten token zawiera niewielki podzbiór informacji przydatnych dla użytkowników. Metoda `DisplayBasicTokenInfo` wyświetla podstawowe informacje zawarte w tokenie. Na przykład wyświetla nazwę wyświetlaną i identyfikator użytkownika, a także datę wygaśnięcia tokenu i ciąg reprezentujący sam token dostępu. Przycisk *wywołania Microsoft Graph API* można wybrać wiele razy i zobaczyć, że ten sam token został ponownie użyty dla kolejnych żądań. Możesz również sprawdzić datę wygaśnięcia, która zostanie rozszerzona, gdy MSAL zdecyduje, że jest czas odnowienia tokenu.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
