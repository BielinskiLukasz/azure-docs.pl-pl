<properties 
    pageTitle="Azure App Service Web Apps による Python の構成" 
    description="このチュートリアルでは、Web Server Gateway Interface (WSGI) に準拠した基本的な Python アプリケーションを Azure App Service Web Apps に作成して構成する方法について説明します。" 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2015" 
    ms.author="huguesv"/>





# Azure App Service Web Apps による Python の構成

このチュートリアルで作成して、基本的な Web Server Gateway Interface (WSGI) 準拠した Python アプリケーションを構成する方法については説明 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)します。

仮想環境や、requirements.txt を使用したパッケージ インストールなどの Git デプロイメントの追加の機能を説明します。


## Bottle、Django、Flask

Azure Marketplace には、Bottle、Django、Flask フレームワーク用のテンプレートが含まれます。 Azure App Service に最初の Web アプリを開発している場合、または Git に習熟していない場合は、これらのチュートリアルのいずれかに従うことをお勧めします。これらのチュートリアルでは、Windows または Mac から Git デプロイメントを使用して、ギャラリーから作業アプリケーションを構築するためのステップ バイ ステップの手順を紹介しています。

- [Bottle を使用した web アプリの作成](web-sites-python-create-deploy-bottle-app.md)
- [Django を使用した web アプリの作成](web-sites-python-create-deploy-django-app.md)
- [Flask を使用した web アプリの作成](web-sites-python-create-deploy-flask-app.md)


## Azure ポータルでの Web アプリの作成

このチュートリアルは、Azure サブスクリプションを既に所有しており、Azure ポータルにアクセスできることを前提としています。

既存の web アプリがあるない場合から 1 つを作成、 [Azure ポータル](https://portal.azure.com)します。 画面左下にある [新規] ボタンをクリックし、次に **[Web + モバイル]**、**[Web アプリ]** の順にクリックします。

## Git 発行

新しく作成した web アプリの Git 発行を構成する」の手順に従って、 [Azure App Service での GIT による継続的なデプロイ](web-sites-publish-source-control.md)します。 このチュートリアルでは、Python Web アプリの作成と管理、Azure App Service への発行を Git を使用して行います。

Git 発行の設定が完了すると、Git リポジトリが作成されて Web アプリに関連付けられます。 このリポジトリの URL が表示され、以後、ローカル開発環境からクラウドにデータをプッシュする目的で使用できます。 Git を介してアプリケーションを発行するには、Git クライアントを併せてインストールする必要があります。提供されるインストラクションに従って Web アプリのコンテンツを Azure App Service にプッシュしてください。


## アプリケーションの概要

次のセクションでは、次のファイルが作成されます。 これらは、Git リポジトリのルートに配置する必要があります。

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py

## WSGI ハンドラー

WSGI はで規定された Python の標準 [PEP 3333](http://www.python.org/dev/peps/pep-3333/) web サーバーと Python 間のインターフェイスを定義します。 各種の Web アプリケーションや Web フレームワークを Python を使って記述するためのインターフェイスが標準化されています。 今日普及している Python Web フレームワークには WSGI が使用されています。 Web フレームワークに必要な機能は Azure App Service Web Apps に用意されています。また、カスタム ハンドラーを WSGI 仕様のガイドラインに準拠させれば、経験豊富なユーザーが Web フレームワークを独自に制作することも可能です。

次の例は、 `app.py` カスタム ハンドラーを定義します。

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()
    
    if __name__ == '__main__':
        from wsgiref.simple_server import make_server
    
        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

このアプリケーションをローカルでを実行する `python app.py`, を選択し、 `http://localhost:5555` web ブラウザーでします。


## 仮想環境

上記の例のアプリは外部パッケージを必要としませんが、アプリケーションによっては外部パッケージが必要になる場合があります。

外部のパッケージとの依存関係を管理するために、Azure の Git デプロイメントでは、仮想環境の作成がサポートされています。

という名前の仮想環境が自動的に作成して Azure では、リポジトリのルートに requirements.txt を検出、ときに `env`します。 これは、最初のデプロイメント、または選択した Python ランタイムを変更した後のデプロイメント時に発生します。

仮想開発環境をローカルで作成する場合、Git リポジトリには含めないでください。


## パッケージの管理

requirements.txt 内のリストにあるパッケージが、pip を使用して仮想環境に自動的にインストールされます。 これはデプロイメントごとに発生しますが、パッケージが既にインストールされている場合は、インストールがスキップされます。

例 `requirements.txt`:

    azure==0.8.4

## Python バージョン

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

例 `runtime.txt`:

    python-2.7

## web.config

サーバーによる要求の処理方法を指定するには、web.config ファイルを作成する必要があります。

リポジトリに web.x.y.config ファイルがある場合は (ここでは x.y は選択した Python ランタイム)、Azure が適切なファイルを web.config として自動的にコピーします。

次の例の web.config は、次のセクションで説明する仮想環境プロキシ スクリプトに依存します。 この例で使用した WSGI ハンドラーを扱う `app.py` 上です。

例 `web.config` Python 2.7 の場合。

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

例 `web.config` Python 3.4 の場合。

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

パフォーマンスを向上させるために、静的ファイルは、Python コードを介さずに、Web サーバーによって直接処理されます。

上記の例では、静的ファイルのディスク上の場所は URL の場所と一致する必要があります。 つまり、この要求を `http://pythonapp.azurewebsites.net/static/site.css` でディスク上のファイルは、 `\static\site.css`します。

`WSGI_ALT_VIRTUALENV_HANDLER` では、WSGI ハンドラーを指定します。 上記の例では `app.wsgi_app` という名前の関数は、ハンドラーがあるため `wsgi_app` で `app.py` ルート フォルダーにします。

`PYTHONPATH` カスタマイズできますが、requirements.txt で指定することで、仮想環境にすべての依存関係をインストールする場合は、変更する必要はありません。


## 仮想環境のプロキシ

次のスクリプトは、WSGI ハンドラーの取得、仮想環境のアクティブ化、エラーの記録に使用されます。 修正することなく、汎用的に使用できます。

内容 `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################
    
    import datetime
    import os
    import sys
    import traceback
    
    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())
    
        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())
    
    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()
    
    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');
    
    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''
    
        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler
    
    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')
    
    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))
    
        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler
    
    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)
    
        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

## Git デプロイメントのカスタマイズ

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## トラブルシューティング - パッケージのインストール

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## トラブルシューティング - 仮想環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## 次のステップ

詳細については、次を参照してください。、 [Python デベロッパー センター](/develop/python/)します。
>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容

* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)











