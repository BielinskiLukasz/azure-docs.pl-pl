<properties
    pageTitle="Python で Azure Redis Cache を使用する方法 | Microsoft Azure"
    description="Python を使用して Azure Redis Cache を使用します"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/03/2015"
    ms.author="sdanie"/>


# Python で Azure Redis Cache を使用する方法

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)


このトピックでは、Python を使用して Azure Redis Cache を使用する方法を説明しています。


## 前提条件

Install [redis-py](https://github.com/andymccurdy/redis-py).


## Azure で Redis Cache を作成する

[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=398536), 、クリックして **新規**, 、**データ + ストレージ**, を選択し、 **Redis Cache**します。

  ![][1]

DNS ホスト名を入力します。フォームが '<name>
  。.redis.cache.windows.net という ' です。 **[作成]** をクリックします。

  ![][2]

  作成すると、キャッシュ [参照する](cache-configure.md#configure-redis-cache-settings) キャッシュ設定を表示します。 次が必要です。

  - **ホスト名。**キャッシュを作成したときに入力した名前です。
  - **ポート。****[ポート]** の下のリンクをクリックしてポートを表示します。 SSL ポートを使用します。
  - **アクセス キー。****[キー]** の下のリンクをクリックして、プライマリ キーをコピーします。

  ## キャッシュに何かを追加して取得する

  >>> redis をインポートします。
  >>> r = redis です。StrictRedis (ホスト ='<name>..redis.cache.windows.net という '、
  >>>        ポート 6380、db の = = 0、パスワード ='<key>'、ssl = True)
  >>> r.set ('foo'、'バー')
  >>>  True
  >>> r.get('foo')
  >>>  b'bar'

置換 *< 名前 >* はキャッシュ名と *< キー >* をアクセス キー。




[1]: ./media/cache-python-get-started/cache01.png 
[2]: ./media/cache-python-get-started/cache02.png 

