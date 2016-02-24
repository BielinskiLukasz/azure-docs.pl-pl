<properties 
    pageTitle="Azure WebJobs のドキュメント リソース" 
    description="Azure の Web ジョブと Azure Web ジョブ SDK を使用する方法を学習するための推奨リソース。" 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2015" 
    ms.author="tdykstra"/>

# Azure WebJobs のドキュメント リソース

## 概要

このトピックでは、Azure Web ジョブと Azure Web ジョブ SDK の使用方法に関するドキュメント リソースへのリンクを掲載しています。 Azure web ジョブでバック グラウンド プロセスとしてスクリプトやプログラムを実行する簡単な方法を提供する [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)します。 cmd、bat、exe (.NET)、ps1、sh、php、py、js、jar などの実行可能ファイルをアップロードして、実行できます。 これらのプログラムは、スケジュールに従って (cron)、または継続的に Web ジョブとして実行されます。

Web ジョブ SDK を使用すると、簡単に Azure Storage を使用できます。 Web ジョブ SDK には、Microsoft Azure Storage の BLOB、キュー、テーブルおよび Service Bus のキューと共に動作するバインドおよびトリガー システムがあります。

Web ジョブの作成、デプロイ、管理は、Visual Studio の統合ツールでシームレスに行うことができます。 テンプレートから Web ジョブを作成し、発行、および管理 (実行、停止、監視、デバッグ) できます。 

Azure ポータルの Web ジョブ ダッシュボードは、Web ジョブ内の個々の関数を呼び出す機能など、強力な管理機能を提供して Web ジョブの実行をきめ細かく制御できます。 また、ダッシュボードでは、関数の実行時間とログ出力も表示されます。 

##<a name="getstarted"></a>Web ジョブと Web ジョブ SDK の概要

