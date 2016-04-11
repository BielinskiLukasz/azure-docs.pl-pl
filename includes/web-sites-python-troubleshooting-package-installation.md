一部のパッケージは、Azure での実行時に pip を使用してインストールできません。  単に、パッケージが Python Package Index で使用できないだけの場合もあります。  コンパイラーが必要な場合もあります (コンパイラーは、Azure App Service で Web アプリを実行しているコンピューターでは利用できません)。

このセクションでは、この問題に対処する方法を紹介します。

### wheel をリクエストする

パッケージのインストールにコンパイラが必要な場合は、パッケージの所有者に連絡して、パッケージ用の wheel を使用可能にするようリクエストします。

最新の利用可能に [Python 2.7 用の Microsoft Visual C コンパイラ][], 、Python 2.7 のネイティブ コードを含むパッケージの作成が容易になります。

### wheel をビルドする (Windows が必要)

このオプションを使用する際は、Azure App Service の Web アプリで使用しているプラットフォーム/アーキテクチャ/バージョン (Windows/32 ビット/2.7 または 3.4) に一致する Python 環境を使用して、パッケージをコンパイルしてください。

コンパイラが必要なためにパッケージがインストールできない場合は、ローカル コンピューターにコンパイラをインストールして、パッケージの wheel をビルドすることができます。このパッケージはリポジトリに組み込まれます。

Mac/linux ユーザー: Windows コンピューターへのアクセスを取得していない場合に表示 [を実行している Windows の仮想マシンの作成][] Azure で VM を作成する方法についてです。  その方法に従って、wheel をビルドしてリポジトリに追加し、必要に応じて VM を破棄します。 

Python 2.7 をインストールできます [Python 2.7 用の Microsoft Visual C コンパイラ][]します。

Python 3.4 の場合は、インストールすることができます [Microsoft の Visual C 2010 Express][]します。

wheel をビルドするには、wheel パッケージが必要です。

    env\scripts\pip install wheel

`pip wheel` を使用して依存関係をコンパイルします。

    env\scripts\pip wheel azure==0.8.4

これによって、\wheelhouse フォルダーに .whl ファイルが作成されます。  \wheelhouse フォルダーと wheel ファイルをリポジトリに追加します。

requirements.txt を編集して先頭に `--find-links` オプションを追加します。 このオプションは、python パッケージのインデックスにアクセスする前に、ローカル フォルダーで完全一致を検索するように pip に指示します。

    --find-links wheelhouse
    azure==0.8.4

\wheelhouse フォルダー内のすべての依存関係を組み込む必要があり、python パッケージのインデックスを一切使用しない場合は、requirements.txt の先頭に `--no-index` を追加すると、pip で強制的にパッケージ インデックスを無視することができます。

    --no-index

### インストールをカスタマイズする

デプロイメント スクリプトをカスタマイズし、easy\_install などの代替インストーラーを使用して、仮想環境にパッケージをインストールできます。  deploy.cmd のコメントアウトされた例を参照してください。  pip でインストールされることを避けるため、このようなパッケージを requirements.txt に指定していないことを確認してください。

これをデプロイメント スクリプトに追加します。

    env\scripts\easy_install somepackage

また、easy\_install を使用して、exe インストーラーからインストールすることもできます (一部のインストーラーは zip と互換性があるため、easy\_install でサポートされます)。  インストーラーをリポジトリに追加し、パスを実行可能ファイルに渡して easy\_install を呼び出します。

これをデプロイメント スクリプトに追加します。

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### 仮想環境をリポジトリに組み込む (Windows が必要)

このオプションを使用する際は、Azure App Service の Web アプリで使用しているプラットフォーム/アーキテクチャ/バージョン (Windows/32 ビット/2.7 または 3.4) に一致する仮想環境を使用してください。

仮想環境をリポジトリに組み込む場合は、空のファイルを作成することによって、デプロイメント スクリプトで仮想環境の管理が実行されることを回避できます。

    .skipPythonDeployment

仮想環境が自動的に管理された場合にファイルが残らないように、アプリ上の既存の仮想環境を削除することをお勧めします。


[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949

