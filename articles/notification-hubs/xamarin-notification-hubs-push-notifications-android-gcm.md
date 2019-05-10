---
title: Wysyłanie powiadomień push do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin Android przy użyciu usługi Azure Notification Hubs.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/01/2019
ms.author: jowargo
ms.openlocfilehash: 00e62226ee7e2b912a909cfa32a25e4562b99e83
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203743"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień push do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie

Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin.Android przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację dla platformy Xamarin.Android służącą do odbierania powiadomień push przy użyciu usługi Firebase Cloud Messaging (FCM). Za pomocą centrum powiadomień będzie można wysyłać powiadomienia push do wszystkich urządzeń, na których działa ta aplikacja. Gotowy kod jest dostępny w [aplikacji NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) próbki.

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging
> * Tworzenie centrum powiadomień
> * Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień
> * Wysyłanie powiadomień testowych z witryny Azure Portal

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Program Visual Studio z platformą Xamarin] w systemie Windows lub [program Visual Studio dla komputerów Mac] w systemie OS X.
* Aktywne konto Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Tworzenie projektu Firebase i włączanie usługi Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Konfigurowanie ustawień usługi GCM dla centrum powiadomień

1. Wybierz pozycję **Google (GCM)** w sekcji **USTAWIENIA POWIADOMIEŃ**.
2. Wprowadź **starszy klucz serwera** zanotowany z konsoli Firebase Google Console.
3. Wybierz pozycję **Zapisz** na pasku narzędzi.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Centrum powiadomień jest skonfigurowane do pracy z usługą FCM i uzyskano parametry połączenia do rejestrowania aplikacji zarówno w celu odbierania, jak i wysyłania powiadomień wypychanych.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Tworzenie aplikacji platformy Xamarin.Android i łączenie jej z centrum powiadomień

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Tworzenie projektu programu Visual Studio i dodawanie pakietów NuGet

1. W programie Visual Studio otwórz menu **Plik**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Projekt**. W **nowy projekt** okna, wykonaj następujące kroki: 
    1. Rozwiń **zainstalowane**, **Visual C#** , a następnie kliknij przycisk **Android**.
    2. Wybierz **aplikacji dla systemu Android (Xamarin)** z listy. 
    3. Wprowadź **nazwę** dla projektu. 
    4. Wybierz **lokalizacji** dla projektu. 
    5. Kliknij przycisk **OK** 

        ![Okno dialogowe nowego projektu](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)        
