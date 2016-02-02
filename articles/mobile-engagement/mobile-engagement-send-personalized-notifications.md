<properties 
    pageTitle="Azure Mobile Engagement で個人用に設定された通知を送信する" 
    description="名前などのユーザー プロファイル情報を通知に含めることで個人用に設定した通知を送信する方法"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="piyushjo" />


# ユーザー名を追加し、通知を個人用に設定する

アプリ ユーザーに通知にもっと注目してもらうには、通知を個人に合わせて設定します。 たとえば、アプリ ユーザーの名前を選択し、個々のユーザーに合わせて通知をカスタマイズすると非常に効果的です。 ここで注意が必要です。この方法を使いすぎると、一部のユーザーにとって気味が悪く感じられる可能性があるため、ユーザーの名前を通知に追加する方法は慎重に利用する必要があります。 また、この方法を利用する前に、モバイル アプリで個人情報の提供に関して完全な同意を与えるかどうかをユーザーが選択できるようにします。

技術的には、Azure Mobile Engagement を利用し、以下の手順で通知をカスタマイズします。ユーザー名を通知に追加するシナリオが利用されています。 アプリ情報またはタグの概念が利用されます。その値はモバイル アプリに統合されている SDK か API で渡されます。 これらのアプリ情報またはタグは次のように利用できます。

1. アプリ情報の値に基づき、通知の対象を特定のユーザーにするために
2. 通知内でデバイス/ユーザーに固有の値で置換されるプレースホルダーとして (そのデバイスに通知が送信されます)

> [AZURE.IMPORTANT] アプリ情報値を各通知で置換するというこの追加処理に起因し、通知の送信速度が落ちることに注意してください。 

## Mobile Engagement ポータルでアプリ情報を登録する

1) 最初に Azure ポータルでアプリ情報またはタグを登録します。 そのために **[設定]** -> **[タグ (アプリ情報)]** に進みます。

![][1]

2) **[新しいタグ (アプリ情報)]** をクリックし、「*user_name*」と名付け、種類を「*文字列*」に指定し、**[送信]** をクリックします。

![][2]

3) 最後に、この登録されたアプリ情報が次のように表示されます。

![][3]

## クライアント SDK からアプリ情報を送信する

ここでは Windows ユニバーサル アプリの例を使用しますが、Microsoft の他の SDK にもこれに相当する手法があります。

場所情報を取得する、プロファイルの名前などのユーザーから可能性がありますそれらを認証した後、モバイル アプリでメソッドがあると仮定した場合を呼び出す `SendAppInfo` ここでメソッドの値の設定と、 `user_name` 以前に登録したモバイル エンゲージメント サービスのバックエンドにアプリ情報。

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## 個人用に設定した通知を送信する

この **user_name** を利用し、通知を送信する用意ができました。

1) Mobile Engagement ポータルに進みます。**[リーチ]** タブで通知を作成できます。このプレースホルダーは、通知タイトルまたは本文のどこでも、次の形式で使用できます。

![][4]
> [AZURE.NOTE] user_name アプリ情報が設定されていないユーザーは通知を受け取りません。 

2) Mobile Engagement がこの通知を送信するデバイスを選択するとき、このアプリ情報を見て、プレースホルダーの値を置換します。  
例では、設定した場合の `str ="Scott"` デバイスとユーザーの登録がのアプリ情報に関連付けられたに取得 **user_name = SCOTT** このユーザーと、このユーザーを次の形式でのアプリのプッシュ通知を参照してください。

![][5]



[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png 
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png 
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png 
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png 
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png 

