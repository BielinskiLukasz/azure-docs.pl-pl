<properties
    pageTitle="Azure Mobile Services および Sencha の使用"
    description="このチュートリアルでは、Mobile Services と Sencha HTML5 モバイル アプリ フレームワークを使用した開発について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-sencha"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="11/06/2015"
    ms.author="glenga"/>

# <a name="getting-started"></a>Mobile Services と Sencha Touch の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##概要

このチュートリアルでは、Sencha Touch アプリケーションで Azure Mobile Services を活用する方法について説明します。 作成、単純な *To Do List* Azure クラシック ポータルで定義したモバイル サービスを利用して Sencha Touch を使用してアプリです。 このチュートリアルでは、JavaScript について適切な知識を持ち、Sencha Touch フレームワークに精通する熟達した Web アプリケーション開発者を対象として中間的な説明を行うことを意図しています。

このチュートリアルのビデオを見る場合は、このクリップで、このチュートリアルと同じ手順が表示されます。 ビデオでは、Arthur Kay は、Azure Mobile Services バックエンドを使用して Sencha Touch アプリケーションを構築する方法を説明します。

> [AZURE.VIDEO getting-started-with-sencha-touch]


完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

##必要条件

- ダウンロードし、インストール [Sencha Touch] (http://wwww.sencha.com/products/touch/download"target ="_blank") です。

- ダウンロードしてインストール [Sencha Cmd Tool] (http://www.sencha.com/products/sencha-cmd/download"target ="_blank") です。

- Java Runtime Environment (JRE) または Java Development Kit (Android アプリケーションを作成している場合)。
- Ruby と SASS gem。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##TodoItems テーブルの作成

作成する場合は、Azure 旧ポータルの簡単なクイック スタートを行うことができる、モバイル サービスを作成した後
Mobile Services 用に新しいデータベース テーブルを作成できます。

1.  [Azure classic portal], 、] をクリックして **Mobile Services**, 、作成したモバイル サービスをクリックします。

2. クイック スタート] タブをクリックして **HTML** [ **プラットフォームの選択]** 展開 **新しい HTML アプリを作成**します。

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    これにより、モバイル サービスに接続された HTML アプリを作成し、ホストするための簡単な 3 つの手順が表示されます。

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. クリックして **作成 TodoItems テーブル** アプリケーション データを格納するテーブルを作成します。

    > [AZURE.NOTE] Azure クラシック ポータルから HTML アプリをダウンロードします。 その代わりに、次のセクションで Sencha Touch アプリケーションを手動で作成します。


