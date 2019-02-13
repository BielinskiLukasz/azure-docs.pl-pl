---
title: Tworzenie własnego rozwiązania odzyskiwania po awarii dla tematów niestandardowych w usłudze Azure Event Grid | Microsoft Docs
description: Zadbaj o odporność na regionalne przestoje, aby zapewnić stałą łączność usługi Azure Event Grid.
services: event-grid
author: banisadr
ms.service: even-grid
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: babanisa
ms.openlocfilehash: d1cf09df2db3abe65d7c3c048438e6791129940a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701438"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Tworzenie własnego rozwiązania odzyskiwania po awarii dla tematów niestandardowych

Odzyskiwanie po awarii powinno być skoncentrowane na odzyskiwaniu po poważnej utracie funkcjonalności aplikacji. Ten samouczek przeprowadzi Cię przez konfigurowanie architektury obsługi zdarzeń pod kątem odzyskiwania w przypadku pogorszenia kondycji usługi Event Grid w konkretnym regionie.

Z tego samouczka dowiesz się, jak utworzyć aktywną/pasywną architekturę trybu failover dla tematów niestandardowych w usłudze Event Grid. Przygotujesz tryb failover przez dublowanie tematów i subskrypcji w dwóch regionach, a następnie zarządzanie tym trybem w przypadku pogorszenia się kondycji tematu. Architektura przedstawiona w tym samouczku realizuje tryb failover dla całego nowego ruchu. Należy pamiętać, że w przypadku tej konfiguracji nie można odzyskać zdarzeń już przetwarzanych do czasu przywrócenia dobrej kondycji problemowego regionu.

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Aby przetestować konfigurację trybu failover, potrzebny jest punkt końcowy do odbierania zdarzeń. Ten punkt końcowy nie jest częścią infrastruktury trybu failover, ale pełni rolę programu obsługi zdarzeń, aby ułatwić testowanie.

