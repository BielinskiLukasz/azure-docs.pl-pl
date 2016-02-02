<properties
   pageTitle="Azure RemoteApp を使用して、どのデバイスでも同じ Office 365 のエクスペリエンスを得るには | Microsoft Azure"
   description="Azure RemoteApp を使用して、ユーザーと任意の Office 365 アプリを共有する方法について説明します。"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="12/05/2015"
   ms.author="guscatal;elizapo"/>



# Azure RemoteApp を使用して、どのデバイスでも同じ Office 365 のエクスペリエンスを得るには

この記事では、会社内の任意のデバイスで Office 365 をデプロイする方法を説明します。 ユーザーには、Android、Apple、Windows のいずれからでも、同じ機能と UI 操作が提供されます。

これは、Azure RemoteApp を使用し、ユーザーが接続できる Azure のスケーラブルな仮想マシンで Office 365 をホストすることにより実現されます。 この仮想マシンのセットを "クラウド コレクション" と呼びます。

## Create a cloud collection (クラウド コレクションを作成する)

Azure アカウントを作成した後に初めて移動 **RemoteApp** 左側にあるリンクをクリックしています。
![Azure ポータルでの Azure RemoteApp の表示](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

次に、下部にある **[新規]** をクリックし、コレクションを "簡易作成" します。 名前、リージョン、サブスクリプション、プランとイメージを指定"Office Proffesional 2013」に提供されています。
![[作成] ダイアログ](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

フォームを完了すると、コレクションの作成プロセスが開始されます。 これには 1 時間ほどかかる場合があります。

![待機中](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

プロセスが完了すると、以下のようになります。 クリックして、場合 **発行** うえでほとんどの Office アプリケーションを既に発行されていることがわかります。
![作成されたコレクション](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![発行されたアプリ](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

この時点で追加することも多くのユーザーをクリックしてこのコレクションにアクセスできる **ユーザー アクセス**します。
![ユーザー アクセスの構成](./media/remoteapp-tutorial-o365anywhere/6-user.png)

それでは、Office 365 への接続を試してみましょう。

## Office 365 への接続

進んでします [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), 、スクロール ダウンして、をクリックして **クライアントのダウンロード** すぐにデバイスに Azure RemoteApp クライアントをインストールします。 次のスクリーンショットは、Windows の場合です。

アプリケーションが開始されると、Microsoft アカウント (以前の "Live ID") でサインインするように求められます。ここでは、Azure アカウントと同じアカウントを使用してください。 サインインすると、新しい招待に関する通知が表示されます。これをクリックすると、次のようなリストが表示されます。 Azure アカウント所有者の電子メールと一致する招待を承諾してください。

![新しい招待](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

新しい招待がある場合、このように表示されます。

![アプリケーションの承諾](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

招待を承諾すると、Azure RemoteApp クライアントにあるすべての Office アプリが表示されます。

![アプリのリスト](./media/remoteapp-tutorial-o365anywhere/9-work.png)

そのいずれかをクリックすると、アプリケーションが Azure 仮想マシンで開始されます。これですべて完了です。 機能を有効にご活用ください。

![開始中](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)





