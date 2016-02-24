<properties 
   pageTitle="PowerShell for StorSimple デバイス管理 | Microsoft Azure"
   description="Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/28/2015"
   ms.author="alkohli@microsoft.com" />

# Windows PowerShell for StorSimple を使用してデバイスを管理する

## 概要

Windows PowerShell for StorSimple は、Microsoft Azure StorSimple デバイスを管理するために使用できるコマンドライン インターフェイスです。 名前が示すように、これは制約付き実行空間に組み込まれている Windows PowerShell ベースのコマンド ライン インターフェイスです。 コマンド ラインを使用するユーザーの観点から見ると、制約付き実行空間は、Windows PowerShell の制限付きバージョンとして表示されます。 このインターフェイスは、Windows PowerShell の基本機能の一部を維持するとともに、Microsoft Azure StorSimple デバイスの管理に適合するように調整された専用のコマンドレットを持っています。 

この記事では、Windows PowerShell for StorSimple の機能について説明します。説明には、このインターフェイスへの接続方法と、このインターフェイスを使用して実行できるワークフローや詳細な手順を示すリンクが含まれています。 ワークフローには、デバイスの登録方法、デバイスでのネットワーク インターフェイスの構成方法、デバイスをメンテナンス モードにする必要がある更新プログラムのインストール方法、デバイスの状態の変更方法、および発生する可能性がある問題のトラブルシューティングの実行方法があります。

この記事を読むと、次のことができるようになります。

- Windows PowerShell for StorSimple を使用して StorSimple デバイスに接続する。

- Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する。

- Windows PowerShell for StorSimple のヘルプを取得する。

>[AZURE.NOTE]   

