<properties
    pageTitle="Apache Storm チュートリアル: HDInsight での Linux ベースの Storm の使用 |Microsoft Azure"
    description="Linux ベースの HDInsight での Apache Storm および Storm Starter サンプルを使用したビッグ データ分析の概要Storm を使用してデータをリアルタイムに処理する方法について説明します。"
    keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>



# Apache Storm チュートリアル: Storm Starter サンプルを使用した HDInsight でのビッグ データ分析の概要

Apache Storm は、データ ストリームの処理を目的とし、スケーラビリティとフォールト トレランスに優れた、分散型のリアルタイム計算システムです。 Azure HDInsight の Storm を使用して、Storm でリアルタイムで ビッグ データ分析を実行するクラウドベースの Storm クラスターを作成できます。
> [AZURE.NOTE] この記事の手順では、Linux ベースの HDInsight クラスターを作成します。 HDInsight クラスター上の Windows ベースの Storm を作成する手順については、を参照してください [Apache Storm チュートリアル: データ分析を使用して HDInsight で Storm Starter サンプルを使ってみる](hdinsight-apache-storm-tutorial-get-started.md)。

## 開始する前に

Apache Storm チュートリアルを正常に完了するには、次の条件を満たす必要があります。

- **Azure サブスクリプション**。 参照してください [取得 Azure 無料試用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)します。

