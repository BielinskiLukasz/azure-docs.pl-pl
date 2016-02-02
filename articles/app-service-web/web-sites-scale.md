<properties 
    pageTitle="Azure App Service の Web アプリをスケーリングする" 
    description="Azure App Service での、自動スケールを含む Web アプリのスケール アップとスケール ダウンの方法について説明します。" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# Azure App Service の Web アプリをスケーリングする

パフォーマンスの向上と Microsoft Azure での web アプリのスループットは、使用することができます、 [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715) スケール、 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) から計画 **Free** モードを **共有**, 、**基本的な**, 、**標準**, 、または **Premium** モードです。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Azure で Web アプリをスケール アップするには、2 つの操作が必要です。App Service プランのモードを上位レベルに変更することと、上位レベルのサービスに切り替えた後、特定の設定を構成することです。 この記事では、この 2 つのトピックについて説明します。 **Standard** モードや **Premium** モードのようにサービス レベルを高くすると、Azure 上のリソースの使用方法を決定する際の堅牢性と柔軟性が向上します。

スケール設定は適用に数秒を要するのみで、App Service プランに含まれるすべての Web アプリに反映されます。 コードの変更やアプリケーションの再デプロイは必要ありません。

App Service プランについては、を参照してください [App Service プランは何ですか?](../web-sites-web-hosting-plan-overview.md)。 と [Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)します。 料金情報や App Service プランの 1 つの機能では、次を参照してください。 [App Service の料金詳細](/pricing/details/web-sites/)します。
> [AZURE.NOTE] Web アプリを **Free** モードから **Basic**、**Standard**、または **Premium** モードに切り替える前に、Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。 Microsoft Azure App Service サブスクリプションのオプションを表示または変更、次を参照してください。 [Microsoft Azure サブスクリプションの ][azuresubscriptions]します。

<a name="scalingsharedorbasic"></a>

## 共有または基本モードへの規模の変更

1. ブラウザーで開く、 [Azure ポータルの ][portal]します。

2. Web アプリのブレードで、**[すべての設定]**、**[スケール]** の順にクリックし、**インスタンスを追加してパフォーマンスを向上させるため、無料プランからアップグレードする**ボタンをクリックします。

    ![プランの選択][choosewhp]

4. **[価格レベルの選択]** ブレードで、**Shared** または **Basic** モードを選択して、**[選択]** をクリックします。

    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

5. **[インスタンス]** バーを左から右へスライドしてインスタンス数を増やし、コマンド バーの **[保存]** をクリックします。 **Shared** モードでは、インスタンス サイズのオプションは利用できません。 これらのインスタンス サイズの詳細については、次を参照してください。 [仮想マシンおよびクラウド サービスの Microsoft Azure ][vmsizes]します。

    ![基本モードのインスタンス サイズ][choosebasicinstances]

    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

<a name="scalingstandard"></a>

## 標準またはプレミアム モードへの規模の変更

> [AZURE.NOTE] App Service プランを **Standard** または **Premium** モードに切り替える前に、Microsoft Azure App Service のサブスクリプションに設定されている使用制限を解除する必要があります。 解除しないと、請求期間が終了する前に使用制限に達した場合に、Web アプリが使用できなくなるおそれがあります。 Microsoft Azure App Service サブスクリプションのオプションを表示または変更、次を参照してください。 [Microsoft Azure サブスクリプションの ][azuresubscriptions]します。

1. **Standar** または **Premium** モードに拡張するには、**Shared** または **Basic** モードに拡張するときと同じ初期手順を実行し、その後、**[価格レベルを選択]** で **Standard** または **Premium** モードを選択して、**[選択]** をクリックします。

    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅し、**自動スケール モード**が有効になります。

    ![Standard または Premium モードへの拡張][scalestandard]

    この場合も、上の **Basic** モードの場合と同様に、**インスタンス** バーをスライドして手動でインスタンス数を増やすことができます。 ただし、ここでは**自動スケール モード**の使用方法について説明します。

2. **[自動スケール モード]** で、**[パフォーマンス]** を選択し、パフォーマンス メトリックに基づいた自動スケールを設定します。

    ![自動スケール モードがパフォーマンスに設定されています][autoscale]

3. **[インスタンス範囲]** で 2 つのスライダーを移動して、App Service 用に自動スケールを設定するインスタンスの最小数と最大数を定義します。 このチュートリアルでは、最大のスライダーを **6** インスタンスに移動します。

4. コマンド バーの **[保存]** をクリックします。

4. **[ターゲット メトリック]** で **[>]** をクリックして、既定のメトリックの自動スケール規則を構成します。

    ![ターゲット メトリックの設定][settargetmetrics]

    CPU、メモリ、ディスク キュー、HTTP キュー、データ フローなど、さまざまなパフォーマンス メトリックの自動スケール規則を構成することができます。 ここでは、次のように CPU の割合の自動スケールを構成します。

    - 過去 10 分間の CPU が 70% を超える場合は、インスタンスを 1 増やす
    - 過去 5 分間の CPU が 90% を超える場合は、インスタンスを 3 増やす
    - 過去 30 分間の CPU が 50% 未満の場合は、インスタンスを 1 減らす

4. **[メトリック]** ドロップダウンは **[CPU の割合]** のままにしておきます。

5. **[スケール アップ規則]** で、**[条件]** を **[より大きい]**、**[しきい値]** を **70** (%)、**[過去の持続時間]** を **10** (分)、**[スケール アップ設定]** を **1** (インスタンス)、**[クール ダウン]** を **10** (分) に設定して、最初の規則を構成します。

    ![1 つ目の自動スケール ルールの設定][setfirstrule]
    >[AZURE.NOTE] **[クール ダウン]** の設定は、直前のスケール操作が実行された後、再度スケール操作を実行するまでに、この規則が待機する時間の長さを指定します。

