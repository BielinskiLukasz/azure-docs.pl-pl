<properties 
    pageTitle="App Services アプリケーションに Azure Active Directory 認証を構成する方法" 
    description="App Services アプリケーションに Azure Active Directory 認証を構成する方法について説明します。" 
    authors="mattchenderson" 
    services="app-service\mobile" 
    documentationCenter="" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/20/2015" 
    ms.author="mahender"/>


# Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Azure Active Directory を使用するように Azure App Services を構成する方法を示します。
> [AZURE.NOTE] このトピックでは、App Service 認証/承認の使用例を示します。 ほとんどのアプリケーションで、これが App Service ゲートウェイに取って代わっています。 ゲートウェイを使用する場合は、[代替の方法] を参照してください。 ゲートウェイを使用する場合との相違点については、そのセクションの注に記載されています。


## <a name="express"> </a>Configure Azure Active Directory 簡単設定を使用します。

13. [Azure ポータル、]、アプリケーションに移動します。 **[設定]**、**[認証/承認]** の順にクリックします。

14. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

15. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[高速]** をクリックします。

16. **[OK]** をクリックして、Azure Active Directory にアプリケーションを登録します。 これで新しい登録が作成されます。 代わりに既存の登録を選択する場合は、**[既存のアプリケーションの選択]** をクリックし、テナント内で以前に作成した登録の名前を検索します。 登録をクリックして選択し、**[OK]** をクリックします。 Azure Active Directory 設定ブレードで **[OK]** をクリックします。

    ![][0]

    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されていないときに実行するアクション]** を **[Azure Active Directory]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

17. **[保存]** をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="advanced"> </a>(代替方法) の詳細設定で、Azure Active Directory を手動で構成します。

構成設定を手動で行うこともできます。 これは、使用する AAD テナントが Azure へのサインインに使用するテナントと異なる場合に推奨されるソリューションです。 構成を完了するには、まず Azure Active Directory で登録を作成し、登録の一部の詳細を App Service に提供する必要があります。

### <a name="register"> </a>Azure Active Directory にアプリケーションを登録

1. [Azure ポータル] にログオンし、アプリケーションに移動します。 **[URL]** をコピーします。 これを使用して、Azure Active Directory アプリケーションを構成します。

3. [Azure クラシック ポータル] にサインインしに移動 **Active Directory**します。

    ![][2]

4. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。 下部の **[追加]** をクリックして、新しいアプリの登録を作成します。

5. **[組織で開発中のアプリケーションを追加]** をクリックします。

6. アプリケーションの追加ウィザードで入力、 **名** ] をクリックしてアプリケーションを  **Web アプリケーションや Web API** 型です。 その後、クリックして続行します。

7. **[サインオン URL]** ボックスに、先ほどコピーしたアプリケーション URL を貼り付けます。 **[アプリケーション ID URI]** ボックスにも同じ URL を入力します。 その後、クリックして続行します。

8. アプリケーションが追加されたら **[構成]** タブをクリックします。 **[シングル サインオン]** の **[応答 URL]** を編集して、アプリケーションの URL の末尾にパス _/.auth/login/aad/callback_ を追加します。 たとえば、 `https://contoso.azurewebsites.net/.auth/login/aad/callback`します。 HTTPS スキームを使用していることを確認します。

    ![][3]

    > [AZURE.NOTE]
    App Service の [認証/承認] 機能ではなく、App Service ゲートウェイを使用している場合は、応答 URL でパス _/signin-aad_ を含むゲートウェイ URL を代わりに使用します。

9. **[保存]** をクリックします。 その後、アプリケーションの**クライアント ID** をコピーします。 後で、これを使用するようにアプリケーションを構成します。

10. 下部のコマンド バーで、**[エンドポイントの表示]** をクリックして、**[フェデレーション メタデータ ドキュメント]** の URL をコピーし、そのドキュメントをダウンロードするか、ブラウザーでそのドキュメントに移動します。

11. ルート内 **EntityDescriptor** 要素、必要があります、 **entityID** フォームの属性 `https://sts.windows.net/` ("テナント ID"と呼ばれます)、テナントに書き込み、続けて、GUID を特定します。 この値をコピーします。これは**発行者の URL** として機能します。 後で、これを使用するようにアプリケーションを構成します。

### <a name="secrets"> </a>をアプリケーションに Azure Active Directory の追加情報

> [AZURE.NOTE]
App Service ゲートウェイを使用する場合は、このセクションの内容は無視し、ポータル内で目的のゲートウェイに移動します。 **[設定]**、**[ID]**、**[Azure Active Directory]** の順に選択します。 ClientID に貼り付け、テナント ID を **[許可されているテナント]** リストに追加します。 **[保存]** をクリックします。


13. ポータルに戻り、[Azure] には、アプリケーションに移動します。 **[設定]**、**[認証/承認]** の順にクリックします。

14. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

15. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[詳細設定]** をクリックします。 以前に取得したクライアント ID と発行者の URL の値を貼り付けます。 次に、 **[OK]** をクリックします

    ![][1]

    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されていないときに実行するアクション]** を **[Azure Active Directory]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。

17. **[保存]** をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## (省略可能) ネイティブ クライアント アプリケーションの構成

Azure Active Directory では、ネイティブ クライアントを登録して、アクセス許可のマッピングをさらに詳細に制御することもできます。 **Active Directory Authentication Library** などのライブラリを使用してログインを実行する場合は、この処理が必要です。

1. 移動 **Active Directory** [Azure クラシック ポータル] にします。

2. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。 下部の **[追加]** をクリックして、新しいアプリの登録を作成します。

3. **[組織で開発中のアプリケーションを追加]** をクリックします。

4. アプリケーションの追加ウィザードで入力、 **名** ] をクリックしてアプリケーションを  **ネイティブ クライアント アプリケーション** 型です。 その後、クリックして続行します。

5. **[リダイレクト URI]** に、HTTPS スキームを使用してサイトの _/.auth/login/done_ エンドポイントを入力します。 この値はのような _https://contoso.azurewebsites.net/.auth/login/done_します。

6. ネイティブ アプリケーションが追加されたら **[構成]** タブをクリックします。 **[クライアント ID]** を検索し、その値をメモします。

7. ページを **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまで下へスクロールし、**[アプリケーションの追加]** ボタンをクリックします。

8. 前に登録した Web アプリケーションを検索し、プラス アイコンをクリックします。 次に、チェックをクリックしてダイアログ ボックスを閉じます。

9. 追加したばかりの新しいエントリで、**[デリゲートされたアクセス許可]** ドロップダウンを開き、**[<アプリ名> にアクセス]** を選択します。 その後、**[保存]** をクリックします。

以上の手順で、App Service アプリケーションにアクセスできるネイティブ クライアント アプリケーションが構成されます。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]






[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png 
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png 
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png 
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png 
[azure portal]: https://portal.azure.com/ 
[azure classic portal]: https://manage.windowsazure.com/ 
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md 
[alternative method]: #advanced 

