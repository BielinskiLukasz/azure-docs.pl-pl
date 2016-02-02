<properties
    pageTitle="Web アプリ リソースを他のリソース グループに移動する"
    description="Web アプリおよびアプリ サービスを一つのリソース グループから他のリソース グループに移動するシナリオについて記述しています。"
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="zarizvi"/>


# サポートされている移動の構成

使用して Azure Web アプリのリソースを移動する、 [ARM Move Resources Api](../resource-group-move-resources.md)します。

現在、Azure の Web アプリでは次の移動のシナリオがサポートされています。

* リソース グループ (Web アプリ、アプリのサービス プラン、および証明書) の内容全体を別のリソース グループに移動します。
    * 注: このシナリオでは、変換先のリソース グループにすべてのMicrosoft.Web リソースを含めることはできません。
* 個々の Web アプリをそれぞれの異なるリソース グループに移動しますが、この時 Web アプリはまだ現行のアプリのサービス プランでホストされています (アプリのサービス プランは古いリソース グループにとどまっています)。





