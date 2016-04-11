<properties
    pageTitle="不規則なサインイン アクティビティ"
    description="機械学習アルゴリズムによって異常と識別されたサインインが含まれているレポート。"
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2015"
    ms.author="saah;kenhoff"/>

# 不規則なサインイン アクティビティ

不規則なサインインは、異常なサインイン場所とデバイスを組み合わせた「不可能な移動」条件に基づいてマイクロソフトの機械学習アルゴリズムによって識別されるサインインです。 これは、ハッカーが他人のアカウントを使用してサインインに成功したことを示している可能性があります。
30 日以内に 10 回以上の異常なサインイン イベントが検出された場合は、グローバル管理者に電子メール通知が送信されます。 差出人セーフ リストに aad-alerts-noreply@mail.windowsazure.com を含めてしてください。

