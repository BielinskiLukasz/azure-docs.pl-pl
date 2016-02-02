<properties
    pageTitle="JavaScript バックエンド モバイル サービスでカスタム API を定義する方法 | Azure Mobile Services"
    description="JavaScript バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>



# JavaScript バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法

> [AZURE.SELECTOR]
- [JavaScript backend](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET backend](./mobile-services-dotnet-backend-define-custom-api.md)


このトピックでは、JavaScript バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法を紹介します。 カスタム API を使用するとサーバー機能を持つカスタム エンドポイントを定義できますが、データベースの insert、update、delete、read 操作には対応しません。 カスタム API を使用することにより、HTTP ヘッダーや本文の形式など、メッセージングをいっそう詳細に制御できます。

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

Mobile Services クライアント ライブラリを使用して、アプリでカスタム API を呼び出す方法については、次を参照してください。 [カスタム API を呼び出す](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) クライアント SDK リファレンスにします。