Aby uprościć testowanie, wdrożysz [wstępnie zbudowaną aplikację internetową](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`
Zapisz ten adres URL, ponieważ będzie on potrzebny później.

1. Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

   ![Wyświetlanie nowej witryny](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Tworzenie tematów podstawowych i pomocniczych

Najpierw utwórz dwa tematy usługi Event Grid. Będą one pełnić role tematów podstawowego i pomocniczego. Domyślnie zdarzenia będą przepływać przez temat podstawowy. Jeśli występuje przerwa w działaniu usługi w regionie podstawowym, obsługę przejmie rozwiązanie pomocnicze.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. W lewym górnym rogu głównego menu platformy Azure wybierz pozycję **Wszystkie usługi** > wyszukaj pozycję **Event Grid** > wybierz pozycję **Tematy usługi Event Grid**.

   ![Menu Tematy usługi Event Grid](./media/custom-disaster-recovery/select-topics-menu.png)

    Wybierz gwiazdkę obok pozycji Tematy usługi Event Grid, aby dodać ją do menu zasobu w celu ułatwienia dostępu do niej w przyszłości.

1. W menu Tematy usługi Event Grid wybierz pozycję **+DODAJ** w celu utworzenia tematu podstawowego.

    * Nadaj tematowi nazwę logiczną i dodaj do niej jako sufiks „-primary” („podstawowy”), aby ułatwić śledzenie go.
    * Region tego tematu będzie regionem podstawowym.

    ![Okno dialogowe tworzenia tematu podstawowego usługi Event Grid](./media/custom-disaster-recovery/create-primary-topic.png)

1. Po utworzeniu tematu przejdź do **punktu końcowego tematu** i skopiuj go. Ten identyfikator URI będzie potrzebny później.

    ![Temat podstawowy usługi Event Grid](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. Pobierz klucz dostępu dla tego tematu. On również będzie potrzebny później. Kliknij pozycję **Klucze dostępu** w menu zasobu i skopiuj pozycję Klucz 1.

    ![Uzyskiwanie klucza tematu podstawowego](./media/custom-disaster-recovery/get-primary-access-key.png)

1. W bloku tematu kliknij pozycję **+ Subskrypcja zdarzeń**, aby utworzyć subskrypcję łączącą witrynę internetową odbiorcy zdarzeń utworzoną w ramach czynności wstępnie wymaganych przez ten samouczek.

    * Nadaj subskrypcji zdarzeń nazwę logiczną i dodaj do niej jako sufiks „-primary” („podstawowy”), aby ułatwić jej śledzenie.
    * Jako typ punktu końcowego wybierz element webhook.
    * Jako punkt końcowy ustaw adres URL odbiorcy zdarzeń. Powinno to wyglądać mniej więcej tak: `https://<your-event-reciever>.azurewebsites.net/api/updates`

    ![Podstawowa subskrypcja zdarzeń usługi Event Grid](./media/custom-disaster-recovery/create-primary-es.png)

1. Powtórz te czynności w celu utworzenia pomocniczych tematu i subskrypcji. W tym przypadku zamiast sufiksu „-primary” („podstawowy”) użyj sufiksu „-secondary” („pomocniczy”), aby ułatwić śledzenie. Na koniec koniecznie umieść te elementy w innym regionie platformy Azure. Chociaż możesz umieścić je, gdziekolwiek zechcesz, zaleca się użycie [regionów sparowanych platformy Azure](../best-practices-availability-paired-regions.md). Umieszczenie tematu i subskrypcji pomocniczej w innym regionie gwarantuje, że nowe zdarzenia będą przepływać nawet wtedy, gdy region podstawowy ulegnie awarii.

Powinny teraz być gotowe:

   * Witryna internetowa odbiorcy zdarzeń do testowania.
   * Temat podstawowy w regionie podstawowym.
   * Podstawową subskrypcję zdarzeń łączącą temat podstawowy z witryną internetową odbiorcy zdarzeń.
   * Temat pomocniczy w regionie pomocniczym.
   * Pomocniczą subskrypcję zdarzeń łączącą temat podstawowy z witryną internetową odbiorcy zdarzeń.

## <a name="implement-client-side-failover"></a>Implementowanie trybu failover po stronie klienta

Są już gotowe regionalnie nadmiarowe para tematów i konfiguracja subskrypcji. Czas zaimplementować tryb failover po stronie klienta. Można to zrobić na kilka sposobów, jednak wszystkie implementacje trybu failover będą mieć pewną cechę wspólną: gdy kondycja jednego z tematów się pogorszy, ruch zostanie przekierowany do drugiego.

### <a name="basic-client-side-implementation"></a>Podstawowa implementacja po stronie klienta

Poniższy kod przykładowy to prosty wydawca .Net, który zawsze będzie próbować opublikować najpierw temat podstawowy. Jeśli to się nie powiedzie, w ramach trybu failover przejdzie do tematu pomocniczego. W obu przypadkach sprawdza również interfejs API kondycji drugiego tematu, wykonując żądanie GET dla `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health`. Temat w dobrej kondycji na żądanie GET w punkcie końcowym **/api/health** powinien zawsze odpowiedzieć **200 OK**.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Testowanie

Wszystkie składniki są już gotowe, możesz więc przetestować tę implementację trybu failover. Uruchom powyższy przykład w programie Visual Studio Code lub w swoim ulubionym środowisku. Zamień następujące cztery wartości na punkty końcowe i klucze z używanych tematów:

   * primaryTopic — punkt końcowy dla tematu podstawowego.
   * secondaryTopic — punkt końcowy dla tematu pomocniczego.
   * primaryTopicKey — klucz tematu podstawowego.
   * secondaryTopicKey — klucz tematu pomocniczego.

Spróbuj uruchomić wydawcę zdarzeń. W przeglądarce usługi Event Grid powinny pojawić się zdarzenia testowe, jak pokazano poniżej.

![Podstawowa subskrypcja zdarzeń usługi Event Grid](./media/custom-disaster-recovery/event-grid-viewer.png)

Aby upewnić się, że tryb failover działa, możesz zmienić kilka znaków w kluczu tematu podstawowego, co sprawi, że stanie się nieprawidłowy. Spróbuj ponownie uruchomić wydawcę. W przeglądarce usługi Event Grid nadal powinny pojawiać się nowe zdarzenia, jednak w konsoli powinny być widoczne informacje, że są one teraz publikowane za pomocą tematu pomocniczego.

### <a name="possible-extensions"></a>Możliwe rozszerzenia

Istnieje wiele sposobów, w jakie można rozszerzyć ten przykład stosownie do potrzeb. W przypadku scenariuszy z dużą liczbą zdarzeń warto zaimplementować niezależne regularne sprawdzanie interfejsu API kondycji tematu. Dzięki temu w razie przestoju tematu nie trzeba sprawdzać go przy każdej pojedynczej publikacji. Po ustaleniu, że temat nie jest w dobrej kondycji, można przestawić domyślne publikowanie na temat pomocniczy.

Podobnie można zaimplementować logikę powrotu po awarii — w zależności od określonych potrzeb. Jeśli publikowanie w najbliższym centrum danych jest bardzo ważne ze względu na zmniejszenie opóźnień, można okresowo badać interfejs API kondycji tematu, dla którego włączono tryb failover. Gdy jego dobra kondycja zostanie przywrócona, będzie wiadomo, że można bezpiecznie powrócić po awarii do bliższego centrum danych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [odbierać zdarzenia w punkcie końcowym http](./receive-events.md)
- Dowiedz się, jak [kierować zdarzenia do połączeń hybrydowych](./custom-event-to-hybrid-connection.md)
- Dowiedz się więcej na temat [odzyskiwania po awarii przy użyciu usług Azure DNS i Traffic Manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)