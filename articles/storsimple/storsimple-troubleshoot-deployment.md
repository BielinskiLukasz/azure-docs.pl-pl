<properties 
   pageTitle="StorSimple のデプロイのトラブルシューティング | Microsoft Azure"
   description="StorSimple を初めてデプロイするときに発生するエラーを診断し、修正する方法について説明します。"
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
   ms.date="12/02/2015"
   ms.author="alkohli" />

# StorSimple デバイスのデプロイメントのトラブルシューティング

## 概要

この記事は、Microsoft Azure StorSimple のデプロイに役立つトラブルシューティングのガイダンスです。 StorSimple の構成時に発生する可能性のある問題の解決に役立つ、一般的な問題、考えられる原因、推奨手順について説明します。 この情報は、StorSimple のオンプレミスの物理デバイスと StorSimple 仮想デバイスの両方に当てはまります。

> [AZURE.NOTE] 最初に、デバイスをデプロイすることも、発生する可能性が、後で、デバイスは稼働時に直面する可能性のあるデバイスの構成に関する問題が発生します。 この記事では、初回デプロイメントでの問題のトラブルシューティングについて説明します。 運用デバイスのトラブルシューティングをするには [運用デバイスの問題のトラブルシューティングを行う](storsimple-troubleshoot-operational-device.md)します。

この記事ではまた、StorSimple のデプロイのトラブルシューティングのためのツールについて説明し、トラブルシューティングの手順の例を示します。

## 初回デプロイメントの問題

デバイスの初回デプロイ時に問題が発生した場合は、次の点を確認してください。

