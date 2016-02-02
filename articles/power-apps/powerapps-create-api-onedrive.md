<properties
    pageTitle="PowerApps Enterprise への OneDrive API の追加 | Microsoft Azure"
    description="組織の App Service 環境で、新しい OneDrive API を作成または構成します。"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="rajeshramabathiran"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# 組織の App Service 環境での新しい OneDrive API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/), 、仕事用アカウントでサインインします。 たとえばでサインイン *yourUserName*@*yourcompany ' と*. com です。 これにより、会社のサブスクリプションに自動的にサインインされます。

2. 選択 **参照** タスク バーで。  
![][14]

3. 一覧で、PowerApps を検索または入力するスクロールできる *powerapps*:  
![][15]

4. **PowerApps**, [ **管理 Api**:    
![登録されている API の参照][1]

5. **管理 Api**, [ **追加** 新しい API を追加します。  
![Add API][2]

6. API のわかりやすい**名前**を入力します。

7. **ソース**, [ **利用可能な Api** 構築済みの Api を選択して [ **OneDrive**:  
![OneDrive API の選択][3]

8. 選択 **の設定 - 必要な設定を構成する**:  
![OneDrive API 設定の構成][4]

9. OneDrive アプリケーションの*アプリケーション キー*と*アプリケーション シークレット*を入力します。 これらがない場合は、このトピックの「PowerApps で使用する OneDrive アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。
    > [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。 この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい OneDrive API が追加されます。

## 省略可能: PowerApps で使用する OneDrive アプリケーションの登録

キーとシークレットの値が割り当てられた既存の OneDrive アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. 移動して、 [アプリの作成] ページ ][5] (_m) アカウント開発者 center_ で、[Account_ (_m) を使用してサインインします。

2. 入力、 **アプリケーション名**, を選択して **同意**:  
![新しい OneDrive アプリケーション][6]

3. 設定ページで次の操作を行います。

    a) 選択 **API 設定**します。  
    b) (」を参照)、Azure ポータルで新しい OneDrive API を追加したときに受信したリダイレクト URL にリダイレクト URL を設定します。  
    c) 選択 **保存**します。

    ![OneDrive アプリケーションの API 設定][7]

新しい OneDrive アプリケーションが作成されます。 Azure ポータルの OneDrive API 構成でこのアプリケーションを使用できます。

## まとめと次のステップ

このトピックでは、OneDrive API を PowerApps Enterprise に追加しました。 次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザー アクセスを付与](powerapps-manage-api-connection-user-access.md)



[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG 
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG 
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG 
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG 
[5]: https://account.live.com/developers/applications/create 
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG 
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG 
[14]: ./media/powerapps-create-api-onedrive/browseall.png 
[15]: ./media/powerapps-create-api-onedrive/allresources.png 

