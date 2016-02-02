1. Visual Studio ソリューション エクスプローラーで、Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインし、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。

3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。 この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. Windows Store アプリ用に以前に作成したものと同じ登録を使用して、Windows Phone Store アプリ プロジェクトに対してステップ 1 と 3 を繰り返します。

7. 移動し、 [Windows デベロッパー センター](https://dev.windows.com/en-us/overview), 、Microsoft アカウントでサインインで新しいアプリケーションの登録] をクリックして **マイ アプリ**, の順に展開 **サービス** > **プッシュ通知**します。

8. **[プッシュ通知]** ページで、**[Microsoft Azure Mobile Services]** の下にある **[Live サービス サイト]** をクリックします。

9. **[アプリ設定]** タブで、**[クライアント シークレット]** と **[パッケージ SID]** の値をメモしておきます。

    ![デベロッパー センターでのアプリの設定](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)
    > [AZURE.IMPORTANT] クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。 これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。




