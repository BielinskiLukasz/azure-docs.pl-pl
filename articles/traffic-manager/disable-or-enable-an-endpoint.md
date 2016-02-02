<properties
   pageTitle="Traffic Manager エンドポイントの無効化または有効化 | Microsoft Azure"
   description="この記事では、Traffic Manager プロファイル エンドポイントの無効化または有効化について説明します。"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />


# Traffic Manager エンドポイントの無効化または有効化

Traffic Manager プロファイルを構成する個々のエンドポイントを無効にすることもできます。 エンドポイントには、クラウド サービスと Web サイトの両方が含まれます。 無効にしたエンドポイントはその後もプロファイルの一部として残りますが、プロファイルは、エンドポイントが含まれていないかのように機能します。 この操作は、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントを一時的に削除するときに役立ちます。 エンドポイントが再度稼働状態になったら、有効にできます。
>[AZURE.NOTE] * * エンドポイントを無効化は Azure でのデプロイメント状態とは無関係です。 正常なエンドポイントは、Traffic Manager で無効になっていても、稼働状態のままでトラフィックを受信できます。 さらに、1 つのプロファイルでエンドポイントを無効にすると使用しない別 profile.* * の状態

## エンドポイントを無効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
1. ページの上部にある、**[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
1. 無効にするエンドポイントをクリックしてから、ページの下部にある **[無効化]** をクリックします。
1. Traffic Manager ドメイン名用に構成された DNS Time To Live (TTL) に基づいて、トラフィックはエンドポイントに送信されなくなります。 TTL は、Traffic Manager プロファイルの [構成] ページから変更できます。

## エンドポイントを有効にするには

1. Azure クラシック ポータルの [Traffic Manager] ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、プロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
1. ページの上部にある、**[エンドポイント]** をクリックして、構成に含まれるエンドポイントを表示します。
1. 有効にするエンドポイントをクリックしてから、ページの下部にある **[有効化]** をクリックします。
1. プロファイルに基づいて、サービスへのトラフィックの送信が再開されます。

## 次のステップ

[トラフィック マネージャー - 無効化、有効にするか、プロファイルを削除します。](disable-enable-or-delete-a-profile.md)

[Traffic Manager のトラブルシューティングに状態が低下しています](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)




