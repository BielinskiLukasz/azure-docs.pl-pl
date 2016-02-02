<properties
   pageTitle="クラウド サービスのロールに .NET をインストールする"
   description="この記事では、クラウド サービスの Web ロールと worker ロールに .NET Framework を手動でインストールする方法について説明します。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/11/2015"
   ms.author="saurabh"/>


# クラウド サービスのロールに .NET をインストールする

この記事では、クラウド サービスの Web ロールと worker ロールに .NET Framework をインストールする方法について説明します。 次の手順を使用すると、Azure ゲスト OS ファミリ 4 に .NET Framework 4.5.2 または .NET 4.6 をインストールすることができます。 最新情報については、ゲスト OS のリリース版を参照してください [Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)します。

クラウド プロジェクトおよびロールのスタートアップ タスクの一部として、インストーラーの起動の一環として .NET インストーラー パッケージを含めて、web ロールとワーカー ロールに .NET をインストールするプロセスが含まれます。

## プロジェクトに .NET インストーラーを追加する

1. インストールする .NET Framework の Web インストーラーをダウンロードします。
    - [.NET 4.5.2 web インストーラー](http://go.microsoft.com/fwlink/p/?LinkId=397703)
    - [.NET 4.6 web インストーラー](http://go.microsoft.com/fwlink/?LinkId=528259)
    - [.NET 4.6.1 web インストーラー](http://go.microsoft.com/fwlink/?LinkId=671729)
2. Web ロールの場合
  1. **ソリューション エクスプローラー**で、該当するクラウド サービス プロジェクトの **[ロール]** の下のロールを右クリックし、**[追加]、[新しいフォルダー]** の順に選択します。 *bin* という名前のフォルダーを作成します。
  2. **bin** フォルダーを右クリックし、**[追加]、[既存の項目]** の順に選択します。 .NET インストーラーを選択して bin フォルダーに追加します。
3. worker ロールの場合
  1. ロールを右クリックし、**[追加]、[既存の項目]** の順に選択します。 .NET インストーラーを選択して、ロールに追加します。

この方法でロールの Content フォルダーに追加したファイルは、クラウド サービス パッケージに自動的に追加され、仮想マシン上の一貫性のある場所にデプロイされます。 クラウド サービス内のすべての Web および worker ロールについて、すべてのロールがインストーラーのコピーを保持するように、このプロセスを繰り返します。

![インストーラー ファイルを持つロール コンテンツ][1]

## ロールのスタートアップ タスクを定義する

スタートアップ タスクでは、ロールが起動する前に操作を実行できます。 .NET Framework をスタートアップ タスクとしてインストールすると、任意のアプリケーション コードが実行される前に Framework がインストールされます。 詳細については、スタートアップ タスクを参照してください: [Azure におけるスタートアップ タスクの実行](https://msdn.microsoft.com/library/azure/hh180155.aspx)します。

1. すべてのロールについて、**WebRole** または **WorkerRole** ノードの下で次のコマンドを *ServiceDefinition.csdef* ファイルに追加します。

    ```xml
     <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    上の構成では、管理者特権でコンソール コマンド *install.cmd* を実行して .NET Framework をインストールできます。 この構成では、*NETFXInstall* という名前の LocalStorage も作成されます。 スタートアップ スクリプトは、.NET framework インストーラーがダウンロードされ、このローカル ストレージ リソースからインストールされるように、このリソースを使用する一時フォルダーを設定します。 このリソースのサイズは少なくとも 1024 MB に設定して、フレームワークが適切にインストールされるようにする必要があります。 詳細については「: [スタートアップ中にファイルを格納するローカル ストレージを使用して](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. **install.cmd** ファイルを作成します。次に、ロールを右クリックし、**[追加]、[既存の項目]** の順に選択して、このファイルをすべてのロールに追加します。 これで、すべてのロールに .NET インストーラー ファイルと、install.cmd ファイルが設定されました。

    ![すべてのファイルを持つロール コンテンツ][2]
    > [AZURE.NOTE] メモ帳などの単純なテキスト エディターを使用してこのファイルを作成します。 Visual Studio を使用してテキスト ファイルを作成し、名前を '.cmd' に変更した場合、ファイルにまだ UTF-8 バイト オーダー マークが含まれていることがあるため、スクリプトの最初の行を実行するとエラーが発生します。 Visual Studio を使用してファイルを作成した場合は、実行時に無視されるよう REM (コメント) をファイルの最初の行に追加してください。 

3. 次のスクリプトを **install.cmd** ファイルに追加します。

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    set netfx="NDP46"


    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%


    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set netfxinstallfile="NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set netfxinstallfile="NDP46-KB3045560-Web.exe"
    set netfxregkey="0x60051"
    goto logtimestamp

    :NDP461
    set netfxinstallfile="NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6041f"

    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"

    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%

    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
    if %ERRORLEVEL%== 0 goto end

    REM ***** Installing .NET *****
    echo Installing .NET: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    ```

    > [AZURE.IMPORTANT] スクリプト内の *netfx* 変数の値を、インストールするフレームワークのバージョンに合わせて更新します。 .NET 4.5.2 をインストールするには、*netfx* 変数を *"NDP452"* に設定します。.NET 4.6 をインストールするには、*netfx* 変数を *"NDP46"* に設定します。.NET 4.6.1 をインストールするには、*netfx* 変数を *"NDP461"* に設定します。

    インストール スクリプトにより、指定されたバージョンの .NET Framework が既にコンピューターにインストールされているかどうか、レジストリを照会することで確認されます。 対象の .NET バージョンがインストールされていない場合、.Net Web インストーラーが起動します。 問題がないかトラブルシューティングするために、スクリプトがすべてのアクティビティを *InstallLogs* ローカル記憶域に保存された *startuptasklog-(currentdatetime) .txt* という名前のファイルに記録します。



## 診断でスタートアップ タスク ログが BLOB ストレージに転送されるように構成する (オプション)

Azure Diagnostics を、スタートアップ スクリプトまたは .NET インストーラーが生成したログ ファイルを BLOB ストレージに転送するように構成できます。この設定はオプションです。 この方法では、リモート デスクトップをロールに移動するのではなく、ログ ファイルを BLOB ストレージから単にダウンロードしてログを表示できます。

診断を構成するには、*diagnostics.wadcfgx* を開き、次のコマンドを **Directories** ノードに追加します。

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

これにより、Azure 診断が *NETFXInstall* リソースの *log* ディレクトリ内のすべてのファイルを *netfx-install* BLOB コンテナーの診断ストレージ アカウントに転送するように構成されます。

## サービスのデプロイ

サービスをデプロイすると、スタートアップ タスクが実行され、またインストールされていない場合、.NET Framework がインストールされます。 Framework のインストール中、ロールはビジー状態になります。また、Framework のインストールで必要な場合、ロールが再起動されることがあります。


## その他のリソース

- [.Net Framework の][]
- [方法: どの .NET Framework のバージョンがインストールされているを確認します。][]
- [.NET Framework のトラブルシューティングのインストール][]



[how to: determine which .net framework versions are installed]: https://msdn.microsoft.com/library/hh925568.aspx 
[installing the .net framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx 
[troubleshooting .net framework installations]: https://msdn.microsoft.com/library/hh925569.aspx 
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png 
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png 

