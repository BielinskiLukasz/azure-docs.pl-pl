<properties
   pageTitle="Traffic Manager のラウンド ロビンによるトラフィック ルーティング方法の構成 | Microsoft Azure"
   description="この記事では、Traffic Manager のエンドポイントにラウンド ロビン負荷分散を構成する方法について説明します。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />


# ラウンド ロビンによるトラフィック ルーティング方法の構成

トラフィック ルーティング方法の一般的なパターンは、クラウド サービスや Web サイトを含む同一のエンドポイントのセットを提供し、ラウンド ロビン方式で各エンドポイントにトラフィックを送信することです。 以下に、このようなトラフィック ルーティング方法を行うために Traffic Manager を構成する手順の概要を示します。 さまざまなトラフィック ルーティング方法の詳細については、次を参照してください。 [Traffic Manager に関するトラフィック ルーティング方法](traffic-manager-load-balancing-methods.md)します。
>[AZURE.NOTE] Azure Websites では、データセンター (リージョンとも呼ばれます) 内の Web サイトに対するラウンド ロビン負荷分散の機能が既に用意されています。 Traffic Manager を使用すると、異なるデータセンター内の Web サイトに対して、ラウンド ロビンによるトラフィック ルーティング方法を指定できます。

## 一連のエンドポイントにトラフィックをルーティングする (ラウンド ロビン方式)

1. Azure クラシック ポータルの左側のウィンドウで、**[Traffic Manager]** アイコンをクリックして [Traffic Manager] ウィンドウを開きます。 Traffic Manager プロファイルを作成がない場合は、次を参照してください。 [Traffic Manager プロファイルの管理](traffic-manager-manage-profiles.md) 、基本的な Traffic Manager プロファイルを作成する手順についてです。
2. Azure クラシック ポータルの Traffic Manager ウィンドウで、変更対象のエンドポイント設定が保存されている Traffic Manager プロファイルを見つけて、そのプロファイル名の右側にある矢印をクリックします。 これにより、プロファイルの設定ページが開きます。
3. プロファイルのページで、ページの上部にある **[エンドポイント]** をクリックし、構成に含めるサービス エンドポイントが存在することを確認します。 追加またはエンドポイントを削除する手順については、次を参照してください。 [Traffic Manager でのエンドポイントの管理](traffic-manager-endpoints.md)します。
4. プロファイル ページで、上部にある **[構成]** をクリックし、構成ページを開きます。
5. **[トラフィック ルーティング方法の設定]** で、トラフィック ルーティング方法が **[ラウンド ロビン]** に設定されていることを確認します。 他の方法に設定されている場合は、ドロップダウン リストで **[ラウンド ロビン]** をクリックします。
6. **[監視の設定]** が適切に構成されていることを確認します。 監視を構成することで、オフラインになっているエンドポイントにトラフィックが送信されなくなります。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。 監視の詳細については、次を参照してください。 [Traffic Manager の監視について](traffic-manager-monitoring.md)します。
7. 構成の変更が完了したら、ページの下部にある **[保存]** をクリックします。
8. 構成の変更をテストします。 詳細については、次を参照してください。 [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)します。
9. Traffic Manager プロファイルが設定されて機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。 これを行う方法の詳細については、次を参照してください。 [Traffic Manager ドメインを会社のインターネット ドメインをポイント](traffic-manager-point-internet-domain.md)します。

## 次のステップ

[Traffic Manager ドメインを会社のインターネット ドメインをポイントします。](traffic-manager-point-internet-domain.md)

[Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)

[フェールオーバーのルーティング方法を構成します。](traffic-manager-configure-failover-routing-method.md)

[パフォーマンスのルーティング方法を構成します。](traffic-manager-configure-performance-routing-method.md)

[Traffic Manager のトラブルシューティングに状態が低下しています](traffic-manager-troubleshooting-degraded.md)

[トラフィック マネージャー - 無効化、有効にするか、プロファイルを削除します。](disable-enable-or-delete-a-profile.md)

[トラフィック マネージャー - 無効または有効にするエンドポイント](disable-or-enable-an-endpoint.md)






