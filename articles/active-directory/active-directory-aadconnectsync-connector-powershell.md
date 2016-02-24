<properties
   pageTitle="Azure AD Connect 同期: PowerShell コネクタ"
   description="この記事では、Microsoft の Windows PowerShell コネクタを構成する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="andkjell"/>

# Windows PowerShell コネクタに関するテクニカル リファレンス

この記事では、Windows PowerShell コネクタについて説明します。 この記事は次の製品に適用されます。

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   4.1.3461.0 修正プログラムを使用する必要がありますか、後で [KB2870703](https://support.microsoft.com/kb/2870703)します。

MIM2016 および FIM2010R2、コネクタはからダウンロードできる、 [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)します。

## PowerShell コネクタの概要

PowerShell コネクタを利用すれば、Windows PowerShell ベースの API (アプリケーション プログラミング インターフェイス) を提供する外部システムと同期サービスを統合できます。 このコネクタは、呼び出しベースの ECMA2 (Extensible Connectivity Management Agent 2) フレームワークと Windows PowerShell の機能の橋渡しとなります。 ECMA フレームワークの詳細については、次を参照してください。、 [拡張可能な接続 2.2 管理エージェントのリファレンス](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx)します。

### 前提条件

コネクタを使用する前に、前述した修正プログラムに加えて次のものが同期サーバーにインストールされていることを確認してください。

- Microsoft .NET 4.5.2 Framework 以降
- Windows PowerShell 2.0、3.0、4.0

同期サービスのサーバーの実行ポリシーで、Windows PowerShell スクリプトの実行をコネクタに許可するように構成する必要があります。 コネクタが実行するスクリプトにデジタル署名がない場合を除き、このコマンドを実行して実行ポリシーを構成します。

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## 新しいコネクタの作成

同期サービスで Windows PowerShell コネクタを作成するには、同期サービスで要求される手順を実行する一連の Windows PowerShell スクリプトを提供する必要があります。 接続先のデータ ソースや必要とする機能により、実装しなければならないスクリプトが変わります。 このセクションでは、実装可能なスクリプトとそれが必要となる状況について説明します。

Windows PowerShell コネクタは、同期サービスのデータベース内に各スクリプトを保存するように設計されています。 ファイル システムに保存されているスクリプトを実行することはできますが、各スクリプトの本文をコネクタの構成に直接挿入する方がはるかに簡単です。

PowerShell コネクタを作成する **同期サービス** 選択 **管理エージェント** と **作成**します。 選択、 **PowerShell (マイクロソフト)** コネクタです。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### 接続

次に、リモート システムに接続するための構成パラメーターを指定できます。 これらのパラメーターは同期サービスにより安全に保存され、コネクタが実行されるとき、Windows PowerShell スクリプトで利用できます。

![接続](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

次の接続パラメーターを構成できます。

**接続**

| パラメーター | 既定値 | 目的 |
| --- | --- | --- |
| サーバー | <Blank> | コネクタの接続先のサーバーの名前です。 |
| ドメイン | <Blank> | コネクタの実行時に使用するために保存する資格情報のドメインです。 |
| ユーザー | <Blank> | コネクタの実行時に使用するために保存する資格情報のユーザー名です。 |
| パスワード | <Blank> | コネクタの実行時に使用するために保存する資格情報のパスワードです。 |
| コネクタ アカウントの偽装 | False | True の場合、同期サービスは上記で指定された資格情報のコンテキストで Windows PowerShell スクリプトを実行します。 可能であれば、各スクリプトに渡される $Credentials パラメーターを偽装の代わりに使用することが推奨されます。 このパラメーターを使用するために必要な追加のアクセス許可の詳細については、「偽装の追加構成」を参照してください。 |
| 偽装時のユーザー プロファイルの読み込み | False | 偽装中に、コネクタの資格情報のユーザー プロファイルの読み込むように Windows に指示します。 偽装するユーザーにローミング プロファイルがある場合、コネクタはローミング プロファイルを読み込みません。 このパラメーターを使用するために必要な追加のアクセス許可の詳細については、「偽装の追加構成」を参照してください。 |
| 偽装時のログオンの種類 | なし | 偽装中のログオンの種類 詳細については、[dwLogonType] [dw] ドキュメントを参照してください。 |
| 署名済みスクリプトのみ | False | True の場合、Windows PowerShell コネクタは、各スクリプトに有効なデジタル署名があることを検証します。 False の場合、同期サービス サーバーの Windows PowerShell 実行ポリシーを「RemoteSigned」または「Unrestricted」にします。 |

**共通モジュール**

このコネクタでは、管理者は共有 Windows PowerShell モジュールを構成に保存できます。 コネクタがスクリプトを実行するとき、各スクリプトでインポートできるように、Windows PowerShell モジュールがファイル システムに抽出されます。

インポート、エクスポート、パスワード同期のスクリプトについては、共通モジュールはコネクタの MAData フォルダーに抽出されます。 スキーマ、検証、階層、パーティションの探索スクリプトについては、共通モジュールは %TEMP% フォルダーに抽出されます。 いずれの場合も、抽出された共通モジュール スクリプトには共通モジュール スクリプト名設定に基づいて名前が付けられます。

MAData フォルダーから FIMPowerShellConnectorModule.psm1 という名前のモジュールをロードするには、次のステートメントを使用します。
`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

FIMPowerShellConnectorModule.psm1 を %TEMP% フォルダーから呼び出されたモジュールをロードするには、次のステートメントを使用します。
`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**パラメーター検証**

検証スクリプトは、管理者が指定したコネクタ構成パラメーターが有効であることを確認するために使用できるオプションの Windows PowerShell スクリプトです。 サーバー、接続資格情報、接続パラメーターの検証が検証スクリプトの共通の使用方法です。 検証スクリプトは、次のタブとダイアログの変更後に呼び出されます。

- 接続
- グローバル パラメーター
- パーティション構成

検証スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameterPage | [ConfigParameterPage][cpp] | 検証要求をトリガーした [構成] タブまたはダイアログ。 |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |

検証スクリプトは、1 つの ParameterValidationResult オブジェクトをパイプラインに返します。

**スキーマ検出**

スキーマ検出スクリプトは必須です。 このスクリプトは、属性フロー ルールの構成時に同期サービスで利用されるオブジェクトの種類、属性、属性制約を返します。 スキーマ検出スクリプトはコネクタ作成中に実行され、コネクタのスキーマにデータを入力し、その後、同期サービス マネージャーのスキーマ更新機能にデータを入力します。

スキーマ検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |

スクリプトは、パイプラインに 1 つの [スキーマ] [schema] オブジェクトを返す必要があります。 スキーマ オブジェクトは、オブジェクトの種類 (例: ユーザー、グループなど) を表す [SchemaType] [schemaT] のオブジェクトで構成されます。 SchemaType オブジェクトは、型の属性 (たとえば、指定された名前、姓、住所の郵便番号など) を表す [SchemaAttribute] [schemaA] オブジェクトのコレクションを格納します。

**追加パラメーター**

ここまでで説明した標準的な構成設定に加え、コネクタのインスタンスに固有のカスタム構成設定を追加で定義できます。 追加パラメーターはコネクタ、パーティション、実行ステップのレベルで指定し、関連 Windows PowerShell スクリプトからアクセスできます。 カスタム構成設定は同期サービス データベースに平文で保存するか、暗号化して保存できます。 同期サービスは、必要に応じて、安全な構成設定を自動的に暗号化し、復号化します。

カスタム構成設定を指定するには、コンマ (,) で各パラメーターの名前を区切ります。

スクリプトからカスタム構成設定にアクセスするには、名に、アンダー スコア (\) と (グローバル、パーティション、または実行手順) のパラメーターのスコープをサフィックスする必要があります。 たとえば、Global FileName パラメーターにアクセスするには、このコード スニペットを使用します。 `$ConfigurationParameters["FileName_Global"].Value`

### 機能

管理エージェント デザイナーの [機能] タブでコネクタの動作と機能を定義します。 コネクタの作成後、このタブの選択を変更することはできません。 次の表は、各機能の設定をまとめたものです。

![機能](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| 機能 | 説明 |
| --- | --- |
| [識別名のスタイル][dnstyle] | コネクタで識別名を利用できるかどうかと、利用できる場合、その様式を示します。 |
| [エクスポートの種類][exportT] | エクスポート スクリプトに表示されるオブジェクトの種類を決定します。 <li>AttributeReplace – には、属性が変更されたときに、複数値属性の値の完全なセットが含まれています。</li><li>AttributeUpdate – には、属性が変更されたときに、複数値属性の差分だけが含まれています。</li><li>MultivaluedReferenceAttributeUpdate - には、非参照型複数値属性の値と複数値の参照属性に対するデルタのみの完全なセットが含まれています。</li><li>ObjectReplace – すべてが含まれますオブジェクトの属性のいずれかの属性の変更時に</li> |
| [データの正規化][DataNorm] | スクリプトに提供する前にアンカー属性を正規化するように同期サービスに指示します。 |
| [オブジェクトの確認][oconf] | 同期サービスの保留中のインポートの動作を構成します。 <li>通常 – インポートを使用して確認するにはエクスポートされたすべての変更が必要ですが、既定の動作</li><li>NoDeleteConfirmation – オブジェクトが削除されるは生成された保留中のインポートです。</li><li>NoAddAndDeleteConfirmation – オブジェクトの作成または削除された場合は生成された保留中のインポート、</li>
| アンカーとして DN を使用する | 識別名の様式が LDAP に設定されている場合、コネクタ スペースのアンカー属性も識別名になります。 |
| 複数のコネクタの同時操作 | 選択した場合、複数の Windows PowerShell コネクタを同時に実行できます。 |
| パーティション | 選択した場合、コネクタは複数のパーティションとパーティション検出に対応します。 |
| 階層 | 選択した場合、コネクタは LDAP 様式の階層構造に対応します。 |
| インポートの有効化 | 選択した場合、コネクタはインポート スクリプトでデータをインポートします。 |
| 差分インポートの有効化 | 選択した場合、コネクタはインポート スクリプトから差分を要求できます。 |
| エクスポートの有効化 | 選択した場合、コネクタはエクスポート スクリプトでデータをエクスポートします。 |
| 完全エクスポートの有効化 | 選択した場合、エクスポート スクリプトはコネクタ スペース全体のエクスポートに対応します。 このオプションを使用するには、エクスポート有効化も選択する必要があります。|
| 最初のエクスポート パスに参照値なし | 選択した場合、参照属性は 2 番目のエクスポート パスでエクスポートされます。 |
| オブジェクトの名前変更の有効化 | 選択した場合、識別名を変更できます。 |
| 置換として削除/追加 | 選択した場合、削除/追加操作が 1 つの置換としてエクスポートされます。 |
| パスワード操作の有効化 | 選択した場合、パスワード同期スクリプトを利用できます。 |
| 最初のパスのエクスポート パスワードの有効化 | 選択した場合、プロビジョニング中に設定されたパスワードがオブジェクトの作成時にエクスポートされます。 |

### グローバル パラメーター

管理エージェント デザイナーの [グローバル パラメーター] タブでは、管理者は、コネクタで実行される各 Windows PowerShell スクリプトと [接続] タブで定義されているカスタム構成設定のグローバル値を構成できます。

**パーティション検出**

パーティションは、1 つの共有スキーマ内の別個の名前空間です。 たとえば、Active Directory では、各ドメインは 1 つのフォレスト内のパーティションになります。 パーティションは、インポート操作とエクスポート操作を論理的にグループ化します。 インポートとエクスポートにはコンテキストとしてパーティションがあり、すべての操作をそのコンテキストで行う必要があります。 パーティションは LDAP の階層を表します。 パーティションの識別名は、返されたすべてのオブジェクトがパーティションの範囲内にあることを検証するためにインポートで使用されます。 パーティションの識別名は、メタバースからコネクタ スペースにプロビジョニングし、エクスポート中にオブジェクトを関連付けるパーティションを決定する際にも使用されます。

パーティション検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters  | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |

スクリプトに返す必要か、単一の [パーティション] [一部] オブジェクトまたはパーティションのオブジェクトの一覧 [T] パイプラインです。

**階層検出**

階層検出スクリプトは、識別名様式の機能が LDAP の場合にのみ使用されます。 このスクリプトは、インポート/エクスポート操作の対象とする/対象としない一連のコンテナーを閲覧し、選択することを管理者に許可するために使用されます。 このスクリプトは、スクリプトに指定されたルート ノードの直接的な子となるノードの一覧のみを提供します。

階層検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| ParentNode | [HierarchyNode][hn] | 階層のルート ノードであり、この下にある直接の子をスクリプトが返します。 |

スクリプトは 1 つの子 HierarchyNode オブジェクトか子 HierarchyNode オブジェクトの List[T] を返します。

#### インポート

インポート操作をサポートするコネクタは、3 つのスクリプトを実装する必要があります。

**インポート開始**

インポート開始スクリプトは、インポート実行手順の最初に実行されます。 この手順の間、ソース システムに接続し、接続したシステムからデータをインポートする前の準備手順を実行できます。

インポート開始スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。
| 型 | [スキーマ][スキーマ] | インポートするコネクタ スペースのスキーマ。 |

スクリプトは、パイプラインに 1 つの [OpenImportConnectionResults] [oicres] オブジェクトを返す必要があります。 下のサンプル コードは、OpenImportConnectionResults オブジェクトがパイプラインに返されるしくみを示しています。

`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**データのインポート**

データのインポート スクリプトは、インポートするデータがなくなったことをスクリプトが示し、差分インポート中に同期サービスで完全なオブジェクト インポートを要求する必要がなくなるまでコネクタにより呼び出されます。 Windows PowerShell コネクタのページ サイズは 9,999 オブジェクトです。 スクリプトがインポートするオブジェクトを 9,999 個以上返す場合、ページングを利用する必要があります。 コネクタは、透かしを保存するためのカスタム データ プロパティを公開します。透かしを保存すると、データのインポート スクリプトが呼び出されるたびに、スクリプトが前回中断した部分からオブジェクトのインポートが再開されます。

データのインポート スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| GetImportEntriesRunStep | [ImportRunStep][内国] | ページングされたインポートと差分インポートで使用される透かし (CustomData) を保持します。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。 |
| 型 | [スキーマ][スキーマ] | インポートするコネクタ スペースのスキーマ。 |

データ スクリプトのインポートは、[CSEntryChange] [csec] の一覧オブジェクトをパイプラインに書き込む必要があります。 このコレクションは、インポートされる各オブジェクトを表す CSEntryChange 属性で構成されます。 完全インポート実行の間、このコレクションには、個々のオブジェクトすべての属性を持つ CSEntryChange オブジェクトの完全セットが与えられます。 差分インポートの間、CSEntryChange オブジェクトには、インポートする各オブジェクトの属性レベル差分か変更された完全なオブジェクト (置換モード) が含まれます。

**インポート終了**

インポート実行の終わりに、インポート終了スクリプトが実行されます。 このスクリプトで必要なクリーンアップ作業が実行されます (システムへの接続の終了やエラーへの対応など)。

インポート終了スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。 |
| CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | インポートが終了した理由をスクリプトに通知します。 |

スクリプトは、パイプラインに 1 つの [CloseImportConnectionResults] [cicres] オブジェクトを返す必要があります。 次のサンプル コードでは、パイプラインに CloseImportConnectionResults オブジェクトを返す方法を示しています。
`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### エクスポート

コネクタのインポート アーキテクチャと同じです。エクスポートをサポートするコネクタは 3 つのスクリプトを実装する必要があります。

**エクスポート開始**

エクスポート開始スクリプトは、エクスポート実行手順の最初に実行されます。 この手順の間、ソース システムに接続し、接続したシステムからデータをエクスポートする前の準備手順を実行できます。

エクスポート開始スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| 型 | [スキーマ][スキーマ] | エクスポートするコネクタ スペースのスキーマ。 |

スクリプトはパイプラインに出力を返しません。

**データのエクスポート**

同期サービスは、保留中のエクスポートをすべて処理するために必要な回数だけデータのエクスポート スクリプトを呼び出します。 コネクタ スペースにコネクタのページ サイズを超える保留中エクスポートがあるかどうか、参照属性やパスワードがあるかどうかに基づき、データのエクスポート スクリプトは複数回呼び出されます。同じオブジェクトに対して複数回呼び出されることもあります。

データのエクスポート スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。|
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。|
| CSEntries | IList [CSEntryChange] [csec] | すべてのコネクタ スペース オブジェクトとこのパスで処理する保留中エクスポートを一覧表示します。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| 型 | [スキーマ][スキーマ] | エクスポートするコネクタ スペースのスキーマ。 |

エクスポート データ スクリプトでは、パイプラインに [PutExportEntriesResults] [peeres] オブジェクトを返す必要があります。 このオブジェクトには、エラーまたはアンカー属性の変更が発生しない限り、エクスポートされたコネクタごとに結果情報を含める必要はありません。

次のサンプル コードでは、パイプラインに PutExportEntriesResults オブジェクトを返す方法を示しています。
`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**エクスポート終了**

エクスポート実行の終わりに、エクスポート終了スクリプトが実行されます。 このスクリプトで必要なクリーンアップ作業が実行されます (システムへの接続の終了やエラーへの対応など)。

エクスポート終了スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | エクスポートが終了した理由をスクリプトに通知します。 |

スクリプトはパイプラインに出力を返しません。

#### パスワードの同期

Windows PowerShell コネクタをパスワードの変更/リセットのターゲットとして利用できます。

パスワード スクリプトは、コネクタから次のパラメーターを受け取ります。

| 名前 | データ型 | 説明 |
| --- | --- | --- |
| ConfigParameters | [KeyedCollection][keyk][文字列 [ConfigParameter] [cp] | コネクタの構成パラメーターのテーブル。 |
| 資格情報 | [PSCredential][pscred] | [接続] タブで管理者が入力した資格情報が含まれます。 |
| Partition | [パーティション][一部] | CSEntry が入るディレクトリ パーティション。 |
| CSEntry | [CSEntry][cse] | パスワードの変更またはリセットを受け取ったオブジェクトのコネクタ スペース エントリ。 |
| OperationType | String | 操作が、リセットであることを示します (**SetPassword**) の変更、または (**ChangePassword**)。 |
| PasswordOptions | [PasswordOptions][pwdopt] | 目的のパスワードのリセット動作を指定するフラグ。 このパラメーターは OperationType 場合のみ **SetPassword**します。 |
| OldPassword | String | パスワード変更のためにオブジェクトの古いパスワードが入力されます。 このパラメーターは OperationType 場合のみ **ChangePassword**します。 |
| NewPassword | String | スクリプトで設定するオブジェクトの新しいパスワードが入力されます。 |

パスワード スクリプトは、Windows PowerShell パイプラインに結果を返しません。 パスワード スクリプトでエラーが発生した場合、スクリプトは次のいずれかの例外をスローし、同期サービスに問題を通知します。

- [PasswordPolicyViolationException][pwdex1] – パスワードは接続のシステムでは、パスワード ポリシーを満たしていない場合にスローされます。
- [PasswordIllFormedException][pwdex2] – パスワードが接続されているシステムで許容できる場合にスローされます。
- [PasswordExtension][pwdex3] – パスワード スクリプト内の他のすべてのエラーがスローされます。

## サンプル コネクタ

使用可能なサンプルのコネクタの完全な概要については、[Windows PowerShell コネクタ サンプル コネクタ コレクション] [samp] を参照してください。

## 他の注意事項

### 偽装のための追加構成

偽装されるユーザーに同期サービス サーバーで次のアクセス許可を与えます。

次のレジストリ キーの読み取りアクセス:

- HKEY_USERS\\ [SynchronizationServiceServiceAccountSID] \Software\Microsoft\PowerShell
- HKEY_USERS\\ [SynchronizationServiceServiceAccountSID] \Environment

同期サービスのサービス アカウントのセキュリティ ID (SID) を決定するには、次の PowerShell コマンドを実行します。

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

次のファイル システム フォルダーの読み取りアクセス:

- %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\MaData\ < ConnectorName >

用 Windows PowerShell のコネクタの名前に置き換えてください、 <ConnectorName> プレース ホルダーです。

## トラブルシューティング

-   コネクタのトラブルシューティングを行うへのログ記録を有効にする方法については、次を参照してください。、 [コネクタの ETW トレースを有効にする方法](http://go.microsoft.com/fwlink/?LinkId=335731)します。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291