6. **[スケール アップ規則の追加]** をクリックし、**[条件]** を **[より大きい]**、**[しきい値]** を **90** (%)、**[過去の持続時間]** を **1** (分)、**[スケール アップ設定]** を **3** (インスタンス)、**[クール ダウン]** を **1** (分) に設定して、2 番目の規則を構成します。

    ![2 つ目の自動スケール ルールの設定][setsecondrule]

5. **[スケール ダウン規則]** で、**[条件]** を **[より小さい]**、**[しきい値]** を **50** (%)、**[過去の持続時間]** を **30** (分)、**[スケール ダウン設定]** を **1** (インスタンス)、**[クール ダウン]** を **60** (分) に設定して、3 番目の規則を構成します。

    ![3 つ目の自動スケール ルールの設定][setthirdrule]

7. コマンド バーの **[保存]** をクリックします。 これで、自動スケール規則が **[スケール]** ブレードに反映されます。

    ![自動スケール ルール設定の結果][setrulesfinal]

<a name="ScalingSQLServer"></a>
## Web アプリに接続されている SQL Server データベースのスケール設定

(App Service のプラン モードにかかわらず) 1 つ以上の SQL Server データベースが Web アプリにリンクされている場合、ニーズに合わせてすばやくスケールを設定できます。

1. リンクされたデータベースのいずれかのスケーリングで、web アプリのブレードを開き、 [Azure ポータルの ][portal]します。 折りたたみ可能な **[Essentials]** ドロップダウンで、**[リソース グループ]** リンクをクリックします。 次に、リソース グループ ブレードの **[概要]** パートで、リンクされているデータベースのいずれかをクリックします。

    ![リンクされたデータベース][resourcegroup]

2. リンクされている SQL Database のブレードで **[価格レベル]** パートをクリックし、パフォーマンス要件に応じていずれかの価格レベルを選択して、**[選択]** をクリックします。

    ![SQL Database のスケーリング][scaledatabase]

3. また、geo レプリケーションを設定して、SQL Database の高可用性と障害復旧機能を向上させることもできます。 そのためには、**[Geo レプリケーション]** パートをクリックします。

    ![SQL Database の geo レプリケーションの設定][georeplication]

<a name="devfeatures"></a>
## 開発者機能

Web アプリのモードに応じて、次の開発者向け機能を使用できます。

### ビット

- **Basic**、**Standard**、および **Premium** モードでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
- **Free** プラン モードと **Shared** プラン モードでは、32 ビットのアプリケーションのみがサポートされます。

### デバッガー サポート

- **Free**、**Shared**、および **Basic** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 1 です。
- **Standard** および **Premium** モードでは、デバッガー サポートを利用する場合、App Service プランあたりの同時接続数は 5 です。

<a name="OtherFeatures"></a>
## その他の機能

### Web エンドポイントの監視

- Web エンドポイントの監視機能は、**Basic**、**Standard**、および **Premium** モードで利用できます。 Web エンドポイントの監視の詳細については、次を参照してください。 [Web アプリの監視方法](web-sites-monitor.md)します。

- すべての App Service で、その他の機能の詳細については料金など、プランとすべてのユーザー (開発者を含む)、関心のある機能を参照してください [App Service の料金詳細](/pricing/details/web-sites/)します。

>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="Next Steps"></a>
## 次のステップ

- Azure で開始するを参照してください。 [Microsoft Azure の無料試用版](/pricing/free-trial/)します。
- 料金、サポート、および SLA については、次のリンクを参照してください。

    [データ転送の料金詳細](/pricing/details/data-transfers/)

    [Microsoft Azure サポート プラン](/support/plans/)

    [サービス レベル アグリーメント](/support/legal/sla/)

    [SQL Database の料金詳細](/pricing/details/sql-database/)

    [仮想マシンおよびクラウド サービスの Microsoft Azure ][vmsizes]

    [App Service の料金詳細](/pricing/details/web-sites/)

    [App Service の料金詳細 - SSL 接続](/pricing/details/web-sites/#ssl-connections)

- Azure App Service で拡張性と回復力のあるアーキテクチャの構築などのベスト プラクティスについては [ベスト プラクティス: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)します。

- Web Apps の拡張に関するビデオ:

    - [タイミング - Stefan Schackow 共演のビデオは、Azure の web サイトの規模の設定](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [自動スケール Azure Websites、CPU またはスケジュール済み - Stefan Schackow 共演](/documentation/videos/auto-scaling-azure-web-sites/)
    - [Azure の web サイト スケール - Stefan Schackow 共演](/documentation/videos/how-azure-web-sites-scale/)

## 変更内容

* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)




[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169 
[sqlaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930 
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288 
[portal]: https://portal.azure.com/ 
[choosewhp]: ./media/web-sites-scale/scale1ChooseWHP.png 
[choosebasicinstances]: ./media/web-sites-scale/scale2InstancesBasic.png 
[savebutton]: ./media/web-sites-scale/05SaveButton.png 
[basiccomplete]: ./media/web-sites-scale/06BasicComplete.png 
[scalestandard]: ./media/web-sites-scale/scale3InstancesStandard.png 
[autoscale]: ./media/web-sites-scale/scale4AutoScale.png 
[settargetmetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png 
[setfirstrule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png 
[setsecondrule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png 
[setthirdrule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png 
[setrulesfinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png 
[resourcegroup]: ./media/web-sites-scale/scale10ResourceGroup.png 
[scaledatabase]: ./media/web-sites-scale/scale11SQLScale.png 
[georeplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png 

