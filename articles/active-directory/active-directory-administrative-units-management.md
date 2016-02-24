<properties
   pageTitle="Azure Active Directory の管理単位の管理"
   description="管理単位を使用した Azure Active Directory での細かいレベルの権限の委任"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="curtand"/>

# Azure AD の管理単位 - パブリック プレビュー

この記事では、管理単位について説明します。管理単位は、管理権限をユーザーのサブセットに委任したり、ユーザーのサブセットにポリシーを適用したりするために使用できる、Azure Active Directory の新しいリソース コンテナーです。 Azure Active Directory では、全体管理者が管理単位を使用して、各地域の管理者に権限を委任したり、細かなレベルでポリシーを設定したりできます。

これは、部門が独立している組織、たとえば、互いに独立している多数の自律的なスクール (ビジネス スクール、エンジニアリング スクールなど) で構成される大きな大学で役に立ちます。 このような部門には、その部門のアクセスの制御、ユーザーの管理、およびポリシーの設定を行う IT 管理者が存在します。 全体管理者は、これらの部門の管理者に、特定の部門のユーザーに適用される権限を与えることができます。 前の例を使用して具体的に説明すると、全体管理者は、特定のスクール (ビジネス スクール) の管理単位を作成し、その中にビジネス スクールのユーザーだけを含めることができます。 その後、全体管理者は、ビジネススクールの IT スタッフに限定されたロールを追加できます。つまり、ビジネス スクールの IT スタッフに、ビジネス スクールという管理単位にのみ適用される権限を与えます。

> [AZURE.IMPORTANT]
> 管理単位は、Azure Active Directory Premium を有効にした場合にのみ、作成して使用できます。 詳細については、次を参照してください。 [Azure AD Premium の概要](active-directory-get-started-premium.md)します。

全体管理者の観点から見ると、管理単位は、作成してリソースを設定できるディレクトリ オブジェクトです。 **このリリースでは、ユーザーのみがこれらのリソースにできます。**管理単位を作成して設定した後、与えられた権限の適用先を管理単位に含まれているリソースに限定するスコープとして使用できます。

## 管理単位の管理

このプレビュー リリースでは、Azure Active Directory Module for Windows PowerShell コマンドレットを使用して、管理単位の作成と管理を実行できます。

詳細については、ソフトウェアの要件と Azure AD モジュールをインストールして構文、パラメーターの説明、および例についてなどの管理単位を管理するための Azure AD Module コマンドレットについて情報を参照してください。 [管理 Windows PowerShell を使用して Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)します。


## 次のステップ
[Azure Active Directory のエディション](active-directory-editions.md)

