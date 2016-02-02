<properties 
    pageTitle="Azure Mobile Engagement 内での絵文字の使用" 
    description="プッシュ通知内で絵文字を使用する方法"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/22/2015" 
    ms.author="piyushjo" />


# プッシュ通知内での絵文字の使用

絵文字を含めることができます、いくつかの簡単なステップでのプッシュ通知に。

1. まず、メッセージで送信する絵文字を検索する必要があります。 デバイス メーカーが、新たに承認された絵文字をデバイス プラットフォームに追加するのに時間がかかるので、選択しようとしている絵文字が対象のデバイスでサポートされることを確認してください。

2. **Windows** -に移動できます [リンク](http://apps.timwhitlock.info/emoji/tables/unicode) 'Native' アイコンをコピーします。

    ![][7]

3. **Mac** の場合- 絵文字は、Dictionary アプリケーションの [Edit] -> [Emoji & Symbols] にあります。

    ![][6]

4. 移動すれば、 **到達** ] タブで、Azure モバイル エンゲージメント ポータルです。 プッシュ通知の種類 (アナウンス、ポーリングなど) を選択します。 この例では、アナウンスのプッシュを選択します。

5. その通知のテキストに到達するまで、通知のさまざまなフィールドを指定していきます。 ここで絵文字を追加します。 タイトルまたはメッセージ、あるいはその両方に配置するかを選択できます。 前述の場所から見つけた絵文字をドラッグ アンド ドロップするか、コピーする必要があります。

    ![][1]

6. 通知の他のフィールドに入力し、保存します。

7. アナウンスをテストまたはアクティブ化すると、指定どおりの絵文字を含む通知が表示されます。

    ![][3] ![][4] ![][5]



[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png 
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png 
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png 
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png 
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png 
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png 

