<properties
    pageTitle="Microsoft 認証用の登録 | Microsoft Azure"
    description="Azure Mobile Services アプリケーションで Microsoft 認証用に登録する方法について説明します。"
    authors="ggailey777"
    services="mobile-services"
    documentationCenter="Mobile"
    manager="dwrede"
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="glenga"/>


# 認証で Microsoft アカウントを使用するためのアプリケーションの登録

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## 概要

このトピックでは、Azure Mobile Services の ID プロバイダーとして Microsoft アカウントを使用できるようにモバイル アプリを登録する方法について説明します。 Live SDK を使用するサービス主導型認証とクライアント主導型認証の両方に同じ手順が適用されます。

## Windows デベロッパー センターで Windows ストア アプリを登録する

Windows ストア アプリを最初に Windows デベロッパー センターで登録する必要があります。
>[AZURE.NOTE]Windows Phone 8、Windows Phone 8.1 Silverlight、および Windows 以外のアプリは、このセクションをスキップできます。

1. アプリを登録済みでない場合に移動、 [Windows デベロッパー センター](https://dev.windows.com/dashboard/Application/New), を Microsoft アカウントでログオン] をクリックし、アプリの名前を [ **アプリ名の予約**します。

3. Visual Studio で Windows アプリ プロジェクトを開き、ソリューション エクスプローラーで Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

5. ウィザードで、**[サインイン]** をクリックしてから Microsoft アカウントでサインインし、予約したアプリケーションを選択して **[次へ]**、**[関連付け]** の順にクリックします。

6. (省略可能) ユニバーサル Windows 8.1 アプリケーションの場合、Windows Phone ストア プロジェクトの手順 4 および 5 を繰り返します。

6. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]**、**[プッシュ通知]** の順にクリックします。

7. **[プッシュ通知]** ページで、**[Windows プッシュ通知サービス (WNS) と Microsoft Azure Mobile Services]** の下にある **[ライブ サービス サイト]** をクリックします。

これにより、アプリの Microsoft アカウント ページが表示されます。 次に、アプリで Microsoft 認証を使用するために Azure が必要とする認証資格情報を取得します。

## Microsoft アカウントの登録の構成と Mobile Services への接続

このセクションの最初の手順は、Windows Phone 8、Windows Phone 8.1 Silverlight、および Windows 以外のストア アプリにのみ適用されます。 これらのアプリの場合、Windows ストア アプリでだけ使用可能なパッケージ セキュリティ ID (SID) も無視できます。

1. 移動し、Windows ストア アプリ、 [マイ アプリケーション](http://go.microsoft.com/fwlink/p/?LinkId=262039) (必要な場合)、Microsoft アカウント デベロッパー センター、Microsoft アカウントでログオン] ページで [ **アプリケーションを作成**, 、型、 **アプリケーション名**, 、順にクリックして **同意**します。

    これにより Microsoft アカウントでアプリ名が確保され、アプリの Microsoft アカウント ページが表示されます。

2. アプリの Microsoft アカウント ページで、**[API 設定]** をクリックして **[モバイル アプリまたはデスクトップ クライアント アプリ]** を有効にし、モバイル サービス URL を **[ターゲット ドメイン]** として設定して **[リダイレクト URL]** に次の URL 形式のいずれかを指定し、**[保存]** をクリックします。

    + **.NET バックエンド**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
    + **JavaScript バックエンド**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`
     >[AZURE.NOTE]Mobile Services バックエンドの種類として、正しいリダイレクト URL パスの形式を使用してください。 これが正しくない場合、認証は失敗します。  **ルート ドメイン** 自動的に入力する必要があります。
&nbsp;

    ![Microsoft アカウント API の設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)

4. **[アプリ設定]** をクリックして、**[クライアント ID]**、**[クライアント シークレット]**、**[パッケージ SID]** の値を書き留めます。

    ![Microsoft アカウント アプリの設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] クライアント シークレットは、重要なセキュリティ資格情報です。 クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。 Windows ストア アプリの登録でのみ、パッケージ SID フィールドが表示されます。

4. [Azure クラシック ポータル]、をクリックして、 **Identity** 、パッケージ SID のタブに移動して、モバイル サービスをクライアント ID、クライアント シークレットを入力、id プロバイダーから取得した順にクリックして **保存**します。
    >[AZURE.NOTE]Windows Phone 8、Windows Phone Store 8.1 Silverlight、または Windows 以外のアプリの場合は、パッケージ SID を指定する必要はありません。

これで、Microsoft アカウントと連携するようにモバイル サービスとアプリケーションが構成されました。








[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[azure classic portal]: https://manage.windowsazure.com/ 