* [Azure Web ジョブの紹介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure web ジョブはすばらしいと、今すぐ使用を開始する必要があります。](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html)(Troy Hunt によるブログの投稿です。)
* [Azure Web ジョブの機能](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Azure Web ジョブ SDK とは](websites-dotnet-webjobs-sdk.md)
* [Microsoft Patterns and Practices によるバック グラウンド ジョブのガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Announcing the 1.0.0 RTM of Microsoft Azure WebJobs SDK (Microsoft Azure Web ジョブ SDK の RTM の発表)](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Azure Web ジョブ SDK の使用](websites-dotnet-webjobs-sdk-get-started.md)
* [Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Web ジョブ SDK で Azure Blob Storage を使用する方法](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Web ジョブ SDK で Azure Service Bus を使用する方法](websites-dotnet-webjobs-sdk-service-bus.md)
* [Azure WebJobs SDK Quick Reference (Azure Web ジョブ SDK クイック リファレンス) (PDF ダウンロード)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [GitHub の web ジョブ設定に関するドキュメント](https://github.com/projectkudu/kudu/wiki/Web-jobs)します。
* ビデオ
    * [Web ジョブと Web ジョブ SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

次のセクションを参照してください [web ジョブのデプロイ](#deploy) と [のテストと web ジョブのデバッグ](#debug)します。

##<a name="deploy"></a>Web ジョブのデプロイ

* [Visual Studio を使用して Azure WebJobs をデプロイする方法](websites-dotnet-deploy-webjobs.md)
* [Azure ポータルを使用して WebJobs をデプロイする方法](web-sites-create-web-jobs.md)
* [Enabling Command-line or Continuous Delivery of Azure WebJobs (Azure Web ジョブのコマンドライン、または継続的な配信を有効にする)](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git deploying a .NET console app to Azure using WebJobs (Git で Web ジョブを使用して Azure に .NET コンソール アプリケーションをデプロイ)](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [F# WebJob を Azure にデプロイする](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Deploying custom services as Azure Webjobs (Azure Web ジョブとしてカスタム サービスをデプロイする)](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* ビデオ
    * [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Web ジョブのスケジュール設定

* [[Azure Web ジョブの追加] ダイアログ](websites-dotnet-deploy-webjobs.md#configure)
* [Azure ポータルでのスケジュール実行の Web ジョブの作成](web-sites-create-web-jobs.md#CreateScheduled)
* [Hooking up a scheduler job to a WebJob (Web ジョブとスケジューラー ジョブを関連付ける)](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Scheduling Azure WebJobs with cron expressions (CRON 式を使用して Azure Web ジョブをスケジュールする)](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)

##<a name="debug"></a>Web ジョブのテストとデバッグ

* [New Developer and Debugging Features for Azure WebJobs in Visual Studio (Azure Web ジョブに使用できる Visual Studio の新しい開発者機能とデバッグ機能)](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Web ジョブ ダッシュボードの表示](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [How to write logs using the WebJobs SDK and view them in the Dashboard (Web ジョブ SDK を使用してログを記述し、ダッシュボードに表示する方法)](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Web ジョブのリモート デバッグ](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Who wrote that blob? (だれがその BLOB を作成したのか)](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosting interactive code in the Cloud (クラウドでの対話型のコードのホスティング)](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用のダッシュ ボードの取得)](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Azure Web ジョブへのトレースの追加](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../storage-monitoring-diagnosing-troubleshooting/)
* ビデオ
    * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Web ジョブの拡張

* [Scaling Your Web Application with Azure Websites (Azure Websites を使用した Web アプリケーションのスケーリング)](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service: 大規模なビジネス対応の準備完了 Web Apps の設計](https://channel9.msdn.com/Events/Build/2014/3-626)します。 Web ジョブ SDK など、Web ジョブを使用した Web アプリのスケール方法について説明します。
* ビデオ
    * [Scaling out WebJobs (Web ジョブのスケール アウト)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>その他の Web ジョブのリソース

* [Magnus Mårtensson 氏による Azure WebJobs GA ブログ記事](http://magnusmartensson.com/azure-webjobs-ga)
* [Azure App Service での Powershell Web ジョブの実行](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Getting notified when your Azure triggered WebJobs completes (Azure がトリガーした Web ジョブ完了時の通知の受信)](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Web ジョブを使用した簡単な Web アプリのバックアップ保持ポリシー](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure の Web Apps および Cloud Services は、最初の要求で低速](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)します。 Web ジョブを使用して、Standard の価格レベルでのみ使用可能な AlwaysOn 機能をシミュレートする方法を示しています。
* [Web ジョブのグレースフル シャット ダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)します。 Web ジョブ SDK のグレースフル シャット ダウンを参照してください [正常なシャット ダウン](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Azure Web ジョブと AzCopy を使用したバックアップの自動化](http://markjbrown.com/azure-webjobs-azcopy/)
* ビデオ
    * [Magnus Mårtensson 氏による Azure Web ジョブ ビデオ](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>その他の Web ジョブ SDK のリソース

* [Web ジョブ SDK のソース コード](https://github.com/Azure/azure-webjobs-sdk)
* [[BlobTrigger] が動作する方法](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Advanced bindings with the Azure Web Jobs SDK (Azure Web ジョブ SDK での高度なバインド)](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJob to upload FREB files to Azure Storage using the WebJobs SDK (Web ジョブ SDK を使用した、FREB のファイルの Azure Storage へのアップロード)](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosting Azure webjobs outside Azure, with the logging benefits from an Azure hosted webjob (Azure がホストする Web ジョブのログに関するメリットが得られる、Azure 外部での Azure Web ジョブのホスティング)](http://bypassion.dk/?p=510)
* [Building a Data Import Tool with Azure WebJobs (Azure Web ジョブを使用したデータ インポート ツールの構築)](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* ビデオ
    * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>サンプル Web ジョブ アプリケーション

* [GitHub 上で Web ジョブ チームが提供するサンプル アプリケーション](https://github.com/azure/azure-webjobs-sdk-samples)
* [Web ジョブ SDK を使用した Web ジョブ バックエンドを備えた簡単な Azure Web アプリ](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)します。 スケジュールされたイベント ドリブンの Web ジョブの使用方法を示します。 ブログの投稿を参照してください [Azure web ジョブ SDK を使用した SiteMonitR の再構築する](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)です。

##<a name="blogs"></a>ブログ

* [Azure のブログ:](/blog)
* [Amit Apple のブログ](http://blog.amitapple.com/)します。 Web ジョブ (SDK ではない) に重点を置いています。
* [Magnus Mårtensson 氏のブログ](http://magnusmartensson.com/)

##<a name="gethelp"></a>Web ジョブに関する支援

* [Web ジョブに関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Web ジョブ SDK に関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure と ASP.NET のフォーラム](http://forums.asp.net/1247.aspx)
* [Azure App Service Web Apps フォーラム](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web Apps のユーザーからの意見のサイト](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/)します。 ハッシュタグ #AzureWebJobs を使用してください。
* [Web ジョブのバグまたは問題を報告する](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

## 変更内容
* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次を参照してください: [プレビュー ポータル内の移動に関するリファレンス。](http://go.microsoft.com/fwlink/?LinkId=529715)
 

