<properties
    pageTitle="Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定 | Microsoft Azure"
    description="Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用して、オンプレミス アプリケーションへの条件付きアクセスを有効にするための手順。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/24/2015"
    ms.author="femila"/>


# Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定

ユーザーに各自のデバイスを Azure Active Directory Device Registration サービスに社内参加させるよう求めることにより、ユーザーが個人的に所有しているデバイスを組織で認識済みとしてマークすることができます。 Windows Server 2012 R2 で Active Directory フェデレーション サービス (AD FS) を使用してオンプレミス アプリケーションへの条件付きアクセスを有効にするための手順を以下に示します。 

> [AZURE.NOTE]
> Azure Active Directory Device Registration Service の条件付きアクセス ポリシーに登録されているデバイスを使用する場合は、Office 365 のライセンスまたは Azure AD Premium ライセンスが必要になります。 これには、Active Directory フェデレーション サービス (AD FS) によってオンプレミス リソースに適用されるポリシーが含まれます。

内部設置型の条件付きアクセス シナリオの詳細については、次を参照してください。 [の SSO およびシームレスな第 2 要素認証企業アプリケーション間で任意のデバイスから職場への参加](https://technet.microsoft.com/library/dn280945.aspx)します。

これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

サポートされているデバイス
-------------------------------------------------------------------------
* Windows 7 ドメイン参加デバイス
* Windows 8.1 個人用およびドメイン参加デバイス
* iOS6 以降、Safari ブラウザー対応
* Android 4.0 以降、Samsung GS3 以降の携帯電話、Samsung Note2 以降のタブレット


シナリオの前提条件
------------------------------------------------------------------------
* Office 365 または Azure Active Directory Premium のサブスクリプション
* Azure Active Directory テナント
* Windows Server Active Directory (Windows Server 2008 以降)
* Windows Server 2012 R2 の更新されたスキーマ
* Azure Active Directory Premium のサブスクリプション
* Azure AD に対する SSO 用に構成された Windows Server 2012 R2 フェデレーション サービス
* Windows Server 2012 R2 Web アプリケーション プロキシ Microsoft Azure Active Directory Connect (Azure AD Connect)。 [Azure AD Connect は、ここをダウンロード](http://www.microsoft.com/en-us/download/details.aspx?id=47594)します。
* 確認済みドメイン 

このリリースの既知の問題
-------------------------------------------------------------------------------
* デバイス ベースの条件付きアクセス ポリシーには、Azure Active Directory から Active Directory へのデバイス オブジェクトの書き戻しが必要です。 デバイス オブジェクトを Active Directory に書き戻すには、最大 3 時間かかる可能性があります。
* iOS7 デバイスの場合、クライアント証明書の認証時に、常に証明書の選択が求められます。 
* iOS8.3 以前の iOS8 の一部のバージョンは機能しない場合があります。 

## シナリオの前提条件
このシナリオでは、Azure AD テナントとローカルの Active Directory で構成されたハイブリッド環境があることを前提としています。 これらのテナントは、Azure AD の接続を使用して、確認済みドメインと SSO 対応 AD FS を介して接続されている必要があります。 次のチェックリストを使用すると、上記で説明した段階に環境を構成できます。 

チェックリスト: 条件付きアクセスのシナリオの前提条件
--------------------------------------------------------------
ローカルの Active Directory と Azure AD テナントを接続します。 

## Azure Active Directory Device Registration サービスの構成
組織用に Azure Active Directory Device Registration Service をデプロイおよび構成するには、以下のガイドに従います。

ここでは、Windows Server Active Directory の構成および Microsoft Azure Active Directory へのサブスクライブが完了していることを前提とします。 前記の前提条件をご覧ください。

Azure Active Directory Device Registration サービスを Azure Active Directory テナントと一緒にデプロイするには、以下のチェックリストに示す作業を順番に実行します。 参照リンクをクリックして概要に関するトピックに移動した場合は、そのトピックを確認した後、このチェックリストに戻って、チェックリストの残りの作業に進んでください。 一部のタスクには、手順が正常に完了したことを確認できるシナリオの検証手順が含まれています。

## パート 1: Azure Active Directory Device Registration を有効にする

以下のチェックリストに従って、Azure Active Directory Device Registration サービスを有効にして構成します。

| タスク                                                                                                                                                                                                                                                                                                                                                                                             | リファレンス                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Azure Active Directory テナント内の [デバイスの登録] を有効にして、デバイスがワークプレースに参加できるようにします。 既定では、サービスに対して Multi-Factor Authentication は有効になっていません。 ただし、デバイスを登録するときは Multi-Factor Authentication をお勧めします。 ADRS で Multi-Factor Authentication を有効にする前に、Multi-Factor Authentication プロバイダー用に AD FS が構成されていることを確認します。 | [Azure Active Directory Device Registration を有効にする](active-directory-conditional-access-device-registration-overview.md)               |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory Device Registration サービスを検出します。 デバイスが Azure Active Directory Device Registration サービスを検出できるように、会社の DNS を構成する必要があります。                                                                                                                                                   | [Azure Active Directory Device Registration の検出を構成する](active-directory-conditional-access-device-registration-overview.md) |

##パート 2: Windows Server 2012 R2 Active Directory フェデレーション サービスをデプロイおよび構成し、Azure AD とのフェデレーション関係をセットアップする

| タスク                                                                                                                                                                                                                                                                                                                                                                                             | リファレンス                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Windows Server 2012 R2 スキーマ拡張で Active Directory ドメイン サービス ドメインをデプロイします。 ドメイン コント ローラーを Windows Server 2012 R2 にアップグレードする必要はありません。 スキーマのアップグレードのみが必要です。 | [Active Directory ドメイン サービス スキーマをアップグレードする](#Active Directory ドメイン サービス スキーマをアップグレードする)               |
| デバイスは既知の DNS レコードを探すことにより、Azure Active Directory Device Registration サービスを検出します。 デバイスが Azure Active Directory Device Registration サービスを検出できるように、会社の DNS を構成する必要があります。                                                                                                                                                   | [デバイスをサポートするために Active Directory の準備を行う](#デバイスをサポートするために Active Directory の準備を行う) |


##パート 3: Azure AD でのデバイスの書き戻しを有効にする

| タスク                                                                                                                                                                                                                                                                                                                                                                                             | リファレンス                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 「Azure AD Connect でのデバイスの書き戻しの有効化」のパート 2 を完了します。 完了したら、このガイドに戻ります。 | [Azure AD Connect でのデバイスの書き戻しの有効化](#Active Directory ドメイン サービス スキーマをアップグレードする)               |
     

##[省略可能] パート 4: 多要素認証を有効にする

多要素認証に関するいくつかのオプションのいずれかを構成することを強くお勧めします。 MFA を必要とする場合は、「 [多要素のセキュリティ ソリューションを選択して](multi-factor-authentication-get-started.md)います。 これには、各ソリューションの説明と、選択したソリューションの構成に役立つリンクが含まれています。 

## パート 5: 検証

これでデプロイは完了しました。 いくつかのシナリオを試せるようになりました。 以下のリンクに従って、サービスを試し、機能をよく理解してください。


| タスク                                                                                                                                                                                                                         | リファレンス                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Azure Active Directory Device Registration を使用して、いくつかのデバイスをワークプレースに参加させます。 iOS、Windows、Android の各デバイスを参加させることができます。                                                                                         | [Azure Active Directory Device Registration を使用してワークプレースにデバイスを参加させる](#Azure Active Directory Device Registration を使用してワークプレースにデバイスを参加させる) |
| 管理者ポータルを使用して、登録されているデバイスを表示し、有効または無効にできます。 このタスクでは、管理者ポータルを使用して一部の登録済みデバイスを表示します。                                                        | [Azure Active Directory Device Registration Overview](active-directory-conditional-access-device-registration-overview.md)                             |
| デバイス オブジェクトが Azure Active Directory から Windows Server Active Directory に書き戻されることを確認します。                                                                                                                  | [登録済みのデバイスが Active Directory に書き戻されていること確認する](#登録済みのデバイスが Active Directory に書き戻されていること確認する)                  |
| ユーザーがデバイスを登録できるようになったので、AD FS で登録済みデバイスのみを許可するアプリケーション アクセス ポリシーを作成できます。 このタスクでは、アプリケーション アクセス ルールと、カスタム アクセス拒否メッセージを作成します。 | [アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する)            |



## Azure Active Directory をローカルの Active Directory と統合する
これにより、Azure AD Connect を使用して Azure AD テナントをローカルの Active Directory と統合できます。 これらの手順は Azure ポータルで使用できますが、このセクションに記載されている特別な指示に留意してください。 

1.  Azure ポータルに管理者としてサインインします。
2.  左側のウィンドウで次のように選択します。 **Active Directory**します。
3.   **ディレクトリ** ] タブで、ディレクトリを選択します。
4.  選択、 **ディレクトリ統合** ] タブをクリックします。
5.   **展開および管理** セクションで、手順 1. ~ 3. をローカル ディレクトリと Azure Active Directory を統合します。
  1.    ドメインを追加します。
  2.    インストールし、Azure AD Connect を実行します。 インストールの Azure AD Connect の次の手順に従って [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)します。
  3. ディレクトリの同期を確認および管理します。 この手順では、シングル サインオンの手順を利用できます。
  >[AZURE.NOTE] 上のリンクから、ドキュメントで説明したように AD FS フェデレーションを構成します。 
  >[AZURE.NOTE] プレビュー機能のいずれかを構成する必要はありません。
  
   


## Active Directory ドメイン サービス スキーマをアップグレードする
> [AZURE.NOTE]
> Active Directory スキーマのアップグレードを行うと、それを元に戻すことはできません。 これは最初にテスト環境で実行することをお勧めします。

1. エンタープライズ管理者権限とスキーマ管理者権限を両方とも有するアカウントでドメイン コントローラーにログインします。
2. コピー、 **[media] \support\adprep** ディレクトリとサブディレクトリに、Active Directory ドメイン コント ローラーのいずれかです。 
3. ここで、[media] は Windows Server 2012 R2 インストール メディアへのパスです。
4. コマンド プロンプトから adprep ディレクトリに移動し、実行: **adprep.exe/forestprep**します。 画面の手順に従ってスキーマのアップグレードを完了します。

## デバイスをサポートするために Active Directory の準備を行う
>[AZURE.NOTE] これは、1 回限りの操作のデバイスをサポートするために、Active Directory フォレストを準備するために実行する必要があります。 この手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。


##デバイスをサポートするために Active Directory フォレストの準備を行う

> [AZURE.NOTE]
>これは、デバイスをサポートするために Active Directory フォレストを準備する際に実行する必要のある 1 回限りの操作です。 この手順を完了するには、エンタープライズ管理者権限を使用してログオンする必要があり、Active Directory フォレストに Windows Server 2012 R2 スキーマが組み込まれている必要があります。

### Active Directory フォレストを準備する

1.  フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。
     Initialize ADDeviceRegistration
2.  入力を求められたら **ServiceAccountName**, 、AD FS のサービス アカウントとして選択したサービス アカウントの名前を入力します。 GMSA アカウントである場合にアカウントを入力して、 **domain \accountname$** 形式です。 ドメイン アカウントの形式を使用して **domain \accountname**します。



### AD FS でデバイス認証を有効にする

1. フェデレーション サーバーで、[AD FS 管理コンソールを開きに移動 **AD FS** > **認証ポリシー**します。
2. 選択 **グローバル プライマリ認証の編集.**  **アクション** ウィンドウです。
3. 確認 **デバイス認証を有効にする** し、[**OK**します。
4. 既定では、AD FS は未使用のデバイスを Active Directory から定期的に削除します。 Azure でデバイスを管理できるように Azure Active Directory Device Registration を使用する場合は、この動作を無効にする必要があります。


### 未使用のデバイスのクリーンアップを無効にする
1. フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。
    セット AdfsDeviceRegistration MaximumInactiveDays 0

### デバイスの書き戻しを行うために Azure AD Connect を準備する

1.  「パート 1: AAD Connect を準備する」を完了します。 


## Azure Active Directory Device Registration を使用してデバイスをワークプレースに参加させる

### Azure Active Directory Device Registration を使用して iOS デバイスを参加させる

Azure Active Directory Device Registration では、iOS デバイスに対して無線プロファイル登録プロセスを使用します。 このプロセスは、ユーザーによる Safari Web ブラウザーからプロファイル登録用 URL への接続で始まります。 URL の形式は次のとおりです。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

ここで `yourdomainname` は、Azure Active Directory で構成したドメイン名です。 たとえば、ドメイン名が contoso.com の場合、URL は次のようになります。

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

この URL は、さまざまな方法ユーザーに伝えることができます。 その 1 つとして、AD FS 内のカスタム アプリケーション アクセス拒否メッセージで、この URL を発行する方法が推奨されます。 これについては、以降のセクションで説明します: [アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する](#アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する)。

###Azure Active Directory Device Registration を使用して Windows 8.1 デバイスを参加させる

1. Windows 8.1 デバイス上に移動 **PC 設定** > **ネットワーク** > **ワークプ レース**します。
2. ユーザー名を UPN 形式で入力します。 たとえば、dan@contoso.com.
3. 選択 **参加**します。
4. メッセージが表示されたら、資格情報でサインインします。 これでデバイスが参加します。

### Azure Active Directory Device Registration を使用して Android デバイスを参加させる

 [Android トピックの Azure Authenticator](active-directory-conditional-access-azure-authenticator-app.md) Azure authenticator アプリを Android デバイスにインストールし、仕事用アカウントを追加する方法手順が記載されています。 Android デバイスで仕事用アカウントが正常に作成されると、そのデバイスを組織に社内参加させることができます。

## 登録済みのデバイスが Active Directory に書き戻されていること確認する
LDP.exe または ADSI Edit を使用して、デバイス オブジェクトが Active Directory に書き戻されたことを表示し、確認することができます。 どちらも、Active Directory 管理ツールで利用できます。

既定では、Azure Active Directory から書き戻されるデバイス オブジェクトは、AD FS ファームと同じドメインに配置されます。

    CN=RegisteredDevices,defaultNamingContext

## アプリケーションのアクセス ポリシーとカスタム アクセス拒否メッセージを作成する
次のシナリオを考慮します。AD FS でアプリケーションの証明書利用者の信頼を作成し、登録されたデバイスのみを許可する発行承認規則を構成します。 これで、登録されたデバイスのみにアプリケーションへのアクセスが許可されます。 ユーザーがアプリケーションに容易にアクセスできるようにするには、ユーザーのデバイスを参加させる手順を含むカスタム アクセス拒否メッセージを構成します。 これで、ユーザーはアプリケーションにアクセスするために、シームレスな方法でデバイスを登録できます。

次の手順に、このシナリオを実装する方法を示します。
>[AZURE.NOTE]
ここでは、AD FS でアプリケーションに対して証明書利用者の信頼を構成済みであると想定します。

1. AD FS MMC ツールを開き、[AD FS] > [信頼関係] > [証明書利用者の信頼] の順に移動します。
2. この新しいアクセス ルールが適用されるアプリケーションを特定します。 アプリケーションを右クリックし、[要求規則の編集...] を選択します。
3. 選択、 **発行承認規則の** ] タブを選択し、 **ルールの追加.**
4.  **要求規則** テンプレートのドロップダウン リストで、 **に基づいて許可または拒否するユーザー入力方向の要求**します。 選択 **次**します。
5. [要求規則名: フィールドの種類: **登録済みのデバイスからのアクセス許可**
6. 受信要求の種類: ドロップダウン リストで、 **登録ユーザー**します。
7. 受信要求の値: フィールドに「: **は true。**
8. 選択、 **この入力方向の要求によるユーザーへのアクセスを許可** オプション ボタンをクリックします。
9. 選択 **完了** し、[ **適用**します。
10. 作成した規則よりも寛容なルールはすべて削除します。 たとえば、既定値を削除 **すべてのユーザーにアクセスを許可** ルール。

これで、ユーザーが自身で登録しワークプレースに参加させたデバイスからアクセスする場合に限り、アクセスを許可するようにアプリケーションが構成されます。 高度なアクセス ポリシーを参照してください [多要素アクセス制御によるリスク管理](https://technet.microsoft.com/library/dn280949.aspx)します。

次に、アプリケーションのカスタム エラー メッセージを構成します。 エラー メッセージは、アプリケーションにアクセスするには事前にユーザーのデバイスをワークプレースに参加させなければならないことをユーザーに通知するものです。 カスタム HTML および Windows PowerShell を使用してカスタム アプリケーション アクセス拒否メッセージを作成できます。

フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のコマンドを入力します。 コマンドの一部をシステムに固有の項目に置き換えます。

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
このアプリケーションにアクセスするには、事前にデバイスを登録する必要があります。

**IOS デバイスを使用している場合は、デバイスを参加させるには、このリンクを選択します**:。 

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

この iOS デバイスを職場に参加させる。


**Windows 8.1 デバイスを使用している場合**, に移動して、デバイスを参加させることができます * * [PC 設定 * * > **ネットワーク** > **ワークプ レース**します。


ここで"**証明書利用者 trust name**"AD FS で、アプリケーションの証明書利用者の信頼オブジェクトの名前を指定します。 
ここで **yourdomain.com** Azure Active Directory を構成したドメイン名です。 たとえば、contoso.com です。 
渡す html コンテンツからすべての改行 (もしあれば) を削除してください、 **Set-adfsrelyingpartywebcontent** コマンドレットです。


これで、ユーザーが Azure Active Directory Device Registration Service に登録していないデバイスからアプリケーションにアクセスすると、以下のスクリーン ショットのようなページが表示されるようになります。 

![ユーザーがデバイスを Azure AD に登録していなかった場合に表示されるエラーのスクリーン ショット](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

