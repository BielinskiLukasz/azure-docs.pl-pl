<properties
   pageTitle="Service Fabric アプリケーション モデル | Microsoft Azure"
   description="Service Fabric のアプリケーションとサービスをモデル化し、記述する方法。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/10/2015"   
   ms.author="seanmck"/>


# Service Fabric でのアプリケーションのモデル化

この記事では、Azure Service Fabric アプリケーション モデルの概要を示します。 また、マニフェストを使用したアプリケーションとサービスを定義する方法とアプリケーションをデプロイできるようパッケージ化する方法について説明します。

## アプリケーション モデルを理解する

アプリケーションは、特定のまたは複数の関数を実行する構成サービスのコレクションです。 サービスは完全なスタンドアロンの機能を実行し (他のサービスから独立して開始、実行できる)、コード、構成、データで構成されます。 各サービスに対して、コードは実行可能ファイルのバイナリで構成され、構成は実行時に読み込まれるサービス設定で構成され、データはサービスが消費する任意の静的データで構成されます。 この階層的なアプリケーション モデル内の各コンポーネントは、個別にバージョン管理されてアップグレードされます。

![][1]


アプリケーションの種類は、サービスの種類の集まりで構成されているアプリケーションの分類です。 サービスの種類は、サービスを分類したものです。 分類にはさまざまな設定と構成を含めることができますが、コア機能は変わりません。 サービスのインスタンスは、同じサービスの種類の別のサービス構成のバリエーションです。

アプリケーションのクラス (または "種類") とサービスは、アプリケーションをインスタンス化する対象となるテンプレートである XML ファイル (アプリケーション マニフェストとサービス マニフェスト) を使用して記述されます。 別のアプリケーション インスタンスのコードは、同じ Service Fabric ノードでホストされている場合でも個別のプロセスとして実行されます。 さらに、各アプリケーション インスタンスのライフサイクルを個別に管理できます (つまりアップグレード)。 次の図では、コード、構成、パッケージで構成されるサービスの種類で、アプリケーションの種類がどのように構成されるかを示しています。

![Service Fabric アプリケーションの種類とサービスの種類][image1]

2 つの異なるマニフェスト ファイル (サービス マニフェストとアプリケーション マニフェスト) はアプリケーションとサービスの記述に使用されます。 サービス マニフェストとアプリケーション マニフェストについては次のセクションで詳しく説明します。

クラスターにはアクティブな 1 つ以上のサービスの種類のインスタンスがある可能性があります。 たとえば、ステートフル サービス インスタンスやレプリカの場合、クラスター内の別のノード上にあるレプリカ間で状態をレプリケートすることで高い信頼性を実現します。 レプリカは基本的に、クラスター内の 1 つのノードが失敗した場合でも使用できるように、サービスの冗長性を実現します。 A [サービスをパーティション分割](service-fabric-concepts-partitioning.md) 、クラスター内のノードで、その状態 (とその状態へのアクセス パターン) をさらに分割します。

次の図は、アプリケーションとサービス インスタンス、パーティション、レプリカ間のリレーションシップを示しています。

![サービス内のパーティションとレプリカ][image2]


## サービスを記述する

サービス マニフェストは、宣言によって、サービスの種類とバージョンを定義します。 サービスの種類、ヘルスのプロパティ、負荷分散のメトリック、サービスのバイナリ、および構成ファイルなどのサービス メタデータを指定します。 別の言い方をすれば、1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するコード、構成、データのパッケージを記述します。 次に、サービス マニフェストの単純な例を示します。

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>サービス マニフェストの例</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**Version** 属性は構造化されていない文字列で、システムでは解析されません。 これらは、アップグレード用の各コンポーネントのバージョン管理に使用されます。

**ServiceTypes** はこのマニフェストで **CodePackages** でサポートされているサービスの種類を宣言します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されることにご注意ください。 したがって、複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。

**SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *LocalSystem* アカウント)。 **EntryPoint** によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 **EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (**SetupEntryPoint** で再起動)。

**DataPackage** は、実行時にプロセスで消費される任意の静的データを含む **Name** 属性を使用して名前が付けられたフォルダーを宣言します。

**ConfigPackage** は、*Settings.xml* ファイルを含む **Name** 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に **ConfigPackage** の**バージョン**のみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 次に、例 *Settings.xml*  ファイル。

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~
> [AZURE.NOTE] サービス マニフェストには、複数のコード、構成、データのパッケージを含めることができます。 それぞれを個別にバージョン管理できます。



## アプリケーションを記述する

アプリケーション マニフェストは、宣言によって、アプリケーションの種類とバージョンについて記述します。 安定した名前、パーティション分割スキーム、インスタンス数とレプリケーション係数、セキュリティと分離ポリシー、配置に関する制約、構成の上書き、構成サービスの種類などのサービス構成のメタデータを指定します。 アプリケーションが置かれる負荷分散のドメインについても記述します。
そのため、アプリケーション マニフェストは、アプリケーション レベルで要素を記述し、1 つ以上のサービス マニフェストを参照してアプリケーションの種類を構成します。 次に、アプリケーション マニフェストの単純な例を示します。

~~~
<?xml version="1.0" encoding="utf-8" ?>
< ApplicationManifest
      ApplicationTypeName ="MyApplicationType"
      ApplicationTypeVersion ="AppManifestVersion1"
      xmlns ="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi ="http://www.w3.org/2001/XMLSchema-instance">
  <Description>アプリケーション マニフェストの例</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

