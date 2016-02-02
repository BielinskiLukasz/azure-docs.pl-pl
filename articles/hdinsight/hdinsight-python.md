<properties
    pageTitle="HDInsight における Python と Hive および Pig の使用 | Microsoft Azure"
    description="HDInsight (Azure の Hadoop テクノロジ スタック) での Hive および Pig の Python ユーザー定義関数 (UDF) の使用方法について説明します。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="12/04/2015" 
    ms.author="larryfr"/>


# HDInsight における Python と Hive および Pig の使用

Hive と Pig は HDInsight でデータを処理する場合にきわめて有益ですが、より汎用的な言語が必要になる場合もあります。 Hive と Pig では、さまざまなプログラミング言語を使用してユーザー定義関数 (UDF) を作成できます。 この記事では、Hive と Pig で Python UDF を使用する方法について説明します。
> [AZURE.NOTE] この記事で説明する手順は、HDInsight クラスター バージョン 2.1、3.0、3.1、3.2 に適用されます。


## <a name="python"></a>HDInsight の Python

Python2.7 は HDInsight 3.0 以降のクラスターに既定でインストール済みです。 このバージョンの Python と共に Hive を使用することで、ストリームを処理できます (Hive と Python の間のデータの受け渡しには STDOUT/STDIN を使用します)。

HDInsight には、Java で記述された Python 実装である Jython も付属しています。 Pig ではストリーミングを用いずに Jython と対話できるため、Pig を使用している場合に有効です。

### <a name="hivepython"></a>Hive と Python

Python は、HiveQL の **TRANSFORM** ステートメントを通じて Hive から UDF として使用することができます。 たとえば、次の HiveQL は **streaming.py** ファイルに格納されている Python スクリプトを呼び出します。

**Linux ベースの HDInsight**

    add file wasb:///streaming.py;
    
    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

**Windows ベースの HDInsight**

    add file wasb:///streaming.py;
    
    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] Windows ベースの HDInsight クラスターでは、**USING** 句で python.exe への完全パスを指定する必要があります。 これは常に `D:\Python27\python.exe`します。

この例では以下のように処理されます。

1. ファイルの先頭の **add file** ステートメントで **streaming.py** ファイルが分散キャッシュに追加されます。これによってクラスター内のすべてのノードからのアクセスが可能になります。

2. **選択の変更...を使用する** ステートメントからのデータの選択、 **hivesampletable**, 、clientid、devicemake、および devicemodel をし、 **streaming.py** スクリプトです。

3. **AS** 句は、**streaming.py** から返されるフィールドを記述します。

<a name="streamingpy"></a>
ここでは、 **streaming.py** HiveQL の使用例で使用するファイルです。

    #!/usr/bin/env python
    
    import sys
    import string
    import hashlib
    
    while True:
      line = sys.stdin.readline()
      if not line:
        break
    
      line = string.strip(line, "\n ")
      clientid, devicemake, devicemodel = string.split(line, "\t")
      phone_label = devicemake + ' ' + devicemodel
      print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

ストリーミングを使用しているため、このスクリプトは以下の処理が必要です。

1. STDIN のデータの読み取り。 使用してこの情報は `sys.stdin.readline()` この例では。

2. 使用して末尾の改行文字を削除 `string.strip (行、"\n")`, であるため、のみが必要なテキスト データと行末のインジケーターはありません。

2. ストリームの処理中は、すべての値が 1 つの行に含まれ、値と値の間はタブ文字で区切られます。 したがって `string.split (行、"\t")` フィールドだけを返す各タブに入力を分割するために使用できます。

3. 処理の完了時には、フィールド間がタブで区切られた単一の行として、STDOUT に出力が書き出される必要があります。 使用してこの情報は `"\t".join の印刷 ([クライアント id、phone_label、hashlib.md5(phone_label).hexdigest()])`します。

4. 内でこれらがすべての実行、 `中に` れなくなるまで繰り返されますループ `行` 読み取られ、この時点で `中断` ループし、スクリプトは終了が終了します。

さらに、スクリプトだけ入力値を連結 `devicemake` と `devicemodel`, 、し、連結された値のハッシュを計算します。 Hive から呼び出された Python スクリプトは、ループ、入力がなくなるまでの読み込み、タブでの各入力行の区切り、処理、タブで区切った単一行への出力の書き出しを行います。単純ですがこれが基本的な動作の説明です。

