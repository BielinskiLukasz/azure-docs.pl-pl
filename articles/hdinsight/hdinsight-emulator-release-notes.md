<properties 
    pageTitle="リリース ノート: Microsoft HDInsight Emulator for Azure | Microsoft Azure" 
    description="HDInsight Hadoop Emulator (Hadoop サンドボックス) 環境の最新リリースについての最新情報を入手してください。" 
    editor="cgronlun" 
    manager="paulettm" 
    services="hdinsight" 
    authors="mumian" 
    documentationCenter=""/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/29/2015" 
    ms.author="jgao"/>




# リリース ノート: Microsoft HDInsight Emulator for Hadoop 



> [AZURE.NOTE] 
> バージョン番号を確認する最も簡単な方法はプログラムの追加/削除のエントリを確認する **Microsoft HDInsight Emulator for Azure** (version 1.0.0.0 以降) または **Microsoft HDInsight 開発者プレビュー** (version 1.0.0.0 より前) にします。 

## Version 2.0.0.0、2014 年 8 月 29 日リリース

* このリリースでは、Version 3.1 で現在サービスが提供されているのと同じセットの Hadoop プロジェクトを対象とするように HDInsight Emulator が更新されました。    

* この製品のプレビュー リリースと同様に、このリリースも引き続き開発者シナリオを対象としており、そのため単一ノード デプロイメントのみをサポートします。 

### 新機能 
 
* [Hadoop コンポーネントのバージョンを更新](hdinsight-component-versioning.md) サービスの version 3.1 に対応します。 これには、Hive 0.13 と Tez のサポートが含まれます。

## Version 1.0.0.0、2013 年 10 月 28 日リリース

* これは、Microsoft HDInsight Emulator for Azure (旧称 Microsoft HDInsight 開発者プレビュー) の一般公開 (GA) リリースです。 

* この製品のプレビュー リリースと同様に、このリリースも引き続き開発者シナリオを対象としており、そのため単一ノード デプロイメントのみをサポートします。 

### 新機能 
 
* Apache Hadoop のすべてのサービスを自動起動または手動起動に簡単に設定できるように、スクリプトが追加されました。 既定では、以前と同様に自動起動ですが、C:\Hadoop にインストールされた set-onebox-manualstart.cmd または set-onebox-autostart.cmd スクリプトを使用して、すべてのサービスを変更できるようになりました。 

* インストール時に必要な依存コンポーネントの数が大幅に減少したため、迅速にインストールできます。 

* このバージョンには、GettingStarted フォルダーにインストールされている RunSamples.ps1 スクリプトで Pig のサンプルを実行するときに使用するコマンドのバグの修正が含まれます。 

* このバージョンには、Azure HDInsight クラスター Version 1.6 で使用できる Hortonworks Data Platform サービスに相当する Hortonworks Data Platform (HDP) Version 1.1 の更新が含まれます。 

## Version 0.11.0.0、2013 年 9 月 30 日リリース

### 新機能 
        
* HDInsight ダッシュボードが削除されました。 

* このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。 

## Version 0.10.0.0、2013 年 8 月 9 日リリース

### 新機能 
    
* このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。

## Version 0.8.0.0、2013 年 6 月 7 日リリース

### 新機能 

* このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。

## Version 0.6.0.0、2013 年 5 月 13 日リリース

### 新機能 

* Hive Server 2 がインストールされるようになりました。 これは、この更新と同時にリリースされた Microsoft ODBC Driver for Hive の Version 0.9.2.0 で必要になります。 

* すべてのサービスが自動起動に設定され、コンピューターの再起動後にすべてを起動し直す必要がなくなりました。 

## Version 0.4.0.0、2013 年 3 月 25 日リリース

### 新機能 

* Microsoft HDInsight 開発者プレビューおよび Hortonworks Data Platform for Windows 開発者プレビューの新リリース。 

* Apache Hadoop、Hive、Pig、Sqoop、Oozie、HCatalog、Templeton を含みます。 

