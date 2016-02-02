<properties
    pageTitle="Visual Studio Team Services と Git を使用した Azure での継続的な配信 | Microsoft Azure" 
    description="Visual Studio Team Services チーム プロジェクトを Git を使用して自動的にビルドして Azure App Service の Web アプリ機能またはクラウド サービスにデプロイするための構成方法について説明します。"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/02/2015"
    ms.author="tarcher"/>


# Visual Studio Team Services と Git を使用した Azure への継続的な配信

Visual Studio Team Services チーム プロジェクトを使用してソース コードの Git リポジトリをホストし、コミットをリポジトリにプッシュするたびに自動的にビルドして Azure の Web アプリまたはクラウド サービスにデプロイすることができます。

Visual Studio 2013 および Azure SDK をインストールする必要があります。 Visual Studio 2013 がない場合はダウンロードを選択して、 **無料で始める** リンクを [www.visualstudio.com](http://www.visualstudio.com)します。 Azure SDK インストール [ここ](http://go.microsoft.com/fwlink/?LinkId=239540)します。

> [AZURE.NOTE] Visual Studio チームのサービス アカウントをこのチュートリアルを完了する必要があります。
> 実行できます [Visual Studio チームのサービス アカウントを無料で開く](http://go.microsoft.com/fwlink/p/?LinkId=512979)します。

Visual Studio Team Services を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

## 手順 1. Git リポジトリを作成する

1. Visual Studio チームのサービス アカウントがない場合は、いずれかを取得  [ここ](http://go.microsoft.com/fwlink/?LinkId=397665)します。 チーム プロジェクトを作成するときに、ソース管理システムとして Git を選択します。 指示に従い、Visual Studio をチーム プロジェクトに接続します。

2. **チーム エクスプローラー**で、**[このリポジトリを複製]** を選択します。

    ![][3]

3. ローカル コピーの場所を指定し、**[複製]** をクリックします。

## 手順 2. プロジェクトを作成してリポジトリにコミットする

1. **チーム エクスプローラー**の **[ソリューション]** セクションで、**[新規作成]** リンクを選択して新しいプロジェクトをローカル リポジトリに作成します。

    ![][4]

2. このチュートリアルの手順に従って、Web アプリまたはクラウド サービス (Azure アプリケーション) をデプロイできます。 新しい Azure クラウド サービス プロジェクトを作成します。
新しい ASP.NET MVC プロジェクトを作成します。 プロジェクトが .NET Framework 4 以降を対象にしていることを確認します。 クラウド サービス プロジェクトを作成する場合は、ASP.NET MVC Web ロールと worker ロールを追加します。
Web アプリを作成する場合は、**[ASP.NET Web アプリケーション]** プロジェクト テンプレートを選択し、次に **[MVC]** を選択します。 参照してください [Azure App Service で ASP.NET web アプリの作成](../web-sites-dotnet-get-started.md) の詳細。

3. ソリューションのショートカット メニューを開き、**[コミット]** を選択します。

    ![][7]

4. Visual Studio Team Services で初めて Git を使用する場合は、Git で自分自身が識別されるように情報を提供する必要があります。 **チーム エクスプローラー**の **[保留中の変更]** 領域で、ユーザー名と電子メール アドレスを入力します。 コミットのコメントを入力し、**[コミット]** をクリックします。

    ![][8]

5. チェックインをするときは、特定の変更を含むまたは除外するためのオプションに注意してください。 必要な変更が除外されている場合は、**[すべて含む]** を選択します。

6. これで、リポジトリのローカル コピーに変更をコミットできました。 次に、**[同期]** リンクを選択してこれらの変更をサーバーと同期させます。

## 手順 3: プロジェクトを Azure に接続する

1. ソース コードを含む Git リポジトリが Visual Studio Team Services に保持され、Git リポジトリを Azure に接続する準備が整いました。  [Azure クラシック ポータル](http://manage.windowsazure.com), をクラウド サービスまたは web アプリを選択するかを選択して新しいものを作成、+ 左クリックして、下にあるアイコン **クラウド サービス** または **Web アプリ** し **簡易作成**します。

    ![][9]

3. クラウド サービスの場合は、**[Visual Studio Team Services 発行の設定]** リンクを選択します。 Web アプリの場合は、**[ソース管理からのデプロイの設定]** リンクを選択します。

    ![][10]

2. ウィザードで、テキスト ボックスに Visual Studio Team Services アカウントの名前を入力し、**[今すぐ承認]** を選択します。 サインインを求められることがあります。

    ![][11]

3. **[接続要求]** ポップアップ ダイアログで、**[承認]** を選択し、Azure が Visual Studio Team Services のチーム プロジェクトを構成することを許可します。

    ![][12]

4. 承認が成功すると、Visual Studio Team Services チーム プロジェクトが含まれるドロップダウン リストが表示されます。 前のステップで作成したチーム プロジェクトの名前を選択し、ウィザードのチェック マーク ボタンを選択します。

    ![][13]

    次にコミットをリポジトリにプッシュするときに、Visual Studio Team Services はプロジェクトをビルドして Azure にデプロイします。

## 手順 4: リビルドをトリガーし、プロジェクトを再デプロイする

1. Visual Studio で、ファイルを開き、変更します。 たとえば、ファイルを変更 `_Layout.cshtml` MVC web ロールで Views\\Shared フォルダーの下です。

    ![][17]

2. サイトのフッター テキストを編集し、そのファイルを保存します。

    ![][18]

3. **ソリューション エクスプローラー**で、ソリューション ノード、プロジェクト ノード、または変更したファイルのショートカット メニューを開き、**[コミット]** を選択します。

4. コメントを入力し、**[コミット]** を選択します。

    ![][20]

5. **[同期]** リンクを選択します。

    ![][38]

6. **[プッシュ]** リンクを選択してコミットを Visual Studio Team Services のリポジトリにプッシュします。 (また、**[同期]** を使用してコミットをリポジトリにコピーすることもできます。 その違いは、**[同期]** ではリポジトリから最新の変更が取得される点です)。

    ![][39]

7. **[ホーム]** を選択して、**チーム エクスプローラー**のホーム ページに戻ります。

    ![][21]

8. **[ビルド]** を選択して処理中のビルドを表示します。

    ![][22]

    **チーム エクスプローラー**に、チェックインのためにビルドが開始されたことが示されます。

    ![][23]

9. ビルドの処理に応じて詳細なログを表示するには、処理中のビルドの名前をダブルクリックします。

10. ビルドの処理中に、Azure にリンクするためのウィザードを使用したときに作成されたビルド定義を調べます。 ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。

    ![][25]

11. **[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。 クラウド サービスの場合は、Visual Studio Team Services により master 分岐がビルドされてステージング環境に自動的にデプロイされます。 その場合も、ライブ サイトにデプロイするための手動による手順を実行する必要があります。 ステージング環境がない Web アプリの場合は、master 分岐が直接ライブ サイトにデプロイされます。

    ![][26]

1. **[プロセス]** タブを見ると、デプロイ環境がクラウド サービスまたは Web アプリの名前に設定されていることがわかります。

    ![][27]

1. 既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。 Azure の発行のプロパティは **[デプロイ]** セクションにあり、MSBuild パラメーターの設定が必要な場合もあります。 たとえば、クラウド サービス プロジェクトで「クラウド」以外のサービス構成を指定する、MSbuild パラメーターに設定 `/p:TargetProfile = [YourProfile]` 、 *[YourProfile]* ServiceConfigurationのような名前のサービス構成ファイルに一致*。YourProfile*.cscfg です。

    次の表は、**[デプロイ]** セクションで使用可能なプロパティを示しています。

   | プロパティ| 既定値|
   |---|---|
   | 信頼されていない証明書を許可| false の場合、SSL 証明書はルート証明機関によって署名される必要があります。|
   | アップグレードの許可| 新規作成の代わりに、既存のデプロイを更新するデプロイを許可します。IP アドレスを保持します。|
   | 削除しない| true の場合、既存の関連のないデプロイを上書きしません (アップグレードは許可)。|
   | デプロイ設定へのパス| リポジトリのルート フォルダーを基準とした Web アプリの .pubxml ファイルへのパス。クラウド サービスでは無視されます。|
   | Sharepoint デプロイ環境| サービス名と同じ。|
   | Azure デプロイ環境| Web アプリ名またはクラウド サービス名。|

1. このころまでには、ビルドが正常に完了しています。

    ![][28]

1. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。

    ![][29]

1. [Azure クラシック ポータル](http://manage.windowsazure.com), に関連する展開を表示する、 **展開** ] タブのステージング環境を選択するとします。

    ![][30]

1.  目的のサイトの URL に移動します。 Web アプリの選択、 **参照** ポータルでボタンをクリックします。 クラウド サービスの場合は、ステージング環境が表示される **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。

    クラウド サービス向けの継続的な統合からのデプロイは、既定ではステージング環境に発行されます。 **[代替クラウド サービス環境]** プロパティを **[運用]** に設定することで、これを変更できます。 ここでは、クラウド サービスのダッシュボード ページにサイト URL が表示されます。

    ![][31]

    新しいブラウザー タブが開いて、実行中のサイトが表示されます。

    ![][32]

1.  プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイが累積されます。 最新のデプロイは [アクティブ] とマークされます。

    ![][33]

## 手順 5: 以前のビルドを再デプロイする

この手順は省略可能です。 Azure クラシック ポータルで以前のデプロイを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。 これによって、TFS で新しいビルドが開始され、デプロイ履歴に新しいエントリが作成されます。

![][34]

## 手順 6. 運用デプロイを変更する

準備が整ったら、Azure クラシック ポータルで **[スワップ]** を選択してステージング環境を運用環境へ昇格できます。 新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。 運用環境とステージング環境でアクティブなデプロイは異なることはありますが、最近のビルドのデプロイ履歴は環境にかかわらず同じです。

![][35]

## 手順 7. working 分岐からデプロイする

Git を使用する場合は、通常、working 分岐で変更を行い、開発が完了状態に到達したときに master 分岐に統合します。 プロジェクトの開発フェーズの間に、この working 分岐をビルドして Azure にデプロイします。

1. **チーム エクスプローラー**で、**[ホーム]** をクリックし、**[分岐]** を選択します。

    ![][40]

2. **[新しい分岐]** リンクを選択します。

    ![][41]

3. "working" など分岐の名前を入力し、**[分岐の作成]** をクリックします。 これにより、新しいローカル分岐が作成されます。

    ![][42]

4. 分岐を発行します。 **[発行されていない分岐]** で分岐名を選択し、**[発行]** を選択します。

    ![][44]

6. 既定では、master 分岐が変更された場合にのみ、継続的なビルドがトリガーされます。 working 分岐に対して継続的なビルドを設定するには、**チーム エクスプローラー**の **[ビルド]** ページで **[ビルド定義の編集]** をクリックします。

7. **[ソースの設定]** タブをクリックします。 **[継続的インテグレーションとビルドのロールの監視対象となる分岐]** で、**[新しい行を追加するにはここをクリックしてください]** をクリックします。

    ![][47]

8. refs/heads/working など作成した分岐を指定します。

    ![][48]

9. コードに変更を加え、変更したファイルのショートカット メニューを開き、**[コミット]** を選択します。

    ![][43]

10. 選択、 **同期されていないコミット** リンク、および選択、 **同期** ボタンまたは **プッシュ** を Visual Studio Team Services の working 分岐のコピーに変更内容をコピーします。

    ![][45]

11. **[ビルド]** ビューに移動して、working 分岐に対してトリガーされたビルドを見つけます。

## 次のステップ

Visual Studio チームのサービスでの Git の使用に関するその他のヒントについては、次を参照してください。 [開発し、Visual Studio を使用して Git でコードを共有](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) し、Azure に発行する Visual Studio チーム サービスによって管理されていない Git リポジトリを使用する方法の詳細については、次を参照してください。 [Azure App Service での GIT による継続的なデプロイ](../web-sites-publish-source-control.md)します。 Visual Studio Team Services の詳細については、次を参照してください。 [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861)します。


[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG 
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG 
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png 
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG 
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG 
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG 
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG 
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG 
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG 
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG 
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG 
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG 
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png 
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png 
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png 
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png 
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG 
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG 
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png 
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG 
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png 
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png 
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png 
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png 
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG 
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png 
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png 
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png 
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png 
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png 
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png 
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png 
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png 
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG 
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG 
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG 
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG 
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG 
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG 
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG 
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG 
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG 
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG 
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG 
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG 

