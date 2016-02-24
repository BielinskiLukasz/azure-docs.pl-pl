<properties
    pageTitle="Xamarin.Android 向け Mobile Services の使用 | Microsoft Azure"
    writer="craigd"
    description="Azure Mobile Services を Xamarin.Android アプリで使用する方法について説明します。"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="11/05/2015"
    ms.author="donnam"/>

# <a name="getting-started"></a>Mobile Services の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

このチュートリアルでは、Azure Mobile Services を使用して Xamarin.Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリ データを保存する簡単な *To do list* アプリの両方を作成します。

このチュートリアルのビデオを見る場合は、下のクリップでこのチュートリアルと同じ手順が表示されます。

ビデオ:「を取得する開始と Xamarin と Azure モバイル サービス」Craig dunn 氏 Xamarin のデベロッパー エバンジェ リスト (継続時間: 10時 05分分)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了する必要があります [Xamarin.Android]、Xamarin Studio、Visual Studio プラグイン (Windows 上) と最新の Android プラットフォームをインストールします。 このほか、Android SDK のバージョン 4.2 以降が必要になります。

ダウンロードしたクイック スタート プロジェクトには、Xamarin.Android 向けの Azure Mobile Services コンポーネントが含まれています。 このプロジェクトではバージョン 4.2 以降の Android をターゲットにしていますが、Mobile Services SDK ではバージョン 2.2 以降があれば問題ありません。

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントがない場合は、Azure 試用版にサインアップして最大 10 の無料モバイル サービスを取得し、試用期間が終わった後でも使用し続けることができます。 詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5)をご覧ください。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい Xamarin.Android アプリを作成する

モバイル サービスを作成したら、Azure クラシック ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリを変更して、モバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Xamarin.Android アプリを作成します。

1.  クリックして、[Azure クラシック ポータル] で、 **Mobile Services**, 、し、先ほど作成したモバイル サービスをクリックします。

2. クイック スタート] タブをクリックして **Xamarin.Android** [ **プラットフォームの選択]** 展開 **新しい Android アプリを作成**します。

    ![][6]

    これにより、モバイル サービスに接続された Xamarin.Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。

    ![][7]

3. クリックして **作成 TodoItem テーブル** アプリケーション データを格納するテーブルを作成します。

4.  **をダウンロードしてアプリケーションを実行する**, をクリックして **ダウンロード**します。

    これにより、サンプルのプロジェクトがダウンロード _作業の一覧に_ 、モバイル サービスに接続されているアプリケーションです。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Android アプリを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開します。

2. Xamarin Studio または Visual Studio では、次のようにクリックします。 **ファイル** し、 **を開く**, 、サンプル ファイルに移動して、選択、 **XamarinTodoQuickStart.Android.sln** を開きます。

3. キーを押して、 **実行** 、プロジェクトをビルドし、アプリケーションを開始] ボタンをクリックします。 エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。

    > [AZURE.NOTE] プロジェクトを Android エミュレーターで実行できるようにするには、少なくとも 1 つ Android Virtual Device (AVD) を定義する必要があります。 これらのデバイスを作成および管理するには、AVD Manager を使用します。

4. アプリケーションで、意味のあるテキストをなど入力 _に関するチュートリアルを完了_, 、] をクリックし、 **追加**します。

    ![][10]

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

    > [AZURE.NOTE]
    > モバイル サービスにアクセスして ToDoActivity.cs C# ファイルにあるデータをクエリおよび挿入するコードを確認できます。

6. ポータルに戻り、[Azure クラシック] をクリックして、 **データ** ] タブでをクリックし、 **TodoItems** テーブルです。

    ![][11]

    これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

    ![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* [オフライン データの同期の開始を取得]
  クイック スタートでオフライン データの同期を使用して、アプリケーションの応答性と信頼性を高める方法について説明します。

* [認証の使用の開始を取得]
  ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [開始プッシュ通知の使用]
  アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法](partner-xamarin-mobile-services-how-to-use-client-library.md)
   モバイル サービスのクエリ、データの処理、およびカスタム Api にアクセスする方法について説明します。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with offline data sync]: mobile-services-xamarin-android-get-started-offline-data.md
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Azure classic portal]: https://manage.windowsazure.com/


