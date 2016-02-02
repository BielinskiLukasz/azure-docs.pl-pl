## SQL シンクのストアド プロシージャの呼び出し

データの SQL Server または Azure SQL/SQL Server Database へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。

組み込みのコピー メカニズムが目的通り機能しない場合は、ストアド プロシージャを利用できます。 これは通常活用される余分な処理するときに (列を結合するには、追加の検索、値の複数のテーブルへの挿入]) 変換先テーブル内のソース データを最終的に挿入する前に実行する必要があります。

任意のストアド プロシージャを呼び出すことができます。 次の例では、データベース内のテーブルに簡単な挿入を行うストアド プロシージャを使用する方法を示します。

**出力データセット**

この例では、type は SqlServerTable に設定されています。 Azure SQL データベースで使用するには、type を AzureSqlTable に設定します。

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

次のように、コピー アクティビティ JSON の SqlSink セクションを定義します。 データの挿入中にストアド プロシージャを呼び出すには、SqlWriterStoredProcedureName と SqlWriterTableType の両方のプロパティが必要です。

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

データベース内で、SqlWriterStoredProcedureName と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルに挿入されます。 ストアド プロシージャのパラメーター名は、テーブル JSON ファイルで定義された tableName と同じにする必要があります。

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

データベースで、SqlWriterTableType と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じにする必要があります。

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
    )

ストアド プロシージャ機能は利用 [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)します。




