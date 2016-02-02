<properties 
    pageTitle="SQL Database への接続: ベスト プラクティス | Microsoft Azure" 
    description="ADO.NET や PHP などのテクノロジから Azure SQL Database に接続するクライアント プログラムのリンクとベスト プラクティスの推奨事項を集めた、開始点となるトピック。" 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jeffreyg" 
    editor=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/13/2015" 
    ms.author="genemi"/>



# SQL Database への接続: ベスト プラクティスと設計のガイドライン

このトピックは、Azure SQL Database にクライアント接続を開始するときにお勧めします。 ここには、SQL Database に接続して対話的に作業するためのさまざまなテクノロジのコード サンプルへのリンクがあります。 これらのテクノロジは、Enterprise Library、JDBC、PHP を含め、その他にもいくつかあります。 提供される情報は、SQL Database への接続に使用する特定のテクノロジとは関係なく適用されます。


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## テクノロジに依存しない推奨事項

- [プログラムで SQL データベースを Azure に接続するためのガイドライン](http://msdn.microsoft.com/library/azure/ee336282.aspx) のディスカッションには、次が含まれます。
 - [ポートとファイアウォール](sql-database-configure-firewall-settings.md)
 - Connection strings
- [Azure SQL Database のリソース管理](http://msdn.microsoft.com/library/azure/dn338083.aspx) のディスカッションには、次が含まれます。
 - リソース ガバナンス
 - 制限の適用
 - 調整


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## 認証における推奨事項

- Azure SQL Database で使用できない Windows 認証ではなく、Azure SQL Database 認証を使用します。
- 既定の*マスター* データベースではなく、特定のデータベースを明示的に指定してください。
 - Transact-SQL の **USE myDatabaseName;** ステートメントを SQL Database に対して使用して別のデータベースに切り替えることはできません。


### 包含ユーザー

SQL Database へのユーザー追加には、以下の選択肢があります。

- **マスター**データベースに*ログイン*とパスワードを追加し、それから同じサーバーの 1 つ以上の他のデータベースに対応する*ユーザー*を追加する。

- 1 つ以上のデータベースに*包含ユーザー*とパスワードを追加し、**マスター**へのいかなる*ログイン*にもリンクしないようにする。


包含ユーザーのアプローチには以下のような長所と短所があります。

- 長所は、データベースのすべてのユーザーが包含ユーザーである場合、データベースを 1 つの Azure SQL Database サーバーから他の Azure SQL Database サーバーに簡単に移動できることです。

- 短所は、日常的な管理がより難解になることです。 次に例を示します。
 - 1 つのログインを削除するよりも、複数の包含ユーザーを削除するほうがより難しくなります。
 - 複数のデータベースにおいて包含ユーザーである作業者は、より多くのパスワードの記憶や更新が必要となります。


詳細情報が指定された [包含データベース ユーザー - ポータブルなデータベース](http://msdn.microsoft.com/library/ff929188.aspx)します。


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## 接続における推奨事項

- クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。
 - 既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。

- クライアント プログラムをホストするコンピューターのファイアウォールで、ポート 1433 での発信 TCP が許可されていることを確認します。

- クライアントが Azure 仮想マシン (VM) で実行されるとき、クライアント プログラムが SQL Database V12 に接続する場合、VM でポート範囲の 11000-11999 と 14000-14999 を開く必要があります。 をクリックして [ここ](sql-database-develop-direct-route-ports-adonet-v12.md) 詳細です。

- 処理する *一時的な障害*, 、追加 [* 再試行 * ロジック](#TransientFaultsAndRetryLogicGm) Azure SQL データベースとやり取りするクライアント プログラムにします。


### 接続プール

使用している場合、 [接続プール](http://msdn.microsoft.com/library/8xx3tyca.aspx), 、接続、プログラムが積極的に、使用しないと再使用の準備されていない時間、接続を閉じます。

プログラムがその接続を別の操作のために休止せずに即座に再使用するというのでない限り、以下のパターンが推奨されています。

- 接続を開きます。
- 接続を通じて操作を 1 つ実行します。
- 接続を閉じます。


### V12 の 1433 以外のポート

Azure SQL Database V12 へのクライアント接続はプロキシを使用せずに、データベースに直接やり取りする場合があります。 1433 以外のポートが重要になります。 詳細情報を参照してください。<br/>
[ADO.NET 4.5 および SQL Database V12 の 1433年以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)


次のセクションでは、再試行のロジックと一時的なエラーの処理についてさらに説明します。



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## 一時的なエラーと再試行のロジック

Azure システムには、SQL Database サービス内で負荷の大きいワークロードが生じた場合に、サーバーを動的に再構成する機能があります。

ただし、再構成をすることでクライアントのプログラム側では SQL Database への接続が失われます。 このエラーを*一時障害*と呼びます。

クライアント プログラムに再試行ロジックが含まれる場合、一時障害に自動的に修復する時間を与えた後、接続の再確立を試行できます。

最初に再試行する前に、5 秒間待つことをお勧めします。 5 秒未満で再試行すると、クラウド サービスに過度の負荷がかかるおそれがあります。 再試行するたびに、待ち時間を比例して、最大 60 秒まで長くする必要があります。

詳細については、 *ブロック期間* ADO.NET を使用しているクライアントで使用できる、 [SQL サーバー接続プール (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)します。


再試行ロジックを示すコード サンプルについては、次の記事をご覧ください。
- [SQL Database のクライアント クイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)


### 一時障害のエラー番号

SQL Database でエラーが発生したときに、 [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) がスローされます。 **SqlException** の **Number** プロパティには、数値のエラー コードが含まれています。 一時的なエラーのエラー コードである場合、プログラムで呼び出しを再試行してください。


- [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - このメッセージの **Transient Errors, Connection-Loss Errors** セクションには、自動再試行が必ず実行される一時エラーのリストが示されています。
 - たとえば、再試行番号 40613 のエラーが発生した場合、以下のように<br/>*データベース サーバー 'theserver' 上には、' mydatabase' は現在使用できません*。


詳細については、以下を参照してください。
- [Azure SQL Database 開発: 操作方法に関するトピック](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Azure SQL Database への接続に関する問題のトラブルシューティングを行う](http://support.microsoft.com/kb/2980233/)


<a id="e-technologies" name="e-technologies"></a>

## テクノロジ

次のトピックには、クライアント プログラムから Azure SQL Database への接続に使用できる、いくつかの言語のコード サンプルと、ドライバー テクノロジへのリンクが含まれています。


Windows、Linux、および Mac OS X で実行するクライアントに使用できるさまざまなコード サンプルがあります。


**一般的なサンプル:** は [コード サンプルは](sql-database-develop-quick-start-client-code-samples.md) さまざまなプログラミング言語、PHP、Python、Node.js、および .NET CSharp などです。 また、Windows、Linux、および Mac OS X 上で実行されるクライアント用のサンプルもあります。


**Elastic Scale:** Elastic Scale データベースへの接続に関する詳細については、次を参照してください。

- [Azure SQL データベース Elastic Scale プレビューの概要します。](sql-database-elastic-scale-get-started.md)
- [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)


**ドライバー ライブラリ:** 推奨バージョンを含む接続ドライバー ライブラリの詳細については、次を参照してください。

- [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)






