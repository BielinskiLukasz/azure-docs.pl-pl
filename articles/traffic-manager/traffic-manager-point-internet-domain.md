<properties
   pageTitle="会社のインターネット ドメインで Traffic Manager ドメインが参照されるようにする | Microsoft Azure"
   description="この記事は、会社のドメイン名で Traffic Manager ドメイン名が参照されるようにするために役立ちます。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# 会社のインターネット ドメイン名で Azure Traffic Manager ドメイン名が参照されるようにする

会社のドメイン名で Traffic Manager ドメイン名が参照されるようにするには、インターネット DNS サーバー上の DNS リソース レコードを変更して、Traffic Manager プロファイルのドメイン名に会社のドメイン名をマップする CNAME レコード タイプを使用します。 Traffic Manager ドメイン名を表示、 **全般的な** Traffic Manager プロファイルの [構成] ページのセクションです。

たとえば、会社のドメイン名 www.contoso.com で Traffic Manager ドメイン名 contoso.trafficmanager.net を参照させるには、次のように DNS リソース レコードを更新します。

    www.contoso.com IN CNAME contoso.trafficmanager.net

すべてのトラフィック要求を *www.contoso.com* にリダイレクトされるよう、 *contoso.trafficmanager.net*します。

>[AZURE.IMPORTANT] などの第 2 レベル ドメインを指すことはできません *contoso.com*, 、Traffic Manager ドメイン。 これは、第 2 レベルのドメイン名の CNAME レコードを許可しない DNS プロトコルの制限です。

## 次のステップ

[Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)

[Traffic Manager  - プロファイルの無効化、有効化、または削除](disable-enable-or-delete-a-profile.md)

[Traffic Manager - エンドポイントの無効化または有効化](disable-or-enable-an-endpoint.md)