参照してください [例を実行する](#running) の HDInsight クラスターでこのサンプルを実行する方法です。

### <a name="pigpython"></a>Pig と Python

Python スクリプトは、**GENERATE** ステートメントを通じて Pig から UDF として使用できます。 たとえば、次の例では **jython.py** ファイルに格納されている Python スクリプトを使用します。

    Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

以下に、このスクリプトの動作の例を示します。

1. **Jython** を使用して Python スクリプト (**jython.py**) が含まれるファイルを登録し、**myfuncs** として Pig に公開します。 Jython は、Java による Python 実装であり、Pig と同じ Java 仮想マシンで動作します。 この方法によって、Hive で使用するストリーミング手法ではなく、従来の関数呼び出しのように Python スクリプトを扱うことができます。

2. 次の行は、サンプルのデータ ファイルである、**sample.log** を **LOGS** に読み込みます。 このファイルには一貫したスキーマがないことから、各レコード (この場合は **LINE**) を **chararray** として定義します。 Chararray は本質的には文字列です。

3. 3 行目はすべての null 値を除去し、操作の結果を **LOG** に格納します。

4. 次に、**LOG** のレコードを反復処理し、**GENERATE** を使用して、**myfuncs** として読み込まれた **jython.py** スクリプトに格納されている **create_structure** メソッドを呼び出します。 **LINE** は現在のレコードを関数に渡すために使用されます。

5. 最後に、**DUMP** コマンドで出力が STDOUT にダンプされます。 この例は、操作完了後の結果をすぐに示すためのものであり、実際のスクリプトでは通常、**STORE** を通じて新しいファイルにデータを保存します。

<a name="jythonpy"></a>
ここでは、 **jython.py** Pig の例で使用されるファイル。

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
      if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
      date, time, classname, level, detail = input.split(' ', 4)
      return date, time, classname, level, detail

先ほど、入力の一貫したスキーマがないために **LINE** 入力を chararray として定義したことを覚えているでしょうか。 これから **jython.py** が行うのは、出力用に、データを一貫したスキーマに変換することです。 次のような処理です。

1. **@outputSchema** ステートメントは、Pig に返されるデータ形式を定義します。 この場合、Pig のデータ型である、**data bag** になります。 この bag には以下のフィールドが含まれ、すべて chararray (文字列) です。

    * date - ログ エントリが作成された日付
    * time - ログ エントリが作成された時刻
    * classname - エントリが作成されたクラスの名前
    * level - ログ レベル
    * detail - ログ エントリの詳細説明

2. 次に、**def create_structure(input)** で Pig が行項目を渡す関数が定義されます。

3. 例のデータである **sample.log** は通常、日付、時刻、クラス名、レベル、および詳細説明のデータを返すスキーマに従います。 しかし、ここにはスキーマとの一致を図るために、"*java.lang.Exception*" という文字列で始まるいくつかの行を変更する必要があります。 **if** ステートメントでそれらを確認した後、想定される出力スキーマと一致するように、入力データの "*java.lang.Exception*" 文字列を末尾に移動します。

4. 次に、**split** コマンドを使用して、最初の 4 つの空白文字でデータを分割します。 その結果 5 つの値が生成され、それぞれ **date**、**time**、**classname**、**level**、および **detail** に割り当てられます。

5. 最後に、値が Pig に返されます。

データが Pig に返された時点で、このデータは、**@outputSchema** ステートメントで定義された一貫したスキーマを持ちます。

## <a name="running"></a>例を実行します。

Linux ベースの HDInsight クラスターを使用している場合は、次の **SSH** の手順を実行します。 Windows ベースの HDInsight クラスターと、Windows クライアントを使用している場合は、**PowerShell** の手順を実行します。

### SSH

SSH の使用に関する詳細については、次を参照してください。 <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">SSH Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop を使用する</a> または <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">SSH Windows から Linux ベースの Hadoop を使用する</a>します。

1. Python の例を使用して [streaming.py](#streamingpy) と [jython.py](#jythonpy), 、開発用コンピューター上のファイルのローカル コピーを作成します。

2. 使用 `scp` 、HDInsight クラスターに、ファイルをコピーします。 以下の例では、**mycluster** という名前のクラスターにファイルをコピーします。

        scp streaming.py jython.py myuser@mycluster-ssh.azurehdinsight.net:

3. SSH を使用してクラスターに接続します。 以下の例では、**mycluster** という名前のクラスターにユーザー **myuser** として接続します。

        ssh myuser@mycluster-ssh.azurehdinsight.net

4. 以前アップロードした python のファイルを SSH セッションからクラスターの WASB ストレージに追加します。

        hadoop fs -copyFromLocal streaming.py /streaming.py
        hadoop fs -copyFromLocal jython.py /jython.py


ファイルをアップロードした後、次の手順に従って、Hive ジョブと Pig ジョブを実行します。

#### Hive

1. 使用して、 `hive` hive シェルを起動するコマンドです。 表示されます、 `hive >` 、シェルが読み込まれたらを確認します。

2. 次のコマンドを入力して、 `hive >` プロンプトです。

        add file wasb:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'streaming.py' AS
          (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

3. 最後の行を入力すると、ジョブが開始されます。 最終的に、次のような出力が返されます。

        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9


#### Pig

1. 使用して、 `pig` コマンド シェルを起動します。 表示されます、 `grunt >` 、シェルが読み込まれたらを確認します。

2. 次のステートメントを入力して、 `grunt >` プロンプトです。

        Register wasb:///jython.py using jython as myfuncs;
        LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;

3. 次の行を入力すると、ジョブが開始されます。 最終的に、次のような出力が返されます。

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))


### PowerShell

次の手順では、Azure PowerShell を使用します。 インストールされていないと、開発用コンピューターで構成されているが場合、は、次を参照してください。 [をインストールして、Azure PowerShell を構成する方法](../install-configure-powershell.md) 、次の手順を使用する前にします。

1. Python の例を使用して [streaming.py](#streamingpy) と [jython.py](#jythonpy), 、開発用コンピューター上のファイルのローカル コピーを作成します。

2. 次の PowerShell スクリプトを使用して、アップロードする、 **streaming.py** と **jython.py** サーバーへのファイルです。 スクリプトの最初の 3 行に、Azure HDInsight クラスターの名前と、**streaming.py** ファイルおよび **jython.py** ファイルへのパスを入力します。

     $clusterName = YourHDIClusterName
     $pathToStreamingFile = "C:\path\to\streaming.py"
     $pathToJythonFile = "C:\path\to\jython.py"
    
     $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
     $resourceGroup = $clusterInfo.ResourceGroup
     $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
     $container=$clusterInfo.DefaultStorageContainer
     $storageAccountKey=Get-AzureRmStorageAccountKey `
         -Name $storageAccountName `
         -ResourceGroupName $resourceGroup `
         | %{ $_.Key1 }
    
     #Create a storage content and upload the file
     $context = New-AzureStorageContext `
         -StorageAccountName $storageAccountName `
         -StorageAccountKey $storageAccountKey
    
     Set-AzureStorageBlobContent `
         -File $pathToStreamingFile `
         -Blob "streaming.py" `
         -Container $container `
         -Context $context
    
     Set-AzureStorageBlobContent `
         -File $pathToJythonFile `
         -Blob "jython.py" `
         -Container $container `
         -Context $context

 このスクリプトは、HDInsight クラスターの情報を取得してから、既定のストレージ アカウント用にアカウントとキーを抽出します。その後、ファイルをコンテナーのルートにアップロードします。
 > [AZURE.NOTE] その他のスクリプトをアップロードする方法は記載されて、 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md) ドキュメントです。

ファイルのアップロード後、次の PowerShell スクリプトを使用してジョブを開始します。 ジョブが完了すると、PowerShell コンソールに出力が書き込まれます。

#### Hive

次のスクリプトは __streaming.py__ スクリプトを実行します。 実行前に、HDInsight クラスターの HTTPs/Admin アカウント情報の入力が求められます。

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    $HiveQuery = "add file wasb:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'D:\Python27\python.exe streaming.py' AS " +
                   "(clientid string, phoneLabel string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"
    
    $jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery
    
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

**Hive** ジョブの出力は次のように表示されます。

    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

#### Pig

次の例では、__jython.py__ スクリプトを使用します。 実行前に、HDInsight クラスターの HTTPs/Admin 情報の入力が求められます。

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName
    
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    $PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"
    
    $jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery
    
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    
    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

**Pig** ジョブの出力は次のように表示されます。

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>トラブルシューティング

例の実行に使用した両方の PowerShell スクリプトには、ジョブのエラー出力を表示するコメント行が含まれています。 ジョブについて想定された出力が確認できない場合は、次の行をコメント解除し、エラー情報で問題が示されるかどうかを確認してください。

    # Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

エラー情報 (STDERR) およびジョブの結果 (STDOUT) は、クラスターの既定の BLOB コンテナーの以下の場所にも記録されます。

 ジョブ| BLOB コンテナーで確認するファイル
---|---
 Hive| /HivePython/stderr<p>/HivePython/stdout
 Pig| /PigPython/stderr<p>/PigPython/stdout

## <a name="next"></a>次のステップ

既定では提供されない Python モジュールをロードする必要がある場合は、次を参照してください。 [モジュールを Azure HDInsight にデプロイする方法](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) これを行う方法の例です。

Pig と Hive を使用する他の方法と、MapReduce の使用方法については、以下をご覧ください。

* [HDInsight での Hive を使用します。](hdinsight-use-hive.md)

* [HDInsight での Pig を使用します。](hdinsight-use-pig.md)

* [HDInsight での MapReduce を使用します。](hdinsight-use-mapreduce.md)





