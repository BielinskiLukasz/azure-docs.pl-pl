#### Xamarin Studio での iOS プロジェクトの構成

1. Xamarin.Studio で、**Info.plist** を開き、新しい App ID で前に作成した Bundle ID で **[Bundle Identifier]** を更新します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. **[Background Modes]** まで下へスクロールし、**[Enable Background Modes]** と **[Remote notifications]** の各チェック ボックスをオンにします。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. ソリューション パネルでプロジェクトをダブルクリックし、**[Project Options]** を開きます。

4.  **[Build]** で **[iOS Bundle Signing]** を選択し、対応する **ID** とこのプロジェクトに対して設定した**プロビジョニング プロファイル**を選択します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。 公式の Xamarin デバイス プロビジョニングのドキュメントについて、[Xamarin デバイス プロビジョニング] を参照してください。

#### Visual Studio での iOS プロジェクトの構成

1. Visual Studio で、プロジェクトを右クリックし、**[プロパティ]** をクリックします。

2. プロパティ ページで、**[iOS Application]** タブをクリックし、前に作成した ID で **[Identifier]** を更新します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. **[iOS Bundle Signing]** タブで、対応する **[Identity]** と、このプロジェクトに対して設定した **[Provisioning profile]** を選択します。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    これで、プロジェクトではコード署名のために新しいプロファイルを使用するようになります。 公式の Xamarin デバイス プロビジョニングのドキュメントについて、[Xamarin デバイス プロビジョニング] を参照してください。

4. Info.plist をダブルクリックして開き、 [バック グラウンド モード] の **RemoteNotifications** を有効にします。




[xamarin device provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/ 

