
1. ログイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/), をクリックして **Mobile Services**, 、モバイル サービスを選択します。

2. **[API]** タブをクリックし、**[作成]** をクリックします。 **[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3. **[API 名]** に「_completeall_」と入力してチェック ボタンをクリックし、新しい API を作成します。
    > [AZURE.TIP] 既定のアクセス許可を使用して、アプリケーション キーを持つだれもがカスタム API を呼び出せます。 ただし、アプリケーション キーは安全に配布または格納されていない場合があるので、安全な資格情報とは見なされません。 セキュリティ強化のための認証されたユーザーにのみアクセスを制限することを検討します。

4. API テーブルの **[completeall]** をクリックします。

5. **[スクリプト]** タブをクリックし、既存のコードを次のコードに置き換えて、**[保存]** をクリックします。 このコードにアクセスする [mssql オブジェクト] を使用して、 **todoitem** テーブルに直接設定、 `完了` すべての項目のフラグ。 **exports.post** 関数を使用するため、クライアントは POST 要求を送信して操作を実行します。 変更された行数は整数値としてクライアントに返されます。

        exports.post = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
            mssql.query(sql, {
                success: function(results) {
                    if(results.length == 1)
                        response.send(200, results[0]);
                }
            })
        };



> [AZURE.NOTE] [要求](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx) と [応答](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx) でカスタム API の関数に指定されているオブジェクトが実装されている、 [Express.js ライブラリ](http://go.microsoft.com/fwlink/p/?LinkId=309046)します。 







[mssql object]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx 

