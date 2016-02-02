## コピー中の再現性

データをリレーショナル ストアへコピーする場合、またはリレーショナル ストアからコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。

**注:**スライスは、再試行ポリシーで指定された回数、 Azure データ ファクトリ内で自動的に再実行することができます。 一時的なエラーを回避するため、再試行ポリシーを設定することをお勧めします。 したがって、再現性は、データの移動中に注意すべき重要な側面です。

**ソースとして:**

多くの場合、リレーショナル ストアからの読み取り時にそのスライスに対応するデータのみを読み込むことになります。 これを行うには、Azure データ ファクトリで使用可能な、WindowStart と WindowEnd 変数を使用します。 変数と Azure Data Factory では、ここでの関数について、 [スケジュールと実行](data-factory-scheduling-and-execution.md) 記事です。 例:

      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

上記のクエリでは、スライス期間の範囲内にある "MyTable" からデータを読み取ります。 このスライスを再実行すると、この動作は常に確認されます。

その他の場合は、テーブル全体の読み取り (たとえば1 回の移動のみ) にし、次のように、sqlReaderQuery を定義することもできます。


    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },



