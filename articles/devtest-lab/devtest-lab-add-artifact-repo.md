    <properties
    pageTitle="Add a Git artifact repository to your DevTest Lab | Microsoft Azure"
    description="Add a GitHub or Visual Studio Team Services Git repository for your custom artifacts to your lab"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

# DevTest ラボへの Git アーティファクト リポジトリの追加

## 概要

既定では、公式の Azure DevTest ラボ アーティファクト リポジトリからのアーティファクトが DevTest ラボに含まれます。 ラボに Git アーティファクト リポジトリを追加することで、チームが作成したアーティファクトを含めることができます。 リポジトリをホストできる [GitHub](https://github.com) または [Visual Studio Team Services (VSTS)](https://visualstudio.com)します。

- GitHub リポジトリを作成する方法については、次を参照してください。 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)します。
- Git リポジトリをチームのサービス プロジェクトを作成する方法については、次を参照してください。 [Visual Studio チームのサービスへの接続](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)します。

次のスクリーン ショットでは、成果物を含むリポジトリが GitHub で検索する方法の例を示します。  
![GitHub アーティファクト リポジトリのサンプル](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## GitHub のアーティファクト リポジトリのラボへの追加

ラボに GitHub のアーティファクト リポジトリを追加するには、最初に HTTPS クローン URL と個人用アクセス トークンをアーティファクト リポジトリから取得し、次にラボでその情報を入力します。

### GitHub リポジトリのクローン URL と個人用アクセス トークンの取得

1. 後で使用するために、チームのアーティファクトを含む GitHub リポジトリのホーム ページで **HTTPS クローンの URL** を保存します。

1. 右上隅にあるプロファイルの画像をタップし、**[設定]** を選択します。

1. **[個人設定]** メニューの左側で、**[個人用アクセス トークン]** をタップします。

1. **[Generate new token]** をタップします。

1. **新しい個人用アクセス トークン**ページで、**トークンの説明**を入力し、**[範囲の選択]** で既定の項目をそのまま使用します。次に **[Generate Token]** を選択します。

1. 後で必要になるため、生成されたトークンを保存します。

1. これで、GitHub を閉じることができます。

### GitHub リポジトリに、ラボを接続します。

1. サインイン、 [Azure プレビュー ポータル](http://portal.azure.com)します。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. ラボのブレードで、**[設定]** をタップします。

1. ラボの **[設定]** ブレードで、**[アーティファクト リポジトリ]** をタップします。

1. **[アーティファクト リポジトリ]** ブレードで、次を実行します。

    1. リポジトリの**名前**を入力します。
    1. 保存された **Git クローン Url** を入力します。
    2. アーティファクトが格納されているアーティファクト リポジトリの**フォルダー パス**を入力します。
    3. アーティファクト リポジトリに保存された**個人用アクセス トークン**を入力します。
    4. **[保存]** をタップします。

これで、**[Add Artifacts]** ブレードにリポジトリ内のアーティファクトが一覧表示されます。

## ラボへの Visual Studio Git のアーティファクト リポジトリの追加

ラボに Visual Studio Git のアーティファクト リポジトリを追加するには、最初に HTTPS クローン URL と個人用アクセス トークンをアーティファクト リポジトリから取得し、次にラボでその情報を入力します。

### アーティファクト プロジェクトの Visual Studio Web ページ

1. チーム コレクションのホーム ページを開きます (たとえば、 `https://contoso-web-team.visualstudio.com`)、アーティファクト プロジェクトをタップします。

2. プロジェクトのホーム ページで、**[コード]** をタップします。

1. クローン URL を表示するには、プロジェクト **[コード]** ページで、**[クローン]** をタップします。

1. このチュートリアルで後ほど必要になるため、URL を保存します。

1. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューから **[プロファイル]** をタップします。

1. プロファイル情報ページで、**[セキュリティ]** をタップします。

1. **[セキュリティ]** タブで **[追加]** をタップします。

1. **[個人用アクセス トークンの作成]** ページで、次を実行します。

    1. トークンの**説明**を入力します。
    2. **[Expires In]** の一覧から **[180 日]** を選択します。
    3. **[アカウント]** の一覧から **[アクセス可能なすべてのアカウント]** を選択します。
    4. **[All scopes]** オプションを選択します。
    5. **[トークンの作成]** を選択します。

1. 完了すると、新しいトークンが **[個人用アクセス トークン]** の一覧に表示されます。 **[トークンのコピー]** をタップし、すぐに使用するため、トークンの値を保存します。

### DevTest ラボ

1. ラボのブレードで、**[設定]** をタップします。

    ![設定の選択](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. **[設定]** ブレードで、**[アーティファクト リポジトリ]** をタップします。

1. **[アーティファクト リポジトリ]** ブレードで、次を実行します。

    1. リポジトリの表示**名**を入力します。
    1. 保存された **Git クローン Url** を入力します。
    2. アーティファクトが格納されているアーティファクト リポジトリの**フォルダー パス**を入力します。
    3. アーティファクト リポジトリに保存された**個人用アクセス トークン**を入力します。
    4. **[保存]** をタップします。





