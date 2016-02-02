<properties 
   pageTitle="起動しないロールのトラブルシューティング | Microsoft Azure"
   description="クラウド サービス ロールが起動に失敗する一般的な原因をいくつか取り上げます。これらの問題に対する解決策も紹介します。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />


# クラウド サービス ロールが起動しないときの一般的なトラブルシューティング手順

ここでは、Azure クラウド サービス ロールの起動失敗に関連した一般的な問題と解決法を取り上げます。

## Azure カスタマー サポートへの問い合わせ

この記事のどの時点でもその他のヘルプを必要がある場合で Azure のエキスパートに連絡することができます [MSDN Azure とスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)します。

または、Azure サポート インシデントを送信できます。 移動して、 [Azure サポート サイト](http://azure.microsoft.com/support/options/) ] をクリック **サポートにお問い合わせ**します。 Azure のサポートを使用する方法の詳細については、読み取り、 [Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)します。


## DLL または依存コンポーネントの欠落

ロールが応答しない、**初期化**、**ビジー**、**停止**の状態を繰り返す、といった症状は、DLL やアセンブリの不足が原因で起こる場合があります。

**現象:**  Dll またはアセンブリの現象があることができます。

- ロール インスタンスが**初期化** / **ビジー** / **停止**の状態を繰り返す。
- ロール インスタンスが**準備完了**状態に移行したにもかかわらず、Web アプリケーションにアクセスしてもページが表示されない。

解決方法: これらの問題を調査するための 3 つのことをお勧めメソッドもあります。

## DLL の欠落を Web ロールで診断する

Web ロールにデプロイされている Web サイトにアクセスすると、ブラウザーに次のようなサーバー エラーが表示されます。

![Server Error in '/' Application.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## カスタム エラーをオフにして問題を診断する

Web ロールの web.config でカスタム エラー モードをオフに設定して再度サービスをデプロイすると、もっと詳しいエラーを表示できます。

リモート デスクトップを使用せずに詳細なエラーを表示するには

1. Visual Studio でソリューションを開きます。

2. **ソリューション エクスプローラー**で、web.config ファイルを探して開きます。

3. web.config ファイルの system.web セクションを探し、次の行を追加します。

    ```xml
    <customErrors mode="Off" />
    ```

4. ファイルを保存します。

5. サービスをパッケージし直して再度デプロイします。

サービスを再度デプロイすると、不足しているアセンブリまたは DLL の名前と共に以下のエラーが表示されます。

## 遠隔からエラーを参照して問題を診断する

離れた場所からリモート デスクトップを使用してロールにアクセスし、詳細なエラーを参照することができます。 リモート デスクトップを使用してエラーを参照するには、次の手順に従います。

1. Azure SDK 1.3 以上がインストールされていることを確認します。

2. Visual Studio を使用したソリューションの展開中に「リモート デスクトップ接続を構成する...」を選択します。 リモート デスクトップ接続を構成する方法については、次を参照してください。 [Azure ロールでリモート デスクトップを使用する](https://msdn.microsoft.com/library/gg443832.aspx)します。

3. Microsoft Azure クラシック ポータルで、インスタンスのステータスが **[準備完了]** として表示されたら、いずれかのロール インスタンスをクリックします。

4. リボンの **[リモート アクセス]** 領域の **[接続]** アイコンをクリックします。

5. リモート デスクトップの構成中に指定した資格情報を使用して仮想マシンにログインします。

6. コマンド プロンプトを開きます。

7. 型 `IPconfig`します。

8. IPV4 アドレスの値をメモします。

9. Internet Explorer を起動します。

10. Web アプリケーションのアドレスと名前を入力します。たとえば、 `http://<IPV4 アドレス >/default.aspx`します。

Web サイトにアクセスすると、詳しいエラー メッセージが表示されます。

* '/' アプリケーションでサーバー エラーが発生しました。

* 説明: 現在の Web 要求を実行中に、ハンドルされていない例外が発生しました。 エラーに関する詳細および例外の発生場所については、スタック トレースを参照してください。

* 例外の詳細: System.IO.FIleNotFoundException: ファイルまたはアセンブリ ’Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’、またはその依存関係の 1 つが読み込めませんでした。 指定されたファイルが見つかりません。

次に例を示します。

![Explicit Server Error in '/' Application](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## コンピューティング エミュレーターを使用した問題の診断

依存コンポーネントの不足や web.config エラーに関する問題の診断とトラブルシューティングは、Azure Microsoft Azure コンピューティング エミュレーターを使って行うことができます。

この診断方法の効果を最大限に高めるには、Windows がクリーン インストールされたコンピューターまたは仮想マシンを使用する必要があります。 Azure 環境のシミュレーションには、Windows Server 2008 R2 x64 が最適です。

1. スタンドアロン バージョン、のインストール [Azure SDK](https://azure.microsoft.com/downloads)

2. 開発コンピューターで、クラウド サービスのプロジェクトをビルドします。

3. エクスプローラーで、クラウド サービス プロジェクトの bin\debug フォルダーに移動します。

4. デバッグ用のコンピューターに .csx フォルダーと .cscfg ファイルをコピーします。

5. クリーン マシンで、Azure SDK コマンド プロンプトを開き `csrun.exe/devstore:start`します。

6. コマンド プロンプトで `実行 csrun < .csx フォルダーへのパス >< .cscfg ファイルへのパス >/launchBrowser`します。

7. ロールが起動すると、詳しいエラー情報が Internet Explorer に表示されます。 Windows の標準的なトラブルシューティング ツールを使用して、さらに詳しく問題を診断することもできます。

## IntelliTrace を使用して問題を診断する

使用できるワーカーと .NET Framework 4 を使用する web ロールでは、 [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx) に用意されている [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs)します。

IntelliTrace を有効にしてサービスをデプロイするには、以下の手順に従います。

1. Azure SDK 1.3 以上がインストールされていることを確認します。

2. Visual Studio を使用してソリューションをデプロイします。 デプロイ中は、**[.NET 4 のロールに対して IntelliTrace を有効にします]** チェック ボックスをオンにしてください。

3. インスタンスが起動したら、**サーバー エクスプローラー**を開きます。

4. 展開、 **Azure\\Cloud サービス** ノードし、デプロイを見つけます。

5. ロール インスタンスが表示されるまでデプロイを展開します。 いずれかのインスタンスを右クリックします。

6. **[IntelliTrace ログの表示]** を選択します。 **[IntelliTrace の概要]** が表示されます。

7. 概要の例外セクションを探します。 例外が存在する場合、**[例外データ]** という見出しで表示されます。

8. **[例外データ]** を展開し、次のような **System.IO.FileNotFoundException** エラーを探します。

![Exception data, missing file or assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## 不足している DLL とアセンブリの解決

不足している DLL とアセンブリのエラーを解決するには、次の手順を実行します。

1. Visual Studio でソリューションを開きます。

2. **ソリューション エクスプローラー**で **[参照設定]** フォルダーを開きます。

3. エラーに表示されているアセンブリをクリックします。

4. **[プロパティ]** ウィンドウで  [ローカル コピー] プロパティを探し、その値を **[True]** に設定します。

5. ホストされるサービスを再デプロイします。

すべてのエラーを修正済みであることが確認できたら、**[.NET 4 のロールに対して IntelliTrace を有効にします]** 設定をオフにしてサービスをデプロイしてください。

## 次のステップ

さらに表示 [トラブルシューティングに関する記事](..\?tag=top-support-issue&service=cloud-services) のクラウド サービスです。