- 物理デバイスのトラブルシューティングを行う場合は、ハードウェアがインストールされているし、のように構成されていることを確認してください [StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md) または [StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)します。
- デプロイの前提条件を確認します。 説明したすべての情報があることを確認、 [展開構成のチェックリスト](storsimple-deployment-walkthrough.md#deployment-configuration-checklist)します。
- StorSimple のリリース ノートを読んで、該当する問題についての記述があるかどうかを確認します。 設置に関して既に確認されている問題については、リリース ノートに回避策が記述されています。 

デバイスをデプロイするときに最も多く見られる問題は、セットアップ ウィザードの実行時と StorSimple 用 Windows PowerShell によるデバイスの登録時に発生します  (StorSimple デバイスの登録と構成は、StorSimple 用 Windows PowerShell を使用して行います。 デバイスの登録の詳細については、次を参照してください。 [手順 3: 構成し、StorSimple 用 Windows PowerShell を使用してデバイスを登録](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple))。

次のセクションは、StorSimple デバイスを初めて構成するときに発生する問題を解決するのに役立ちます。

## 初回セットアップ ウィザード プロセス

次の手順は、セットアップ ウィザードのプロセスの概要を示します。 セットアップの詳細については、次を参照してください。 [オンプレミス StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)します。

1. 実行、 [Invoke-hcssetupwizard](https://technet.microsoft.com/library/dn688135.aspx) コマンドレットは、残りを案内するセットアップ ウィザードを起動する手順です。 
2. ネットワークの構成: セットアップ ウィザードの指示に従って、StorSimple デバイスに対する DATA 0 ネットワーク インターフェイスのネットワーク設定を構成します。 これらの設定には、次の内容が含まれています。
  - 仮想 IP (VIP)、サブネット マスク、およびゲートウェイ:、 [Set-hcsnetinterface](https://technet.microsoft.com/library/dn688161.aspx) コマンドレットがバック グラウンドで実行します。 StorSimple デバイスの DATA 0 ネットワーク インターフェイスの IP アドレス、サブネット マスク、ゲートウェイが構成されます。
  - プライマリ DNS サーバー:、 [Set-hcsdnsclientserveraddress](https://technet.microsoft.com/library/dn688172.aspx) コマンドレットがバック グラウンドで実行します。 StorSimple ソリューションの DNS 設定が構成されます。
  - NTP サーバー –、 [Set-hcsntpclientserveraddress](https://technet.microsoft.com/library/dn688138.aspx) コマンドレットがバック グラウンドで実行します。 StorSimple ソリューションの NTP サーバーの設定が構成されます。
  - 省略可能な web プロキシ:、 [Set-hcswebproxy](https://technet.microsoft.com/library/dn688154.aspx) コマンドレットがバック グラウンドで実行します。 StorSimple ソリューションの Web プロキシの構成が設定され、有効化されます。
3. パスワードの設定: 次に、デバイス管理者と StorSimple Snapshot Manager のパスワードを設定します。 Update 1 を実行している場合、StorSimple Snapshot Manager パスワードを設定する必要はありません。
  - デバイス管理者のパスワードは、デバイスへのログオンに使用します。 デバイスの既定のパスワードは **Password1**します。
  - StorSimple Snapshot Manager のパスワードは、StorSimple Snapshot Manager を使用するための構成をデバイスに対して行う際に必要となります。 このパスワードは、セットアップ ウィザードで最初に設定しておく必要があります。その後、StorSimple Manager サービスから設定したり変更したりすることができます。 StorSimple Snapshot Manager に対しては、このパスワードによってデバイスが認証されます。
 
    > [AZURE.IMPORTANT] パスワードは、登録前に収集されますが、デバイスの登録が成功した後にのみ適用されます。 パスワードの適用に失敗した場合、必要な (複雑さの要件を満たした) パスワードが収集されるまで、パスワードを再入力するように求められます。

4. デバイスの登録: 最後に、Microsoft Azure 内で実行されている StorSimple Manager サービスにデバイスを登録します。 登録が必要とすると、 [サービス登録キーを取得](storsimple-manage-service.md#get-the-service-registration-key) Azure クラシック ポータルでは、セットアップ ウィザードに提供します。 デバイスが正常に登録されると、サービス データ暗号化キーが支給されます。 この暗号化キーは以後、他のすべてのデバイスをサービスに登録する際に必要となります。安全な場所に保管しておいてください。

## デバイスのデプロイ時に発生する一般的なエラー

次の表は、以下の状況で発生することがある一般的なエラーの一覧です。

- 必要なネットワーク設定を構成する。
- オプションの Web プロキシ設定を構成する。
- デバイス管理者と StorSimple Snapshot Manager のパスワードを設定する。 
- デバイスを登録する。 

## 必要なネットワークの設定中のエラー

| 番号| エラー メッセージ | 考えられる原因 | 推奨される操作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invoke-HcsSetupWizard: このコマンドは、アクティブなコントローラーでのみ実行することができます。 | パッシブなコントローラーに対して構成を行った可能性があります。| アクティブなコントローラーからコマンドを実行してください。 詳細については、次を参照してください。 [デバイス上のアクティブなコント ローラーの識別](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)します。|
| 2 | Invoke-HcsSetupWizard: デバイスの準備ができていません。 | DATA 0 にネットワーク接続の問題があります。| DATA 0 の物理的なネットワーク接続を確認してください。|
| 3 | Invoke-HcsSetupWizard: ネットワーク上の別のシステムと競合する IP アドレスがあります (HRESULT からの例外: 0x80070263)。 | DATA 0 に対して指定した IP は既に別のシステムによって使用されています。 | 使用されていない新しい IP を指定してください。|
| 4 | Invoke-HcsSetupWizard: クラスター リソースが失敗しました  (HRESULT からの例外: 0x800713AE)。 | VIP が重複しています。 指定された IP は既に使用されています。| 使用されていない新しい IP を指定してください。|
| 5 | Invoke-HcsSetupWizard: IPv4 アドレスが無効です。 | 指定された IP アドレスの形式に誤りがあります。| 形式を確認し、IP アドレスを指定し直してください。 詳細については、次を参照してください。 [Ipv4 のアドレス指定][1]します。 |
| 6 | Invoke-HcsSetupWizard: IPv6 アドレスが無効です。 | 指定された IP アドレスの形式に誤りがあります。| 形式を確認し、IP アドレスを指定し直してください。 詳細については、次を参照してください。 [Ipv6 のアドレス指定][2]します。|
| 7 | Invoke-HcsSetupWizard: エンドポイント マッパーから使用できるエンドポイントはこれ以上ありません  (HRESULT からの例外: 0x800706D9)。 | クラスターの機能が正常に動作していません。 | [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。

## 省略可能な Web プロキシの設定中のエラー

| 番号| エラー メッセージ | 考えられる原因 | 推奨される操作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invoke-HcsSetupWizard: 無効なパラメーター (HRESULT からの例外: 0x80070057) | プロキシ設定に指定されたパラメーターの 1 つが有効ではありません。| URI が正しい形式で指定されていません。 次の形式を使用します http://*<IP address or FQDN of the web proxy server>*:。*<TCP port number>* |
| 2 | Invoke-HcsSetupWizard: RPC セーバーを利用できません (HRESULT: 0x800706ba からの例外) | 根本原因は、次のいずれか。<ol><li>クラスターが最大されていません。</li><li>パッシブ コント ローラーがアクティブなコント ローラーと通信できないし、パッシブ コント ローラーからコマンドを実行します。</li></ol> | 根本的な原因によって異なります。<ol><li>[Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) 、クラスターが稼働するかどうかを確認します。</li><li>アクティブなコント ローラーからコマンドを実行します。 パッシブ コントローラーからコマンドを実行する場合、パッシブ コントローラーがアクティブ コントローラーと通信できることが必要となります。 必要があります [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) 場合、この接続を確立します。</li></ol> |
| 3 | Invoke-HcsSetupWizard: RPC コールに失敗しました (HRESULT からの例外: 0x800706be) | クラスターはダウンしています。 | [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) 、クラスターが稼働するかどうかを確認します。|
| 4 | Invoke-HcsSetupWizard: クラスター リソースが見つかりません (HRESULT からの例外: 0x8007138f) | クラスター リソースが見つかりません。 これはインストールが適切でなかった場合に発生することがあります。 | デバイスを工場出荷時の既定の設定にリセットすることが必要な場合があります。 [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) クラスター リソースを作成します。|
| 5 | Invoke-HcsSetupWizard: クラスター リソースがオンラインになっていません (HRESULT からの例外: 0x8007138c)| クラスター リソースがオンラインになっていません。 | [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。|

## デバイス管理者パスワードと StorSimple Snapshot Manager のパスワードに関連するエラー

既定のデバイスの管理者パスワードは **Password1**します。 このパスワードは、初回ログオン後に有効期限が切れるため、セットアップ ウィザードを使用して変更する必要があります。 デバイスの初回登録時に、デバイス管理者の新しいパスワードを指定してください。 

Windows Server ホスト上で実行されている StorSimple Snapshot Manager ソフトウェアを使用してデバイスを管理している場合は、初回登録時に StorSimple Snapshot Manager のパスワードも指定する必要があります。 

パスワードは次の要件を満たしている必要があります。

- デバイス管理者のパスワードは 8 ～ 15 文字とする必要があります。
- StorSimple Snapshot Manager のパスワードは、14 文字または 15 文字とする必要があります。
- パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 
- 直近 24 個のパスワードは使用できません。

パスワードは毎年更新する必要があります。パスワードは、デバイスの登録が成功した後でのみ変更されます。 何らかの理由で登録に失敗した場合、パスワードは変更されません。 
デバイス管理者と StorSimple Snapshot Manager のパスワードの詳細についてを参照してください [StorSimple Manager サービスを使用した StorSimple のパスワードを変更](storsimple-change-passwords.md)します。

デバイス管理者のパスワードまたは StorSimple Snapshot Manager のパスワードを設定するときに、以下に示したエラーが発生することがあります。エラーは複数発生する場合もあります。

| 番号| エラー メッセージ | 推奨される操作 |
| ---| ------------- | ------------------ | 
| 1  | パスワードが最大長を超えています。 |これらの要件を満たすパスワードを使用します。<ul><li>デバイス管理者のパスワードは 8 ~ 15 文字である必要があります。</li><li>StorSimple Snapshot Manager パスワードは 14 または 15 文字である必要があります。</li></ul> | 
| 2 | パスワードが、必要な長さを満たしていません。 | これらの要件を満たすパスワードを使用します。<ul><li>デバイス管理者のパスワードは 8 ~ 15 文字である必要があります。</li><li>StorSimple Snapshot Manager パスワードは 14 または 15 文字である必要があります。</lu></ul> |
| 3 | パスワードには、小文字を含める必要があります。 | パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 4 | パスワードには、数字を含める必要があります。 | パスワードは、次の文字の 4 種類の 3 つを含める必要があります: 小文字、大文字、数字、および特殊なです。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 5 | パスワードには、特殊文字を含める必要があります。 | パスワードには、小文字、大文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 6 | パスワードには、大文字、小文字、数字、および特殊文字の 4 つのうちの 3 つの組み合わせを含める必要があります。 | ご使用のパスワードには、必要な文字種が含まれていません。 パスワードがこれらの要件を満たしていることを確認してください。 |
| 7 | パスワードが確認内容と一致しません。 | パスワードがすべての要件を満たしていることと、正しく入力されていることを確認してください。 |
| 8 | 既定のパスワードと同じパスワードは使用できません。 | 既定のパスワードは  *Password1*します。 このパスワードは、初回ログオン後に変更する必要があります。 |
| 9 | 入力されたパスワードがデバイスのパスワードと一致しません。 パスワードを再入力してください。 | パスワードを確認し、もう一度入力します。 |

パスワードは、デバイスの登録前に収集されますが、適用されるのは、登録に成功した後になります。 パスワード復元ワークフローを使用するには、デバイスが登録されている必要があります。 

> [AZURE.IMPORTANT] 一般に、失敗した場合、パスワードを適用し、ソフトウェア繰り返し試行が成功するまで、パスワードを収集します。 まれに、パスワードを適用できないことがあります。 その場合、デバイスを登録して続行することもできますが、パスワードは変更されません。 変更されなかったのがデバイス管理者のパスワードなのか、StorSimple Snapshot Manager のパスワードなのかは通知されません。 この状況が生じた場合は、両方のパスワードを変更することをお勧めします。

Azure クラシック ポータルで StorSimple Manager サービスを使用してパスワードをリセットできます。 詳細については、次を参照してください。 

- [デバイス管理者のパスワードを変更する](storsimple-change-passwords.md#change-the-device-administrator-password)です。
- [StorSimple Snapshot Manager のパスワードを変更する](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)です。

## デバイス登録中のエラー

デバイスを登録するために、Microsoft Azure で実行されている StorSimple Manager サービスを使用します。 デバイスの登録中、次に示すエラーが発生することがあります。エラーは複数発生する場合もあります。

| 番号| エラー メッセージ | 考えられる原因 | 推奨される操作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | エラー 350027: StorSimple Manager にデバイスを登録できませんでした。 |  | しばらくしてから操作をやり直してください。 問題が解決しない場合は [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md)します。|
| 2  | エラー 350013: デバイスの登録中にエラーが発生しました。 サービス登録キーが正しくない可能性があります。 | | 正しいサービス登録キーを使用して再度デバイスを登録してください。 詳細については、次を参照してください。 [サービス登録キーを取得します。](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | エラー 350063: StorSimple Manager サービスに対する認証は成功しましたが登録に失敗しました。 しばらくしてから、操作を再試行してください。 | このエラーは、ACS での認証には成功しましたが、サービスに対する登録呼び出しに失敗したことを示します。 突発的なネットワーク障害が原因として考えられます。 | ください。 問題が解決しない場合は [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md)します。 |
| 4 | エラー 350049: 登録時にサービスに到達できませんでした。 | サービスの呼び出しで、Web 例外が発生しました。 後で操作を再試行すると解決する場合があります。 | IP アドレスと DNS 名を確認して、操作を再試行してください。 問題が解決しない場合は [Microsoft サポートに問い合わせてください。](storsimple-contact-microsoft-support.md) | 
| 5 | エラー 350031: デバイスは既に登録されています。 | | 対処不要です。 |
| 6 | エラー 350016: デバイスの登録に失敗しました。 | |登録キーが正しいことを確認してください。 |
| 7 | Invoke-HcsSetupWizard: デバイスの登録中にエラーが発生しました。原因として、IP アドレスまたは DNS 名の誤りが考えられます。 ネットワーク設定を確認して、もう一度実行してください。 問題が解決しない場合は [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md)します。 (エラー 350050)。 | デバイスから外部のネットワークに ping を実行できることを確認してください。 外部ネットワークに接続できない場合、このエラーが発生して登録に失敗することがあります。 このエラーは、次の 1 つ以上の組み合わせにする可能性があります。<ul><li>IP が正しくないです。</li><li>サブネットが正しくないです。</li><li>ゲートウェイが正しくないです。</li><li>DNS 設定が不適切</li></ul> | 」の手順を参照してください、 [ステップ バイ ステップのトラブルシューティングの例](#step-by-step-storsimple-troubleshooting-example)します。 |
| 8 | Invoke-HcsSetupWizard: サービスの内部エラー [0x1FBE2] が発生したため、現在の操作を実行できませんでした。 しばらくしてから、操作をやり直してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 | これは、ユーザーからは見えないすべてのエラーについてサービスまたはエージェントからスローされる総称的なエラーです。 その最も一般的な原因として ACS 認証の失敗が挙げられます。 NTP サーバーの構成に問題があって、デバイス上の時刻が正しく設定されていない可能性があります。 | (問題がある場合は) 時刻を修正してから、登録操作を再試行してください。 この問題が解決しない場合は [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。 |
| 9 | 警告: デバイスをアクティブ化できませんでした。 デバイス管理者のパスワードおよび StorSimple Snapshot Manager のパスワードが変更されていません。 | 登録に失敗した場合、デバイス管理者のパスワードおよび StorSimple Snapshot Manager のパスワードは変更されません。 |

## StorSimple デプロイメントのトラブルシューティング用ツール

StorSimple には、StorSimple ソリューションのトラブルシューティングに使用できるツールがいくつか含まれています。 学習した内容は次のとおりです。

- サポート パッケージとデバイス ログ 
- トラブルシューティング用に設計されたコマンドレット 

## トラブルシューティングに使用できるサポート パッケージとデバイス ログ

サポート パッケージには、Microsoft サポート チームがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログが含まれています。 StorSimple 用 Windows PowerShell を使用すると、暗号化されたサポート パッケージが生成され、サポート スタッフと共有できます。

### サポート パッケージのログまたは内容を表示するには

1. StorSimple 用 Windows PowerShell を使用してサポート パッケージの作成」の説明に従ってを [作成してサポート パッケージを管理](storsimple-create-manage-support-package.md)します。

2. ダウンロード、 [復号化スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) クライアント コンピューターのローカルにします。

3. これを使用して [手順](storsimple-create-manage-support-package.md#edit-a-support-package) を開き、サポート パッケージの暗号化を解除します。

4. 暗号化が解除されたサポート パッケージのログは etw/etvx 形式です。 次の手順を実行すると、これらのファイルを Windows イベント ビューアーで表示できます。
  1. 実行、 **eventvwr** コマンドを Windows クライアント。 イベント ビューアーが起動します。
  2.  **アクション** ] ウィンドウで、] をクリックして **保存されたログを開いている** etvx/etw 形式 (サポート パッケージ) のログ ファイルをポイントします。 これで、ファイルを表示できます。 ファイルを開いた後、ファイルを右クリックし、テキストとして保存できます。
   
    > [AZURE.IMPORTANT] 使用することも、 **Get-winevent** コマンドレットを Windows PowerShell でこれらのファイルを開きます。 詳細については、次を参照してください。 [Get-winevent](https://technet.microsoft.com/library/hh849682.aspx) Windows PowerShell コマンドレット リファレンス ドキュメント。

5. イベント ビューアーでログを開いたら、デバイスの構成に関連する問題を含む次のログを探します。

  - hcs_pfconfig/Operational Log
  - hcs_pfconfig/Config

6. ログ ファイル内で、セットアップ ウィザードによって呼び出されたコマンドレットに関連する文字列を検索します。 参照してください [初回セットアップ ウィザード プロセス](#first-time-setup-wizard-process) これらのコマンドレットの一覧にします。 

7. 場合は、問題の原因を解明されない [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。 手順に従って [サポート要求を作成](storsimple-contact-microsoft-support.md#create-a-support-request) サポートが必要な Microsoft サポートに連絡するとき。

## トラブルシューティングに使用できるコマンドレット

接続エラーを検出するには、次の Windows PowerShell コマンドレットを使用します。

- `Get-NetAdapter`: ネットワーク インターフェイスの正常性を検出するには、このコマンドレットを使用します。 

- `Test-Connection`: ネットワークの内部と外部のネットワーク接続を確認するには、このコマンドレットを使用します。

- `Test-HcsmConnection`: 正常に登録されたデバイスの接続を確認するには、このコマンドレットを使用します。

StorSimple デバイスで Update 1 を実行している場合、次の診断のコマンドレットも使用できます。

- `Sync-HcsTime`: デバイスの時刻を表示し、NTP サーバーとの時間の同期を強制するにはこのコマンドレットを使用します。

- `Enable-HcsPing` と `Disable-HcsPing`: StorSimple デバイスでホストがネットワーク インターフェイスを ping を実行するにはこれらのコマンドレットを使用します。 既定では、StorSimple のネットワーク インターフェイスは ping 要求に応答しません。

- `Trace-HcsRoute`: ルート トレース ツールとしてこのコマンドレットを使用します。 最終的な宛先までにある各ルーターに長期にわたってパケットを送信し、各ホップから返されるパケットに基づく結果を計算します。 `Trace-HcsRoute` では、指定のルーターやリンクのパケット損失の程度を表示するため、ネットワークの問題の原因となっているルーターやリンクが特定できます。 

- `Get-HcsRoutingTable`: ローカル IP ルーティング テーブルを表示するにはこのコマンドレットを使用します。

## Get-NetAdapter コマンドレットを使用したトラブルシューティング

デバイスを初めてデプロイするためにネットワーク インターフェイスを構成する場合は、デバイスがサービスにまだ登録されていないため、StorSimple Manager サービスの UI にハードウェアの状態が表示されません。 また、[ハードウェアの状態] ページにデバイスの状態が必ずしも正しく反映されない場合があります (特に、サービスの同期に影響する問題がある場合)。 このような場合は、`Get-NetAdapter` コマンドレットを使用して、ネットワーク インターフェイスの正常性と状態を確認できます。

### デバイスのすべてのネットワーク アダプターの一覧を表示するには

1. StorSimple 用 Windows PowerShell を起動し、「`Get-NetAdapter`」と入力します。 

2. `Get-NetAdapter` コマンドレットの出力と次のガイドラインを使用して、ネットワーク インターフェイスの状態を把握します。
  - インターフェイスは正常であり、有効な場合、 **ifIndex** 状態が [ **を**します。
  - インターフェイスが正常な状態だが物理的に接続されていない (ネットワーク ケーブルの場合) 場合、 **ifIndex** として示す **無効になっている**します。
  - インターフェイスが異常な状態が有効になっていない場合、 **ifIndex** 状態が [ **NotPresent**します。
  - インターフェイスが存在しない場合は、この一覧に表示されません。 StorSimple Manager サービスの UI では、このインターフェイスの状態は失敗として今までどおり表示されます。

このコマンドレットを使用する方法の詳細についてを参照してください [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) Windows PowerShell コマンドレット リファレンスにします。 

次のセクションでは、`Get-NetAdapter` コマンドレットの出力のサンプルを示します。 

 これらのサンプルでは、コントローラー 0 はパッシブ コントローラーで、次のように構成されています。

- デバイスには、DATA 0、DATA 1、DATA 2、および DATA 3 の各ネットワーク インターフェイスが存在します。
- DATA 4 と DATA 5 のネットワーク インターフェイス カードは存在しません。そのため、これらのインターフェイスは出力に表示されません。
- DATA 0 が有効になっています。

コントローラー 1 はアクティブ コントローラーで、次のように構成されています。

- デバイスには、DATA 0、DATA 1、DATA 2、DATA 3、DATA 4、および DATA 5 の各ネットワーク インターフェイスが存在します。
- DATA 0 が有効になっています。

**出力の例 – コントローラー 0**

コントローラー 0 (パッシブ コントローラー) からの出力を次に示します。 DATA 1、DATA 2、および DATA 3 は接続されていません。 DATA 4 と DATA 5 は、デバイスに存在しないため、表示されません。 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**出力の例 – コントローラー 1**

コントローラー 1 (アクティブ コントローラー) からの出力を次に示します。 デバイスでは、DATA 0 ネットワーク インターフェイスのみが構成され、動作しています。

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## Test-Connection コマンドレットを使用したトラブルシューティング

`Test-Connection` コマンドレットを使用すると、StorSimple デバイスを外部ネットワークに接続できるかどうかを確認できます。 セットアップ ウィザードですべてのネットワーク パラメーター (DNS など) が正しく構成されている場合は、`Test-Connection` コマンドレットを使用して、outlook.com など、ネットワークの外部の既知のアドレスに ping を実行できます。 

このコマンドレットでの接続の問題のトラブルシューティングを行うには、ping を有効にする必要があります。

`Test-Connection` コマンドレットの次の出力サンプルをご覧ください。 

> [AZURE.NOTE] 最初のサンプルでは、デバイスは DNS が正しく構成されます。 2 番目のサンプルでは、DNS が正しく構成されています。
 
**出力の例 – 正しくない DNS**

次の例では、IPv4 と IPv6 のアドレスが出力されていません。これは、DNS が解決されていないことを示します。 つまり、外部ネットワークに接続されておらず、正しい DNS を指定する必要があることを意味します。 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**出力の例 – 正しい DNS**

次の例では、DNS が IPv4 アドレスを返します。これは、DNS が正しく構成されていることを示します。 これは、外部ネットワークに接続されていることの確認になります。 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## Test-HcsmConnection コマンドレットを使用したトラブルシューティング

StorSimple Manager サービスに既に接続され、登録されているデバイスには、`Test-HcsmConnection` コマンドレットを使用します。 このコマンドレットを使用すると、登録済みのデバイスと、対応する StorSimple Manager サービス間の接続を確認できます。 このコマンドは StorSimple 用 Windows PowerShell で実行できます。 

### Test-HcsmConnection コマンドレットを実行するには

1. デバイスが登録されていることを確認します。

2. デバイスの状態を確認します。 デバイスが非アクティブ化されているか、メンテナンス モードまたはオフラインの場合、次のエラーが発生します。 

   - ErrorCode.CiSDeviceDecommissioned – デバイスが非アクティブ化されていることを示します。
   - ErrorCode.DeviceNotReady – デバイスがメンテナンス モードであることを示します。
   - ErrorCode.DeviceNotReady – デバイスがオンラインでないことを示します。

3. StorSimple Manager サービスが実行されていることを確認 (を使用して、 [Get-clusterresource](https://technet.microsoft.com/library/ee461004.aspx) コマンドレット)。 サービスが実行されていない場合、次のエラーが表示されます。

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – Get-ClusterResource を実行したときに例外が発生したことを示します。

4. Access Control Service (ACS) トークンを確認します。 Web の例外がスローされた場合は、ゲートウェイの問題が発生した、プロキシ認証が行われなかった、DNS が正しくない、認証に失敗した、などの原因が考えられます。 次のエラーが表示されることがあります。

   - ErrorCode.CiSApplianceGateway – HttpStatusCode.BadGateway 例外を示します。名前解決サービスがホスト名を解決できませんでした。 
   - ErrorCode.CiSApplianceProxy – HttpStatusCode.ProxyAuthenticationRequired 例外 (HTTP 状態コード 407) を示します。クライアントがプロキシ サーバーで認証できませんでした。 
   - ErrorCode.CiSApplianceDNSError – WebExceptionStatus.NameResolutionFailure 例外を示します。 名前のリゾルバー サービスは、ホスト名を解決できませんでした。 .
   - ErrorCode.CiSApplianceACSError – サービスから認証エラーが返されたが、接続されていることを示します。
   
    Web の例外がスローされなかった場合は、ErrorCode.CiSApplianceFailure を確認します。 これは、アプライアンスが失敗したことを示します。

5. クラウド サービスの接続を確認します。 サービスによって Web の例外がスローされた場合は、次のエラーが表示されることがあります。

  - ErrorCode.CiSApplianceGateway – HttpStatusCode.BadGateway 例外を示します。中間プロキシ サーバーが別のプロキシまたは元のサーバーから正しくない要求を受け取りました。
  - ErrorCode.CiSApplianceProxy – HttpStatusCode.ProxyAuthenticationRequired 例外 (HTTP 状態コード 407) を示します。クライアントがプロキシ サーバーで認証できませんでした。 
  - ErrorCode.CiSApplianceDNSError – WebExceptionStatus.NameResolutionFailure 例外を示します。 名前のリゾルバー サービスは、ホスト名を解決できませんでした。 .
  - ErrorCode.CiSApplianceACSError – サービスから認証エラーが返されたが、接続されていることを示します。
  
    Web の例外がスローされなかった場合は、ErrorCode.CiSApplianceSaasServiceError を確認します。 これは、StorSimple Manager サービスの問題を示します。
 
6. Azure Service Bus の接続を確認します。 ErrorCode.CiSApplianceServiceBusError は、デバイスが Service Bus に接続できないことを示します。
 
ログ ファイルの CiSCommandletLog0Curr.errlog と CiSAgentsvc0Curr.errlog には、例外の詳細など、詳しい情報が含まれています。 

コマンドレットを使用する方法の詳細についてを参照してください [Test-hcsmconnection](https://technet.microsoft.com/library/dn715782.aspx) Windows PowerShell のドキュメントを参照しています。

> [AZURE.IMPORTANT] このコマンドレットは、アクティブとパッシブ コント ローラーの両方に対して行うことができます。 
 
`Test-HcsmConnection` コマンドレットの次の出力サンプルをご覧ください。 

**サンプル出力 - StorSimple リリースを実行している正常に登録済みのデバイス (2014 年 7 月)**

最初のサンプルは、StorSimple Manager サービスに正常に登録され、接続の問題が発生していないデバイスの出力です。 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**サンプル出力 - StorSimple Update 1 を実行している正常に登録済みのデバイス**

StorSimple デバイスで Update 1 を実行している場合、Verbose スイッチを使用して実行する必要はありません。

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**サンプル出力 - StorSimple リリースを実行しているオフライン デバイス (2014 年 7 月)**

このサンプルは、デバイスのステータスを持つ **オフライン** Azure クラシック ポータルで。

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

デバイスは現在の Web プロキシ構成では接続できませんでした。 原因としては、Web プロキシ構成またはネットワーク接続の問題が考えられます。 この場合は、Web プロキシの設定が正しく、Web プロキシ サーバーがオンラインになっており、アクセスできることを確認する必要があります。 

## Sync-HcsTime コマンドレットを使用したトラブルシューティング

デバイスの時刻を表示するには、このコマンドレットを使用します。 デバイスの時刻が NTP サーバーでオフセットになる場合、このコマンドレットを使用して時間を NTP サーバーと強制同期します。 NTP サーバーとデバイス間のオフセットが 5 分以上の場合は、警告が表示されます。 オフセットが 15 分を超えている場合、デバイスはオフラインになります。 それでもこのコマンドレットを使用して時間を強制同期できます。 ただし、オフセットが 15 時間を超える場合、時間を強制同期することはできず、エラー メッセージが表示されます。

**サンプル出力 – Sync-HcsTime を使用した時間の強制同期**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## Enable-HcsPing と Disable-HcsPing コマンドレットを使用したトラブルシューティング

デバイス上のネットワーク インターフェイスが ICMP ping 要求に応答することを確認するには、これらのコマンドレットを使用します。 既定では、StorSimple ネットワーク インターフェイスは ping 要求に応答しません。 このコマンドレットを使用すると、最も簡単な方法でデバイスがオンラインかどうか、到達可能かどうかを確認できます。  

**サンプル出力 – Enable-HcsPing と Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## Trace-HcsRoute コマンドレットを使用したトラブルシューティング

ルート トレース ツールとしてこのコマンドレットを使用します。 最終的な宛先までにある各ルーターに長期にわたってパケットを送信し、各ホップから返されるパケットに基づく結果を計算します。 コマンドレットでは指定のルーターやリンクのパケット損失の程度を表示するため、ネットワークの問題の原因となっているルーターやリンクが特定できます。

**Trace-HcsRoute でパケットのルートをトレースする方法を示すサンプル出力**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## Get-HcsRoutingTable コマンドレットを使用したトラブルシューティング

StorSimple デバイスのルーティング テーブルを表示するには、このコマンドレットを使用します。 ルーティング テーブルは、インターネット プロトコル (IP) ネットワーク上を流れるデータ パケットの宛先の特定に役立つ一連のルールです。 

ルーティング テーブルでは、インターフェイスとデータを指定したネットワークにルーティングするゲートウェイが表示されます。 また、これは特定の送信先に到達するために使用するパスの意思決定者となるルーティング メトリックも提供します。 ルーティング メトリックが低いと、優先順位が高くなります。 

たとえば、2 つのネットワーク インターフェイス、DATA 2、DATA 3 をインターネットに接続しているとします。 DATA 2 と DATA 3 のルーティング メトリックがそれぞれ 15 と 261 である場合、下位のルーティング メトリックの DATA 2 は、インターネットに接続するために使用される優先インターフェイスとなります。

StorSimple デバイスで Update 1 を実行している場合は、DATA 0 のネットワーク インターフェイスがクラウド トラフィックで最優先されます。 これは、クラウド対応インターフェイスがほかにある場合でも、クラウド トラフィックは DATA 0 を経由してルーティングされることを意味します。 

パラメーターを指定しないで `Get-HcsRoutingTable` コマンドレットを実行する場合 (次の例を参照)、コマンドレットでは IPv4 と IPv6 両方のルーティング テーブルを出力します。 または、指定することができます `Get-HcsRoutingTable -IPv4` または `Get-HcsRoutingTable -IPv6`  を関連するルーティング テーブルを取得します。

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## StorSimple のステップ バイ ステップのトラブルシューティングの例

次の例では、StorSimple デプロイメントのステップ バイ ステップのトラブルシューティングを示します。 この例では、ネットワーク設定または DNS 名に誤りがあるという内容のエラー メッセージが表示されてデバイスの登録に失敗する、というシナリオを取り上げています。

表示されるエラー メッセージは次のとおりです。

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

エラーの原因として、次のいずれかが考えられます。

- ハードウェアの設置が不適切
- ネットワーク インターフェイスの不具合
- IP アドレス、サブネット マスク、ゲートウェイ、プライマリ DNS サーバー、Web プロキシのいずれかが正しくない
- 登録キーが正しくない
- 不適切なファイアウォール設定

### デバイス登録問題を見つけ、修正するには

1. デバイス構成を確認します。アクティブ コントローラーで `Invoke-HcsSetupWizard` を実行します。

     > [AZURE.NOTE] アクティブなコント ローラーでセットアップ ウィザードを実行する必要があります。 アクティブ コントローラーに接続されているかどうかは、シリアル コンソールに表示されるバナーを見て確認できます。 接続先がコントローラー 0 であるかコントローラー 1 であるか、また、コントローラーがアクティブであるかパッシブであるかがバナーに表示されます。 詳細についてを参照してください [デバイス上のアクティブなコント ローラーの識別](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)します。
 
2. デバイスが正しく配線されていることを確認します。デバイスのバック プレーンでネットワークのケーブル配線を確認します。 ケーブル配線はデバイスのモデルによって異なります。 詳細についてを参照してください [StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md) または [StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md)します。

     > [AZURE.NOTE] 10 GbE ネットワーク ポートを使用している場合は、提供されている QSFP-SFP アダプターと SFP ケーブルを使用する必要があります。 詳細については、次を参照してください。、 [ケーブル、スイッチ、トランシーバー Mellanox ポートの OEM サプライヤー推奨の一覧](http://www.mellanox.com/page/cables?mtag=cable_overview)します。
 
3. ネットワーク インターフェイスの状態を確認します。

   - Get-NetAdapter コマンドレットを使用し、DATA 0 のネットワーク インターフェイスの状態を検出します。 
   - リンクが機能していない場合、 **ifindex** インターフェイスがダウンしているステータスによって示されます。 その場合は、ポートとアプライアンス間およびポートとスイッチ間のネットワーク接続を確認する必要があります。 ケーブルに不具合がないかも十分に確認してください。 
   - アクティブ コントローラーの DATA 0 ポートの不具合が疑われる場合、コントローラー 1 の DATA 0 ポートに接続することで確認できます。 確認するには、デバイスの背面のコントローラー 0 からネットワーク ケーブルを取り外し、コントローラー 1 に接続してから再度、Get-NetAdapter コマンドレットを実行します。 
   コント ローラー上の DATA 0 ポートは失敗し、 [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。 場合によっては、ご使用のシステム上のコントローラーを交換する必要があります。
 
4. スイッチとの接続を確認します。
   - 主エンクロージャのコントローラー 0 の DATA 0 ネットワーク インターフェイスとコントローラー 1 の DATA 0 ネットワーク インターフェイスが同じサブネットに属していることを確認します。 
   - ハブまたはルーターを確認します。 通常、両方のコントローラーを同じハブまたは同じルーターに接続する必要があります。 
   - 接続に使用しているスイッチで、両方のコントローラーの DATA 0 が同じ vLAN に割り当てられていることを確認してください。
   
5. ユーザー エラーを排除します。

  - セットアップ ウィザードを再実行 (実行 **Invoke-hcssetupwizard**)、エラーがないかどうかを確認するには、もう一度値を入力します。 
  - 使用している登録キーを確認します。 StorSimple Manager サービスには、同じ登録キーを使用して複数のデバイスを接続できます。 」の手順に従って [サービス登録キーの取得](storsimple-manage-service.md#get-the-service-registration-key) を正しい登録キーを使用していることを確認します。

    > [AZURE.IMPORTANT] 実行している複数のサービスがある場合は、適切なサービスの登録キーをデバイスの登録に使用されるようにする必要があります。 デバイスを間違った StorSimple Manager サービスに登録している場合にする必要があります。 [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) に対処します。 場合によっては、デバイスを出荷時の設定にリセットしたうえで、目的のサービスに接続する必要があります。その場合は、データが失われる可能性があります。

6. Test-Connection コマンドレットを使用して、外部のネットワークとの接続を確認します。 詳細についてを参照してください [Test-connection コマンドレットを使用したトラブルシューティング](#troubleshoot-with-the-test-connection-cmdlet)します。

7. ファイアウォールによってブロックされている可能性がないか確認します。 仮想 IP (VIP)、サブネット、ゲートウェイ、DNS の設定がすべて正しいことを確認してもなお、接続の問題が解消しない場合、デバイスと外部ネットワーク間の通信がファイアウォールによってブロックされている可能性があります。 内側から外側への通信について、StorSimple デバイスのポート 80 とポート 443 を開放する必要があります。 詳細については、次を参照してください。 [StorSimple デバイスのネットワーク要件](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)します。

8. ログを確認します。 移動して [トラブルシューティングのためのサポート パッケージとデバイスのログが利用可能な](#support-packages-and-device-logs-available-for-troubleshooting)です。

9. 上記の手順では、問題が解決しない場合 [Microsoft サポートにお問い合わせ](storsimple-contact-microsoft-support.md) サポートが必要です。

## 次のステップ
[運用デバイスのトラブルシューティングする方法について説明](storsimple-troubleshoot-operational-device.md)します。

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 


