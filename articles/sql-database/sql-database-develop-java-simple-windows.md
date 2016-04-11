<properties
    pageTitle="Windows 上で JDBC を含む Java を使用して、SQL Database に接続する"
    description="Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。 サンプルは JDBC を使用し、Windows クライアント コンピューター上で実行されます。"
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jeffreyg"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="lbosq"/>


# Windows 上で JDBC を含む Java を使用して、SQL Database に接続する


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


このトピックでは、Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。 Java サンプルは、Java Development Kit (JDK) バージョン 1.8 に依存します。 サンプルは、JDBC ドライバーを使用して、Azure SQL Database に接続されます。


## 前提条件

### ドライバーとライブラリ

- [Microsoft JDBC Driver for SQL Server - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774)します。
- 実行する任意のオペレーティング システム プラットフォーム [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)します。

### SQL Database

参照してください、 [開始ページ](sql-database-get-started.md) にデータベースを作成する方法について説明します。  

### SQL テーブル

このトピックの Java コード例は、次のようなテスト テーブルが Azure SQL Database のデータベース内に既に存在していることを前提としています。

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


    CREATE TABLE Person
    (
        id         INT    PRIMARY KEY    IDENTITY(1,1),
        firstName  VARCHAR(32),
        lastName   VARCHAR(32),
        age        INT
    );


## 手順 1. 接続文字列を取得する

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] JTDS JDBC ドライバーを使用しているかどうかは、追加する必要があります"ssl = が必要"の接続の URL に文字列とする必要があります、JVM を次のオプションを設定する"-Djsse.enableCBCProtection=false"です。 この JVM オプションはセキュリティの脆弱性を修正するプログラムを無効にするため、このオプションを設定する前に、どのようなリスクがあるかを必ず理解しておいてください。


## 手順 2: Java コード サンプルをコンパイルします。


Java サンプル コードの全体について説明します。 後続のセクションには小さい Java セグメントをコピー アンド ペーストする場所を示すコメントが含まれています。 このセクションのサンプルはコメントの近くでコピー アンド ペーストしなくてもコンパイルされ、実行されますが、接続して、終了するのみになります。 コメントを次に示します。


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


次に Java コード サンプルの全体について説明します。 この例では、`SQLDatabaseTest` クラスの `main` の関数が含まれています。


    import java.sql.*;
    import com.microsoft.sqlserver.jdbc.*;

    public class SQLDatabaseTest {

        public static void main(String[] args) {
            String connectionString =
                "jdbc:sqlserver://your_server.database.windows.net:1433;"
                + "database=your_database;"
                + "user=your_user@your_server;"
                + "password=your_password;"
                + "encrypt=true;"
                + "trustServerCertificate=false;"
                + "hostNameInCertificate=*.database.windows.net;"
                + "loginTimeout=30;";

            // Declare the JDBC objects.
            Connection connection = null;
            Statement statement = null;
            ResultSet resultSet = null;
            PreparedStatement prepsInsertPerson = null;
            PreparedStatement prepsUpdateAge = null;

            try {
                connection = DriverManager.getConnection(connectionString);

                // INSERT two rows into the table.
                // ...

                // TRANSACTION and commit for an UPDATE.
                // ...

                // SELECT rows from the table.
                // ...
            }
            catch (Exception e) {
                e.printStackTrace();
            }
            finally {
                // Close the connections after the data has been handled.
                if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
                if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
                if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
                if (statement != null) try { statement.close(); } catch(Exception e) {}
                if (connection != null) try { connection.close(); } catch(Exception e) {}
            }
        }
    }


もちろん、実際に以前の Java コード サンプルを実行するには、実際の値を接続文字列に入力して、プレース ホルダーを置き換える必要があります。


- your_server
- your_database
- your_user
- your_password


## 手順 3. 行を挿入する


この Java セグメントは、TRANSACT-SQL の INSERT ステートメントを発行して、Person テーブルに 2 つの行を挿入します。 一般的な順序は次のとおりです。


1. `Connection` オブジェクトを使用して、`PreparedStatement` オブジェクトを生成します。
 - パラメーターが含まれて `Statement.RETURN_GENERATED_KEYS` 後に自動的に生成された値を取得できるように、 **id** キーの値。
2. `PreparedStatement` オブジェクトの `execute` メソッドを呼び出します。
3. `PreparedStatement` オブジェクトを使用して、プライマリ キーに自動的に生成される数値を取得します。
 - これはの AUTO_INCREMENT 仕様に関連して、 **id** Person テーブルの列


この短い Java セグメントをコメント `// INSERT two rows into the table.` が表示されているプライマリ コード サンプルにコピー アンド ペーストします。


    // Create and execute an INSERT SQL prepared statement.
    String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
        + "('Bill', 'Gates', 59), "
        + "('Steve', 'Ballmer', 59);";

    prepsInsertPerson = connection.prepareStatement(
        insertSql,
        Statement.RETURN_GENERATED_KEYS);
    prepsInsertPerson.execute();
    // Retrieve the generated key from the insert.
    resultSet = prepsInsertPerson.getGeneratedKeys();
    // Iterate through the set of generated keys.
    while (resultSet.next()) {
        System.out.println("Generated: " + resultSet.getString(1));
    }


## 手順 4. トランザクションをコミットする

次の Java コードのセグメントは、TRANSACT-SQL の UPDATE ステートメントを発行して、person テーブルのすべての行の `age` 値を増やします。 一般的な順序は次のとおりです。


1. `setAutoCommit` メソッドが呼び出され、更新プログラムがデータベースで自動的にコミットされることを防止します。
2. `executeUpdate` メソッドが呼び出され、トランザクションのコンテキストで更新プログラムを実行します。
3. `commit` メソッドが呼び出され、トランザクションを明示的にコミットします。


この短い Java セグメントをコメント `// TRANSACTION and commit for an UPDATE.` が表示されているプライマリ コード サンプルにコピー アンド ペーストします。


    // Set AutoCommit value to false to execute a single transaction at a time.
    connection.setAutoCommit(false);

    // Write the SQL Update instruction and get the PreparedStatement object.
    String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
    prepsUpdateAge = connection.prepareStatement(transactionSql);

    // Execute the statement.
    prepsUpdateAge.executeUpdate();

    //Commit the transaction.
    connection.commit();

    // Return the AutoCommit value to true.
    connection.setAutoCommit(true);


## 手順 5. クエリを実行する


この Java セグメントは、TRANSACT-SQL SELECT ステートメントを実行して、Person テーブルから更新されたすべての行を表示します。 一般的な順序は次のとおりです。


1. `Connection` オブジェクトを使用して、`Statement` オブジェクトを生成します。
2. `Statement` オブジェクトを使用して、`ResultSet` オブジェクトを生成します。
3. `resultSet.next` への呼び出しをループして 、返されたすべての行を反復処理します。


この短い Java セグメントをコメント `// SELECT rows from a table.` が表示されているプライマリ コード サンプルにコピー アンド ペーストします。


    // Create and execute a SELECT SQL statement.
    String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
    statement = connection.createStatement();
    resultSet = statement.executeQuery(selectSql);

    // Iterate through the result set and print the attributes.
    while (resultSet.next()) {
        System.out.println(resultSet.getString(2) + " "
            + resultSet.getString(3));
    }

## 次のステップ

詳細については、次を参照してください。、 [Java デベロッパー センター](/develop/java/)します。