- **SSH と SCP を熟知していること**。 HDInsight での SSH と SCP の使用方法の詳細については、次の記事をご覧ください。

    - **Linux、Unix または OS X クライアント**:を参照してください [SSH Linux、OS X または Unix から HDInsight 上の Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    - **Windows クライアント**:を参照してください [SSH Windows から Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

## Storm クラスターを作成する

HDInsight の Storm では、ログ ファイルとクラスターに送信されるトポロジを格納する Azure Blob Storage を使用します。 次の手順を使用して、クラスターに使用する Azure ストレージ アカウントを作成します。

1. サインイン、 [Azure ポータルの ][preview-portal]します。

2. **[新規]**、__[データ分析]__、__[HDInsight]__ の順にクリックします。

    ![Azure ポータルでの新しいクラスターの作成](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. __[クラスター名]__ を入力し、__[クラスターの種類]__ として __[Storm]__ を選択します。 クラスターを使用できる場合は、__[クラスター名]__ の横に緑色のチェック マークが表示されます。

    ![クラスター名、クラスターの種類、および OS の種類](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

    __[Ubuntu]__ を選択して Linux ベースの HDInsight クラスターを作成します。

4. 複数のサブスクリプションがある場合は、__[サブスクリプション]__ エントリを選択し、クラスターで使用する Azure サブスクリプションを選択します。

5. __[リソース グループ]__ では、エントリを選択して既存のリソース グループの一覧を表示し、クラスターを作成するグループを選択できます。 または、__[新規作成]__ をクリックし、新しいリソース グループの名前を入力できます。 新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。
    > [AZURE.NOTE] このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. __[資格情報]__ を選択し、__[クラスターのログイン ユーザー名]__ に対応する __[クラスターのログイン パスワード]__ を入力します。 さらに、SSH ユーザーを認証するために使用される __[SSH ユーザー名]__ と、__[パスワード]__ または __[公開キー]__ のどちらかを入力する必要があります。 最後に、__[選択]__ ボタンをクリックして資格情報を設定します。

    ![[クラスターの資格情報] ブレード](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

    HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

    * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Windows から Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-windows.md)

6. __[データ ソース]__ では、エントリを選択することで既存のデータ ソースを選択するか、新しいデータ ソースを作成できます。

    ![[データ ソース] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)

    現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。 次の説明を参照して、__[データ ソース]__ ブレードのエントリを理解してください。

    - __[選択方法]__: サブスクリプションのストレージ アカウントを参照可能にする場合は、__[すべてのサブスクリプションから]__ を設定します。 既存のストレージ アカウントの __[ストレージ名]__ と __[アクセス キー]__ を入力する場合は、__[アクセス キー]__ を設定します。

    - __新規作成__: これを使用して、新しいストレージ アカウントを作成します。 表示されたフィールドに、ストレージ アカウントの名前を入力します。 名前を使用できる場合は、緑色のチェック マークが表示されます。

    - __[既定のコンテナーの選択]__: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。 任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

    - __[場所]__: ストレージ アカウントが存在するリージョン、またはストレージ アカウントの作成先のリージョンです。
        > [AZURE.IMPORTANT] 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。 クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

    - __[選択]__: これを使用してデータ ソースの構成を保存します。

7. __[ノード価格レベル]__ を選択して、このクラスターに対して作成されるノードに関する情報を表示します。 既定では、worker ノードの数は __4__ に設定されます。 クラスターの推定コストがブレードの下部に表示されます。

    ![[ノード価格レベル] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)

    __[選択]__ ボタンを使用して、__[ノード価格レベル]__ 情報がを保存します。

8. __[オプションの構成]__ を選択します。 このブレードでは、クラスターのバージョンを選択し、__Virtual Network__ への参加や Hive と Oozie 用のデータを保持する__外部メタストア__の設定などのその他のオプションの設定を構成できます。

    ![[オプションの構成] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)

9. __[スタート画面にピン留めする]__ が選択されていることを確認し、__[作成]__ をクリックします。 これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。 アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

   | プロビジョニング中| プロビジョニング完了|
   | ------------------ | --------------------- |
   | ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png)| ![プロビジョニングされたクラスターのタイル](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png)|

    > [AZURE.NOTE] クラスターが作成されるまで、通常は約 15 分かかります。 プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の __[通知]__ エントリを使用します。

## HDInsight での Storm Starter サンプルの実行

[Storm starter](https://github.com/apache/storm/tree/master/examples/storm-starter) の例は、HDInsight クラスターに含まれています。 次の手順では、WordCount の例を実行します。

1. SSH を使用して HDInsight クラスターに接続します。

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合は、使用する必要があります、 `-i` パラメーターを対応する秘密キーを指定します。 たとえば、 `ssh-i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`します。

    Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

    * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Windows から Linux ベースの Hadoop で SSH を使用します。](hdinsight-hadoop-linux-use-ssh-windows)

2. 次のコマンドを実行してトポロジの例を開始します。

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount

    > [AZURE.NOTE] `0.9.3.2.2.4.9-1` Storm の新しいバージョンに HDinsight が更新されると、ファイル名の部分を変更することがあります。

    クラスター上で、’wordcount’ というフレンドリ名の WordCount トポロジの例が開始されます。 文はランダムに生成され、文中の各単語の出現回数がカウントされます。
    > [AZURE.NOTE] クラスターにトポロジを送信するときにまずを使用する前に、クラスターを含む jar ファイルをコピーする必要があります、 `storm` コマンドです。 これを使用して実行することができます、 `scp` コマンド ファイルが存在するクライアントを実行します。 たとえば、 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > ワード カウント例とその他の storm starter 例については、クラスターに含まれて `/usr/hdp/current/storm-client/contrib/storm-starter/`します。

## トポロジの監視

Storm UI には、トポロジの実行を操作する Web インターフェイスがあり、HDInsight クラスターに含まれています。
> [AZURE.IMPORTANT] Storm UI は、インターネットでパブリックに使用できません。SSH トンネルを使用して HDInsight クラスター ヘッド ノードにアクセスする必要があります。 詳細については、次を参照してください。 [使用 SSH トンネリング ResourceManager、JobHistory、NameNode、Oozie、およびその他の web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)します。

次の手順で Storm UI を表示します。

1. クラスターへの SSH トンネルを作成した後に https://CLUSTERNAME.azurehdinsight.net、web ブラウザーを開き、 __CLUSTERNAME__ 、クラスターの名前を指定します。 Ambari Web UI が開きます。
    > [AZURE.NOTE] ユーザー名とパスワードの入力が求められたら、クラスターの作成時に使用したクラスター管理者名 (admin) とパスワードを入力します。 ブラウザーと Ambari Web UI からそれぞれ認証を求められることがあります。この場合は、両方に同じ資格情報を使用します。

2. ページの左側のサービスの一覧で、__[Storm]__ を選択します。 __[クイック リンク]__ で __[Storm UI]__ を選択します。

    ![クイック リンクの Storm UI エントリ](./media/hdinsight-apache-storm-tutorial-get-started-linux/ambari-storm.png)

    これにより、Storm UI が表示されます。

    ![Storm UI](./media/hdinsight-apache-storm-tutorial-get-started-linux/stormui.png)
    > [AZURE.NOTE] サーバーが見つからないというエラーを受信する場合は、クラスターに対する SSH トンネルが確立していない可能性があります。 参照してください [使用 SSH トンネリング ResourceManager、JobHistory、NameNode、Oozie、およびその他の web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md) の詳細。

4. **[Topology summary]** で、**[名前]** 列の **[wordcount]** エントリを選択します。 これにより、トポロジの詳細が表示されます。

    ![Storm Starter WordCount トポロジの情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

    このページには、次の情報が表示されます。

    * **トポロジの統計** - 時間枠で整理された、トポロジのパフォーマンスに関する基本的な情報。
        > [AZURE.NOTE] 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。

    * **スパウト** - 各スパウトによって返された最後のエラーを含む、スパウト関する基本的な情報。

    * **ボルト** - ボルトに関する基本的な情報。

    * **トポロジの構成** - トポロジの構成に関する詳細情報。

    このページには、トポロジで実行できるアクションも表示されます。

    * **アクティブ化** - アクティブ化が解除されたトポロジの処理を再開します。

    * **アクティブ化の解除** - 実行中のトポロジを一時停止します

    * **再調整** - トポロジの並列処理を調整します。 クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。 この操作で、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。 詳細については、次を参照してください。 [Storm トポロジの並列処理を理解する](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)します。

    * **強制終了** - 指定したタイムアウト後に Storm トポロジを停止します。

5. このページで、**[スパウト]** または **[ボルト]** セクションからエントリを選択します。 選択したコンポーネントに関する情報が表示されます。

    ![選択したコンポーネントに関する情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

    このページには次の情報が表示されます。

    * **スパウト / ボルトの統計** - 時間枠で整理された、コンポーネントのパフォーマンスに関する基本的な情報。
        > [AZURE.NOTE] 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。

    * **入力の統計** (ボルトのみ) - ボルトによって使用されるデータを生成するコンポーネントに関する情報。

    * **出力の統計** - このボルトによって出力されるデータに関する情報。

    * **エグゼキュータ** - このコンポーネントのインスタンスに関する情報。

    * **エラー** - このコンポーネントで生成されたエラー。

5. スパウトかボルトに関する詳細を表示した状態で、**[エグゼキュータ]** セクションの **[ポート]** 列でエントリを選択 し、コンポーネントの特定のインスタンスの詳細を表示します。

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    このデータでは、**seven** という単語が 1493957 回発生したことを確認できます。 これは、このトポロジが開始されてから発生した回数です。

## トポロジを停止する

ワードカウント トポロジの **[トポロジの概要]** ページに戻り、**[トポロジのアクション]** セクションで **[強制終了]** を選択します。 メッセージが表示されたら、トポロジを停止するまでの待機秒数として「10」を入力します。 タイムアウト期間後は、ダッシュボードの **[Storm UI]** セクションにアクセスしても、トポロジは表示されません。

## 概要

この Apache Storm チュートリアルでは、Storm Starter を使用して、HDInsight クラスターで Storm を作成する方法と、Storm ダッシュボードを使用して Storm トポロジをデプロイ、監視、管理する方法について説明しました。

## <a id="next"></a>次のステップ

* 次のドキュメントには、HDInsight の Storm と使用できるその他のサンプルの一覧が含まれています。

    * [HDInsight での Storm に関するトポロジ例](hdinsight-storm-example-topology.md)


[apachestorm]: https://storm.incubator.apache.org 
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html 
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter 
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/ 
[azureportal]: https://manage.windowsazure.com/ 
[hdinsight-provision]: hdinsight-provision-clusters.md 
[preview-portal]: https://portal.azure.com/ 

