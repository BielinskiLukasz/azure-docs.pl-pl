<properties 
    pageTitle="Azure App Service での WordPress から Multisite への変換" 
    description="Azure のギャラリーを使って作成された既存の WordPress Web アプリを取得し、WordPress Multisite に変換する方法を説明します。" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="tomfitz"/>




# Azure App Service での WordPress から Multisite への変換

## 概要

* で [Ben Lobaugh ][ben-lobaugh], 、[Microsoft 開く Technologies Inc. ][ms-open-tech]*

このチュートリアルでは、Azure のギャラリーを使って作成された既存の WordPress Web アプリを取得し WordPress Multisite インストールに変換する方法を説明します。 また、インストール内の各サブサイトにカスタム ドメインを割り当てる方法も説明します。

ここでは、WordPress のインストールが既にあるものとします。 そうしない場合は」の説明に従ってください [[web サイトからギャラリー] Azure でギャラリーから WordPress web サイトを作成][website-from-gallery]します。

変換する既存の WordPress から Multisite へのシングル サイト インストールには一般に非常に単純なもので、ここでは、最初の手順を実行直線が、 [ネットワークの作成 ][wordpress-codex-create-a-network] ] ページで、 [WordPress Codex](http://codex.wordpress.org)します。

それでは始めましょう。

## Multisite の許可

まず、Multisite を有効にする必要があります、 `wp-config.php` ファイルと、 **\_allow\_multisite** 定数です。 Web アプリ ファイルを編集するには、2 つの方法があります。FTP を使用する方法と、Git を使用する方法です。 どちらのセットアップ方法にも慣れていない場合は、以下のチュートリアルを参照してください。

* [MySQL および FTP][website-w-mysql-and-ftp-ftp-setup]

* [MySQL および Git][website-w-mysql-and-git-git-setup]

開いている、 `wp-config.php` 独自のエディターを使用してファイルを以下の行を追加、'/* すべてを s, stop editing! Happy blogging. */' 行の上に追加します。

    /* Multisite */
    
    define( 'WP_ALLOW_MULTISITE', true );

必ずファイルを保存し、サーバーにアップロードし直してください。

## ネットワークのセットアップ

Web アプリの *wp-admin* 領域にログインし、**[ツール]** メニューに **[Network Setup]** という新しい項目があることを確認します。 **[Network Setup]** をクリックし、ネットワークの詳細を入力します。

![[Network Setup] 画面][wordpress-network-setup]

このチュートリアルでは、*Sub-directories* サイト スキーマを使用します。このスキーマは常に機能する必要があるためです。このチュートリアルの後の方では、各サブサイトのカスタム ドメインをセットアップします。 ただし、ことができます、サブドメイン インストールを経由でドメインをマップする場合は、セットアップに、 [Azure ポータル](https://portal.azure.com) し、ワイルドカード DNS を適切にセットアップします。

サブディレクトリのセットアップを参照するくださいとサブディレクトリの詳細については、 [タイプのマルチサイト ネットワーク ][wordpress-codex-types-of-networks] 、WordPress Codex の記事です。

## ネットワークの有効化

これで、ネットワークがデータベースで構成されましたが、ネットワーク機能を有効にするにはもう 1 つ手順が残っています。 最終処理、 `wp-config.php` 設定を確認して `web.config` 各サイトを適切にルーティングします。


クリックすると、 **インストール** のボタンでは、 *ネットワーク セットアップ* ] ページで、WordPress に更新されます、 `wp-config.php` と `web.config` ファイルです。 ただし、これらのファイルを必ずチェックし、正常に更新されていることを確認してください。 正常に更新されていない場合は、この画面に必要な更新が表示されます。 ファイルを編集して保存します。


これらの更新を行った後で、いったんログアウトしてから、この wp-admin ダッシュボードに再ログインする必要があります。

この時点で、管理バーに **[My Sites]** というメニューが追加されています。 このメニューを使用すると、**Network Admin** ダッシュボードを通じて新しいネットワークを管理できます。

## カスタム ドメインの追加

[WordPress MU ドメイン Mapping][wordpress-plugin-wordpress-mu-domain-mapping] プラグインを利用する簡単に、ネットワーク内のサイトにカスタム ドメインを追加します。 このプラグインを正常に機能させるには、ポータルとドメイン レジストラーで追加のセットアップを行う必要があります。

## Web アプリへのドメイン マッピングの有効化

**Free** [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プラン モードでは、Web アプリへのカスタム ドメインの追加はサポートされません。 **Shared** モードまたは **Standard** モードに切り替える必要があります。 これを行うには、次の手順を実行します。

* Azure ポータルにログインして、自分の Web アプリに移動します。
* メイン コンテンツ領域の **[スケール]** タブをクリックします。
* **[全般]** で、*[Shared]* または *[Standard]* を選択します。
* **[保存]** をクリックします。

設定してある使用方法やその他の構成によっては、変更の確認と、Web アプリへの今後の課金の承認を求めるメッセージが表示される場合があります。

新しい設定の処理には少し時間がかかるので、ドメインのセットアップを開始します。

## ドメインの確認

Azure Web Apps でドメインをサイトにマップするには、まず、ドメインをマップする権限が与えられていることを確認する必要があります。 そのためには、新しい CNAME レコードを DNS エントリに追加します。

* 自分のドメインの DNS マネージャーにログインします
* 新しい CNAME *awverify* を作成します
* *awverify* が *awverify.YOUR_DOMAIN.azurewebsites.net* を指すようにします

場合によっては、DNS の変更が完全に有効になるまで少し時間がかかります。以下の手順がすぐにはうまく動作しない場合は、しばらくしてからやり直してください。

## Web アプリへのドメインの追加

Azure ポータルを通じて Web アプリに戻り、**[設定]** をクリックし、**[Custom domains and SSL]** をクリックします。

*SSL 設定*が表示されると、Web アプリに割り当てるすべてのドメインを入力するためのフィールドが表示されます。 ドメインがこの一覧に表示されない場合、ドメイン DNS がどのようにセットアップされているかにかかわらず、そのドメインは WordPress 内でのマッピングに使用できません。

![[カスタム ドメインの管理] ダイアログ][wordpress-manage-domains]

ドメインをテキスト ボックスに入力すると、以前に作成した CNAME レコードが確認されます。 DNS が完全に反映されていない場合は、赤いインジケーターが表示されます。 成功した場合は、緑のチェック マークが表示されます。

ダイアログの下部に表示される IP アドレスをメモしておきます。 ドメインのための A レコードをセットアップするときに必要になります。

## ドメイン A レコードのセットアップ

ここまでの手順が成功すると、DNS A レコードを通じてドメインを自分の Azure Web アプリに割り当てられるようになります。

ここで重要なのは、Azure Web アプリでは CNAME と A レコードのどちらも許容されるものの、適切なドメイン マッピングを可能にするには A レコードを使う*必要*があるということです。 Azure では、YOUR_DOMAIN.azurewebsites.net で CNAME が作成されますが、CNAME は他の CNAME に転送することができません。

DNS マネージャーに戻り、前の手順でメモした IP アドレスを指すように A レコードをセットアップします。


## プラグインのインストールとセットアップ

現在、WordPress Multisite には、カスタム ドメインをマップするための方法が組み込まれていません。 ただし、というプラグインは [WordPress MU ドメイン Mapping][wordpress-plugin-wordpress-mu-domain-mapping] 機能を追加します。 自分のサイトの Network Admin 部分にログインして、**WordPress MU Domain Mapping** プラグインをインストールします。

プラグインをインストールおよびアクティブ化した後で、**[設定]** > **[Domain Mapping]** にアクセスし、プラグインを構成します。 1 つ目のテキスト ボックス *[Server IP Address]* で、ドメインの A レコードのセットアップに使用した IP アドレスを入力します。 必要に応じて *[Domain Options]* を設定し (通常は既定のままでよい)、**[Save]** をクリックします。

## ドメインのマップ

ドメインのマップ対象のサイトの**ダッシュボード**にアクセスします。 **[ツール]**、**[Domain Mapping]** の順にクリックし、テキスト ボックスに新しいドメインを入力して、**[Add]** をクリックします。

既定では、新しいドメインは自動生成されたサイト ドメインに書き換えられます。 すべてのトラフィックが新しいドメインに送信されるようにするには、保存する前に *[Primary domain for this blog]* チェック ボックスをオンにします。 サイトにドメインの無制限の数を追加することができますが、1 つのみをプライマリにすることができます。

## 繰り返し

Azure Web Apps では、Web アプリに追加できるドメインの数に制限はありません。 他のドメインを追加するには、各ドメインに対して「**ドメインの確認**」および「**ドメイン A レコードのセットアップ**」セクションの手順を繰り返します。
>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容

* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)


[ben-lobaugh]: http://ben.lobaugh.net 
[ms-open-tech]: http://msopentech.com 
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/ 
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network 
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0 
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1 
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png 
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network 
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/ 
[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png 

