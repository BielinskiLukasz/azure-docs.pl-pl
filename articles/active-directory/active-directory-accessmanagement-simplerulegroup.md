<properties
    pageTitle="単純なルールを作成してグループの動的メンバーシップ管理を行う| Microsoft Azure"
    description="単純なルールを作成してグループの動的メンバーシップ管理を行う方法について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="curtand"/>



# 単純なルールを作成してグループの動的メンバーシップ管理を行う

特定のグループの動的メンバーシップ管理を有効にするには、次の手順を実行します。

1. Azure ポータルの **[グループ]** タブで編集対象のグループを選択し、そのグループの **[構成]** タブで **[動的メンバーシップを有効にする]** スイッチを **[はい]** に設定します。

2. ここで、このグループに対する動的メンバーシップの働きを制御する単純なルールを 1 つグループに設定することができます。 **[追加するユーザーの所属]** オプションがオンになっていることを確認し、ユーザーのプロパティ (department、jobTitle など) をリストから選択します。

3. 次に、条件 (等しくない、等しい、指定値で始まらない、指定値で始まる、指定値を含まない、指定値を含む、一致しない、一致する) を選び、選択したユーザー プロパティの値を指定します。 たとえば、グループを SaaS アプリケーションに割り当てる場合で、なおかつ jobTitle が Sales Rep と等しい (-eq) ときに**ユーザーを追加**するようにルールを設定して、そのグループの動的メンバーシップを有効にした場合、Azure AD ディレクトリ内で、役職が Sales Rep に設定されているすべてのユーザーに、この SaaS アプリケーションへのアクセス権が割り当てられます。

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセスを管理します。](active-directory-manage-groups.md)
* [Azure Active Directory とは何ですか。](active-directory-whatis.md)
* [内部設置型 id と Azure Active Directory の統合](active-directory-aadconnect.md)