2. Na **nową aplikację systemu Android** okno dialogowe, wybierz opcję **pusta aplikacja**i wybierz **OK**. 

    ![Okno dialogowe nowego projektu](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
1. W oknie **Eksploratora rozwiązań** rozwiń węzeł **Właściwości** i kliknij pozycję **AndroidManifest.xml**. Zaktualizuj nazwę pakietu tak, aby pasowała do nazwy pakietu wprowadzonej podczas dodawania usługi Firebase Cloud Messaging do projektu w konsoli Google Firebase Console.

    ![Nazwa pakietu w usłudze GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Kliknij projekt prawym przyciskiem myszy i wybierz pozycję **Zarządzaj pakietami NuGet**.
4. Wybierz kartę **Przeglądaj**. Wyszukaj pozycję **Xamarin.GooglePlayServices.Base**. Na liście wyników wybierz pozycję **Xamarin.GooglePlayServices.Base**. Następnie wybierz pozycję **Zainstaluj**.

    ![Pakiet NuGet usług Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. W oknie **Menedżera pakietów NuGet** wyszukaj pozycję **Xamarin.Firebase.Messaging**. Na liście wyników wybierz pozycję **Xamarin.Firebase.Messaging**. Następnie wybierz pozycję **Zainstaluj**.
6. Teraz wyszukaj pozycję **Xamarin.Azure.NotificationHubs.Android**. Na liście wyników wybierz pozycję **Xamarin.Azure.NotificationHubs.Android**. Następnie wybierz pozycję **Zainstaluj**.

### <a name="add-the-google-services-json-file"></a>Dodawanie pliku JSON usług Google

1. Skopiuj plik `google-services.json` pobrany z konsoli Google Firebase Console do folderu projektu.
2. Dodaj plik `google-services.json` do projektu.
3. Wybierz plik `google-services.json` w oknie **Eksploratora rozwiązań**.
4. W okienku **Właściwości** ustaw **GoogleServicesJson** jako akcję kompilacji. Jeśli nie widzisz opcji **GoogleServicesJson**, zamknij program Visual Studio, uruchom go ponownie, otwórz projekt i spróbuj ponownie.

    ![Akcja kompilacji GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurowanie centrów powiadomień w projekcie

#### <a name="registering-with-firebase-cloud-messaging"></a>Rejestrowanie w usłudze Firebase Cloud Messaging

1. Otwórz plik `AndroidManifest.xml` i wstaw następujące elementy `<receiver>` do elementu `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```
2. Dodaj następujące instrukcje **przed zastosowaniem** elementu. 

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```
1. Zbierz następujące informacje dotyczące aplikacji dla systemu Android i centrum powiadomień:

   * **Listen connection string (Parametry połączenia nasłuchiwania)**: na pulpicie nawigacyjnym w witrynie [Azure Portal] wybierz pozycję **Wyświetl parametry połączeń**. Skopiuj parametry połączenia `DefaultListenSharedAccessSignature` dla tej wartości.
   * **Nazwa centrum**: nazwa centrum z witryny [Azure Portal]. Na przykład *moje_centrum_powiadomien_2*.
3. W oknie **Eksploratora rozwiązań** kliknij prawym przyciskiem myszy **projekt**, wskaż pozycję **Dodaj** i wybierz pozycję **Klasa**.
4. Utwórz klasę `Constants.cs` w projekcie Xamarin i zdefiniuj w tej klasie następujące wartości stałych. Zastąp tekst zastępczy własnymi wartościami.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```
5. Dodaj następujące instrukcje using w pliku `MainActivity.cs`:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```
6. Dodaj następujące właściwości do klasy MainActivity. Zmienna TAG będzie służyć do wyświetlenia okna dialogowego alertu podczas działania aplikacji:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```
7. Dodaj następującą metodę do klasy MainActivity. Sprawdza, czy **usług Google Play** są dostępne na urządzeniu. 

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }
     
        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```
1. Dodaj następującą metodę do klasy MainActivity, który tworzy kanał powiadomień.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }
     
        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };
     
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```
1. W pliku `MainActivity.cs` dodaj następujący kod do elementu `OnCreate` po elemencie `base.OnCreate(savedInstanceState)`:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }
    
    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```
8. Utwórz nową klasę `MyFirebaseIIDService` w sposób, w jaki utworzono klasę `Constants`.
9. Dodaj następujące instrukcje using w pliku `MyFirebaseIIDService.cs`:

    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

10. W pliku `MyFirebaseIIDService.cs` dodaj następującą deklarację `class` i spraw, aby ta klasa dziedziczyła z klasy `FirebaseInstanceIdService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
11. W pliku `MyFirebaseIIDService.cs` dodaj następujący kod:

    ```csharp
    const string TAG = "MyFirebaseIIDService";
    NotificationHub hub;

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "FCM token: " + refreshedToken);
        SendRegistrationToServer(refreshedToken);
    }

    void SendRegistrationToServer(string token)
    {
        // Register with Notification Hubs
        hub = new NotificationHub(Constants.NotificationHubName,
                                    Constants.ListenConnectionString, this);

        var tags = new List<string>() { };
        var regID = hub.Register(token, tags.ToArray()).RegistrationId;

        Log.Debug(TAG, $"Successful registration of ID {regID}");
    }
    ```
12. Utwórz nową klasę dla projektu i nadaj jej nazwę `MyFirebaseMessagingService`.
13. Dodaj następujące instrukcje using w pliku `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;
    using Build = Android.OS.Build;
    ```
14. Dodaj następujący kod nad deklaracją klasy i spraw, aby klasa dziedziczyła z klasy `FirebaseMessagingService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
15. Dodaj następujący kod do pliku `MyFirebaseMessagingService.cs`:

    ```csharp
    const string TAG = "MyFirebaseMsgService";
    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);
        if(message.GetNotification()!= null)
        {
            //These is how most messages will be received
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
            SendNotification(message.GetNotification().Body);
        }
        else
        {
            //Only used for debugging payloads sent from the Azure portal
            SendNotification(message.Data.Values.First());

        }
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetContentTitle("FCM Message")
                    .SetSmallIcon(Resource.Drawable.ic_launcher)
                    .SetContentText(messageBody)
                    .SetAutoCancel(true)
                    .SetShowWhen(false)
                    .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(MainActivity.CHANNEL_ID);
        }

        var notificationManager = NotificationManager.FromContext(this);

        notificationManager.Notify(0, notificationBuilder.Build());
    }
    ```
16. **Skompiluj** projekt.
17. **Uruchom** aplikację na urządzeniu lub załadowanym emulatorze.

## <a name="send-test-notification-from-the-azure-portal"></a>Wysyłanie powiadomienia testowego z witryny Azure Portal

Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji **Wysyłanie testowe** w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Powiadomienia wypychane są zwykle wysyłane w usłudze zaplecza, takiej jak Mobile Services czy ASP.NET, za pośrednictwem zgodnej biblioteki. Jeśli biblioteka nie jest dostępna w danym zapleczu, powiadomienia można również wysyłać bezpośrednio za pomocą interfejsu API REST.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem Android zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem Android, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Program Visual Studio z platformą Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[program Visual Studio dla komputerów Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
