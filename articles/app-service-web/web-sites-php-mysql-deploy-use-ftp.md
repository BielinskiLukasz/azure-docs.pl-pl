<properties 
    pageTitle="Azure App Service で PHP-MySQL Web アプリを作成して FTP でデプロイする" 
    description="MySQL にデータを保存する PHP Web アプリを作成し、Azure への FTP デプロイを使用する方法を説明するチュートリアル。" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="tomfitz"/>


#Azure App Service で PHP-MySQL Web アプリを作成して FTP でデプロイする

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

このチュートリアルでは、PHP-MySQL Web アプリを作成する方法と、FTP を使用してそれをデプロイする方法について説明します。 このチュートリアルでは、ある [PHP][install-php], 、[MySQL][install-mysql], 、web サーバー、および FTP クライアントがコンピューターにインストールします。 このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで実行できます。 このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web アプリが完成します。
 
学習内容:

* Azure ポータルを使用して Web アプリと MySQL データベースを作成する方法。 Web Apps では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* FTP を使用して Azure にアプリケーションを発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリを PHP で作成します。 このアプリケーションは Web Apps でホストされます。 完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

>[AZURE.NOTE] 場合は、アカウントにサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。 


##Web アプリの作成と FTP 発行の設定

Web アプリと MySQL データベースを作成するには、次のステップに従います。

1. ログイン、 [Azure ポータル][management-portal]します。
2. クリックして、 **+ 新規** 下部にあるアイコンは、Azure ポータルの左します。

    ![新しい Azure の Web サイトの作成][new-website]

3. クリックして **Web + モバイル**, 、し **Web アプリ + MySQL**します。

    ![Custom Create a new Web Site][custom-create]

4. リソース グループの有効な名前を入力します。

    ![リソース グループ名の設定][resource-group]

5. 新しい Web アプリについての値を入力します。

     ![Web アプリの作成][new-web-app]

6. 法律条項への同意も含めて、新しいデータベースについての値を入力します。

    ![新しい MySQL データベースの作成][new-mysql-db]
    
7. Web アプリが作成されると、新しいリソース グループが表示されます。 設定を構成する Web アプリの名前をクリックします。

    ![Web アプリを開く][go-to-webapp]

6. 表示されるまで下へスクロール **デプロイ資格情報を設定**します。 

    ![デプロイメント資格情報の設定][set-deployment-credentials]

7. FTP 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。 資格情報を保存します。作成したユーザー名とパスワードはメモしておいてください。

    ![発行資格情報の作成][portal-ftp-username-password]

##アプリケーションの作成とローカル テスト

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。 それまでの登録者情報がテーブルに表示されます。 登録情報は MySQL データベースに保存されます。 アプリケーションは、次の 2 つのファイルで構成されます。

* **index.php**: 登録と登録者情報を含むテーブルにフォームが表示されます。
* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。 このファイルは 1 度しか使用されません。

アプリケーションを作成してローカルで実行するには、次の手順に従います。 次の手順は、PHP、MySQL、および web サーバーが、ローカル コンピューターで設定があることを前提としてに有効にした、 [MySQL 用 PDO 拡張機能][pdo-mysql]です。

1. "`registration`" という MySQL データベースを作成します。 これには、MySQL コマンド プロンプトで次のコマンドを実行します。

        mysql> create database registration;

2. Web サーバーのルート ディレクトリで、`registration` というフォルダーを作成し、その中に 2 つのファイル (`createtable.php` と `index.php`) を作成します。

3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。 このコードは、`registration_tbl` データベースに `registration` テーブルを作成するために使用します。

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > <code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

4. Web ブラウザーを開きを参照 [http://localhost/registration/createtable.php][localhost-createtable]します。 このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。

5. 開いている、 **index.php** ファイルをテキスト エディターまたは IDE でし (PHP コードは後で追加する) ページの基本的な HTML および CSS コードを追加します。

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6. PHP タグ内に、データベースに接続するための PHP コードを追加します。

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > ここでも、<code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

7. データベース接続コードの次に、登録情報をデータベースに挿入するためのコードを追加します。

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8. 上のコードの次に、データベースからデータを取得するためのコードを追加します。

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

移動できるようになりました [http://localhost/registration/index.php][localhost-index] アプリケーションをテストします。

##MySQL と FTP の接続情報の取得

Web Apps で実行されている MySQL データベースに接続するには、接続情報が必要になります。 MySQL の接続情報を取得するには、次の手順に従います。

1. リソース グループで、データベースをクリックします。

    ![データベースの選択][select-database]

2. データベースのサマリで、次のように選択します。 **プロパティ**します。

    ![プロパティの選択][select-properties]
    
2. `Database`、`Host`、`User Id`、`Password` の各値をメモします。

    ![プロパティへの注記][note-properties]

3. Web アプリをクリックして、 **発行プロファイルのダウンロード** 、ページの右下隅の下部にあるリンク。

    ![発行プロファイルのダウンロード][download-publish-profile]

4. XML エディターで `.publishsettings` ファイルを開きます。 

3. 次のように `<publishProfile >` が指定されている `publishMethod="FTP"` 要素を確認します。

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
`publishUrl`、`userName`、`userPWD` の各属性を記録します。

##アプリケーションの発行

アプリケーションをローカルでテストした後、FTP を使用してそのアプリケーションを Web アプリに発行できます。 ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。 先に取得したデータベース接続情報を使用して (で、 **取得 MySQL と FTP の接続情報** セクション) で次の情報を更新 **両方** 、 `createdatabase.php` と `index.php` ファイルの適切な値。

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

これで、FTP を使用してアプリケーションを発行する準備ができました。

1. 好みの FTP クライアントを開きます。

2. 入力、 *ホスト名部分* から、 `publishUrl` FTP クライアントに上でメモした属性です。

3. 先ほどメモしておいた `userName` 属性および `userPWD` 属性をそのまま FTP クライアントに入力します。

4. 接続を確立します。

接続した後、必要に応じて、ファイルをアップロードおよびダウンロードすることができます。 ファイルのアップロード先は、必ずルート ディレクトリ (`/site/wwwroot`) にしてください。

両方をアップロードした後は `index.php` と `createtable.php`, を参照 **http://[site name].azurewebsites.net/createtable.php** するアプリケーション用の MySQL テーブルを作成しを参照して **http://[site name].azurewebsites.net/index.php** アプリケーションの使用を開始します。
 
## 次のステップ

詳細については、次を参照してください。、 [PHP デベロッパー センター](/develop/php/)します。

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 