* 新しい HDInsight ダッシュボードには次の機能があります。 
 
    * ローカル インストールのほか、Azure HDInsight サービスを使用してリモートで実行されているクラスターも含む、複数のクラスターに接続します。 HDInsight サービスの詳細については、次を参照してください。 [http://azure.microsoft.com/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/)します。
 
    * ローカル クラスターで Azure BLOB ストレージを構成します。 詳細な手順は以下を参照してください。

    * 新しい対話的な Hive コンソールで Hive クエリを作成し編集します。

    * ジョブの履歴と結果を表示してダウンロードできます。

### リリース ノート 

* ローカル HDInsight インストールと Azure HDInsight サービスの REST API エンドポイントは、次のように、同じサービスに対して異なるポート番号でアクセスされます。 

    ローカル: 
    Oozie - http://localhost:11000/oozie/v1/管理/状態 
    Templeton - http://localhost:50111、templeton、v1/状態 
    ODBC - DSN 構成または接続文字列でポート 10000 を使用します 

    HDInsight サービス: 
    Oozie - http://ServerFQDN:563/oozie/v1/管理/状態 
    Templeton - http://ServerFQDN:563、templeton、v1/状態 
    ODBC - DSN 構成または接続文字列でポート 563 を使用します 


* 次のように、ローカル クラスターで Azure BLOB ストレージを構成します。 

    ダッシュボードには、"ローカル (hdfs)" という名前の既定のローカル クラスターが表示されます。 ローカル インストールのストレージとして Azure BLOB ストレージを使用する場合は、次の手順を実行します。 

    1. C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf にある core-site.xml にアカウント タグを追加します。       

            <property>
                <name>fs.azure.account.key.{AccountName}</name>
                <value>{Key}</value>
            </property>

            <property>
                <name>fs.default.name</name>
                <!-- cluster variant -->
                <value>asv://ASVContainerName@ASVAccountName</value>
                <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
                <final>true</final>
            </property>

            <property>
                <name>dfs.namenode.rpc-address</name>
                <value>hdfs://localhost:8020</value>
                <description>A base for other temporary directories.</description>
            </property>
      
        例:       

            <property>
                <name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
                <value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
            </property>

            <property>
                <name>fs.default.name</name>
                <!-- cluster variant -->
                <value>asv://MyASVContainer@MyASVAccount</value>
                <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
                <final>true</final>
            </property>
        

    2. デスクトップから Hadoop コマンド シェルを昇格モードで開き、次のコマンドを実行します。
     
            %HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

    3. フル URI を使用して、アカウントのすべてのファイルにアクセスします。 asv://{container}@{account}/{path} (asvs:/]、[HTTPS を使用してデータにアクセスする場合)。 例:
     
            hadoop fs -lsr 
            asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

    4. 現在登録されているローカル クラスターを削除し、新しい Azure BLOB ストレージの資格情報を使って再登録します。 


## Version 0.3.0.0、2012 年 12 月 13 日リリース 

* ダッシュボードの Web サイトは、Windows 資格情報の代わりに匿名認証を使用するように変更されました。 これにより、以前のバージョンのリリース ノートに記載されていたログイン プロンプトに関する問題が解消されます。 

* エクスポートと一部の種類のインポートにおける Sqoop のバグをいくつか修正しました。 

### リリースの問題 

* JavaScript コンソールの読み込みに失敗します。 詳細については、Version 0.2.0.0 のリリース ノートを参照してください。 
* Sqoop コマンド ラインによって次のような警告が表示されます。 これは今後の更新プログラムで修正される予定であり、無視してもかまいません。 
    
        c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
        Setting HBASE_HOME to 
        Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
        Please set HBASE_HOME to the root of your HBase installation. 
        Setting ZOOKEEPER_HOME to 
        Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
        Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
        Sqoop 1.4.2 
        git commit id 3befda0a456124684768348bd652b0542b002895 
        Compiled by  on Thu 11/29/2012- 3:26:26.10

## Version 0.2.0.0、2012 年 12 月 3 日リリース

* Windows インストーラーに対するセマンティック バージョン管理の導入 

* MSDN フォーラムで報告されたさまざまなインストール バグ、特に HDInsight ダッシュボードのインストール関係のバグの修正 

* **開始** メニュー項目を見つけやすいように追加 

* Hive コンソールの複数行入力に関する修正 

* 入門用コンテンツの細かな更新 

### リリースの問題 

* JavaScript コンソールの読み込みに失敗します。 

    * 一部のインストールで、ページに HTTP 404 エラーが表示され JavaScript コンソールの読み込みに失敗します。 この問題を回避するには、コンソールを使用して http://localhost:8080 に直接移動します。 

* HDInsight ダッシュボードに移動するとログイン プロンプトが表示されます。 

    * HDInsight ダッシュボードへの移動中に、ログイン ダイアログ ボックスが表示されるという報告がいくつかありました。 その場合は、現在のユーザーのログイン情報を入力すると、ダッシュボードに移動できます。 


## Version 1.0.0.0、2012 年 10 月 23 日リリース

* 最初のリリース 

### リリースの問題 

* Hive コンソール 

    * 送信した Hive コマンドに改行文字が含まれていると、構文エラーになります。 改行を削除すると、クエリは意図したとおりに実行ざれます。 



## 一般的な既知の問題

* Hadoop ユーザーのパスワードの有効期限 

    コンピューターに適用された Active Directory ポリシーによっては、Hadoop ユーザーのパスワードが期限切れになることがあります。 以下の Windows PowerShell スクリプトによって、パスワードが期限切れにならないように設定されます。このスクリプトは管理コマンド プロンプトから実行できます。    

        $username = "hadoop"
        $ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

        $computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
        $users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

        foreach($user in $users)
        {
            if($user.Name -eq $username)
            {
                $user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
                $user.SetInfo()
 
                $user.PasswordExpired = 0
                $user.SetInfo()
 
                Write-Host "$username user maintenance completed. "
            }
        }


* 一時ディレクトリ 
    
    hadoop.tmp.dir ファイルが間違った場所を指しており、 C:\hadoop\hdfs ではなく、c:\hdfs を指しています。 このバグは、HDP 関連の次の更新プログラムで修正されます。 

* OS の制限 

    HDInsight Server は、64 ビット OS にインストールする必要があります。 

* Web Platform Installer (WebPI) の検索結果 

    WebPI の検索結果に HDInsight が見つかりません。 これは通常、OS の制限です。 HDInsight には 64 ビットのオペレーティング システムが必要であり、バージョンは Windows 7 Service Pack 1、Windows Server 2008 R2 Service Pack 1、Windows 8、または Windows Server 2012 以上が必要です。

* 管理コマンド プロンプトのマニュアル 

    * などのコマンドを実行するために **hadoop mradmin** または **hadoop defadmin**, 、Hadoop ユーザーとして実行する必要があります。 

    * そのユーザーとして実行されるシェルを簡単に作成するには、Hadoop コマンド プロンプトを開いて、次のコマンドを実行します。 
     
            start-hadoopadminshell

    * これにより Hadoop の管理者権限で実行されている新しいコマンド シェルが開きます。 

##<a name="nextsteps"></a> 次のステップ

- [HDInsight Emulator の概要][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 
