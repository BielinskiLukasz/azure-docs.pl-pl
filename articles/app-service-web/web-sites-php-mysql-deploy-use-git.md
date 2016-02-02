<properties
    pageTitle="Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする"
    description="MySQL にデータを保存する PHP Web アプリを作成し、Azure への Git デプロイを使用する方法を説明するチュートリアル。"
    services="app-service\web"
    documentationCenter="php"
    authors="tfitzmac"
    manager="wpickett"
    editor="mollybos"
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="tomfitz"/>


# Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)


このチュートリアルでは、Php-mysql web アプリを作成する方法に展開する方法 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Git を使用しています。 使用する [PHP ][install-php], 、MySQL コマンド ライン ツール (の一部では [MySQL のインストール、mysql][install-mysql])、および [Git ][install-git] お使いのコンピューターにインストールされています。 このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで実行できます。 このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web アプリが完成します。

学習内容:

* Web アプリと MySQL データベースを使用して、作成する方法、 [Azure ポータル](https://portal.azure.com)します。 PHP がで有効になっているため [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 既定では、特に何もしなくて PHP コードを実行します。
* Git を使用して Azure にアプリケーションを発行および再発行する方法。

このチュートリアルでは、登録用の単純な Web アプリを PHP で作成します。 アプリケーションは、Web Apps でホストされます。 完成したアプリケーションのスクリーンショットは次のようになります。

![Azure の PHP Web サイト][running-app]

## 開発環境を設定する

このチュートリアルには、必要 [PHP ][install-php], 、MySQL コマンド ライン ツール (の一部では [MySQL のインストール、mysql][install-mysql])、および [Git ][install-git] 、コンピューターにインストールします。


## <a id="create-web-site-and-set-up-git"></a>Web アプリの作成し、Git 発行の設定

Web アプリと MySQL データベースを作成するには、次のステップに従います。

1. ログイン、 [Azure ポータルの ][management-portal]します。
2. **[次へ]** アイコンをクリックします。

3. **[Marketplace]** の横の **[すべて表示]** をクリック します。

4. **[Web + モバイル]** をクリックし、**[Web アプリ + MySQL]** をクリックします。 **[作成]** をクリックします。

4. リソース グループの有効な名前を入力します。

    ![リソース グループ名の設定][resource-group]

5. 新しい Web アプリについての値を入力します。

    ![Web アプリの作成][new-web-app]

6. 法律条項への同意も含めて、新しいデータベースについての値を入力します。

    ![新しい MySQL データベースの作成][new-mysql-db]

7. Web アプリが作成されると、新しい Web アプリのブレードが表示されます。

7. **設定** ] をクリックして **継続的なデプロイ**, 、必要な _Configure settings_ をクリックします。

    ![Git 発行の設定][setup-publishing]

8. ソース コード用の **[ローカル Git リポジトリ]** を選択します。

    ![Git リポジトリの設定][setup-repository]

9. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。 作成するユーザー名とパスワードはメモしておいてください (Git リポジトリを設定したことがある場合は、この手順をスキップできます)。

    ![発行資格情報の作成][credentials]


## MySQL のリモート接続情報の取得

Web Apps で実行されている MySQL データベースに接続するには、接続情報が必要になります。 MySQL の接続情報を取得するには、次の手順に従います。

1. リソース グループで、データベースをクリックします。

    ![データベースの選択][select-database]

2. データベースの **[設定]** で、**[プロパティ]** を選択します。

    ![プロパティの選択][select-properties]

2. 値を書き留めて `データベース`, 、`ホスト`, 、`ユーザー Id`, 、および `パスワード`します。

    ![プロパティへの注記][note-properties]

## アプリケーションの作成とローカル テスト

Web アプリを作成したので、アプリケーションをローカルで作成し、それをテストした後にデプロイすることができます。

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。 それまでの登録者情報がテーブルに表示されます。 登録情報は MySQL データベースに保存されます。 アプリケーションを構成するファイルは 1 つです (下にあるコードをコピーし、貼り付けて使用できます)。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。

アプリケーションを作成してローカルで実行するには、次の手順に従います。 次の手順は、PHP と MySQL コマンド ライン ツール (MySQL の一部)、ローカル コンピューターで設定があることを前提としてに有効にした、 [MySQL pdo、mysql 用 PDO 拡張機能][pdo-mysql]します。

1. 値を使用して、リモートの MySQL サーバーに接続 `データ ソース`, 、`ユーザー Id`, 、`パスワード`, 、および `データベース` 先ほどメモしておいたします。

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. MySQL コマンド プロンプトが表示されます。

        mysql>

3. 次の貼り付け `CREATE TABLE` を作成するコマンド、 `registration_tbl` データベース内のテーブル。

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. ローカル アプリケーション フォルダーのルートに **index.php** ファイルを作成します。

5. 開いている、 **index.php** ファイルをテキスト エディターまたは IDE で、次のコードを追加してでマークされた必要な変更を完了させる `//TODO:` コメントします。

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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

4.  ターミナルで、アプリケーション フォルダーに移動し、次のコマンドを入力します。

        php -S localhost:8000


移動できるようになりました **http://localhost:8000/** アプリケーションをテストします。


## アプリケーションの発行

アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを Web Apps に発行できます。 ローカルの Git リポジトリを初期化して、アプリケーションを発行します。
> [AZURE.NOTE]
> これらは、上の「Web アプリの作成と Git 発行の設定」セクションの最後で Azure ポータルに示された手順と同じです。

1. (省略可能) ディスクの作成または Git リモート リポジトリの URL が間違っているしたら、Azure ポータルで web アプリのプロパティに移動します。

1. Gitbash (Git にある場合は、ターミナル、 `パス`)、ディレクトリ、アプリケーションのルート ディレクトリに変更し、次のコマンドを実行します。

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    先ほど作成したパスワードを入力するように求められます。

    ![Git 経由による Azure への初期プッシュ][git-initial-push]

2. 参照 **http://[site name].azurewebsites.net/index.php** (この情報はアカウント ダッシュ ボードに保存されます)、アプリケーションの使用を開始します。

    ![Azure の PHP Web サイト][running-app]

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。

## アプリケーションへの変更の発行

アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. Gitbash (ターミナル Git が、 `パス`)、アプリのルート ディレクトリに、次のコマンドを実行します。

        git add .
        git commit -m "comment describing changes"
        git push azure master

    先ほど作成したパスワードを入力するように求められます。

    ![サイト変更の Git 経由による Azure へのプッシュ][git-change-push]

3. 参照 **http://[site name].azurewebsites.net/index.php** アプリおよび自分が行った変更を確認します。

    ![Azure の PHP Web サイト][running-app]

>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ

詳細については、次を参照してください。、 [PHP デベロッパー センター](/develop/php/)します。

## 変更内容

* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)


[install-php]: http://www.php.net/manual/en/install.php 
[install-sqlexpress]: http://www.microsoft.com/download/details.aspx?id=29062 
[install-drivers]: http://www.microsoft.com/download/details.aspx?id=20098 
[install-git]: http://git-scm.com/ 
[install-mysql]: http://dev.mysql.com/downloads/mysql/ 
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php 
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png 
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png 
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png 
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg 
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png 
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png 
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png 
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png 
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png 
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png 
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png 
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png 
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png 
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png 
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png 
[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png 
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png 
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png 
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png 
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png 
[management-portal]: https://portal.azure.com 
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx 

