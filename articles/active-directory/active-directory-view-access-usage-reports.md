<properties
    pageTitle="アクセスおよび使用状況レポートを表示する | Microsoft Azure"
    description="アクセスおよび使用状況レポートを使用して組織のディレクトリの整合性とセキュリティを把握する方法を説明します。"
    services="active-directory"
    documentationCenter=""
    authors="kenhoff"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="kenhoff;Justinha;curtand"/>


# アクセスおよび使用状況レポートの表示

*このドキュメントの一部である、 [Azure Active Directory のレポート作成ガイド](active-directory-reporting-guide.md)します。*

Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

レポートは、Microsoft Azure 管理ポータルで、次の方法で分類されます。

- 異常レポート: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。
- 統合アプリケーション レポート – 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure Active Directory は、何千ものクラウド アプリケーションとの統合を提供します。
- エラー レポート: 外部アプリケーションにアカウントをプロビジョニングするときに発生するエラーを示します。
- ユーザー固有レポート: ディスプレイ デバイス/サインイン アクティビティのデータを特定のユーザーに表示します。
- アクティビティ ログ - 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

> [AZURE.NOTE]
>
- いくつかの高度な異常およびリソース使用状況レポートは有効にした場合にのみ使用できます [Azure Active Directory Premium](active-directory-get-started-premium.md)します。 高度なレポートでは、アクセスのセキュリティ、潜在的な脅威への対処を向上させ、デバイスへのアクセスやアプリケーションの使用状況に関する分析機能を利用できます。
- 中国のお客様は、Azure Active Directory の世界中のインスタンスを使用して、Azure Active Directory Premium および Basic エディションを利用できます。 中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure Active Directory Premium および Basic エディションは現在サポートされていません。 詳細については、下記までお問い合わせください、 [Azure Active Directory フォーラム](http://feedback.azure.com/forums/169401-azure-active-directory)します。

## レポート

|   レポート                                              |   説明                                                                                 |
|   ------                                              |   -----                                                                                       |
|   **異常アクティビティ レポート**
|   [不明なソースからのサインイン](active-directory-reporting-sign-ins-from-unknown-sources.md)    |   追跡されないサインインの試行を示す場合があります。                                    |
|   [複数のエラー後のサインイン](active-directory-reporting-sign-ins-after-multiple-failures.md)      |   ブルート フォース攻撃の成功を示す場合があります。                                               |
|   [複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md)  |   複数のユーザーが同じアカウントでサインインしていることを示す場合があります。                      |
|   [不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)    |   持続的な侵入の試行後のサインインの成功を示す場合があります。                      |
|   [感染している可能性があるデバイスからのサインイン](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)                |   感染の疑いのあるデバイスからサインインしようとした可能性があります。                          |
|   [不規則なサインイン アクティビティ](active-directory-reporting-irregular-sign-in-activity.md)                          |   ユーザーのサインイン パターンに対して異常なイベントを示す場合があります。                                   |
|   [異常なサインイン アクティビティがあるユーザー](active-directory-reporting-users-with-anomalous-sign-in-activity.md)                |   アカウントが侵害された可能性があるユーザーを示します。                                   |
|   資格情報が漏洩したユーザー                       |   資格情報が漏洩したユーザー                                                               |
|   **アクティビティ ログ**
|   監査レポート                                        |   ディレクトリの監査されたイベント                                                            |
|   パスワード リセット アクティビティ                             |   組織内で行われたパスワード リセットに関する詳しい情報を示します。                |
|   パスワード リセット登録アクティビティ                |   組織内で行われたパスワード リセット登録の詳しい情報を示します。   |
|   セルフ サービス グループ アクティビティ                        |   ディレクトリ内のすべてのグループ セルフ サービス アクティビティに関するアクティビティ ログを提供します。               |
|   **統合アプリケーション**
|   アプリケーションの使用状況                                   |   ディレクトリと統合されたすべての SaaS アプリケーションの利用状況の概要を提供します。          |
|   アカウント プロビジョニングのアクティビティ                       |   外部アプリケーションにアカウントのプロビジョニングを試行した回数の履歴を示します。              |
|   パスワード ロールオーバーの状態                            |   SaaS アプリケーションの自動パスワード ロールオーバーの状態の詳細な説明を入力します。    |
|   アカウント プロビジョニング エラー                         |   外部アプリケーションへのユーザーのアクセスに対する影響を示します。                              |
|   **Rights management**
|   RMS の利用状況                                           |   Rights Management の利用状況の概要を示します。                                              |
|   最もアクティブな RMS ユーザー                               |   RMS で保護されたファイルにアクセスした上位 1,000 名のアクティブ ユーザーを示します。                                |
|   RMS デバイスの利用状況                                    |   RMS で保護されたファイルにアクセスするために使用されたデバイスを示します。                                        |
|   RMS 対応アプリケーションの利用状況                       |   RMS 対応アプリケーションの利用状況を示します。                                                  |

## レポートのエディション

|   レポート                                              |   無料    |   基本   |   Premium     |
|   ------                                              |   ----    |   -----   |   --------    |
|   **異常アクティビティ レポート**
|   不明なソースからのサインイン                       |   ✓       |   ✓   |   ✓           |
|   複数のエラー後のサインイン                    |   ✓       |   ✓   |   ✓           |
|   複数の地域からのサインイン                  |   ✓       |   ✓   |   ✓           |
|   不審なアクティビティのある IP アドレスからのサインイン |           |       |   ✓           |
|   感染している可能性があるデバイスからのサインイン             |           |       |   ✓           |
|   不規則なサインイン アクティビティ                          |           |       |   ✓           |
|   異常なサインイン アクティビティがあるユーザー               |           |       |   ✓           |
|   資格情報が漏洩したユーザー                       |           |       |   ✓           |
|   **アクティビティ ログ**
|   監査レポート                                        |   ✓       |   ✓   |   ✓           |
|   パスワード リセット アクティビティ                             |           |       |   ✓           |
|   パスワード リセット登録アクティビティ                |           |       |   ✓           |
|   セルフ サービス グループ アクティビティ                        |           |       |   ✓           |
|   **統合アプリケーション**
|   アプリケーションの使用状況                                   |           |       |   ✓           |
|   アカウント プロビジョニングのアクティビティ                       |   ✓       |   ✓   |   ✓           |
|   パスワード ロールオーバーの状態                            |           |       |   ✓           |
|   アカウント プロビジョニング エラー                         |   ✓       |   ✓   |   ✓           |
|   **Rights Managment**
|   RMS の利用状況                                           |           |       |   RMS のみ    |
|   最もアクティブな RMS ユーザー                               |           |       |   RMS のみ    |
|   RMS デバイスの利用状況                                    |           |       |   RMS のみ    |
|   RMS 対応アプリケーションの利用状況                       |           |       |   RMS のみ    |



## 異常アクティビティ レポート
<p>異常サインイン アクティビティ レポートは、Office365、Microsoft Azure 管理ポータル、Azure AD アクセスパネル、Sharepoint Online、Dynamics CRM Online、その他の Microsoft オンライン サービスに対する不審なサインイン アクティビティにフラグを設定します。</p>
<p>すべての「複数のエラー後サインイン」レポートを除くこれらのレポートもフラグ疑わしい <i>フェデレーション</i> フェデレーション プロバイダーに関係なく、前述のサービスへのサインインします。 </p>
<p>次のレポートを使用できます。 </p><ul>
<li>[Sign ins from unknown sources](active-directory-reporting-sign-ins-from-unknown-sources.md).</li>
<li>[Sign ins after multiple failures](active-directory-reporting-sign-ins-after-multiple-failures.md).</li>
<li>[Sign ins from multiple geographies](active-directory-reporting-sign-ins-from-multiple-geographies.md).</li>
<li>[Sign ins from IP addresses with suspicious activity](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).</li>
<li>[Irregular sign in activity](active-directory-reporting-irregular-sign-in-activity.md).</li>
<li>[Sign ins from possibly infected devices](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).</li>
<li>[Users with anomalous sign in activity](active-directory-reporting-users-with-anomalous-sign-in-activity.md).</li>
<li>資格情報が漏洩したユーザー</li></ul>










## アクティビティ ログ

### 監査レポート

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| 過去 24 時間、過去 7 日間、または過去 30 日間内のすべての監査イベントのレコードを表示します。 <br /> 詳細については、次を参照してください [Azure Active Directory 監査レポート イベント。](active-directory-reporting-audit-events.md) | [ディレクトリ] > [レポート] タブ |

![監査レポート](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### パスワード リセット アクティビティ

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| 組織で実行されたすべてのパスワードのリセットの試行が表示されます。 | [ディレクトリ] > [レポート] タブ |

![パスワード リセット アクティビティ](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### パスワード リセット登録アクティビティ

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| 組織で実行されたすべてのパスワード リセットの登録が表示されます。 | [ディレクトリ] > [レポート] タブ |

![パスワード リセット登録アクティビティ](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### セルフ サービス グループ アクティビティ

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| ディレクトリ内でセルフ サービスにより管理されるグループのすべてのアクティビティが表示されます。 | [ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [デバイス] タブ |

![セルフ サービス グループ アクティビティ](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## 統合アプリケーション レポート

### アプリケーションの使用状況: 概要

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| ディレクトリ内のすべての SaaS アプリケーションについて使用状況を表示するには、このレポートを使用します。 このレポートは、アクセス パネルでユーザーがアプリケーションをクリックした回数の合計に基づきます。 | [ディレクトリ] > [レポート] タブ |

このレポートには、符号が含まれています。 へ _すべて_ 事前に統合された Microsoft アプリケーションなど、目的のディレクトリがアプリケーションにアクセスします。

事前に統合された Microsoft アプリケーションには、Office 365、Sharepoint、Microsoft Azure 管理ポータルなどが含まれます。

![アプリケーションの利用状況の概要](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### アプリケーションの使用状況: 詳細

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| どの程度、特定の SaaS アプリケーションが使用されているかを表示するときに、このレポートを使用します。 このレポートは、アクセス パネルでユーザーがアプリケーションをクリックした回数の合計に基づきます。 | [ディレクトリ] > [レポート] タブ |

### アプリケーション ダッシュボード

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| このレポートは、選択した間隔で、組織内のユーザーによるアプリケーションへの累計サインインを示します。 ダッシュボード ページ上のグラフは、そのアプリケーションのすべての使用状況の傾向を把握するために役立ちます。 | [ディレクトリ] > [アプリケーション] > [ダッシュボード] タブ |

## エラー レポート

### アカウント プロビジョニング エラー

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| SaaS アプリケーションから Azure Active Directory へのアカウントの同期中に発生するエラーを監視する場合に使用します。 | [ディレクトリ] > [レポート] タブ |

![アカウント プロビジョニング エラー](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## ユーザー固有レポート

### デバイス

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| 特定のユーザーが Azure Active Directory へのアクセスに使用したデバイスの IP アドレスと地理的な場所を表示する場合は、このレポートを使用します。 | [ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [デバイス] タブ |

### アクティビティ

| 説明        | レポートの場所 |
| :-------------     | :-------        |
| ユーザーのサインイン アクティビティを表示します。 このレポートには、サインインされたアプリケーション、使用されたデバイス、IP アドレス、および場所などの情報が含まれています。 Microsoft アカウントでサインインしたユーザーの履歴は収集されません。 | [ディレクトリ] > [ユーザー] > <i>[ユーザー]</i> > [アクティビティ] タブ |

#### ユーザーのアクティビティ レポートに含まれるサインイン イベント

特定の種類のイベントへのサインインだけが、ユーザーのアクティビティ レポートに表示されます。

| イベントの種類                                        | 表示の有無     |
| ----------------------                                | ---------     |
| サインイン、 [パネルにアクセスします。](http://myapps.microsoft.com/)              | あり           |
| サインイン、 [Azure 管理ポータル](https://manage.windowsazure.com/)       | あり           |
| サインイン、 [Microsoft Azure ポータル](http://portal.azure.com/)            | あり           |
| サインイン、 [Office 365 ポータル](http://portal.office.com/)            | あり           |
| Outlook などのネイティブのアプリケーションへのサインイン (次の例外を参照してください)          | あり           |
| アクセス パネルを通じた Salesforce などのフェデレーション/プロビジョニング アプリケーションへのサインイン | あり           |
| アクセス パネルを通じた Twitter などのパスワードに基づくアプリケーションへのサインイン       | あり           |
| ディレクトリに追加されているカスタム ビジネス アプリケーションへのサインイン        | なし (近日対応予定)  |
| ディレクトリに追加されている Azure AD アプリケーション プロキシ アプリへのサインイン    | なし (近日対応予定)  |

> 注: このレポートでノイズの量を減らすためには、サインインして、 [Microsoft Online Services サインイン アシスタント](http://community.office365.com/en-us/w/sso/534.aspx) は表示されません。










## セキュリティ違反が疑われる場合の考慮事項

ユーザー アカウントが漏洩した可能性があると疑う場合、またはクラウド内のディレクトリ データのセキュリティ違反につながる可能性があるなんらかの種類の不審なユーザー アクティビティがある場合は、次の 1 つ以上のアクションを検討することができます。

- ユーザーに連絡を取って、アクティビティについて確認する
- ユーザーのパスワードをリセットする
- [多要素認証を有効にする](multi-factor-authentication-get-started.md) 追加のセキュリティ

## レポートの閲覧またはダウンロード:

1. Azure 管理ポータルで、クリックして **Active Directory**, を組織のディレクトリの名前をクリックして、クリックして **レポート**します。
2. [レポート] ページで、閲覧またはダウンロードするレポート をクリックします。

    > [AZURE.NOTE] Azure Active Directory のレポート機能を使用した初めての場合を選択するためのメッセージが表示されます。 同意する場合は、チェック マーク アイコンをクリックして続行します。

3. [間隔] の横にあるドロップダウン メニューをクリックしてから、このレポートを生成するときに使用する、次のいずれかの期間をクリックします。
    - 過去 24 時間
    - 過去 7 日間
    - 過去 30 日間
4. レポートを実行するには、チェック マーク アイコンをクリックします。
    - Microsoft Azure 管理ポータルに最大 1,000 件のイベントが表示されます。
5. クリックして、必要に応じて **ダウンロード** オンライン表示またはアーカイブ目的のためのコンマ区切り値 (CSV) 形式で圧縮されたファイルにレポートをダウンロードします。
    - ダウンロードしたファイルには、最大 75,000 件のイベントが含まれます。
    - 追加のデータをチェック アウト、 [Azure AD Reporting API](active-directory-reporting-api-getting-started.md)します。

## イベントを無視する

異常レポートを表示している場合は、関連するレポートに表示されるさまざまなイベントを無視できることがあります。 イベントを無視する、レポート内のイベントを強調表示し、クリックして **無視**します。  **無視** ボタンが強調表示されたイベントをレポートから完全に削除され、ライセンスを付与されたグローバル管理者によってのみ使用できます。

## 電子メールの自動通知

詳細については、Azure AD でのレポート通知、チェック アウト [Azure Active Directory のレポート通知](active-directory-reporting-notifications.md)します。

## 次の手順

- [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)


