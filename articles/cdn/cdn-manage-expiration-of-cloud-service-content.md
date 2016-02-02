<properties 
 pageTitle="Azure Content Delivery Network (CDN) でクラウド サービスのコンテンツの有効期限を管理する方法" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="camsoper" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/02/2015" 
 ms.author="casoper"/>


# Azure Content Delivery Network (CDN) でクラウド サービスのコンテンツの有効期限を管理する方法

有効期限 (TTL) の期間中に頻繁にアクセスされるオブジェクトでは、Azure CDN のキャッシュを最大限に活用できます。 オブジェクトは、TTL の期間中キャッシュ内に保持され、その期間が経過した後、クラウド サービスによって更新されます。 このプロセスが繰り返されます。

キャッシュの値を指定しない場合は、オブジェクトの TTL は 7 日間です。

画像やスタイル シートなどの静的なコンテンツの場合、コンテンツが含まれる CDN フォルダーに web.config を含め、**clientCache** 設定を修正してコンテンツの Cache-Control ヘッダーを制御することによって、更新頻度を制御することができます。 web.config の設定は、フォルダーとすべてのサブフォルダー内にあるすべてのものに影響を与えます。ただし、それらのフォルダーの下位にある別のサブフォルダーでオーバーライドが行われていない場合に限ります。 たとえば、静的なコンテンツはすべて 3 日間キャッシュされるが、可変コンテンツが含まれているサブフォルダーではキャッシュの設定が 6 時間になるように、ルートに対して既定の有効期限を設定できます。

次の XML は、3 日間の最大有効期間を指定するように **clientCache** を設定する例を示します。

    <configuration> 
      <system.webServer> 
            <staticContent> 
                <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
            </staticContent> 
      </system.webServer> 
    </configuration>

指定する **UseMaxAge** キャッシュ制御を追加します。 最大継続期間 =<nnn> で指定された値に基づいて応答ヘッダー、 **CacheControlMaxAge** 属性です。期間の形式は、 **cacheControlMaxAge** 属性は <days>.<hours>:<min>:<sec>します。詳細については、 **clientCache** ] ノードを参照してください [クライアント キャッシュ <clientCache>] (http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。

.aspx ページなどのアプリケーションから返されるコンテンツの場合、**HttpResponse.Cache** プロパティを設定して CDN のキャッシュ動作をプログラムで設定できます。 詳細については、 **HttpResponse.Cache** プロパティを参照してください [HttpResponse.Cache プロパティ](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) と [HttpCachePolicy クラス](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)します。

アプリケーション コンテンツをプログラムでキャッシュする場合は、HttpCacheability を *Public* に設定してコンテンツをキャッシュ可能としてマークします。 また、キャッシュ検証が設定されていることを確認します。 キャッシュ検証では、SetLastModified を呼び出して最終更新のタイムスタンプを設定することや、SetETag を呼び出して etag 値を設定することができます。 必要に応じて、SetExpires を呼び出してキャッシュの有効期限を指定できます。また、このドキュメントで既に説明した既定のキャッシュのヒューリスティックに依存することもできます。

たとえば、1 時間のコンテンツをキャッシュするには、次を追加します。

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

## 関連項目

[Azure の Content Delivery Network (CDN) で Blob コンテンツの有効期限を管理する方法](./cdn-manage-expiration-of-blob-content.md
)




