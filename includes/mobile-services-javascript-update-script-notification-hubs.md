

最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1. をクリックして **TodoItem**, 、] をクリックして **スクリプト** 選択 **挿入**します。 

2. クリックして insert 関数を次のコードに置き換えます **保存**:

        function insert(item, user, request) {
        // Define a payload for the Windows Store toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
            '<binding template="ToastText01">  <text id="1">' +
            item.text + '</text></binding></visual></toast>';
        
        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.wns.send(null, payload, 'wns/toast', {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });
        }

    この insert スクリプトによって、挿入が成功した後、すべての Windows ストア アプリケーション登録にプッシュ通知が (挿入された項目のテキストと共に) 送信されます。


