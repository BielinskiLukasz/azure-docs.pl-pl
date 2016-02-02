
1. アプリを登録済みでない場合は、移動、[アプリ送信のページ] に Windows ストア アプリのデベロッパー センターでは、Microsoft アカウントでログオンし、をクリックして **アプリ名**します。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

    これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio で、チュートリアル「**モバイル サービスの使用**」を実行したときに作成した Windows ストア アプリ プロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. ステップ 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. (省略可能) 手順 4 ～ 6 を繰り返して、ユニバーサル Windows アプリの Windows Phone ストア プロジェクトも登録します。

8. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. [サービス] ページで **[Azure のモバイル サービス]** の **[Live サービス サイト]** をクリックします。

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. **[サービスの認証]** をクリックし、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)
    > [AZURE.NOTE] クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。 これらの機密情報は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。 

11. ログオン、 [Azure クラシック ポータル](https://manage.windowsazure.com/), 、] をクリックして **Mobile Services**, 、アプリケーションをクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. **[プッシュ]** タブをクリックし、ステップ 4. で WNS から取得した **[クライアント シークレット]** と **[パッケージ SID]** の値を入力して、**[保存]** をクリックします。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)
    >[AZURE.NOTE]ポータルの [**プッシュ**] タブで拡張プッシュ通知に対応する WNS の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が Notification Hubs と共有されます。



[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 

