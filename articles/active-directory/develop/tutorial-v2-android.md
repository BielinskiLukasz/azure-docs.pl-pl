---
title: Logowanie się do/wylogowania użytkowników & wywołań Microsoft Graph (Android) — Microsoft Identity platform | Azure
description: Uzyskiwanie tokenu dostępu i Microsoft Graph wywołań lub interfejsów API, które wymagają tokenów dostępu z platformy Microsoft Identity platform (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c4c9bc025e8fd506b298ed676674899e318481
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689338"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Samouczek: Logowanie użytkowników i wywoływanie Microsoft Graph z aplikacji systemu Android 

>[!NOTE]
>W tym samouczku przedstawiono uproszczone Przykłady sposobu pracy z programem MSAL for Android. Dla uproszczenia w tym samouczku jest stosowany tylko tryb jednego konta. Możesz również wyświetlić repozytorium i sklonować [wstępnie skonfigurowaną przykładową aplikację](https://github.com/Azure-Samples/ms-identity-android-java/) , aby poznać bardziej złożone scenariusze. Zapoznaj się z [przewodnikiem Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) , aby uzyskać więcej informacji na temat przykładowej aplikacji, konfiguracji i rejestracji. 

W tym samouczku dowiesz się, jak zintegrować aplikację systemu Android z platformą tożsamości firmy Microsoft przy użyciu biblioteki uwierzytelniania firmy Microsoft dla systemu Android. Dowiesz się, jak zalogować się i wylogować użytkownika, uzyskać token dostępu w celu wywołania interfejsu API Microsoft Graph i przetworzyć żądanie do interfejs API programu Graph. 

> [!div class="checklist"]
> * Integrowanie aplikacji systemu Android z platformą tożsamości firmy Microsoft 
> * Zaloguj użytkownika 
> * Uzyskiwanie tokenu dostępu w celu wywołania interfejsu API Microsoft Graph 
> * Wywoływanie interfejsu API Microsoft Graph 
> * Wylogowywanie użytkownika 

Po ukończeniu tego samouczka aplikacja będzie akceptować logowania do osobistych kont Microsoft (w tym outlook.com, live.com i innych), a także kont służbowych z dowolnej firmy lub organizacji korzystającej z Azure Active Directory.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="how-this-tutorial-works"></a>Jak działa ten samouczek

![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten samouczek](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikacja w tym samouczku zaloguje użytkowników i pobierze dane w ich imieniu. Dostęp do tych danych uzyskuje się za pomocą chronionego interfejsu API (Microsoft Graph API), który wymaga autoryzacji i jest chroniony przez platformę tożsamości firmy Microsoft.

Więcej szczegółów:

* Twoja aplikacja zaloguje użytkownika za pomocą przeglądarki lub Microsoft Authenticator i Intune — Portal firmy.
* Użytkownik końcowy zaakceptuje uprawnienia wymagane przez aplikację.
* Aplikacja zostanie wystawiona token dostępu dla interfejsu API Microsoft Graph.
* Token dostępu zostanie uwzględniony w żądaniu HTTP do internetowego interfejsu API.
* Przetwórz odpowiedź Microsoft Graph.

Ten przykład używa biblioteki uwierzytelniania firmy Microsoft dla systemu Android (MSAL) w celu zaimplementowania uwierzytelniania: [com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 Usługa MSAL automatycznie odnawia tokeny, dostarcza Logowanie jednokrotne między innymi aplikacjami na urządzeniu i zarządza kontami.

### <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek wymaga Android Studio wersji 3.5 +

## <a name="create-a-project"></a>Tworzenie projektu
Jeśli nie masz jeszcze aplikacji systemu Android, wykonaj następujące kroki, aby skonfigurować nowy projekt. 

1. Otwórz Android Studio i wybierz pozycję **Rozpocznij nowy projekt Android Studio**.
2. Wybierz **działanie podstawowe** i wybierz pozycję **dalej**.
3. Nadaj nazwę aplikacji.
4. Zapisz nazwę pakietu. Wprowadzisz ją później do Azure Portal.
5. Zmień język z **Kotlin** na **Java**.
6. Ustaw **minimalny poziom interfejsu API** na **interfejs API 19** lub nowszy, a następnie kliknij przycisk **Zakończ**.
7. W widoku Projekt wybierz **projekt** na liście rozwijanej, aby wyświetlić źródło i nieźródłowe pliki projektu, Otwórz **aplikację/Build. gradle** i ustaw `targetSdkVersion` na `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integracja z biblioteką uwierzytelniania firmy Microsoft 

### <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Przejdź do witryny [Azure Portal](https://aka.ms/MobileAppReg).
2. Otwórz [blok rejestracje aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i kliknij pozycję **+ Nowa rejestracja**.
3. Wprowadź **nazwę** aplikacji, a następnie, **bez** ustawienia identyfikatora URI przekierowania, kliknij pozycję **zarejestruj**.
4. W sekcji **Zarządzanie** w wyświetlonym okienku wybierz pozycję **uwierzytelnianie** >  **+ Dodaj platformę** > **Android**. (Może być konieczne wybranie pozycji "Przełącz do nowego środowiska" w górnej części bloku, aby zobaczyć tę sekcję)
5. Wprowadź nazwę pakietu projektu. Jeśli pobrano kod, ta wartość jest `com.azuresamples.msalandroidapp`.
6. W sekcji **skrót podpisu** na stronie **Konfigurowanie aplikacji systemu Android** kliknij pozycję **generowanie skrótu sygnatury deweloperskiej.** i skopiuj polecenie Narzędzia klawiaturowego, które ma być używane dla danej platformy.

   > [!Note]
   > Narzędzie narzêdzi. exe jest instalowane w ramach zestawu Java Development Kit (JDK). Należy również zainstalować narzędzie OpenSSL, aby wykonać polecenie Narzędzia.

7. Wprowadź **skrót podpisu** wygenerowany przez narzędzie.
8. Kliknij `Configure` i Zapisz **konfigurację MSAL** , która jest wyświetlana na stronie **konfiguracji systemu Android** , aby można ją było wprowadzić podczas późniejszej konfiguracji aplikacji.  Kliknij przycisk **Gotowe**.

### <a name="configure-your-application"></a>Konfigurowanie aplikacji 

1. W okienku projektu Android Studio przejdź do **app\src\main\res**.
2. Kliknij prawym przyciskiem myszy pozycję **res** i wybierz pozycję **Nowy** **katalog** > . Wprowadź `raw` jako nazwę nowego katalogu, a następnie kliknij przycisk **OK**.
3. W **aplikacji** > **src** > **głównej** > **res** > **RAW**, Utwórz nowy plik JSON o nazwie `auth_configbn_single_account.json` i wklej wcześniej zapisaną konfigurację MSAL. 

    Poniżej identyfikatora URI przekierowania wklej: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Plik konfiguracji powinien wyglądać podobnie do poniższego przykładu: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >W tym samouczku przedstawiono tylko sposób konfigurowania aplikacji w trybie jednego konta. Zapoznaj się z dokumentacją, aby uzyskać więcej informacji na temat [trybu pojedynczego i wielu kont](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) oraz [konfigurowania aplikacji](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. W oknie **app** > **src** > **Main** > **pliku AndroidManifest. XML**Dodaj poniżej działanie `BrowserTabActivity` do treści aplikacji. Ten wpis umożliwia firmie Microsoft wywoływanie z powrotem do aplikacji po zakończeniu uwierzytelniania:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Zastąp nazwę pakietu zarejestrowanego w Azure Portal dla wartości `android:host=`.
    Zastąp skrót klucza zarejestrowany w Azure Portal dla wartości `android:path=`. Wartość skrótu podpisu **nie** powinna być zakodowana w adresie URL. Upewnij się, że na początku skrótu podpisu występuje wiodąca `/`. 
    >[!NOTE]
    >"Nazwa pakietu" spowoduje zamianę wartości `android:host`, która powinna wyglądać podobnie do: "com. azuresamples. msalandroidapp", "skrót sygnatury" spowoduje zamienienie `android:path` wartości na powinien wyglądać podobnie do: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =" można również znaleźć te wartości w bloku uwierzytelniania rejestracji aplikacji. Zwróć uwagę, że identyfikator URI przekierowania będzie wyglądać podobnie do: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Gdy skrót sygnatury jest zakodowany na końcu tej wartości, wartość skrótu podpisu **nie** powinna być zakodowana w adresie URL w wartości `android:path`. 

## <a name="use-msal"></a>Użyj MSAL 

### <a name="add-msal-to-your-project"></a>Dodawanie MSAL do projektu

1. W oknie projekt Android Studio przejdź do **aplikacji** > **src** > **Build. Gradle** i Dodaj następujące elementy: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.0.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    ```
    [Więcej informacji na Microsoft Graph zestawie SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Wymagane importy 

Dodaj następujący kod w górnej części **aplikacji** > **src** > **Main**> **Java** > **com. przykład (yourapp)**  > **Main. Java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Tworzenie wystąpienia PublicClientApplication
#### <a name="initialize-variables"></a>Inicjuj zmienne 
```java
private final static String[] SCOPES = {"User.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Wewnątrz klasy `MainActivity` zapoznaj się z następującą metodą OnCreate (), aby utworzyć wystąpienie MSAL przy użyciu `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Posłuchaj przycisków i wywołaj metody lub Rejestruj odpowiednio błędy. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Wylogowanie za pomocą usługi MSAL usuwa wszystkie znane informacje o użytkowniku z aplikacji, ale użytkownik będzie nadal mieć aktywną sesję na swoim urządzeniu. Jeśli użytkownik spróbuje się zalogować ponownie, może wyświetlić interfejs użytkownika logowania, ale może nie być konieczne ponowne wprowadzenie poświadczeń, ponieważ sesja urządzenia jest nadal aktywna. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Wywołanie zwrotne używane dla żądań interaktywnych.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Wywołanie zwrotne używane na potrzeby żądań dyskretnych 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Wywoływanie interfejsu API programu Microsoft Graph 

Poniższy kod ilustruje sposób wywoływania GraphAPI przy użyciu zestawu Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika
### <a name="activity"></a>Działanie 
Jeśli chcesz zamodelować interfejs użytkownika w tym samouczku, następujące metody zapewniają Przewodnik aktualizowania tekstu i nasłuchiwanie przycisków.

#### <a name="updateui"></a>updateUI
Włącz/Wyłącz przyciski na podstawie stanu logowania i Ustaw tekst.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metoda aktualizowania tekstu w interfejsie użytkownika w celu odzwierciedlenia wylogowania. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

Przykładowy plik `activity_main.xml` do wyświetlania przycisków i pól tekstowych. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testowanie aplikacji

### <a name="run-locally"></a>Uruchamianie lokalnie

Kompiluj i Wdróż aplikację na urządzeniu testowym lub w emulatorze. Powinno być możliwe zalogowanie się i uzyskanie tokenów dla usługi Azure AD lub osobistych kont Microsoft.

Po zalogowaniu aplikacja będzie wyświetlać dane zwrócone z punktu końcowego Microsoft Graph `/me`.

### <a name="consent"></a>Posiadacz

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, otrzyma monit o tożsamość firmy Microsoft, aby wyrazić zgodę na wymagane uprawnienia. Niektórzy dzierżawy usługi Azure AD wyłączyli zgodę użytkownika, która wymaga od administratorów wyrażania zgody w imieniu wszystkich użytkowników. Aby obsługiwać ten scenariusz, trzeba utworzyć własną dzierżawę lub odebrać zgodę administratora. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń obiekt aplikacji, który został utworzony w kroku [zarejestruj aplikację](#register-your-application) .

## <a name="get-help"></a>Uzyskiwanie pomocy

Odwiedź [Pomoc i pomoc techniczną,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Jeśli masz problemy z tym samouczkiem lub platformą tożsamości firmy Microsoft.

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wykonując krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Microsoft Identity platform — ankieta](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