>- Windows PowerShell for StorSimple コマンドレットを使用すると、シリアル コンソールから、または Windows PowerShell リモート処理経由で StorSimple デバイスを管理できます。 このインターフェイスで使用できる個々 のコマンドレットのそれぞれについての詳細についてを参照してください [StorSimple 用 Windows PowerShell のコマンドレット リファレンス](https://technet.microsoft.com/library/dn688168.aspx)します。

>- Azure PowerShell StorSimple コマンドレットは、StorSimple サービス レベルのタスクと移行タスクをコマンド ラインから自動化できるコレクションです。 StorSimple 用 Azure PowerShell コマンドレットの詳細についてを参照してください、 [Azure StorSimple コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn920427.aspx)します。

Windows PowerShell for StorSimple には、次のいずれかの方法でアクセスできます。

- [StorSimple デバイスのシリアル コンソールに接続する](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Windows PowerShell を使用して StorSimple にリモート接続する](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## Windows PowerShell for StorSimple にデバイスのシリアル コンソール経由で接続する

実行できます [PuTTY をダウンロード](http://www.putty.org/) などのターミナル エミュレーション ソフトウェアを Windows PowerShell for StorSimple に接続します。 Microsoft Azure StorSimple デバイスにアクセスするように PuTTY を明確に構成する必要があります。 次のトピックで、PuTTy の構成方法とデバイスへの接続方法の詳細な手順について説明します。 シリアル コンソールのさまざまなメニュー オプションについても説明します。

### シリアル コンソールについて

シリアル コンソール経由で StorSimple デバイスの Windows PowerShell インターフェイスにアクセスすると、バナー メッセージとメニュー オプションが表示されます。 

バナー メッセージには、モデル、名前、インストールされているソフトウェアのバージョン、アクセス中のコントローラーの状態などの StorSimple デバイスの基本情報が含まれます。 次の図に、バナー メッセージの例を示します。

![シリアル バナー メッセージ](./media/storsimple-windows-powershell-administration/IC741098.png)



>[AZURE.IMPORTANT] 接続しているコント ローラーがアクティブまたはパッシブであるかどうかを識別するために、バナー メッセージを使用することができます。


次の図に、シリアル コンソール メニューで利用できるさまざまな実行空間オプションを示します。

![デバイス 2 の登録](./media/storsimple-windows-powershell-administration/IC740906.png)

次の設定から選択できます。

1. **フル アクセスでログインします。**
このオプションでは、(適切な資格情報) に接続することができます、 **SSAdminConsole** ローカル コント ローラー上の実行空間です。 (ローカル コント ローラーとは、StorSimple デバイスのシリアル コンソール経由でアクセス中のコントローラーのことです)。 
このオプションは、デバイスの問題をトラブルシューティングするために無制限実行空間にアクセスすること (サポート セッション) を Microsoft サポートに許可するために使用することもできます。 オプション 1 を使用してログオンした後、特定のコマンドレットを実行することで、無制限実行空間へのアクセスを Microsoft サポート エンジニアに許可できます。 詳細についてを参照して [サポート セッションを開始](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)します。

1. **フル アクセスでピア コント ローラーにログインします。**
このオプションは、オプション 1 と同じに (適切な資格情報) に接続できる点を除いて、 **SSAdminConsole** 、ピア コント ローラー上の実行空間です。 StorSimple デバイスはアクティブ/パッシブ構成された 2 つのコントローラーを持つ可用性の高いデバイスであるため、ピア コントローラーとは、シリアル コンソールからアクセス中のデバイスのもう一方のコントローラーを指します。
オプション 1 と同じように、このオプションも、ピア コントローラーの無制限実行空間にアクセスすることを Microsoft サポートに許可するために使用できます。

1. **アクセスが制限された接続します。**
Limited モードでの Windows PowerShell インターフェイスにアクセスするには、このオプションを使用します。 アクセス資格情報の入力は必要ありません。 このオプションでは、オプション 1 とオプション 2 に比べて、さらに制限された制限付き実行空間に接続します。  オプション 1 では利用できるがこのオプションでは利用できないタスクの一部を次に示します。

    - 工場出荷時設定をリセットする
    - パスワードを変更する
    - サポート アクセスを有効または無効にする
    - 更新プログラムを適用する
    - 修正プログラムをインストールする 
                                                

    >[AZURE.NOTE] **This is the preferred option if you have forgotten the device administrator password and cannot connect through option 1 or 2.**

1. **言語の変更**
このオプションでは、Windows PowerShell インターフェイスでの表示言語を変更することができます。 サポートされている言語は、英語、日本語、ロシア語、フランス語、韓国語、スペイン語、イタリア語、ドイツ語、中国語、およびポルトガル語 (ブラジル) です。

シリアル コンソールから Windows PowerShell インターフェイスに接続するには、次の PuTTY 設定を必ず使用してください。

#### PuTTY を構成するには

1. PuTTY Reconfiguration] ダイアログ ボックスでの **カテゴリ** ] ウィンドウの [ **キーボード**します。

1. 次のオプションが選択されていることを確認します (これらは新しいセッションを開始したときの既定の設定です)。 

    |キーボードの項目|[|
    |---|---|
    |Backspace key|Control-? (127)|
    |Home and End keys|Standard|
    |Function keys and keypad|ESC[n~|
    |Initial state of cursor keys|Normal|
    |Initial state of numeric keypad|Normal|
    |Enable extra keyboard features|Control-Alt is different from AltGr|

    ![サポートされる Putty 設定](./media/storsimple-windows-powershell-administration/IC740877.png)

1. クリックして **適用**します。

1.  **カテゴリ** ] ウィンドウの [ **翻訳**します。

1.  **リモート文字セット** リスト ボックスで、 **utf-8**します。

1.  **線画文字の処理**, [ **Use Unicode 線画コード ポイント**します。 次の図に、正しい PuTTY の選択を示します。

    ![UTF PuTTY 設定](./media/storsimple-windows-powershell-administration/IC740878.png)

1. クリックして **適用**します。


これで、PuTTY を使用して、次の手順に従ってデバイスのシリアル コンソールに接続できます。

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


## Windows PowerShell for StorSimple を使用して StorSimple にリモート接続する
Windows PowerShell リモート処理を使用して StorSimple デバイスに接続できます。 この方法で接続すると、メニューは表示されません  (メニューは、デバイスのシリアル コンソールを使用して接続する場合のみ表示されます)。Windows PowerShell リモート処理を使用して、特定の実行空間に接続します。 表示言語を指定することもできます。 

表示言語が使用して設定した言語に依存しない、 **言語の変更** シリアル コンソール メニューでオプションです。 リモート PowerShell は、何も指定されていない場合は、接続元のデバイスのロケールを自動的に選択します。

>[AZURE.NOTE] Microsoft Azure の仮想ホストと StorSimple 仮想デバイスで作業している場合は、仮想デバイスへの接続に Windows PowerShell リモート処理と仮想ホストを使用できます。 Windows PowerShell セッションからの情報を保存するホスト上に共有場所を設定している場合、Everyone プリンシパルには認証ユーザーのみが含まれることに注意してください。 そのため、Everyone によって共有へのアクセスを許可するように設定しているときに、資格情報を指定せずに接続した場合、認証されていない Anonymous プリンシパルが使用され、エラーが表示されます。 この問題を解決するには、共有ホストで Guest アカウントを有効にし、Guest アカウントに共有へへのフル アクセス許可を与えるか、Windows PowerShell コマンドレットを有効な資格情報を使用して指定する必要があります。


HTTP または HTTPS を使用して Windows PowerShell リモート処理経由で接続できます。 次のチュートリアルの手順を使用してください。

- [Http を使用してリモート接続する](storsimple-remote-connect.md#connect-through-http)
- [Https を使用してリモート接続する](storsimple-remote-connect.md#connect-through-https)

## 接続のセキュリティに関する考慮事項

Windows PowerShell for StorSimple に接続する方法を決定するときは、次の点を考慮してください。

- デバイスのシリアル コンソールへの直接接続はセキュリティで保護されていますが、ネットワーク スイッチ経由での接続は保護されていません。 ネットワーク スイッチ経由でデバイスのシリアル コンソールに接続するときは、セキュリティ上のリスクに注意してください。

- HTTP セッション経由での接続は、ネットワーク経由でシリアル コンソールに接続するよりもセキュリティが高くなる可能性があります。 これは最も安全な方法ではありませんが、信頼されたネットワークでは容認できます。

- 自己署名証明書を使用する HTTPS セッション経由での接続は、最も安全であり、推奨されるオプションです。


## Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する
次の表に、StorSimple デバイスの Windows PowerShell インターフェイスで実行できるすべての一般的な管理タスクと複雑なワークフローの概要を示します。各ワークフローの詳細については、テーブル内の適切なエントリをクリックしてください。

#### Windows PowerShell for StorSimple のワークフロー

|目的の操作|実行する手順|
|---|---|
|デバイスを登録する|[Windows PowerShell for StorSimple を使用してデバイスを構成して登録する](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Web プロキシを構成します。</br>Web プロキシ設定を表示する|[ StorSimple デバイスの Web プロキシを構成する](storsimple-configure-web-proxy.md)|
|デバイスの DATA 0 ネットワーク インターフェイス設定を変更する|[StorSimple デバイスの DATA 0 ネットワーク インターフェイスを変更する](storsimple-modify-data-0.md)|
|コント ローラーの停止します。 </br> 再起動またはシャット ダウン、コント ローラー </br> デバイスのシャット ダウン</br>デバイスを出荷時の設定にリセットする|[デバイス コントローラーを管理する](storsimple-manage-device-controller.md)|
|メンテナンス モードで更新プログラムと修正プログラムをインストールする|[デバイスを更新する](storsimple-update-device.md)|
|メンテナンス モードを開始する </br>メンテナンス モードを終了する|[StorSimple デバイスのモード](storsimple-device-modes.md)|
|サポート パッケージを作成します。</br>サポート パッケージの暗号化を解除して編集する|[サポート パッケージを作成および管理する](storsimple-create-manage-support-package.md)|
|サポート セッションを開始します。</br>|[StorSimple 用 Windows PowerShell でのサポート セッションの開始](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## Windows PowerShell for StorSimple のヘルプを取得する

Windows PowerShell for StorSimple では、コマンドレット ヘルプを利用できます。 このヘルプのオンラインの最新バージョンも利用できます。これを使用して、システム上のヘルプを更新できます。

このインターフェイスでのヘルプの取得は、Windows PowerShell での操作に似ています。また、ほとんどのヘルプ関連コマンドレットで機能します。 TechNet ライブラリの Windows PowerShell のヘルプ オンラインを見つけることができます: [Windows PowerShell でのスクリプティング](http://go.microsoft.com/fwlink/?LinkID=108518)します。

Windows PowerShell インターフェイスで取得できるヘルプの種類の簡単な説明を、ヘルプの更新方法も含めて次に示します。

#### コマンドレットのヘルプを取得するには

- 任意のコマンドレッドまたは関数のヘルプを表示するには、次のコマンドを使用します。`Get-Help <cmdlet-name>`

- 任意のコマンドレットのオンライン ヘルプを取得するには、上のコマンドレットと `-Online`パラメーターを使用します。`Get-Help <cmdlet-name> -Online`

- ヘルプ全体を取得するには、–Full パラメーターと、たとえば `–Examples`パラメーターを使用します。

#### ヘルプを更新するには

Windows PowerShell インターフェイスのヘルプは簡単に更新できます。 ヘルプを更新するには、次の手順を実行します。

#### コマンドレットのヘルプを更新するには

1. Windows PowerShell を起動、 **管理者として実行** オプション。

1. コマンド プロンプトに、次のコマンドを入力します。
    `Update-Help`

1. 更新されたヘルプ ファイルがインストールされます。

1. ヘルプ ファイルがインストールされた後、「`Get-Help Get-Command`」と入力します。 これにより、ヘルプを利用できるコマンドレットの一覧が表示されます。


>[AZURE.NOTE] 実行空間のいずれかで利用可能なすべてのコマンドレットの一覧を取得するには対応するメニュー オプションにログインし、実行、 `Get-Command` コマンドレットです。

## 次のステップ
上記のワークフローのいずれかを実行するときに、StorSimple デバイスで問題が発生した場合を参照してください [StorSimple デプロイメントのトラブルシューティング用ツール](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)します。


