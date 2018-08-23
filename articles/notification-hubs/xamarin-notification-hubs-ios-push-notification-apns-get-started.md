---
title: Wysyłanie powiadomień push do aplikacji platformy Xamarin.iOS przy użyciu usługi Azure Notification Hubs | Microsoft Docs
description: Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji platformy Xamarin dla systemu iOS przy użyciu usługi Azure Notification Hubs.
services: notification-hubs
keywords: powiadomienia wypychane w systemie ios, wiadomości wypychane, powiadomienia wypychane, wiadomość wypychana
documentationcenter: xamarin
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: ac8e5240f4544ccbb7256acbc6e70f00eff15092
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920084"
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Samouczek: wysyłanie powiadomień push do aplikacji platformy Xamarin.iOS przy użyciu usługi Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Omówienie
Korzystając z tego samouczka, dowiesz się, jak wysyłać powiadomienia wypychane do aplikacji dla systemu iOS przy użyciu usługi Azure Notification Hubs. Utworzysz pustą aplikację platformy Xamarin.iOS służącą do odbierania powiadomień push przy użyciu usługi [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Po zakończeniu będzie można za pomocą centrum powiadomień wysyłać powiadomienia push do wszystkich urządzeń, na których działa ta aplikacja. Gotowy kod jest dostępny w przykładowej aplikacji [NotificationHubs][GitHub].

W tym samouczku utworzysz/zaktualizujesz kod, aby wykonać następujące zadania: 

> [!div class="checklist"]
> * Generowanie pliku żądania podpisania certyfikatu
> * Rejestrowanie aplikacji dla usługi powiadomień wypychanych
> * Tworzenie profilu inicjowania obsługi dla aplikacji
> * Konfigurowanie centrum powiadomień dla powiadomień push systemu iOS
> * Wysyłanie testowych powiadomień push

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Najnowsza wersja środowiska [Xcode][Install Xcode]
- Urządzenie zgodne z systemem iOS 10 (lub nowszą wersją)
- Członkostwo w [programie dla deweloperów firmy Apple](https://developer.apple.com/programs/).
- [Visual Studio dla komputerów Mac]
  
  > [!NOTE]
  > Ze względu na wymagania dotyczące konfiguracji powiadomień wypychanych w systemie iOS należy wdrożyć i przetestować aplikację przykładową na fizycznym urządzeniu z systemem iOS (telefonie iPhone lub tablecie iPad), a nie w symulatorze.

Wykonanie czynności opisanych w tym samouczku jest wymaganiem wstępnym dla wszystkich innych samouczków usług Notification Hubs dotyczących aplikacji platformy Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurowanie centrum powiadomień dla powiadomień push systemu iOS
W tej sekcji opisano kroki tworzenia nowego centrum powiadomień i konfigurowania uwierzytelniania w usłudze APNs przy użyciu utworzonego wcześniej certyfikatu powiadomień wypychanych **p12**. Jeśli chcesz użyć już utworzonego centrum powiadomień, możesz przejść do kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>Konfigurowanie ustawień systemu iOS dla centrum powiadomień
1. Wybierz pozycję **Apple (APNS)** w grupie **USTAWIENIA POWIADOMIEŃ**. 
2. Wybierz pozycję **Certyfikat**, kliknij ikonę **pliku** i wybierz wyeksportowany wcześniej plik **p12**. 
3. Określ **hasło** certyfikatu. 
4. Wybierz tryb **Piaskownica**. Trybu **Produkcja** używaj wyłącznie wtedy, gdy chcesz wysyłać powiadomienia push do użytkowników, którzy kupili Twoją aplikację w sklepie.

    ![Konfigurowanie usługi APNs w witrynie Azure Portal][6]

    ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal][7]

Twoje centrum powiadomień jest teraz skonfigurowane do pracy z usługą APNs i uzyskano parametry połączenia służące do rejestrowania aplikacji w celu wysyłania powiadomień wypychanych.

## <a name="connect-your-app-to-the-notification-hub"></a>Łączenie aplikacji z centrum powiadomień
#### <a name="create-a-new-project"></a>Tworzenie nowego projektu
1. W programie Visual Studio utwórz nowy projekt dla systemu iOS i wybierz szablon **Single View Application** (Aplikacja z jednym widokiem), a następnie kliknij przycisk **Next** (Dalej).
   
     ![Visual Studio — wybieranie typu aplikacji][31]

2. Wprowadź nazwę aplikacji i identyfikator organizacji, kliknij przycisk **Dalej**, a następnie pozycję **Utwórz**

3. W widoku rozwiązania kliknij dwukrotnie plik *Info.plist* i w obszarze **Tożsamość** upewnij się, że identyfikator pakietu jest zgodny z użytym podczas tworzenia profilu aprowizacji. W obszarze **Podpisywanie** upewnij się, że konto dewelopera jest wybrane w obszarze **Zespół**, opcja „Automatically manage signing” (Automatycznie zarządzaj podpisywaniem) jest zaznaczona, a Twój certyfikat podpisywania i profil aprowizacji zostały automatycznie wybrane.

    ![Visual Studio — konfiguracja aplikacji systemu iOS][32]

4. W widoku rozwiązania kliknij dwukrotnie plik *Entitlements.plist* i upewnij się, że zaznaczono opcję „Włącz powiadomienia wypychane”.

    ![Visual Studio — konfigurowanie uprawnień systemu iOS][33]

5. Dodaj pakiet składnika Azure Messaging. W widoku Solution kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **Dodawanie pakietów NuGet**. Wyszukaj pakiet **Xamarin.Azure.NotificationHubs.iOS** i dodaj go do projektu.

6. Dodaj nowy plik do klasy, nadaj mu nazwę **Constants.cs**, dodaj następujące zmienne i zastąp symbole zastępcze literału ciągu przy użyciu *nazwy centrum* i zanotowanej wcześniej wartości *DefaultListenSharedAccessSignature*.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ListenConnectionString = "<Azure connection string>";
        public const string NotificationHubName = "<Azure hub path>";
    ```

7. W pliku **AppDelegate.cs** dodaj następującą instrukcję using:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

8. Zadeklaruj wystąpienie **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

9.  W pliku **AppDelegate.cs** zaktualizuj metodę **FinishedLaunching()** zgodnie z poniższym kodem:
  
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
            {
                UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Sound,
                                                                      (granted, error) =>
                {
                    if (granted)
                        InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
                });
            } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

10. Zastąp metodę **RegisteredForRemoteNotifications()** w pliku **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

11. Zastąp metodę **ReceivedRemoteNotification()** w pliku **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

12. Utwórz następującą metodę **ProcessNotification()** w pliku **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Możesz również opcjonalnie zastąpić metodę **FailedToRegisterForRemoteNotifications()** w celu obsługi sytuacji takich jak brak połączenia z siecią. Jest to szczególnie istotne w przypadku, gdy użytkownik może uruchomić aplikację w trybie offline (na przykład w trybie samolotowym), a chcesz obsługiwać scenariusze powiadomień push specyficzne dla aplikacji.
  

13. Uruchom aplikację na urządzeniu.

## <a name="send-test-push-notifications"></a>Wysyłanie testowych powiadomień wypychanych
Odbieranie powiadomień w aplikacji możesz przetestować za pomocą opcji *Wysyłanie testowe* w witrynie [Azure Portal]. Powoduje to wysłanie testowego powiadomienia push na urządzenie.

![Witryna Azure Portal — wysyłanie testowe][30]

Powiadomienia wypychane są zwykle wysyłane za pośrednictwem usługi wewnętrznej bazy danych, takiej jak Mobile Apps czy ASP.NET, przy użyciu zgodnej biblioteki. Jeśli biblioteka nie jest dostępna w danym zapleczu, powiadomienia można również wysyłać bezpośrednio za pomocą interfejsu API REST.

## <a name="next-steps"></a>Następne kroki
W tym samouczku wysłano wyemitowane powiadomienia do wszystkich urządzeń z systemem iOS zarejestrowanych w zapleczu. Aby dowiedzieć się, jak wysyłać powiadomienia push do konkretnych urządzeń z systemem iOS, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Wysyłanie powiadomień push do konkretnych urządzeń](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio dla komputerów Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
