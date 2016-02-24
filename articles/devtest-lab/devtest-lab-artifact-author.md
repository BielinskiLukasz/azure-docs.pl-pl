    <properties 
    pageTitle="Create custom artifacts for your DevTest Lab VM | Microsoft Azure"
    description="Learn how to author your own artifacts for use with DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

#DevTest ラボ VM のカスタム アーティファクトの作成

**成果物** 展開し、VM がプロビジョニングされたら、アプリケーションを構成するために使用します。 アーティファクトは、Git リポジトリ内のフォルダーに格納されているアーティファクト定義ファイルとその他のスクリプト ファイルで構成されます。 アーティファクト定義ファイルは、VM にインストールするものを指定するのに使用できる JSON と式で構成されます。 たとえば、アーティファクトの名前、実行するコマンド、コマンド実行時に使用可能なパラメーターを定義できます。 アーティファクト定義ファイル内では、他のスクリプト ファイルを名前で参照できます。

##アーティファクト定義ファイルの形式
次の例では、定義ファイルの基本構造を構成するセクションを示します。

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 要素名
 | 必須 | 説明
| ------------ | --------- | -----------
| $schema      | いいえ        | 定義ファイルの有効性のテストに役立つ JSON スキーマ ファイルの場所
| title        | はい       | ラボで表示されるアーティファクトの名前
| description  | はい       | ラボで表示されるアーティファクトの説明
| iconUri      | いいえ        | ラボで表示されるアイコンの URI
| targetOsType | はい       | アーティファクトをインストールする VM のオペレーティング システム。 サポートされているオプションは、Windows と Linux です。
| parameters   | いいえ        | アーティファクトのインストール コマンドがマシンで実行されるときに指定する値。 これは、アーティファクトのカスタマイズに役立ちます。
| runCommand   | はい       | VM 上で実行されるアーティファクトのインストール コマンド。

###アーティファクトのパラメーター

定義ファイルの parameters セクションでは、アーティファクトのインストール時にユーザーが入力できる値を指定します。 アーティファクトのインストール コマンドでこれらの値を参照できます。

次の構造でパラメーターを定義します。

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 要素名
 | 必須 | 説明
| ------------ | --------- | -----------
| type         | はい       | パラメーター値の型。 使用できる型については、下にある一覧を参照してください。
| [はい] の表示名       | ラボのユーザーに対して表示されるパラメーターの名前。
| description  | はい       | ラボで表示されるパラメーターの説明。

使用できる型は次のとおりです。

- string - 有効な JSON 文字列
- int - 有効な JSON 整数
- bool - 有効な JSON ブール値
- array - 有効な JSON 配列

##アーティファクトの式と関数

式と関数を使用して、アーティファクトのインストール コマンドを作成できます。
式は角かっこ ([ と ]) で囲み、アーティファクトのインストール時に評価されます。 式は、JSON 文字列値内の任意の場所に配置でき、常に別の JSON 値を返します。
 角かっこ [ で始まるリテラル文字列を使用する必要がある場合は、2 つの角かっこ [[ を使用する必要があります。
通常は、関数と共に式を使用して値を構成します。 JavaScript の場合と同様に、関数呼び出しは functionName(arg1,arg2,arg3) という形式になります。

次に、一般的な関数を示します。

- parameters(parameterName) - アーティファクト コマンドの実行時に指定するパラメーター値を返します。
- concat (arg1、arg2, arg3...) では、複数の文字列値を結合します。 この関数は、任意の数の引数を取ることができます。

次の例では、式と関数を使用して値を構成する方法を示します。

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

##カスタム アーティファクトの作成

カスタム アーティファクトを作成するには、次の手順を実行します。

1. JSON エディターのインストール - アーティファクト定義ファイルを操作するには、JSON エディターが必要です。 使用することをお勧め [Visual Studio Code](https://code.visualstudio.com/), 、Windows、Linux および OS X で使用されています。

1. Get Azure DevTest ラボ チームによって作成されたサンプル artifactfile.json の成果物を確認して、 [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab) を成果物の豊富なライブラリを作成しました、役立つ独自の成果物を作成します。 アーティファクト定義ファイルをダウンロードし、変更を加えて独自のアーティファクトを作成します。

1. IntelliSense の利用 - IntelliSense を利用して、アーティファクト定義ファイルの作成に使用できる有効な要素を確認します。 要素の値のさまざまなオプションを確認することもできます。 たとえば、IntelliSense を表示する Windows または Linux の 2 つの選択肢を編集するとき、 **targetOsType** 要素。

1. Git リポジトリへのアーティファクトの格納
    1. アーティファクトごとに個別のディレクトリを作成します。ディレクトリ名は、アーティファクト名と同じにします。
    1. 作成したディレクトリに、アーティファクト定義ファイル (artifactfile.json) を格納します。
    1. アーティファクト インストール コマンドから参照されるスクリプトを格納します。

    アーティファクト フォルダーの例を次に示します。

    ![Artifact git repo example](./media/devtest-lab-artifact-author/git-repo.png)

1. アーティファクトのリポジトリを追加、ラボの記事を参照してください [DevTest ラボに成果物の Git リポジトリを追加](devtest-lab-add-artifact-repo.md)します。

## 次のステップ

学習方法 [DevTest ラボに成果物の Git リポジトリを追加](devtest-lab-add-artifact-repo.md)します。