サービス マニフェストと同様に、**Version** 属性は構造化されていない文字列で、システムでは解析されません。 これらは、アップグレード用の各コンポーネントのバージョン管理にも使用されます。

**ServiceManifestImport** には、このアプリケーションの種類を構成するサービス マニフェストへの参照が含まれています。 インポートされたサービス マニフェストは、このアプリケーション内で有効なサービスの種類を決定します。

**DefaultServices** は、アプリケーションがこのアプリケーションの種類に対してインスタンス化されるたびに自動的に作成されるサービス インスタンスを宣言します。 既定のサービスは便利で、作成後はすべての面で通常のサービスと同様に動作します。 アプリケーション インスタンスの他のサービスと共にアップグレードされ、削除することもできます。
> [AZURE.NOTE] アプリケーション マニフェストには、複数のサービス マニフェストのインポートと既定のサービスを含めることができます。 各サービス マニフェストのインポートは、個別にバージョン管理できます。

別のアプリケーションおよびサービス パラメーターの個々 の環境の管理方法については、次を参照してください。 [アプリケーションのパラメーターを複数の環境を管理する](service-fabric-manage-multiple-environment-app-configuration.md)します。



## アプリケーションをパッケージ化する

### パッケージのレイアウト

アプリケーション マニフェスト、サービス マニフェスト、その他の必要なパッケージ ファイルを Service Fabric クラスターへのデプロイメント用の特定のレイアウトにまとめる必要があります。 この記事のマニフェスト例は、次のディレクトリ構造でまとめる必要があります。

~~~
PS D:\temp > ツリー/f \MyApplicationType。

D:\TEMP\MYAPPLICATIONTYPE
│ ApplicationManifest.xml
│
└───MyServiceManifest
    │ ServiceManifest.xml
    │
    ├───MyCode
    │ MyServiceHost.exe
    │
    ├───MyConfig
    │ Settings.xml
    │
    └───MyData
            init.dat
~~~

フォルダーには、それぞれの対応する要素の **Name** 属性と一致する名前が付けられます。 たとえば、**MyCodeA** と **MyCodeB** という名前の付いた 2 つのコード パッケージがサービス マニフェストに含まれている場合、各コード パッケージと同じ名前のフォルダにそれぞれのコード パッケージに必要なバイナリが含まれます。

### SetupEntryPoint の使用

**SetupEntryPoint** を使用する際の一般的なシナリオは、サービス開始前に実行可能ファイルを実行する必要がある場合や、昇格した特権で操作を実行する必要がある場合になります。 次に例を示します。

- サービス実行可能ファイルが使用する可能性がある環境変数の設定と初期化などです。 これは、Service Fabric のプログラミング モデルによって記述されの実行可能ファイルだけに限定されません。 たとえば、npm.exe は node.js アプリケーションのデプロイに構成されているいくつかの環境変数が必要です。

- セキュリティ証明書のインストールによるアクセス制御の設定

### Visual Studio を使用したパッケージ構築

Visual Studio 2015 を使用して、アプリケーションを作成する場合、パッケージのコマンドを使用して、前述のレイアウトと一致するパッケージを自動的に作成できます。

パッケージを作成するには、次のように、ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックして [パッケージ] コマンドを選択します。

![][2]

パッケージ化が完了したら、[**出力**] ウィンドウにパッケージの場所が表示されます。 アプリケーションを Visual Studio でデプロイまたはデバッグする場合、パッケージ化の手順は自動で行われることにご注意ください。

### パッケージのテスト

パッケージ構造を PowerShell の **Test-ServiceFabricApplicationPackage** コマンドを使用して、ローカルで検証することができます。 このコマンドは、マニフェストの解析の問題がチェックし、すべての参照を検証します。 このコマンドは、パッケージ内のディレクトリやファイルの構造的な正確性を検証するだけであることに注意してください。 コードやデータ パッケージのコンテンツのいずれについても検証は行われず、それらがすべてそろっているかどうかは確認されません。

~~~
PS D:\temp > Test-servicefabricapplicationpackage \MyApplicationType。
False
Test-servicefabricapplicationpackage: EntryPoint MySetup.bat が見つかりません。
ファイル名: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

このエラーは、**SetupEntryPoint** サービス マニフェストで参照される *MySetup.bat* ファイルがコード パッケージに見つからないことを示しています。 不足しているファイルを追加すると、アプリケーションの検証に合格します。

~~~
PS D:\temp > ツリー/f \MyApplicationType。

D:\TEMP\MYAPPLICATIONTYPE
│ ApplicationManifest.xml
│
└───MyServiceManifest
    │ ServiceManifest.xml
    │
    ├───MyCode
    │ MyServiceHost.exe
    │ MySetup.bat
    │
    ├───MyConfig
    │ Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp > Test-servicefabricapplicationpackage \MyApplicationType。
True
PS D:\temp >
~~~

アプリケーションが正しくパッケージ化されて検証に合格すると、デプロイメントの準備完了です。

## 次のステップ

[展開し、][10]

[複数の環境 ][11]

[RunAs: Service Fabric アプリケーションを実行するなさまざまなセキュリティ アクセス許可 ][12]




[1]: ./media/service-fabric-application-model/application-model.jpg 
[2]: ./media/service-fabric-application-model/vs-package-command.png 
[image1]: media/service-fabric-application-model/Service1.jpg 
[image2]: media/service-fabric-application-model/Service2.jpg 
[10]: service-fabric-deploy-remove-applications.md 
[11]: service-fabric-manage-multiple-environment-app-configuration.md 
[12]: service-fabric-application-runas-security.md 

