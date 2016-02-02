* 手順に従う [クライアント SSL 署名 Id をサーバーにインストールする](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) .p12 ファイルには、前の手順でダウンロードした証明書をエクスポートします。

* Azure クラシック ポータルで、**[Mobile Services]**、使用するアプリ、**[プッシュ]** タブ、**[Apple プッシュ通知の設定]**、**[アップロード]** の順にクリックします。 .p12 ファイルをアップロードし、正しい **[モード]** が選択されていることを確認します (生成したクライアント SSL 証明書が開発用か配布用かに応じて、[サンドボックス] または [運用])。 これで、iOS のプッシュ通知と連携するようにモバイル サービスが構成されました。





