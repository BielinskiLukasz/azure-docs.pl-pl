<properties
    pageTitle="SQL Database に接続する PHP の再試行ロジック | Microsoft Azure"
    description="一時的なエラーの処理で Windows クライアントから Azure SQL Database に接続するサンプル PHP プログラムを示し、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供します。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="meetb"/>


# 一時的なエラーの処理での Windows 上の PHP を使用した SQL Database への接続


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Windows 上で実行される PHP で記述されたクライアント アプリケーションから Azure SQL Database に接続する方法について説明します。


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### SQL Database

参照してください、 [開始ページ](sql-database-get-started.md) にサンプル データベースを作成する方法について説明します。  作成するガイドを実行する重要である、 **AdventureWorks データベースのテンプレート**します。 のみ以下に示す例を使用、 **AdventureWorks スキーマ**します。


## 手順 1. 接続の詳細を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 手順 2: 接続し、クエリ

デモ プログラムは、接続の試行中に一時的なエラーが生じると再試行が行われるように設計されています。 しかし、クエリ コマンドの実行中に一時的なエラーが生じると、プログラムによって接続が破棄され、新しい接続が作成された後、クエリ コマンドが再試行されます。 この設計の選択については、推奨も、非推奨もしません。 デモ プログラムは、使用可能な設計上の柔軟性をいくつか示しています。

<br>このコード サンプルの内容の大部分は、例外キャッチのロジックの記述です。 この Program.cs ファイルの短いバージョンはこの [ここ](sql-database-develop-php-simple-windows.md)します。
<br>Main メソッドは Program.cs にあります。 コール スタックで次のとおり実行します。
* Main が ConnectAndQuery を呼び出します。
* ConnectAndQuery 呼び出し establishconnection を呼び出します。
* Issuequerycommand を呼び出します。

 [Sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) 関数を使用して、結果 SQL Database に対するクエリのセットを取得します。 この関数は本質的には任意のクエリと接続オブジェクトを受け入れるしの使用を反復処理できる結果セットを返します [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php)します。

    <?php
        // Variables to tune the retry logic.  
        $connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
        $maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
        $secondsBetweenRetries = 4;  // Simple retry strategy.
        $errNo = 0;
        $serverName = "tcp:yourdatabase.database.windows.net,1433";
        $connectionOptions = array("Database"=>"AdventureWorks",
           "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
        $conn;
        $errorArr = array();
        for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
        {
            $errorArr = array();
            $ctr = 0;
            // [A.2] Connect, which proceeds to issue a query command.
            $conn = sqlsrv_connect($serverName, $connectionOptions);  
            if( $conn == true)
            {
                echo "Connection was established";
                echo "<br>";

                $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
                $getProducts = sqlsrv_query($conn, $tsql);
                if ($getProducts == FALSE)
                    die(FormatErrors(sqlsrv_errors()));
                $productCount = 0;
                $ctr = 0;
                while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
                {   
                    $ctr++;
                    echo($row['CompanyName']);
                    echo("<br/>");
                    $productCount++;
                    if($ctr>10)
                        break;
                }
                sqlsrv_free_stmt($getProducts);
                break;
            }
            // Adds any the error codes from the SQL Exception to an array.
            else {  
                if( ($errors = sqlsrv_errors() ) != null) {
                    foreach( $errors as $error ) {
                        $errorArr[$ctr] = $error['code'];
                        $ctr = $ctr + 1;
                    }
                }
                $isTransientError = TRUE;
                // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
                $isTransientError = IsTransientStatic($errorArr);
                if ($isTransientError == TRUE)  // Is a static persistent error...
                {
                    echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate.");
                    echo "<br>";
                    // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
                    // Break the loop, let the hopeless program end.
                    exit(0);
                }
                // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
                // So let this method reloop and try again. However, we recommend that the new query
                // attempt should start at the beginning and establish a new connection.
                if ($cc >= $maxCountTriesConnectAndQuery)
                {
                    echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
                    echo "<br>";
                    exit(0);
                }
                echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
                echo "<br>";
                echo $cc . " Attempts so far. Might retry.";
                echo "<br>";
                // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
                sleep(1*$secondsBetweenRetries);
            }
            // [A.3] All has gone well, so let the program end.
        }
        function IsTransientStatic($errorArr) {
            $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
            $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
            $count = count($result);
            if($count >= 0) //change to > 0 later.
                return TRUE;
        }
    ?>

## 次のステップ

PHP のインストールと使用に関する詳細については、次を参照してください。 [PHP で SQL Server データベースをアクセスする](http://technet.microsoft.com/library/cc793139.aspx)です。

