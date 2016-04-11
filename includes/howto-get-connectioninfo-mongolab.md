MongoLab データベースをプロビジョニングすると、MongoLab により Azure に MongoDB の標準接続文字列形式で URI が送信されます。 この値は、選択した MongoDB ドライバーをとおして MongoDB 接続を初期化するために使用されます。 接続文字列の詳細については、次を参照してください。 [接続](http://www.mongodb.org/display/DOCS/Connections) を確認してください。

**この URI は、データベース ユーザー名とパスワードを含んでいます。  機密情報として扱い、他人と共有しないでください。**

この URI は、Azure ポータルで次のステップを使用して取得できます。

1. 選択 **アドオン**します。  
![AddonsButton][button-addons]
1. アドオンの一覧で MongoLab サービスを見つけます。  
![MongolabEntry][entry-mongolabaddon]
1. アドオンの名前をクリックして、アドオン ページに移動します。
1. クリックして **接続情報]**します。  
![ConnectionInfoButton][button-connectioninfo]  
MongoLab の URI が表示されます。  
![ConnectionInfoScreen][screen-connectioninfo]  
1.  MONGOLAB_URI 値の右側のクリップボード ボタンをクリックし、値全体をクリップボードにコピーします。

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

