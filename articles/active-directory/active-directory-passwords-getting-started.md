<properties 
    pageTitle="概要: Azure AD でのパスワード管理 | Microsoft Azure" 
    description="自分のパスワードのリセットを可能にし、パスワード リセットの前提条件を学習するほか、パスワード ライトバックを有効化してオンプレミスの Active Directory パスワードを管理します。" 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>


# パスワード管理の概要

ユーザーは、簡単な手順を実行するだけで、自分のクラウドの Azure Active Directory パスワードまたはオンプレミス Active Directory パスワードを管理できるようになります。 いくつかの簡単な前提条件を満たせば、すぐに組織全体のパスワードの変更とリセットが実行できるようになります。 この記事では、次の概念を説明します。

* [* * ユーザーにそのクラウド Azure Active Directory パスワード * * のリセットを有効にする方法](#enable-users-to-reset-their-azure-ad-passwords)
 - [セルフ サービス パスワード リセットの前提条件](#prerequisites)
 - [手順 1: パスワードのリセット ポリシーを構成します。](#step-1-configure-password-reset-policy)
 - [手順 2: 連絡先の追加テスト ユーザーのデータ](#step-2-add-contact-data-for-your-test-user)
 - [手順 3: ユーザーとしてパスワードをリセットします。](#step-3-reset-your-azure-ad-password-as-a-user)
* [* * ユーザーによるリセットまたは変更するには、内部設置型 Active Directory パスワード * * を有効にする方法](#enable-users-to-reset-or-change-their-ad-passwords)
 - [パスワード ライトバックの前提条件](#writeback-prerequisites)
 - [手順 1: Azure AD Connect の最新バージョンをダウンロードします。](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [手順 2: UI または powershell を使用して Azure AD Connect でパスワード ライトバックを有効にして確認](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [手順 3: ファイアウォールを構成します。](#step-3-configure-your-firewall)
 - [手順 4: 適切なアクセス許可を設定します。](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [手順 5: ユーザーとして AD パスワードをリセットして、検証](#step-5-reset-your-ad-password-as-a-user)

## ユーザーによる Azure AD パスワードのリセットを有効にする

このセクションでは、AAD クラウド ディレクトリに対してセルフサービスのパスワード リセットを有効化し、セルフサービスのパスワード リセットが実行できるようにユーザーを登録し、最後にユーザーとしてセルフサービスのパスワード リセットのテストを実行する方法について説明します。

- [セルフ サービス パスワード リセットの前提条件](#prerequisites)
- [手順 1: パスワードのリセット ポリシーを構成します。](#step-1-configure-password-reset-policy)
- [手順 2: 連絡先の追加テスト ユーザーのデータ](#step-2-add-contact-data-for-your-test-user)
- [手順 3: ユーザーとしてパスワードをリセットします。](#step-3-reset-your-azure-ad-password-as-a-user)


### 前提条件

セルフサービスのパスワード リセットを有効にして使用するには、次の前提条件を満たす必要があります。

- AAD テナントを作成する。 詳細については、を参照してください [Azure AD の概要](https://azure.microsoft.com/trial/get-started-active-directory/)。
- Azure サブスクリプションを取得する。 詳細については、を参照してください [Azure AD テナントは何ですか?](active-directory-administer.md#what-is-an-azure-ad-tenant)。.
- AAD テナントを Azure サブスクリプションに関連付ける。 詳細については、次を参照してください。 [方法 Azure サブスクリプションを Azure AD に関連付ける](https://msdn.microsoft.com/library/azure/dn629581.aspx)します。
- Azure AD Premium または Basic にアップグレードする。 詳細については、次を参照してください。 [Azure Active Directory のエディション](http://azure.microsoft.com/pricing/details/active-directory/)します。
  >[AZURE.NOTE] セルフサービスのパスワード リセットを有効にするには、Azure AD Premium または Azure AD Basic にアップグレードする必要があります。 詳細については、「Azure Active Directory のエディション」を参照してください。 この情報には、Azure AD Premium または Basic にサインアップする方法、ライセンス プランをアクティブ化して Azure AD アクセスをアクティブ化する方法、および管理者とユーザーのアカウントにアクセス権を割り当てる方法の詳細が含まれます。

- AAD ディレクトリで、管理者アカウントとユーザー アカウントを少なくとも 1 つずつ作成する。
- AAD Premium または Basic のライセンスを、作成済みの管理者およびユーザーのアカウントに割り当てる。

### 手順 1: パスワードのリセット ポリシーを構成する

ユーザー パスワードのリセット ポリシーを構成するには、以下の手順を実行します。

1.  任意のブラウザーを開きに移動、 [Azure 管理ポータル](https://manage.windowsazure.com)します。
2.  [Azure 管理ポータル](https://manage.windowsazure.com), 、検索、 **Active Directory 拡張機能** 左側のナビゲーション バー。

    ![][001]

3. [**ディレクトリ**] タブで、Wingtip Toys など、ユーザー パスワードのリセット ポリシーを構成するディレクトリをクリックします。

    ![][002]

4.  **[構成]** タブをクリックします。

    ![][003]

5.  [**構成**] タブで、下にスクロールして [**ユーザー パスワードのリセット ポリシー**] セクションを表示します。 ここでは、特定のディレクトリについて、ユーザー パスワードのリセット ポリシーのあらゆる側面を構成できます。
    >[AZURE.NOTE] この**ポリシーが適用されるのは組織内のエンド ユーザーのみで、管理者には適用されません**。 セキュリティ上の理由から、管理者のパスワード リセット ポリシーの管理はマイクロソフトが行います。 このセクションが表示されない場合は、Azure Active Directory Premium または Basic にサインアップ済みであることと、この機能の構成を行う管理者アカウントに**ライセンスを割り当て済み**であることを確認してください。 

    ![][004]

6.  ユーザー パスワードのリセット ポリシーを構成するには、[**パスワードのリセットが有効になっているユーザー**] トグルを [**はい**] に設定します。 この操作を行うと、組織のディレクトリでのこの機能の動作を構成するためのさまざまなコントロールが追加表示されます。 組織のニーズに合わせて自由にパスワード リセットをカスタマイズしてください。 さらに学習するにはどのような各パスワードのリセット ポリシー コントロールを参照してください [カスタマイズ: Azure AD パスワード管理](active-directory-passwords-customize)します。

    ![][005]

7.  テナントのユーザー パスワード リセット ポリシーの構成が完了したら、画面の一番下にある [**保存**] ボタンをクリックします。
  >[AZURE.NOTE] 最も複雑な状況での機能の動作を表示できるようには、ユーザー パスワードのリセット ポリシーの 2 つのチャレンジを使うことをお勧めします。

  ![][006]

### 手順 2: テスト ユーザー用の連絡先データを追加する

組織内のユーザーがパスワードのリセットに使用するデータを指定する方法はいくつかあります。

-   ユーザーを編集、 [Azure 管理ポータル](https://manage.windowsazure.com) または [Office 365 管理ポータル](https://portal.microsoftonline.com)
-   AAD Connect を使用して、ユーザー プロパティをオンプレミスの Active Directory ドメインから Azure AD に同期する
-   Windows PowerShell を使用してユーザー プロパティを編集する
-   で登録ポータルで自分のデータを登録できるように [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
-   アクセス パネルにサインインしたときにパスワード リセットの登録を必要と [http://myapps.microsoft.com](http://myapps.microsoft.com) を設定して、 **ユーザーのアクセス パネルにサインインするときに登録が必要** SSPR の構成オプション **はい**します。

データの種類については、パスワードのリセットおよび形式の要件によってデータの使用この詳細を学習する場合は、を参照してください [パスワードのリセットで使用されるデータですか?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### ユーザー登録ポータルからユーザーの連絡先データを追加するには

1.  パスワード リセット登録ポータルを使用するのには、このページへのリンクと、組織内ユーザーを入力する必要があります ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) かユーザーに対して自動登録を要求するオプションを有効にします。 ユーザーがこのリンクをクリックすると、組織のアカウントを使ってサインインするよう求められます。 サインインが終わると、次のページが表示されます。

    ![][007]

2.  ここでは、携帯電話、代替電子メール アドレス、またはセキュリティの質問の指定や確認ができます。 携帯電話の番号の確認画面は、以下のようになります。

    ![][008]

3.  ユーザーがこの情報を指定すると、ページが更新され、情報が有効である旨が表示されます (以下の画面では、情報を非表示にしています)。 ユーザーが [**完了**] ボタンまたは [**キャンセル**] ボタンをクリックすると、アクセス パネルが表示されます。

    ![][009]

4.  ユーザーがここに挙げた情報を 2 つとも確認すると、入力されたデータでプロファイルが更新されます。 この例では、**会社電話番号**が手動で指定されたため、この番号をパスワードをリセットする際の連絡方法として利用できます。

    ![][010]

### 手順 3: ユーザーとして Azure AD パスワードをリセットする

これでユーザーのリセット ポリシーが構成され、ユーザーの連絡先の詳細が指定できたので、このユーザーは、セルフサービスのパスワード リセットを実行できます。

#### セルフサービスのパスワード リセットを実行するには

1.  サイトにアクセスする場合と同様に [**portal.microsoftonline.com**](http://portal.microsoftonline.com), のようなログイン画面が表示されます、下です。 [**アカウントにアクセスできません**] リンクをクリックして、パスワード リセットの UI をテストします。

    ![][011]

2.  [**アカウントにアクセスできません**] をクリックすると新しいページが表示され、パスワードをリセットする**ユーザー ID** の入力を求められます。 テスト **ユーザー ID** と captcha を入力し、[**次へ**] をクリックします。

    ![][012]

3.  この場合、ユーザーは**会社電話**、**携帯電話**、および**代替電子メール**を指定していたため、最初のチャレンジの選択肢にこれら 3 つがすべて表示されています。

    ![][013]

4.  今回は、最初に**会社電話**に**電話する**ことを選択します。 電話ベースの方法を選択した場合、パスワードをリセットするには**電話番号の確認**が求められます。 これには、悪意のある個人によって組織内のユーザーの電話番号が漏洩されることを防ぐ目的があります。

    ![][014]

5.  ユーザーが電話番号を確認した後は、[通話] ウォールをクリックするとスピナーが表示され、電話が鳴ります。 ユーザーが電話を取ると、アカウントの確認のため **「#」 を押すよう**指示するメッセージが再生されます。 # キーを押すと、ユーザーが最初のチャレンジをクリアしたことが自動的に確認され、2 番目の確認手順の UI に進みます。

    ![][015]

6.  最初のチャレンジをクリアすると、UI が自動的に更新され、クリアしたチャレンジが選択肢の一覧から除外されます。 今回は、**会社電話**を最初に使用しているため、2 番目の確認手順に利用できるチャレンジの選択肢には、**携帯電話**と**代替電子メール**のみが残っています。 [**連絡用電子メール アドレスにメールを送信**] オプションをクリックします。 次に [電子メール] をクリックすると、登録済みの連絡用電子メール アドレスにメールが送信されます。

    ![][016]

7.  ユーザーに送信される電子メールのサンプルを次に示します。テナントのブランド化に注目してください。

    ![][017]

8.  電子メールが届くとページが更新され、電子メールに記載されている確認コードを、以下に示す入力ボックスに入力できるようになります。 コードが正しく入力されると、[次へ] ボタンが点灯し、2 番目の確認手順が完了となります。

    ![][018]

9.  組織のポリシーの要件をクリアすると、新しいパスワードを作成できるようになります。 パスワードの検証ベースの場合、AAD の「強力な」パスワード要件を満たしている (を参照してください [Azure AD でのパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx)), 、され、ユーザーに入力したパスワードがそのポリシーを満たしているかどうかを示すために強度検証コントロールが表示されます。

    ![][019]

10. 組織のポリシーに合致するパスワードを指定すると、パスワードがリセットされ、直ちに新しいパスワードでログインできるようになります。

    ![][020]


## ユーザーによる Azure AD パスワードのリセットまたは変更を有効にする

このセクションでは、オンプレミスの Active Directory パスワードにライトバックするためのパスワード リセットの構成方法を説明します。

- [パスワード ライトバックの前提条件](#writeback-prerequisites)
- [手順 1: Azure AD Connect の最新バージョンをダウンロードします。](#step-1-download-the-latest-version-of-azure-ad-connect)
- [手順 2: UI または powershell を使用して Azure AD Connect でパスワード ライトバックを有効にして確認](#step-2-enable-password-writeback-in-azure-ad-connect)
- [手順 3: ファイアウォールを構成します。](#step-3-configure-your-firewall)
- [手順 4: 適切なアクセス許可を設定します。](#step-4-set-up-the-appropriate-active-directory-permissions)
- [手順 5: ユーザーとして AD パスワードをリセットして、検証](#step-5-reset-your-ad-password-as-a-user)


### ライトバックの前提条件

ライトバックを有効にして使用する前に、次の前提条件を満たす必要があります。

- Azure AD テナントで Azure AD Premium が有効になっている。 詳細については、次を参照してください。 [Azure Active Directory のエディション](active-directory-editions.md)します。
- テナントでパスワード リセットが構成され、有効になっている。 詳細については、を参照してください [ユーザーによる Azure AD パスワードのリセットを有効にする](#enable-users-to-reset-their-azure-ad-passwords)。
- 管理者アカウントと、この機能をテストするために使用できる Azure AD Premium ライセンスが付与されたテスト ユーザー アカウントが少なくとも 1 つずつある。 詳細については、次を参照してください。 [Azure Active Directory のエディション](active-directory-editions.md)します。
  > [AZURE.NOTE] パスワード ライトバックの有効化に使用する管理者アカウントは、クラウドの管理者アカウント (Azure AD で作成された) であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではありません。

- Windows Server 2008、Windows Server 2008 R2、Windows Server 2012、または Windows Server 2012 R2 を最新のサービス パックをインストールして実行している、1 つまたは複数のマルチフォレスト AD オンプレミスのデプロイを使用している。
  > [AZURE.NOTE] この機能をも使用できますが、する必要がありますが、古いバージョンの Windows Server 2008 または 2008 R2 を実行している場合 [をダウンロードしてインストール KB 2386717](https://support.microsoft.com/kb/2386717) 、クラウドでローカル AD パスワード ポリシーを適用する前にします。

- Azure AD Connect ツールをインストールし、クラウドに同期するための AD 環境が準備されている。 詳細については、次を参照してください。 [、クラウドの内部設置型の id インフラストラクチャを使用して](active-directory-aadconnect.md)します。
  > [AZURE.NOTE] パスワード ライトバックをテストする前に、Azure AD Connect で AD と Azure AD の両方から、フル インポートと完全同期を完了していることを確認してください。

- Azure AD Sync または Azure AD Connect を使用しているかどうかは  **TCP 443** 送信 (および場合によっては **TCP 9350-9354**) 開いておく必要があります。 参照してください [手順 3: ファイアウォールを構成する](#step-3-configure-your-firewall) の詳細。 このシナリオでの DirSync の使用はサポートされなくなりました。 DirSync をまだ使用している場合は、パスワード ライトバックをデプロイする前に Azure AD Connect の最新バージョンにアップグレードしてください。
  > [AZURE.NOTE] Azure AD Sync ツールまたは DirSync ツールを使用している場合、優れたエクスペリエンスと、リリースされる新しい機能を利用できるように、Azure AD Connect の最新バージョンにアップグレードすることを強くお勧めします。


### 手順 1: Azure AD Connect の最新バージョンをダウンロードする

パスワード ライトバックは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0419.0911** 以降の Azure AD Sync ツールで使用できます。 アカウントが自動的にロック解除されるパスワード ライトバックが使用できるのは、Azure AD Connect のリリース、またはバージョン番号が **1.0.0485.0222** 以降の Azure AD Sync ツールです。 以前のバージョンを実行している場合は、続行する前に、少なくともこのバージョンにアップグレードしてください。 [ここをクリックして、Azure AD Connect の最新バージョンをダウンロードする](active-directory-aadconnect.md#download-azure-ad-connect)します。

#### Azure AD Sync のバージョンを確認するには

1.  移動 **%ProgramFiles%\Azure Active Directory sync \**します。
2.  **ConfigWizard.exe** 実行可能ファイルを参照します。
3.  この実行可能ファイルを右クリックして、 コンテキスト メニューから [**プロパティ**] オプションを選択します。
4.  [**詳細**] タブをクリックします。
5.  [**ファイル バージョン**] フィールドを参照します。

    ![][021]

このバージョン番号より大きいまたは等しいかどうかは **1.0.0419.0911**, を Azure AD Connect をインストールしているかにスキップできます [手順 2: UI または powershell を使用して Azure AD Connect でパスワード ライトバックを有効にして、確認](#step-2-enable-password-writeback-in-azure-ad-connect)します。
 > [AZURE.NOTE] Azure AD Connect ツールを初めてインストールする場合は、いくつかのベスト プラクティスに従って、ディレクトリ同期の環境を準備することをお勧めします。 Azure AD Connect ツールをインストールする前にいずれかでディレクトリ同期をアクティブ化する必要があります、 [Office 365 管理ポータル](https://portal.microsoftonline.com) または [Azure 管理ポータル](https://manage.windowsazure.com)します。 詳細については、次を参照してください。 [を管理する Azure AD Connect の](active-directory-aadconnect-whats-next.md)します。


### 手順 2: Azure AD Connect でパスワード ライトバックを有効にする

Azure AD Connect ツールをダウンロードしたので、パスワード ライトバックを有効にする準備ができました。 次の 2 つの方法のいずれかで有効にします。 パスワード ライトバックは、Azure AD Connect のセットアップ ウィザードの [オプション機能] 画面で有効にできますし、Windows PowerShell を使用して有効にすることもできます。

#### 構成ウィザードでパスワード ライトバックを有効にするには

1.  **ディレクトリ同期コンピューター**上で、**Azure AD Connect** の構成ウィザードを開きます。
2.  [**オプション機能**] 構成画面が表示されるまで、クリックして進みます。
3.  [**パスワード ライトバック**] オプションをオンにします。

    ![][022]

4.  ウィザードを完了します。最後のページには、パスワード ライトバックの構成の変更を含む、変更の概要が表示されます。

> [AZURE.NOTE] このウィザードを再実行して、機能の選択を解除するか、設定、いつでもでパスワード ライトバックを無効にすることができます、 **内部設置型ディレクトリにときは、[パスワードの書き戻し** に設定 **いいえ** で、 **ユーザー パスワードのリセット ポリシー** セクションで、ディレクトリの **構成** ] タブで、 [Azure 管理ポータル](https://manage.windowsazure.com)です。 詳細については、パスワードのカスタマイズに関するリセットのエクスペリエンス、チェック アウト [カスタマイズ: Azure AD パスワード管理](active-directory-passwords-customize.md)します。

#### Windows PowerShell を使用して、パスワード ライトバックを有効にするには

1.  **ディレクトリ同期コンピューター**上で、新しい [**管理者特権の Windows PowerShell**] ウィンドウを開きます。
2.  モジュールがまだ読み込まれていない場合に入力、 `Import-module ADSync` に現在のセッションに、Azure AD Connect コマンドレットを読み込みます。
3.  実行して、システム内の AAD コネクタの一覧を取得、 `Get-adsyncconnector` コマンドレットの結果を保存して `$aadConnectorName`
4.  次のコマンドレットを実行して、現在のコネクタについてライトバックの現在の状態を取得する: `Get-adsyncaadpasswordresetconfiguration – Connector $aadConnectorName`
5.  コマンドレットを実行してパスワード ライトバックを有効にする: `セット ADSyncAADPasswordResetConfiguration – Connector $aadConnectorName: $true を有効にする`

> [AZURE.NOTE] 資格情報を求められた場合、AzureADCredential に指定した管理者アカウントが、**クラウドの管理者アカウント (Azure AD で作成された)** であり、フェデレーション アカウント (オンプレミスの AD で作成され、Azure AD に同期された) ではないことを確認します。
> [AZURE です。メモ] 渡しますが、上記の同じ手順を繰り返して、PowerShell を使用してパスワード ライトバックを無効にすることができます `$false` 手順でを設定したり、 **内部設置型ディレクトリにときは、[パスワードの書き戻し** に設定 **いいえ** で、 **ユーザー パスワードのリセット ポリシー] セクション** 、ディレクトリの **構成** ] タブで、 [Azure 管理ポータル](https://manage.windowsazure.com)します。

#### 構成が正常に完了したことを確認する

構成が正常に完了すると、[Windows PowerShell] ウィンドウに「パスワード リセット ライトバックが有効になりました。」というメッセージが表示されます。

サービスが正しくインストールされたことを確認するには、イベント ビューアーを開き、アプリケーション イベント ログに移動して、ソース ”**PasswordResetService**” でイベント ”**31005 - OnboardingEventSuccess**” が表示されているかを確認します。

  ![][023]

### 手順 3: ファイアウォールを構成する

Azure AD Connect ツールでパスワード ライトバックを有効にした後、サービスからクラウドに接続できることを確認する必要があります。

1.  インストールの完了後、ご使用の環境で不明な送信接続をブロックしている場合は、次の規則をファイアウォールに追加する必要があります。 これらの変更を行った後に、AAD Connect のコンピューターを必ず再起動してください。
   - TCP ポート 443 経由の送信接続を許可する
   - Https://ssprsbprodncu-sb.accesscontrol.windows.net/への発信接続を許可します。
   - プロキシを使用している場合や、一般的な接続問題が発生した場合は、TCP ポート 9350-9354 の送信接続を許可する

### 手順 4: Active Directory の適切な権限を設定する

ユーザーが含まれるすべてのフォレストの場合、X は、構成ウィザードでそのフォレストに対して (初期構成の場合) 中に指定されたアカウントのパスワードがリセットされる、X を指定する必要があります、 **パスワードのリセット**, 、**パスワードの変更**, 、**書き込みアクセス許可の** に `lockoutTime`, 、および **の書き込みアクセス許可** に `pwdLastSet`, 、そのフォレスト内の各ドメインのルート オブジェクトに対する権限を拡張します。 この権限は、すべてのユーザー オブジェクトにより継承されるものとしてマークされます。

上記のどのアカウントが参照されるか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、**[ソリューションの確認]** オプションをクリックします。 以下のスクリーンショットで、アクセス許可を追加する必要があるアカウントには赤色の下線が表示されています。

* *<font color="red">確認に設定した各ドメインに対してこのアクセス許可各フォレストで、システム、それ以外の場合のパスワードの書き戻しが正しく機能しません</font>。**

  ![][032]

  これらの権限を設定すると、パスワード管理が、フォレストに含まれるユーザー アカウントからではなく、各フォレストのMA サービス アカウントから可能になります。 これらの権限を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。 ここでは、[**Active Directory ユーザーとコンピューター**] 管理スナップインを使用した権限の設定方法の手順を詳しく説明します。
>[AZURE.NOTE] ディレクトリ内のすべてのオブジェクトにこれらの権限をレプリケートするには、最大 1 時間かかることがあります。

#### ライトバックを行うために適切な権限を設定するには

1.  適切なドメインの管理権限を持つアカウントで [**Active Directory ユーザーとコンピューター**] を開きます。
2.  [**表示メニュー**] オプションで、[**高度な機能**] がオンになっていることを確認します。
3.  左側のパネルで、ドメインのルートを表すオブジェクトを右クリックします。
4.  [**セキュリティ**] タブをクリックします。
5.  [**詳細設定**] をクリックします。

    ![][024]

6.  [**アクセス権限**] タブで [**追加**] をクリックします。

    ![][025]

7.  アクセス許可するアカウント (フォレストの同期をセットアップ中に指定したものと同じアカウント) を選択します。
8.  上部のドロップダウンで、[**下位ユーザー オブジェクト**] を選択します。
9.  **アクセス許可エントリ** 表示されるダイアログ ボックスのボックスをオンに **パスワードのリセット**, 、**パスワードの変更**, 、**の書き込みアクセス許可** に `lockoutTime`, 、および **書き込みアクセス許可の** に `pwdLastSet`します。

    ![][026]
    ![][027]
    ![][028]

10. 開いているすべてのダイアログ ボックスで**[適用] または [OK]**をクリックします。

### 手順 5: ユーザーとして AD パスワードをリセットする

これでパスワード ライトバックが有効になったので、その動作をテストできます。テストするには、アカウントがクラウド テナントと同期されているユーザーのパスワードをリセットします。

#### パスワード ライトバックの正常な動作を確認するには

1.  移動 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) または組織の ID ログイン画面に移動し、クリックして、 **アカウントにアクセスできない?** リンクします。

    ![][029]

2.  新しいページが表示され、パスワードをリセットするユーザー ID を求められます。 テスト ユーザー ID を入力し、パスワード リセット フローを続行します。
3.  パスワードをリセットすると、次のような画面が表示されます。 これは、オンプレミスのディレクトリまたはクラウド ディレクトリ、あるいは両方のディレクトリでパスワードが正常にリセットされたことを意味します。

    ![][030]

4.  操作が正常に完了したか、またはエラーが診断されたかを確認するには、**ディレクトリ同期コンピューター**に進んで [**イベント ビューアー**] を開き、[**アプリケーション イベント ログ**] に移動して、ソース ”**PasswordResetService**” でテスト ユーザーのイベント ”**31002 - PasswordResetSuccess**” が表示されているかを確認します。

    ![][031]


<br/>
<br/>
<br/>

## パスワードのリセットに関するドキュメントへのリンク

Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* [* * リセットするには、独自のパスワード * *](active-directory-passwords-update-your-own-password.md) -リセットまたはシステムのユーザーとして、自分のパスワードを変更する方法について説明
* [* * 方法には * *](active-directory-passwords-how-it-works.md) -サービスとの 6 つの異なるコンポーネントについて学習それぞれの機能
* [* * * * カスタマイズ](active-directory-passwords-customize.md) -、外観と、組織のニーズに合わせてサービスの動作をカスタマイズする方法について
* [* * ベスト プラクティス * *](active-directory-passwords-best-practices.md) -を迅速に展開し、組織内のパスワードを効果的に管理する方法について説明
* [* * Get * * insights](active-directory-passwords-get-insights.md) -統合レポート機能について
* [* * * * FAQ](active-directory-passwords-faq.md) -よく寄せられる質問に対する回答を得る
* [* * * * トラブルシューティング](active-directory-passwords-troubleshoot.md) -サービスに問題を迅速にトラブルシューティングする方法について説明
* [* * より * * について](active-directory-passwords-learn-more.md) - 掘り下げますサービスの機能の技術的な詳細




[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

