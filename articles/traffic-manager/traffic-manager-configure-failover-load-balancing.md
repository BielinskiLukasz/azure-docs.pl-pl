<properties 
   pageTitle="Traffic Manager のフェールオーバーによるトラフィック ルーティング方法の構成 | Microsoft Azure"
   description="この記事では、Traffic Manager でフェールオーバーによるトラフィック ルーティング方法を構成する方法について説明します。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="joaoma" />


# フェールオーバーのルーティング方法の構成

多くの場合、組織ではサービスの信頼性を維持する必要があります。 そのために、会社のプライマリ サービスがダウンした場合に備えてバックアップ サービスを提供します。 サービスのフェールオーバーの一般的なパターンでは、同一サービスのセットを用意し、プライマリ サービスにトラフィックを送信しながら、1 つ以上のバックアップ サービスで構成したリストを維持します。 この種類のバックアップは、以下の手順に従って、Azure Cloud Services および Azure Websites を使用して構成できます。

Azure Websites では、Web サイトのモードに関係なく、データセンター (「リージョン」と呼びます) 内の Web サイト用に、フェールオーバーによるトラフィック ルーティング方法があらかじめ用意されています。 Traffic Manager を使用すると、異なるデータセンター内の Web サイトに対して、フェールオーバーによるトラフィック ルーティング方法を指定できます。

## フェールオーバーによるトラフィック ルーティング方法の構成

1. Azure ポータルの左側のウィンドウで、**[Traffic Manager]** アイコンをクリックして [Traffic Manager] ウィンドウを開きます。 Traffic Manager プロファイルを作成がない場合は、次を参照してください。 [Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md) 、基本的な Traffic Manager プロファイルを作成する手順についてです。
2. Azure ポータルの Traffic Manager ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、そのプロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
3. プロファイル ページで、ページの上部にある **[エンドポイント]** をクリックし、構成に含めるクラウド サービスと Web サイトの両方 (エンドポイント) が存在することを確認します。 追加またはエンドポイントを削除する手順については、次を参照してください。 [Traffic Manager でのエンドポイントの管理](traffic-manager-endpoints.md)します。
4. プロファイル ページで、上部にある **[構成]** をクリックし、構成ページを開きます。
5. **[トラフィック ルーティング方法の設定]** で、トラフィック ルーティング方法が **[フェールオーバー]** に設定されていることを確認します。 他の方法に設定されている場合は、ドロップダウン リストから **[フェールオーバー]** をクリックします。
6. **[フェールオーバー優先度リスト]** で、エンドポイントのフェールオーバー順序を調整します。 **フェールオーバー**によるトラフィック ルーティング方法を選択する場合、選択したエンドポイントの順序が重要です。 最上位のエンドポイントがプライマリ エンドポイントになります。 上下の矢印を使用して、必要に応じて順序を変更します。 Windows PowerShell を使用してフェールオーバーの優先度を設定する方法については、次を参照してください。 [Set-azuretrafficmanagerprofile](http://go.microsoft.com/fwlink/p/?LinkId=400880)します。
7. **[監視の設定]** が適切に構成されていることを確認します。 監視を構成することで、オフラインになっているエンドポイントにトラフィックが送信されなくなります。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。 監視の詳細については、次を参照してください。 [Traffic Manager の監視](traffic-manager-monitoring.md)します。
8. 構成の変更が完了したら、ページの下部にある **[保存]** をクリックします。
9. 構成の変更をテストします。 参照してください [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md) の詳細。
10. Traffic Manager プロファイルが設定されて機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。 これを行う方法の詳細については、次を参照してください。 [Traffic Manager ドメインを会社のインターネット ドメインをポイント](traffic-manager-point-internet-domain.md)します。

## 次のステップ

[Traffic Manager のトラフィックのルーティング メソッド](traffic-manager-load-balancing-methods.md)

[Traffic Manager とは何ですか。](traffic-manager-overview.md)

[トラフィック マネージャー - 無効化、有効にするか、プロファイルを削除します。](disable-enable-or-delete-a-profile.md)

[トラフィック マネージャー - 無効または有効にするエンドポイント](disable-or-enable-an-endpoint.md)

[クラウド サービス](http://go.microsoft.com/fwlink/?LinkId=314074)

[Web サイト](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Traffic Manager (REST API リファレンス) の操作](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=400769)