1. メモ、 **appKey** と **appUrl** Azure クラシック ポータルで。 これらは、このチュートリアルの他のセクションで使用します。

    ![アプリ キー](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1.  **構成** いることを確認] タブで、 `localhost` で既に表示されている、 **ホスト名からの要求を許可する** の下に一覧表示 **クロス オリジン リソース共有 (CORS)**します。 そうでない場合は、入力 `localhost` で、 **ホスト名** フィールドをクリックして **保存**します。

    ![ローカルホストの CORS をセットアップ](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##Touch アプリケーションの生成

Sencha Touch テンプレート アプリケーションを生成することは、Sencha Cmd によって簡単に実行可能であり、アプリケーションを非常に迅速に動作させることができる優れた方法です。

Touch フレームワークをインストールしたディレクトリから、次のコマンドを発行します。

    $ sencha generate app Basic /path/to/application

これによって、'Basic' という名前のアプリケーションを含むテンプレート Touch アプリケーションが生成されます。 アプリケーションを起動するには、ブラウザーで単純に directory /path/to/application を指定します。これによって、標準の Touch サンプル アプリケーションが表示されます。

##Azure 用 Sencha Touch Extensions のインストール

Azure 用の拡張機能は、手動または Sencha パッケージとしてインストールされます。 いずれの方法を使用してもかまいません。

###手動のインストール

大半の Touch アプリケーションでは、クラスの外部ライブラリを追加する場合、単純にパッケージをダウンロードし、アプリケーション ディレクトリ内でパッケージを展開して、ライブラリの場所について Touch ローダーを構成します。

次の手順に従って、アプリケーションに Azure 拡張機能を手動で追加できます。

1. Azure 拡張機能パッケージをダウンロード [ここ](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)します。 (この領域にアクセスするために Sencha Forums ID を使用できます)。

2. ダウンロード ディレクトリから最終的に配置および展開する場所に Azure 拡張機能パッケージをコピーします。

        $ cd /path/to/application
        $ mv /download-location/azure.zip .
        $ unzip azure.zip

    こうと、 **azure** ソース、例、およびドキュメント全体を含むディレクトリをパッケージ化します。 ソースが存在する、 **azure/src** ディレクトリ。


###Sencha パッケージとしてのインストール

> [AZURE.NOTE] 使用して、アプリケーションを生成している場合のみ、このメソッドを使用できます、 <code>sencha generate app</code> コマンドです。

Sencha Cmd によって生成されるすべてのアプリケーションで、ルートに "パッケージ" フォルダーがあります。 このフォルダーの場所は構成可能ですが、その場所にかかわらず packages フォルダーの役割は、アプリケーション (または Sencha ワークスペースを作成している場合は複数のアプリケーション) によって使用されるすべてのパッケージのストレージとして機能することです。

Azure は Sencha Cmd "パッケージ" であるため、ソース コードは Sencha Cmd を使用して簡単にアプリケーションにインストールし、含めることができます (参照 [Sencha Cmd パッケージ](http://docs.sencha.com/cmd/6.x/cmd_packages/cmd_packages.html) の詳細)。

ダウンロードして、Sencha パッケージ リポジトリから Azure 拡張機能パッケージをインストール、パッケージ名を追加する必要がある、 **app.json** ファイルし、アプリケーションを作成します。

1. app.json ファイルの requires セクションに Azure パッケージを追加します。

        {
            "name": "Basic",
            "requires": [
                "touch-azure"
            ]
        }

2. 使用して、アプリケーションを再構築 **sencha cmd** をフェッチして、パッケージをインストールします。

        $ sencha app build

両方とも **sencha アプリのビルド** と **sencha app 更新** は共に今すぐ実行で、アプリケーションのパッケージを統合するために必要な手順を実行します。 通常、パッケージ要件の変更後、実行する必要 **sencha app 更新** 「開発モード」が最新の状態をサポートするために必要なメタデータ。

いずれのコマンドを実行しても、Sencha Cmd は "パッケージ" フォルダーにパッケージをダウンロードして展開します。 これによって、ワークスペースに "packages/touch-azure" フォルダーが表示されます。

##Azure を含めて構成する

**ファイル名**: app.js

Azure 拡張機能がダウンロードされ、アプリケーション ディレクトリにインストールされたので、次の手順ではソース ファイルの検出元と要求先をアプリケーションに指示します。

1. Sencha ローダーでソース コードの場所を構成します。

        Ext.Loader.setConfig({
            enabled : true,
            paths   : {
                'Ext'       : 'touch/src',
                'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Azure クラス ファイルを要求します。

        Ext.application({

            requires: [ 'Ext.azure.Azure' ],

            // ...

        });


3. Azure の構成

    Azure パッケージは、呼び出しによって初期化されます、 **Ext.Azure.init** 、アプリケーションの起動セクション内のメソッドです。 このメソッドでは、モバイル サービスの資格情報と、使用したいその他の資格情報および機能を含む構成オブジェクトを渡します。

    構成オブジェクトは直接 init メソッドに渡すことができます、お勧めという Sencha アプリケーション構成プロパティを作成 **azure** そこにすべての適切な情報を格納するとします。 次に、このプロパティ値を Ext. Azure.init メソッドに渡すことができます。

    Azure でモバイル サービスを作成する場合 (を参照してください [Azure 入門](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started))、アプリケーション キーおよび URL が、そのサービスに割り当てられます。 この情報は、サービスに接続できるように Azure パッケージに提供する必要があります。

    この例では、アプリケーション キーと URL のみを提供する非常に簡単な Azure の構成と初期化を示しています。

        Ext.application({
            name: 'Basic',

            requires: [ 'Ext.azure.Azure' ],

            azure: {
                appKey: 'myazureservice-access-key',
                appUrl: 'myazure-service.azure-mobile.net'
            },

            launch: function() {

                // Call Azure initialization

                Ext.Azure.init(this.config.azure);

           }
        });

    Azure の構成オプションについては、Ext. Azure API のドキュメントを参照してください。


ご利用ありがとうございます。 これでアプリケーションは、モバイル サービスにアクセスできます。

##ToDo アプリケーションのビルド

ここまで、Azure 拡張機能を含めるようにアプリケーションを構成し、モバイル サービス資格情報を指定したので、サービスに格納された ToDo リスト データを表示および編集するためにモバイル サービスを活用する Touch アプリケーションの作成に着手できます。

###Azure データ プロキシの構成

**ファイル名:** app/model/TodoItem.js

Touch アプリケーションは、データ プロキシを通じてモバイル サービスと通信します。 プロキシは、モバイル サービスに対する要求の送信およびデータの受信の両方についてすべての処理を行います。 Touch データ モデルおよびストアと組み合わせて使用することで、リモート データの処理およびアプリケーションへの提供にかかるすべての困難な処理が排除され、その処理が Touch 自体によって実行されます。

Sencha Touch モデルでは、アプリケーションで使用するデータ レコードの定義を提供します。 このモデルでは、データ フィールドを定義するだけでなく、アプリケーションと Azure モバイル サービス間の通信を処理するプロキシに関する構成を提供できます。

次のコードでは、モデルのフィールド (およびその種類) を定義し、またプロキシの構成を提供しています。 プロキシを構成する場合、種類 (この場合は 'azure')、モバイル サービス テーブル名 (ToDoItem)、その他のオプション パラメーターを渡す必要があります。 この例では、プロキシのページングを有効にすることで、一覧の項目について前後にシームレスにページングできます。

Azure プロキシは、Azure API によって想定される適切な CRUD 操作と共に、すべての HTTP ヘッダーを自動的に設定します (存在する場合は、認証資格情報が含まれます)。

    Ext.define('Basic.model.TodoItem', {
        extend : 'Ext.data.Model',

        requires : [
            'Ext.azure.Proxy'
        ],

        config : {
            idProperty : 'id',
            useCache   : false,

            fields     : [
                {
                    name : 'id',
                    type : 'int'
                },
                {
                    name : 'text',
                    type : 'string'
                },
                {
                    name : 'complete',
                    type : 'boolean'
                }
            ],

            proxy : {
                type               : 'azure',
                tableName          : 'TodoItem',
                enablePagingParams : true
            }
        }
    });


###ToDo 項目の格納

**ファイル名**: app/store/TodoItems.js

Sencha Touch ストアでは、さまざまな方法でレコードを表示するために、Touch コンポーネントのソースとして使用できるデータ レコード (モデル) のコレクションを保存します。 これには、グリッド、グラフ、リストなどを含めることができます。

ここでは、Azure モバイル サービスから取得したすべてのストア ToDo リスト項目を保持するために使用するストアを定義します。 ストア構成には、モデルの種類の名前が含まれます (Basic.model.TodoItem - 先に定義されています)。 これによって、ストアに含まれるレコードの構造が定義されます。

また、ページ サイズ (8 レコード) を指定するなど、ストアに対するいくつかの追加構成オプションが用意されています。このストアのレコードの並べ替えは、Azure モバイル サービスによってリモートに行われます (並べ替えは、ストア自体でローカルには行われません)。

    Ext.define('Basic.store.TodoItems', {
        extend : 'Ext.data.Store',

        requires : [
            'Basic.model.TodoItem'
        ],

        config : {
            model        : 'Basic.model.TodoItem',
            pageSize     : 8,
            remoteSort   : true,
            remoteFilter : true
        }
    });


###ToDo 項目の表示および編集

**ファイル名**: app/view/DataItem.js

これで、各 ToDo 項目の構造を定義し、すべてのレコードを配置するためのストアを作成したので、アプリケーションのユーザーに対してこの情報をどのように表示するかについて考えます。 通常の使用により、ユーザーに情報を表示して **ビュー**します。 任意の数の Touch コンポーネントの 1 つを個別にまたは他と組み合わせて表示できます。

次の表示は、各レコードの表示方法を定義する ListItem と、各項目を削除するためのアクションを格納するいくつかのボタンから構成されています。

    Ext.define('Basic.view.DataItem', {
        extend : 'Ext.dataview.component.ListItem',
        xtype  : 'basic-dataitem',

        requires : [
            'Ext.Button',
            'Ext.layout.HBox',
            'Ext.field.Checkbox'
        ],

        config : {
            checkbox : {
                docked     : 'left',
                xtype      : 'checkboxfield',
                width      : 50,
                labelWidth : 0
            },

            text : {
                flex : 1
            },

            button : {
                docked   : 'right',
                xtype    : 'button',
                ui       : 'plain',
                iconMask : true,
                iconCls  : 'delete',
                style    : 'color: red;'
            },

            dataMap : {
                getText : {
                    setHtml : 'text'
                },

                getCheckbox : {
                    setChecked : 'complete'
                }
            },

            layout : {
                type : 'hbox',
                align: 'stretch'
            }
        },

        applyCheckbox : function(config) {
            return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
        },

        updateCheckbox : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        },

        applyButton : function(config) {
            return Ext.factory(config, Ext.Button, this.getButton());
        },

        updateButton : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        }

    });


###プライマリ ビューの作成

**ファイル名**: app/view/Main.js

ここまで、個々の ToDo リスト項目 (上) のレイアウトを定義したので、実際の項目のリスト、アプリケーションのタイトル、および新しいタスクを追加するためのボタンを定義する完全なユーザー インターフェイスでリストをラップします。

    Ext.define('Basic.view.Main', {
        extend : 'Ext.dataview.List',
        xtype  : 'main',

        requires : [
            'Ext.TitleBar',
            'Ext.dataview.List',
            'Ext.data.Store',
            'Ext.plugin.PullRefresh',
            'Ext.plugin.ListPaging',
            'Basic.view.DataItem'
        ],

        config : {
            store : 'TodoItems',

            useSimpleItems : false,
            defaultType    : 'basic-dataitem',

            plugins : [
                {
                    xclass          : 'Ext.plugin.PullRefresh',
                    pullRefreshText : 'Pull down to refresh!'
                },
                {
                    xclass     : 'Ext.plugin.ListPaging',
                    autoPaging : true
                }
            ],

            scrollable : {
                direction     : 'vertical',
                directionLock : true
            },

            items : [
                {
                    docked : 'top',
                    xtype  : 'titlebar',
                    title  : 'Azure Mobile - Basic Data Example'
                },
                {
                    xtype  : 'toolbar',
                    docked : 'bottom',
                    items  : [
                        {
                            xtype       : 'textfield',
                            placeHolder : 'Enter new task',
                            flex        : 1
                        },
                        {
                            xtype  : 'button',
                            action : 'add',
                            text   : 'Add'
                        }
                    ]
                }
            ]
        }
    });

###すべての共同作業

**ファイル名**: app/controller/Main.js

アプリケーションの最後の手順は、押されたボタン (削除、保存など) に応答し、こうしたすべての要求に対応するロジックを提供することです。 Sencha Touch は、こうしたイベントをリッスンするコントローラーを活用し、それに応じて応答します。

    Ext.define('Basic.controller.Main', {
        extend : 'Ext.app.Controller',

        config : {
            refs : {
                todoField : 'main toolbar textfield',
                main      : 'main'
            },

            control : {
                'button[action=add]'    : {
                    tap : 'onAddItem'
                },
                'button[action=reload]' : {
                    tap : 'onReload'
                },

                main : {
                    activate      : 'loadInitialData',
                    itemdoubletap : 'onItemEdit'
                },

                'basic-dataitem checkboxfield' : {
                    change : 'onItemCompleteTap'
                },

                'basic-dataitem button' : {
                    tap : 'onItemDeleteTap'
                }
            }
        },

        loadInitialData : function () {
            Ext.getStore('TodoItems').load();
        },

        onItemDeleteTap : function (button, e, eOpts) {
            var store    = Ext.getStore('TodoItems'),
                dataItem = button.up('dataitem'),
                rec      = dataItem.getRecord();

            rec.erase({
                success: function (rec, operation) {
                    store.remove(rec);
                },
                failure: function (rec, operation) {
                    Ext.Msg.alert(
                        'Error',
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}',
                        operation.error.status,
                        operation.error.statusText)
                    );
                }
            });
        },

        onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
            var dataItem = checkbox.up('dataitem'),
                rec      = dataItem.getRecord(),
                recVal   = rec.get('complete');

            // this check is needed to prevent an issue where multiple creates get triggered from one create
            if (newVal !== recVal) {
                rec.set('complete', newVal);
                rec.save({
                    success: function (rec, operation) {
                        rec.commit();
                    },
                    failure: function (rec, operation) {
                        // since there was a failure doing the update on the server then silently reject the change
                        rec.reject(true);
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        },

        onItemEdit : function (list, index, target, record, e, eOpts) {
            var rec = list.getSelection()[0];

            Ext.Msg.prompt('Edit', 'Rename task',
                function (buttonId, value) {
                    if (buttonId === 'ok') {
                        rec.set('text', value);
                        rec.save({
                            success: function (rec, operation) {
                                rec.commit();
                            },
                            failure: function (rec, operation) {
                                // since there was a failure doing the update on the server then reject the change
                                rec.reject();
                                Ext.Msg.alert(
                                    'Error',
                                    Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                                    operation.error.status,
                                    operation.error.statusText)
                                );
                            }
                        });
                    }
                },
                null,
                false,
                record.get('text')
            );
        },

        onReload : function () {
            Ext.getStore('TodoItems').load();
        },

        onAddItem : function () {
            var me = this,
                rec,
                store = Ext.getStore('TodoItems'),
                field = me.getTodoField(),
                value = field.getValue();

            if (value === '') {
                Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
            }
            else {
                rec = Ext.create('Basic.model.TodoItem', {
                    complete : false,
                    text     : value
                });
                //store.insert(0, rec); //insert at the top
                //store.sync();
                rec.save({
                    success: function (rec, operation) {
                        store.insert(0, rec); //insert at the top
                        field.setValue('');
                    },
                    failure: function (rec, operation) {
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        }
    });

###すべてをまとめた配置

**ファイル名**: app.js

最後の手順は、メイン アプリケーション ファイルの編集を終了し、定義したモデル、ストア、ビュー、およびコントローラーに関する情報を提供することです。 これらのリソースのソース ファイルは、アプリケーションに自動的に読み込まれます。 最後に、プライマリ アプリケーション ビューである "Basic.main.View" を作成および表示する起動メソッドを呼び出します。


    Ext.Loader.setConfig({
        enabled : true,
        paths   : {
            'Ext'       : 'touch/src',
            'Ext.azure' : 'packages/azure/src'
        }
    });

    Ext.application({
        name : 'Basic',

        requires : [
            'Ext.MessageBox',
            'Ext.azure.Azure'
        ],

        views : [
            'Main'
        ],

        controllers : [
            'Main'
        ],

        stores : [
            'TodoItems'
        ],

        azure : {
            appUrl : 'YOUR_APP_URL.azure-mobile.net',
            appKey : 'YOUR_APP_KEY'
        },

        icon : {
            '57'  : 'resources/icons/Icon.png',
            '72'  : 'resources/icons/Icon~ipad.png',
            '114' : 'resources/icons/Icon@2x.png',
            '144' : 'resources/icons/Icon~ipad@2x.png'
        },

        isIconPrecomposed : true,

        startupImage : {
            '320x460'   : 'resources/startup/320x460.jpg',
            '640x920'   : 'resources/startup/640x920.png',
            '768x1004'  : 'resources/startup/768x1004.png',
            '748x1024'  : 'resources/startup/748x1024.png',
            '1536x2008' : 'resources/startup/1536x2008.png',
            '1496x2048' : 'resources/startup/1496x2048.png'
        },

        launch : function () {
            // Destroy the #appLoadingIndicator element
            Ext.fly('appLoadingIndicator').destroy();

            // Initialize Azure
            Ext.Azure.init(this.config.azure);

            // Initialize the main view
            Ext.Viewport.add(Ext.create('Basic.view.Main'));
        },

        onUpdated : function () {
            Ext.Msg.confirm(
                "Application Update",
                "This application has just successfully been updated to the latest version. Reload now?",
                function (buttonId) {
                    if (buttonId === 'yes') {
                        window.location.reload();
                    }
                }
            );
        }
    });

###Sencha Touch アプリケーションのホストおよび実行

このチュートリアルの最後に、ローカル コンピューターの新しいアプリケーションをホストして実行します。

  1. ターミナルで、展開されたアプリケーションの場所を参照します。

  2. Sencha Cmd を使用し、次のコマンドを実行します。

    * *sencha app refresh* : This will instruct Sencha Cmd to locate all app dependencies,
      and download any needed packages (for example, [Sencha Touch Extensions for Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)).

    * *sencha web start* : This will start a local web server to test our application.

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. アプリケーションを開始するためにターミナルに示された URL を Web ブラウザーで開きます (たとえば、http://localhost:1841 など)。

  4. アプリケーションで、「このチュートリアルを完了」などの意味のあるテキストを入力し、クリックして **追加**します。

    ![new todo item](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    This sends a POST request to the new mobile service hosted in Azure. Data from the request is inserted into the TodoItem table.

  5. 戻り、 [Azure classic portal], をクリックして、 **データ** ] タブでし、TodoItems テーブルをクリックします。

    ![Todo Items table](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    This lets you browse the data inserted by the app into the table.

    ![browse todo table](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##次のステップ
ファースト ステップ ガイドはこれで完了です。Sencha による Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

[ダウンロード](https://github.com/arthurakay/sencha-touch-azure-example) その他のスタイルと Sencha Touch の他の内容を確認する機能を備えた完全なサンプル アプリケーションです。

さらに、Azure 用 Sencha Touch Extensions の詳細について参照してください。

  * サンプル アプリ [チュートリアル](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters)
  * サポートを得て、 [Sencha Forums](http://www.sencha.com/forum)
  * [参照]、 [Sencha のドキュメント](http://docs.sencha.com/)
  * Azure Mobile Services での Sencha の使用: [(ビデオ)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##その他のリソース

  * [Sencha Touch のダウンロード](http://pages.sencha.com/touch-for-azure.html)
  * [Azure 用 Sencha Touch 拡張機能](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)


##まとめ

ここで概要を示した例は、Azure パッケージ用 Sencha Touch 拡張機能で提供されており、例のディレクトリに基本データ例として格納されています。 詳細なコメントおよび説明と共に、この拡張機能の他の機能をデモするいくつかの例も提供されています。

詳細については、Sencha Touch の概要の完全なセットを参照してください [ガイド](http://docs.sencha.com/touch/#!/guide)


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png

[Azure classic portal]: https://manage.windowsazure.com/

