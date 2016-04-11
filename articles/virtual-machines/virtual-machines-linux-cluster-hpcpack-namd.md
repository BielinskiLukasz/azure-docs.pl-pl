<properties
 pageTitle="Linux VM で Microsoft HPC Pack を使用してNAMD を実行する| Microsoft Azure"
 description="Microsoft HPC Pack クラスターを Azure にデプロイし、複数の Linux コンピューティング ノード上で charmrun を使用して NAMD シミュレーションを実行します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する

この記事は、複数の Linux 計算ノードと共に Azure 上の Microsoft HPC Pack クラスターをデプロイして実行する方法を示します、 [NAMD](http://www.ks.uiuc.edu/Research/namd/) ジョブが **charmrun** を計算し、大規模な biomolecular システムの構造を視覚化します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。



NAMD (ナノスケール分子力学プログラム) とは、ウイルス、セル構造体、および巨大タンパク質など、最大で数百万個の原子を含む大規模な生体分子系を高いパフォーマンスでシミュレーションするために設計された並列分子動力学パッケージです。 NAMD は、通常のシミュレーションでは数百個のコアに対応し、大規模なシミュレーションでは 500,000 個を超えるコアに対応します。

Microsoft HPC Pack は、Microsoft Azure 仮想マシンのクラスター上で各種の大規模な HPC および並列アプリケーション (MPI アプリケーションなど) を実行する機能を備えています。 Microsoft HPC Pack 2012 R2 Update 2 以降、HPC Pack では、HPC Pack クラスターにデプロイされた Linux 計算ノード VM で Linux HPC アプリケーションを実行する機能もサポートしています。 参照してください [Azure で HPC Pack クラスターで Linux 計算ノードを使ってみる](virtual-machines-linux-cluster-hpcpack.md) の概要についてです。


## 前提条件

* **Linux での HPC Pack クラスター計算ノード** -を参照してください [Azure で HPC Pack クラスターで Linux 計算ノードを使ってみる](virtual-machines-linux-cluster-hpcpack.md) の前提条件と Linux の HPC Pack クラスターを展開する手順は、Azure Marketplace で Azure PowerShell スクリプトと HPC Pack イメージを使用して、Azure 上のノードを計算します。

    Windows Server 2012 R2 ヘッド ノードおよび 4 つの Large サイズ (A3) CentOS 6.6 コンピューティング ノードから成る Azure ベースの HPC Pack クラスターをデプロイする際に、スクリプトと一緒に使用することができる XML 構成ファイルのサンプルを次に示します。 該当する値は、実際のサブスクリプションとサービス名に置き換えてください。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **NAMD ソフトウェアおよびチュートリアル ファイル** -から Linux 用のソフトウェアのダウンロード NAMD、 [NAMD](http://www.ks.uiuc.edu/Research/namd/) サイトです。 この記事は、バージョン 2.10、NAMD に基づいておりを使用して、 [Linux x86_64 (64 ビット Intel と AMD イーサネット)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) NAMD を複数の Linux で実行に使用する、アーカイブがクラスタ ネットワーク内のノードを計算します。 ダウンロードも、 [NAMD チュートリアル ファイル](http://www.ks.uiuc.edu/Training/Tutorials/#namd)します。 クラスター ヘッド ノードにアーカイブとチュートリアル サンプルを抽出する手順は、この記事の後の方に説明しています。

* **VMD** (省略可能) - NAMD ジョブの結果を表示し、ダウンロードし、分子の視覚エフェクトのプログラムをインストールする [VMD](http://www.ks.uiuc.edu/Research/vmd/) 、任意のコンピューターにします。 現行バージョンは 1.9.2 です。 作業を開始するには、VMD ダウンロード サイトを参照してください。  


## コンピューティング ノードの相互の信頼関係をセットアップする
複数の Linux ノードにノード間のジョブを実行しているノードが相互に信頼関係が必要です (によって **rsh** または **ssh**)。 Microsoft HPC Pack IaaS デプロイ スクリプトを使用して HPC Pack クラスターを作成する場合は、指定した管理者アカウントに対して永続的な相互の信頼関係がスクリプトによって自動的にセットアップされます。 管理者以外のユーザーをクラスター ドメインに作成した場合は、それらのユーザーにジョブを割り当てるときに、ノード間に一時的な相互の信頼関係をセットアップする必要があります。ジョブ終了後、この関係は破棄します。 これをユーザーごと行うには、HPC Pack で使用するクラスターに RSA キー ペアを指定して、信頼関係を確立します。

### RSA キー ペアの生成
Linux を実行して、公開キーと秘密キーが含まれている RSA キー ペアを生成する簡単 **で問題** コマンドです。

1.  Linux コンピューターにログオンします。

2.  次のコマンドを実行します。

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] キーを押して **Enter** コマンドが完了するまで、既定の設定を使用します。 ここでは、パスフレーズを入力しないでください。パスワードが表示されたら、キーを押して **Enter**します。

    ![RSA キー ペアの生成][keygen]

3.  ディレクトリを ~/.ssh ディレクトリに変更します。 秘密キーは id_rsa に格納され、公開キーは id_rsa.pub に格納されます。

    ![公開キーと秘密キー][keys]

### HPC Pack クラスターにキー ペアを追加する
1.  HPC Pack の管理者アカウント (デプロイ スクリプトを実行したときにセットアップした管理者アカウント) を使用してヘッド ノードへのリモート デスクトップ接続を行います。

2. 標準的な Windows Server 手順を使用して、クラスターの Active Directory ドメインにドメイン ユーザー アカウントを作成します。 たとえば、ヘッド ノードで Active Directory ユーザーとコンピューター ツールを使用します。 この記事の例では、hpclab\hpcuser という名前のドメイン ユーザーを作成することを前提とします。

2.  C:\cred.xml という名前のファイルを作成し、そこに RSA キーのデータをコピーします。 例については、この記事の最後にあるサンプル ファイルを参照してください。

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  コマンド プロンプトを開き、次のコマンドを入力して、hpclab\hpcuser アカウントの資格情報データを設定します。 使用する、 **extendeddata** キーのデータ用に作成した C:\cred.xml ファイルの名前を渡すためのパラメーターです。

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    このコマンドは、出力なしで正常に終了します。 ジョブを実行するために必要なユーザー アカウントの資格情報を設定したら、cred.xml ファイルを安全な場所に保存するか、または削除します。

5.  Linux ノードの 1 つに対して RSA キー ペアを生成した場合は、キーの使用が終わった後に、それらを削除することを忘れないでください。 既存の id_rsa ファイルまたは id_rsa.pub ファイルが見つかった場合、HPC Pack は相互の信頼関係をセットアップしません。

>[AZURE.IMPORTANT] Linux ノードにルート アカウントで、管理者によって実行されるジョブが実行されるので、クラスター管理者として共有クラスターで Linux ジョブを実行する推奨されません。 管理者以外のユーザーによって送信されたジョブは、ジョブ ユーザーと同じ名前を持つローカルの Linux ユーザー アカウントで実行されます。HPC Pack は、ジョブに割り当てられたすべてのノード間に、この Linux ユーザー用の相互の信頼関係をセットアップします。 ジョブを実行する前に Linux ノードに対して手動で Linux ユーザーをセットアップすることも、ジョブの送信時に HPC Pack がユーザーを自動的に作成するようにすることもできます。 HPC Pack でユーザーを作成した場合、ジョブの完了後にユーザーは HPC Pack によって削除されます。 ノード上でジョブが完了すると、セキュリティ上の脅威を軽減するためにキーは削除されます。

## Linux ノード用にファイル共有をセットアップする

ヘッド ノード上のフォルダーに対して標準の SMB 共有をセットアップし、すべての Linux ノード上に共有フォルダーをマウントすることで、すべての Linux ノードが共通のパスを使用して NAMD ファイルにアクセスできるようにしました。 ファイル共有オプションし、手順を参照してください [Azure で HPC Pack クラスターで Linux 計算ノードを使ってみる](virtual-machines-linux-cluster-hpcpack.md)します。 (CentOS 6.6 Linux ノードでは現在時点で、同様の機能を提供する Azure File Service がサポートされていないので、この記事ではヘッド ノードに共有フォルダーをマウントすることをお勧めしています。 Azure File 共有をマウントする方法の詳細を参照してください [Microsoft Azure Files への接続の維持](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)。)。

1.  ヘッド ノードにフォルダーを作成します。読み書き権限を設定して、フォルダーを全員で共有します。 この例では、\\\CentOS66HN\Namd は、ヘッド ノードのホスト名を CentOS66HN がここでは、フォルダーの名前です。

2. Windows バージョンを使用して、フォルダー内の NAMD ファイルを抽出 **tar** または .tar アーカイブで動作する他の Windows ユーティリティです。 \\\CentOS66HN\Namd\namd2、NAMD tar アーカイブを抽出し、[\\\CentOS66HN\Namd\namd2\namdsample チュートリアル ファイルを抽出します。

2.  Windows PowerShell ウィンドウを開き、次のコマンドを実行し、共有フォルダーをマウントします。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

最初のコマンドで、LinuxNodes グループに属するすべてのノードに /namd2 という名前のフォルダーが作成されます。 2 番目のコマンドにより、そのフォルダーに共有フォルダー //CentOS66HN/Namd/namd2 がマウントされ、dir_mode および file_mode ビットが 777 に設定されます。  *Username* と *パスワード* コマンドでヘッド ノード上のユーザーの資格情報である必要があります。

>[AZURE.NOTE]"\'"2 番目のコマンドで記号は PowerShell のエスケープ記号です。 "\'、"、「,」(コンマ) ことを意味のコマンドの一部であります。


## NAMD ジョブを実行する準備

 NAMD ジョブ必要のある、 *nodelist* 用ファイル **charmrun** NAMD プロセスの開始時に使用するノードの数を知る。 Nodelist ファイルを生成し、実行される、Bash スクリプトを作成できる **charmrun** この nodelist ファイルを使用します。 NAMD HPC クラスター マネージャーでこのスクリプトを呼び出すジョブを送信できます。

### 環境変数と nodelist ファイル
ノードとコアに関する情報は $CCP_NODES_CORES 環境変数内にあります。この環境変数は、ジョブのアクティブ化の際に、HPC Pack ヘッド ノードにより自動的に設定されます。 $CCP_NODES_CORES 変数の形式は次のとおりです。

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

これには、ノードの総数、ノード名、およびジョブに割り当てられた各ノード上のコア数が一覧表示されます。 たとえば、ジョブを実行する上で 10 個のコアが必要である場合、$CCP_NODES_CORES の値は次のようになります。

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

スクリプトによって生成される nodelist ファイル内の情報を次に示します。

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

次に例を示します。

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### nodelist ファイルを作成する Bash スクリプト

任意のテキスト エディターを使用して、NAMD プログラム ファイルが格納されているフォルダーに次の Bash スクリプトを作成し、hpccharmrun.sh という名前を付けます。 完全な例がこの記事の最後にあるサンプル ファイルにあります。 この Bash スクリプトは次のことを行います。

>[AZURE.TIP] スクリプトを Linux でのテキスト ファイルとして保存改行 (LF のみ、CR LF されません)。 これにより、スクリプトは Linux ノード上で適切に動作します。

1.  いくつかの変数を定義します。

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  環境変数からノード情報を取得します。 $NODESCORES では、$CCP_NODES_CORES から単語の分割のリストを保存します。 $COUNT は、$NODESCORES のサイズです。

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.  $CCP_NODES_CORES 変数が設定されていない場合だけ開始 **charmrun** 直接します。 (Linux ノードでこのスクリプトを直接実行する場合に限ります)。

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Nodelist ファイルを作成または **charmrun**します。

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  実行 **charmrun** nodelist ファイルを使用して、リターン ステータスを取得し、最後に nodelist ファイルを削除します。

    ${CCP_NUMCPUS} は、HPC Pack ヘッド ノードによって設定されるもう一つの環境変数です。 この環境変数には、このジョブに割り当てられたコアの合計数が格納されます。 charmrun のプロセス数を指定するために使用します。

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  終了、 **charmrun** ステータスを返します。

    ```
    exit ${RTNSTS}
    ```

## NAMD ジョブの送信

これで、HPC クラスター マネージャーで NAMD ジョブを送信する準備が整いました。

1.  クラスター ヘッド ノードに接続し、HPC クラスター マネージャーを開始します。

2.   **ノード管理**, 、Linux 計算ノードが確実に、 **オンライン** 状態です。 間違っている場合は、選択し、をクリックして **オンライン**します。

2.   **ジョブ管理**, 、クリックして **新しいジョブ**します。

3.  ジョブの名前を入力します。 *hpccharmrun*します。

    ![新しい HPC ジョブ][namd_job]

4.   **ジョブの詳細** ] ページで、[ **ジョブ リソース**, 、としてリソースの種類を選択 **ノード** し、設定、 **最小** 3 にします。 この例では、3 つの Linux ノードでジョブを実行します。各ノードには 4 つのコアがあります。

    ![ジョブ リソース][job_resources]

5.   **タスクの詳細と I/O リダイレクト** ] ページで、ジョブに新しいタスクを追加して、次の値を設定します。

    * **コマンド ライン** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **作業ディレクトリ** -/namd2

    * **最小** - 3

    ![タスクの詳細][task_details]

    >[AZURE.NOTE] 作業ディレクトリの設定がここで **charmrun** 各ノードで同じ作業ディレクトリに移動しようとしています。 作業ディレクトリが設定されていない場合、HPC Pack は、Linux ノードの 1 つに作成された無作為に命名されたフォルダーでコマンドを開始します。 これにより、他のノードで次のエラーが発生します。
`/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` これを回避するには、すべてのノードでの作業ディレクトリとしてアクセスできるフォルダーのパスを指定します。

5.  クリックして **送信** このジョブを実行します。

    既定では、HPC Pack は、ログオンした現在のユーザー アカウントとしてジョブを送信します。 ダイアログ ボックスをクリックした後に、ユーザー名とパスワードを入力するように求める **送信**します。

    ![ジョブの資格情報][creds]

    条件によっては、HPC Pack は前に入力されたユーザー情報を記憶していて、このダイアログ ボックスを表示しません。 このダイアログ ボックスが HPC Pack によって再び表示されるようにするには、コマンド ウィンドウに次のコマンドを入力し、ジョブを送信します。

    ```
    hpccred delcreds
    ```

6.  ジョブが終了するまで数分かかります。

7.  ジョブのログを見つける \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log と出力ファイルに \\<headnode>\Namd\namd2\namdsample\1-2-sphere\ します。

8.  必要に応じて、VMD を起動してジョブの結果を表示します。 この記事では、NAMD を視覚化するための手順 (この場合は、水球体のユビキチン タンパク質分子) については説明しません。 参照してください [NAMD チュートリアル](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) 詳細です。

    ![ジョブの結果][vmd_view]

## サンプル ファイル

### サンプル hpccharmrun.sh スクリプト

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
### サンプル cred.xml ファイル

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png


