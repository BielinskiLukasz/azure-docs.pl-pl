---
title: Monitorowanie aplikacji internetowej ASP.NET za pomocą usługi Azure Application Insights | Microsoft Docs
description: Przedstawiono instrukcje pozwalające szybko skonfigurować aplikację sieci Web ASP.NET do monitorowania za pomocą usługi Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/01/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a43ad92181415593b309b9fafb20f9934a997924
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805350"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Rozpoczynanie monitorowania aplikacji internetowej ASP.NET

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej.  Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika.  Dzięki uzyskiwanym za pomocą usługi Application Insights informacjom o wydajności i efektywności aplikacji można dokonywać świadomych wyborów dotyczących konserwacji i udoskonalania aplikacji.

W tym przewodniku Szybki start pokazano, jak dodać usługę Application Insights do istniejącej aplikacji internetowej ASP.NET i rozpocząć korzystanie z jednej z wielu metod analizowania aplikacji — badania statystyk udostępnianych na żywo. Jeśli nie masz aplikacji internetowej ASP.NET, można utworzyć, wykonując [Szybki Start aplikacja sieci Web ASP.NET tworzenie](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start:

- Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

1. Otwórz projekt w programie Visual Studio 2017.
2. Wybierz pozycję **Konfiguruj usługę Application Insights** z menu Projekt. Program Visual Studio doda zestaw SDK usługi Application Insights do aplikacji.

    > [!IMPORTANT]
    > Proces dodawania usługi Application Insights różni się w zależności od typu szablonu platformy ASP.NET. Jeśli używasz szablonu **Pusty** lub **Aplikacja mobilna platformy Azure**, wybierz pozycję **Projekt** > **Dodaj Telemetrię usługi Application Insights**. W przypadku wszystkich pozostałych szablonów platformy ASP.NET zapoznaj się z instrukcjami w powyższym kroku. 

3. Kliknij przycisk **Rozpocznij pracę** (w starszych wersjach programu Visual Studio zamiast tego przycisku znajduje się przycisk **Rozpocznij bezpłatnie**).

    ![Dodawanie usługi Application Insights do programu Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Wybierz swoją subskrypcję i kliknij przycisk **Zarejestruj**.

5. Uruchom aplikację, wybierając pozycję **Rozpocznij debugowanie** z menu **Debuguj** lub naciskając klawisz F5.

## <a name="confirm-app-configuration"></a>Potwierdzanie konfiguracji aplikacji

Usługa Application Insights zbiera dane telemetryczne aplikacji niezależnie od tego, gdzie jest ona uruchomiona. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Otwórz usługę Application Insights, klikając pozycję **Widok** -> **Inne okna** -> **Wyszukiwanie usługi Application Insights**.  Pojawią się dane telemetryczne z bieżącej sesji.<BR><br>![Dane telemetryczne w programie Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Kliknij pierwsze żądanie na liście (w tym przykładzie GET Home/Index), aby wyświetlić jego szczegóły. Zwróć uwagę, że są widoczne kod stanu i czas odpowiedzi, a także inne cenne informacje dotyczące żądania.<br><br>![Szczegóły odpowiedzi w programie Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

Możesz teraz otworzyć usługę Application Insights w witrynie Azure Portal i wyświetlić różne szczegółowe informacje o uruchomionej aplikacji.

1. Rozwiń **podłączone usługi** folder (ikona chmury i plug-in) w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **usługi Application Insights** folder i kliknij przycisk **Otwórz Portal usługi Application Insights** .  Pojawią się wybrane informacje o aplikacji i różne opcje.

    ![Mapa aplikacji](media/quick-monitor-portal/4overview.png)

2. Kliknij pozycję **Mapa aplikacji**, aby uzyskać wizualny układ relacji zależności między składnikami aplikacji.  Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

    ![Mapa aplikacji](media/quick-monitor-portal/5appmap.png)

3. Kliknij pozycję **analizy aplikacji** ikonę ![mapy aplikacji](media/quick-monitor-portal/app-analytics-icon.png) **Wyświetl w obszarze analiza** na jeden ze składników aplikacji. Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights.  W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

    ![Analiza](media/quick-monitor-portal/6viewanalytics.png)

4. Kliknij pozycję **Live Stream metryki** po lewej stronie w obszarze badania. Spowoduje to wyświetlenie danych statystycznych dotyczących uruchomionej aplikacji. Dane te obejmują informacje dotyczące liczby żądań przychodzących, czasu trwania tych żądań i błędów. Można też sprawdzić krytyczne metryki wydajności, takie jak wydajność procesora i pamięci.

    ![Transmisja strumieniowa na żywo](media/quick-monitor-portal/7livemetrics.png)

    Jeśli wszystko jest gotowe do hostowania aplikacji na platformie Azure, można opublikować aplikację. Wykonaj instrukcje opisane w artykule [Create an ASP.NET Web App Quickstart (Przewodnik Szybki Start dotyczący tworzenia aplikacji internetowej ASP.NET)](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

5. Jeśli dodajesz monitorowanie w usłudze Application Insights za pomocą programu Visual Studio, możesz automatycznie dodać monitorowanie po stronie klienta. Aby ręcznie dodać do aplikacji monitorowanie po stronie klienta, dodaj do niej następujący kod JavaScript:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Aby dowiedzieć się więcej, odwiedź repozytorium GitHub [naszego zestawu SDK typu open-source dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="video"></a>Połączenia wideo

* Zewnętrzne wideo krok po kroku dotyczące [Konfigurowanie usługi Application Insights za pomocą aplikacji platformy .NET od podstaw](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu testowania, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby. Można więc wykonaj poniższe kroki.
1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start zostało włączone, aplikacja do monitorowania usługi Azure Application Insights.  Przejdź do kolejnych samouczków, aby dowiedzieć się, jak użyć tej usługi do monitorowania statystyk i wykrywania problemów w aplikacji.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Application Insights](tutorial-runtime-exceptions.md)
