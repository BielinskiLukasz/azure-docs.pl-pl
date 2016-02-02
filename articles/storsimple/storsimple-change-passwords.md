<properties 
   pageTitle="StorSimple パスワードを変更する | Microsoft Azure" 
   description="StorSimple Manager サービスを使用して、StorSimple Snapshot Manager のパスワードとデバイス管理者のパスワードを変更する方法について説明します。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="12/02/2015"
   ms.author="v-sharos@microsoft.com"/>


# StorSimple Manager サービスを使用した StorSimple のパスワードの変更

## 概要

Azure クラシック ポータルの **[構成]** ページには、StorSimple Manager サービスの管理対象の StorSimple デバイスで再構成することができるすべてのデバイス パラメーターが含まれます。 このチュートリアルでは、**[構成]** ページを使用して、StorSimple Snapshot Manager のパスワードまたはデバイス管理者のパスワードを変更する方法について説明します。

## StorSimple Snapshot Manager のパスワードの変更

StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードとデバイスの IP アドレスを入力するように求められます。 このパスワードは、Windows PowerShell のインターフェイスを使用して最初に構成されます。 詳細については、次を参照してください。 [手順 3: 構成し、StorSimple 用 Windows PowerShell を使用してデバイスを登録](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) で [オンプレミス StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)します。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、クラシック ポータルから変更できます。 StorSimple Snapshot Manager のパスワードを変更するには、以下の手順を実行します。

#### StorSimple Snapshot Manager のパスワードを変更するには

1. クラシック ポータルで、デバイスの **[デバイス]**、**[構成]** の順にクリックします。

2. **[StorSimple Snapshot Manager]** セクションまでスクロールします。 14 または 15 文字のパスワードを入力します。 大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用してください。

3. パスワードを確認入力します。

4. ページの下部にある **[保存]** をクリックします。

StorSimple Snapshot Manager のパスワードが更新されました。

## デバイス管理者のパスワードの変更

Windows PowerShell インターフェイスを使用して StorSimple デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 サービスに初めて StorSimple デバイスを登録した場合、このインターフェイスの初期パスワードは *Password1* です。 データのセキュリティ上、登録プロセスの最後にこのパスワードを変更することが必須となっています。 このパスワードを変更せずに登録プロセスを終了することはできません。 詳細については、次を参照してください。 [手順 3: 構成し、StorSimple 用 Windows PowerShell を使用してデバイスを登録](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) で [オンプレミス StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)します。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、Azure クラシック ポータルから変更できます。 デバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### デバイス管理者のパスワードを変更するには

1. クラシック ポータルで、デバイスの **[デバイス]**、**[構成]** の順にクリックします。

2. **[デバイス管理者パスワード]** セクションまでスクロールします。 管理者のパスワードは、8 ～ 15 文字で指定してください。 大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用する必要があります。

3. パスワードを確認入力します。

4. ページの下部にある **[保存]** をクリックします。

これでデバイス管理者のパスワードが更新されました。 この変更されたパスワードを使用して、Windows PowerShell インターフェイスにアクセスすることができます。

## 次のステップ

- 詳細について [StorSimple のセキュリティ](storsimple-security.md)します。

- 詳細について [デバイスの構成を変更する](storsimple-modify-device-config.md)します。

- 詳細について [StorSimple Manager サービスを使用して、StorSimple デバイスを管理する](storsimple-manager-service-administration.md)します。




