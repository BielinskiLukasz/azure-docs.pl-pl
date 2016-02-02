<properties
    pageTitle="PowerApps Enterprise への SharePoint Server API の追加 | Microsoft Azure"
    description="組織の App Service 環境での新しい SharePoint Server API の作成または構成"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="rajram"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>


# 組織の App Service 環境での新しい SharePoint Server API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/), 、仕事用アカウントでサインインします。 たとえばでサインイン *yourUserName*@*yourcompany ' と*. com です。 これにより、会社のサブスクリプションに自動的にサインインされます。

2. 選択 **参照** タスク バーで。  
![][14]

3. 一覧で、PowerApps を検索または入力するスクロールできる *powerapps*:  
![][15]

4. **PowerApps**, [ **管理 Api**:  
![登録されている API の参照][5]

5. **管理 Api**, [ **追加** 新しい API を追加します。  
![Add API][6]

6. API のわかりやすい**名前**を入力します。
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[SharePoint Server]** を選択します。
8. **[設定] - [必要な設定の構成]** を選択します。
9. SharePoint Server Azure Active Directory (AAD アプリ) の*クライアント ID* と*アプリ キー*、および AAD プロキシ アプリの *SharePoint URL* と*リソース ID* を入力します。 次のセクションで説明されている手順に従い、オンプレミスの SharePoint Server への接続を構成します。
    > [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。 この値は、このトピックで後ほど必要になる場合があります。  

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい SharePoint Server API が追加されます。


## オンプレミスの SharePoint Server への接続の構成

SharePoint Server は、ユーザー認証に Active Directory を使用します。 アプリがサービスを提供する環境での API は、Azure Active Directory (AAD) を使用して認証されます。 ユーザーの AAD トークンを交換し、AD のトークンに変換する必要があります。 その後、この AD トークンを使用してオンプレミスのサービスに接続できます。

[Azure アプリケーション プロキシ (AAD プロキシ)](../active-directory-application-proxy-publish.md) この要件のために使用します。 これは GA における Azure サービスであり、オンプレミスの Web アプリケーションに対するリモート アクセスと SSO をセキュリティで保護します。 AAD プロキシを有効にする手順は、MSDN で詳しく解説されています。 手順の概要を次に示します。

1. [アプリケーション プロキシ サービスを有効にする](../active-directory-application-proxy-enable.md) – これが含まれます。

    - Azure AD でアプリケーション プロキシを有効化にします
    - Azure アプリケーション プロキシ コネクタをインストールして登録します

2. [アプリケーション プロキシによるアプリケーションの発行](../active-directory-application-proxy-publish.md) – これが含まれます。

    - ウィザードを使ってアプリケーション プロキシ アプリを発行します。 プロキシ アプリを作成した後、イントラネットの SharePoint サイトの外部 URL を記録しておきます。
    - ユーザーとグループをアプリケーションに割り当てます。
    - Kerberos トークンをオンプレミスでフェッチするためにアプリケーション プロキシ コネクタによって使用される SPN (サービス プリンシパル名) など、高度な構成を入力します。

プロキシ アプリを作成した後は、プロキシ アプリケーションに委任するもう 1 つの AAD アプリを作成する必要があります。 これは、同意フローに必要なアクセス トークンと更新トークンを取得しするために必要です。 に従って、新しい AAD アプリケーションを作成することも [手順](../active-directory-integrating-applications.md)します。

## まとめと次のステップ

このトピックでは、Office 365 Users API を PowerApps Enterprise に追加しました。 次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザー アクセスを付与](powerapps-manage-api-connection-user-access.md)




[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx 
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx 
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx 
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG 
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG 
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png 
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png 

