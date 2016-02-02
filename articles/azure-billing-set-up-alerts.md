<properties
    pageTitle="Microsoft Azure サブスクリプションの請求の通知を設定する | Microsoft Azure"
    description="予想外の課金を回避するために Azure の請求に関する通知を設定する方法について説明します。"
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015" 
    ms.author="vikdesai"/>


# Microsoft Azure サブスクリプションの請求の通知を設定する

Azure サブスクリプションにかかる月額料金に懸念がありますか。 Azure サブスクリプションのアカウント管理者の場合、Azure 課金アラート サービスを使用して、Azure アカウントの課金アクティビティを監視と管理するのに役立つ、カスタマイズされた請求アラートを作成できます。

このサービスはプレビュー版サービス用の記号は、最初に行う必要があること、- を参照してください <a href="https://account.windowsazure.com/PreviewFeatures">プレビュー機能のページ </a> 、Azure アカウント管理ポータルでこれを行う。

## アラートのしきい値と電子メールの受信者を設定する

サブスクリプションの請求書発行サービスが有効になっている確認の電子メールを受信したら <a href="https://account.windowsazure.com/Subscriptions">サブスクリプション ページ</a> アカウント ポータルで。 監視するサブスクリプションをクリックし、**[アラート]** をクリックします。

![][image1]

次に、**[アラートの追加]** をクリックして、最初のアラートを作成します。サブスクリプションあたり、合計 5 つの課金アラートを設定できます。それぞれのアラートに対して、別のしきい値と最大 2 名までの電子メール受信者を設定できます。

![][image2]

アラートを追加するとき、一意の名前を付けて、使用金額のしきい値を選択して、アラートの送信先の電子メール アドレスを選択します。 しきい値を設定するとき、**[アラート対象]** リストから **[請求合計]**、**[通貨クレジット]** のいずれかを選択できます。 請求合計の場合は、サブスクリプションの使用金額がしきい値を超えるとアラートが送信されます。 通貨クレジットの場合は、通貨クレジットが制限を下回るとアラートが送信されます。 通常、通貨クレジットは、無料試用版と MSDN アカウントに関連付けられているサブスクリプションに適用されます。

![][image3]

Azure は、任意の電子メール アドレスをサポートしていますが、電子メール アドレスが有効であるかは確認されないため、入力ミスがないようダブルチェックしてください。

## アラートを確認する

アラートを設定した後、アカウント センターで一覧表示され、設定可能な残数も表示されます。 それぞれのアラートに対し、送信された日付と時刻、アラートが請求合計または通貨クレジットであるか、設定した制限が表示されます。 日付と時刻形式は 24 時間協定世界時 (UTC) で、日付は yyyy-mm-dd の形式です。 アラートを編集するには、リストのアラートのプラス記号をクリックします。アラートを削除するには、ごみ箱アイコンをクリックします。


[image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[image2]: ./media/azure-billing-set-up-alerts/billingalert2.png 
[image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 

