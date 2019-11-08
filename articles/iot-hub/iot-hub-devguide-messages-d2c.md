---
title: Informacje o usłudze Azure IoT Hub Routing komunikatów | Microsoft Docs
description: Przewodnik dla deweloperów — jak wysyłać komunikaty z urządzenia do chmury przy użyciu routingu komunikatów. Zawiera informacje o wysyłaniu danych telemetrycznych i niemetrycznych.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: ff50d972ad9590fb70dbcf67e21f8b5dc8c32fad
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748056"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia wysyłanie komunikatów z urządzeń do usług w chmurze w sposób zautomatyzowany, skalowalny i niezawodny. Routingu wiadomości można używać w programie: 

* **Wysyłanie komunikatów telemetrycznych urządzenia** oraz zdarzeń, a w tym zdarzeń związanych z cyklem życia urządzenia oraz zdarzeń zmiany obszaru urządzenia do wbudowanego punktu końcowego i niestandardowych punktów końcowych. Dowiedz się więcej o [punktach końcowych routingu](#routing-endpoints).

* **Filtrowanie danych przed ich kierowaniem do różnych punktów końcowych** przez zastosowanie rozbudowanych zapytań. Routing komunikatów umożliwia wykonywanie zapytań dotyczących właściwości komunikatów i treści wiadomości, a także znaczników sznurka urządzenia i właściwości sznurów urządzenia. Dowiedz się więcej o używaniu [zapytań w routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

IoT Hub potrzebuje dostępu do zapisu w tych punktach końcowych usługi, aby Routing komunikatów działał. W przypadku skonfigurowania punktów końcowych za pomocą Azure Portal są dla Ciebie dodawane odpowiednie uprawnienia. Upewnij się, że usługi zostały skonfigurowane do obsługi oczekiwanej przepływności. Na przykład jeśli używasz Event Hubs jako niestandardowego punktu końcowego, musisz skonfigurować **jednostki przepływności** dla tego centrum zdarzeń, aby mogły obsługiwać ruch przychodzący zdarzeń, które planujesz wysyłać za pośrednictwem IoT Hub Routing komunikatów. Podobnie w przypadku używania kolejki Service Bus jako punktu końcowego należy skonfigurować **Maksymalny rozmiar** , aby zapewnić, że kolejka może przechowywać wszystkie ingressed danych, dopóki nie zostanie egressed przez konsumentów. Podczas pierwszej konfiguracji rozwiązania IoT może być konieczne monitorowanie dodatkowych punktów końcowych i wprowadzenie wszelkich niezbędnych zmian w rzeczywistym obciążeniu.

IoT Hub definiuje [typowy format](iot-hub-devguide-messages-construct.md) dla wszystkich komunikatów przesyłanych z urządzenia do chmury w celu współdziałania z protokołami. Jeśli komunikat jest zgodny z wieloma trasami, które wskazują ten sam punkt końcowy, IoT Hub dostarcza komunikat do tego punktu końcowego tylko raz. W związku z tym nie trzeba konfigurować deduplikacji w kolejce Service Bus lub tematu. W przypadku kolejek partycjonowanych koligacja partycji gwarantuje porządkowanie komunikatów. Skorzystaj z tego samouczka, aby dowiedzieć się, jak [skonfigurować Routing komunikatów](tutorial-routing.md).

## <a name="routing-endpoints"></a>Punkty końcowe routingu

Centrum IoT Hub ma domyślny wbudowany punkt końcowy (**komunikaty/zdarzenia**), który jest zgodny z Event Hubs. Można utworzyć [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) , do których będą kierowane komunikaty, łącząc inne usługi w ramach subskrypcji z IoT Hub. 

Każdy komunikat jest kierowany do wszystkich punktów końcowych, których zapytania routingu są zgodne. Innymi słowy, komunikat można skierować do wielu punktów końcowych.

IoT Hub obecnie obsługuje następujące usługi jako niestandardowe punkty końcowe:

### <a name="built-in-endpoint"></a>Wbudowany punkt końcowy

Możesz użyć standardowej [integracji i zestawów sdk Event Hubs,](iot-hub-devguide-messages-read-builtin.md) aby odbierać komunikaty z urządzenia do chmury z wbudowanego punktu końcowego (**komunikaty/zdarzenia**). Po utworzeniu trasy dane przestają przepływać do wbudowanego punktu końcowego, o ile nie zostanie utworzona trasa do tego punktu końcowego.

### <a name="azure-storage"></a>Azure Storage

Istnieją dwa usługi magazynu IoT Hub mogą kierować wiadomości do kont [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) i [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). Konta Azure Data Lake Storage to [hierarchiczne](../storage/blobs/data-lake-storage-namespace.md)konta magazynu z obsługą nazw oparte na usłudze BLOB Storage. Oba te elementy używają obiektów BLOB dla ich magazynu.

IoT Hub obsługuje zapisywanie danych w usłudze Azure Storage w formacie [Apache Avro](https://avro.apache.org/) oraz w formacie JSON. Wartość domyślna to AVRO. Format kodowania można ustawić tylko w przypadku skonfigurowania punktu końcowego magazynu obiektów BLOB. Nie można edytować formatu dla istniejącego punktu końcowego. W przypadku korzystania z kodowania JSON należy ustawić wartość ContentType na **Application/JSON** i ContentEncoding na **UTF-8** we [właściwościach systemu](iot-hub-devguide-routing-query-syntax.md#system-properties)komunikatów. W obu tych wartościach jest rozróżniana wielkość liter. Jeśli kodowanie zawartości nie jest ustawione, IoT Hub będzie zapisywać komunikaty w standardowym formacie 64. Można wybrać format kodowania za pomocą IoT Hub tworzenia lub aktualizacji interfejsu API REST, w tym [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)lub programu [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Na poniższym diagramie przedstawiono sposób wybierania formatu kodowania w Azure Portal.

![Kodowanie punktu końcowego magazynu obiektów BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub partie komunikatów i zapisuje dane w magazynie, gdy partia osiągnie określony rozmiar lub upłynął określony czas. IoT Hub domyślne do następującej konwencji nazewnictwa plików: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Licencjobiorca może korzystać z dowolnej konwencji nazewnictwa plików, jednak należy używać wszystkich wymienionych tokenów. IoT Hub będzie zapisywać do pustego obiektu BLOB, jeśli nie ma danych do zapisania.

Zalecamy utworzenie listy obiektów blob lub plików, a następnie ich iterację, aby upewnić się, że wszystkie obiekty blob lub pliki są odczytywane bez założeń partycji. Zakres partycji może ulec zmianie podczas pracy w [trybie failover zainicjowanej przez firmę Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) lub IoT Hub [ręcznej pracy awaryjnej](iot-hub-ha-dr.md#manual-failover). Korzystając z [interfejsu API listy obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) , można wyliczyć listę obiektów blob lub [listę interfejsów API ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) dla listy plików. Zapoznaj się z poniższym przykładem.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Aby utworzyć konto magazynu zgodne z usługą Azure Data Lake Gen2, Utwórz nowe konto magazynu w wersji 2 i wybierz pozycję *włączone* w polu *hierarchiczna przestrzeń nazw* na karcie **Zaawansowane** , jak pokazano na poniższej ilustracji:

![Wybierz pozycję Azure Data Lake Gen2 Storage](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus kolejki i Service Bus tematy

Kolejki Service Bus i tematy używane jako punkty końcowe IoT Hub nie mogą mieć włączonej **sesji** lub **wykrywania duplikatów** . Jeśli jedna z tych opcji jest włączona, punkt końcowy jest wyświetlany jako **nieosiągalny** w Azure Portal.

### <a name="event-hubs"></a>Usługa Event Hubs

Oprócz wbudowanego punktu końcowego zgodnego z Event Hubs można również kierować dane do niestandardowych punktów końcowych typu Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Odczytywanie danych, które zostały rozesłane

Trasę można skonfigurować, wykonując czynności opisane w tym [samouczku](tutorial-routing.md).

Skorzystaj z poniższych samouczków, aby dowiedzieć się, jak odczytać komunikat z punktu końcowego.

* Odczytywanie z [wbudowanego punktu końcowego](quickstart-send-telemetry-node.md)

* Odczytywanie z [magazynu obiektów BLOB](../storage/blobs/storage-blob-event-quickstart.md)

* Odczytywanie z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Odczytywanie z [kolejek Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Przeczytaj w [tematach Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Trasa rezerwowa

Trasa rezerwowa wysyła wszystkie komunikaty, które nie spełniają warunków zapytania na żadnej z istniejących tras do wbudowanych Event Hubs (**komunikaty/zdarzenia**), które są zgodne z [Event Hubs](/azure/event-hubs/). Jeśli jest włączona funkcja routingu wiadomości, można włączyć funkcję trasy rezerwowej. Po utworzeniu trasy dane przestają przepływać do wbudowanego punktu końcowego, chyba że trasa zostanie utworzona do tego punktu końcowego. Jeśli nie ma tras do wbudowanego punktu końcowego, a trasa rezerwowa jest włączona, tylko komunikaty, które nie pasują do żadnych warunków zapytania dotyczące tras, będą wysyłane do wbudowanego punktu końcowego. Ponadto jeśli wszystkie istniejące trasy zostaną usunięte, należy włączyć trasy rezerwowe, aby odbierać wszystkie dane w wbudowanym punkcie końcowym.

Można włączyć/wyłączyć trasę alternatywną w bloku routing wiadomości Azure Portal->. Można również użyć Azure Resource Manager do [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) , aby użyć niestandardowego punktu końcowego dla trasy rezerwowej.

## <a name="non-telemetry-events"></a>Zdarzenia inne niż telemetrii

Oprócz danych telemetrycznych urządzenia Routing komunikatów umożliwia również wysyłanie zdarzeń zmiany sznurka urządzenia, zdarzeń cyklu życia urządzenia i cyfrowych dróg zmiany (w publicznej wersji zapoznawczej). Na przykład jeśli zostanie utworzona trasa ze źródłem danych ustawionym na **zdarzenia zmiany sznurka urządzenia**, IoT Hub wysyła komunikaty do punktu końcowego, który zawiera zmiany w bliźniaczych urządzeniach. Podobnie w przypadku utworzenia trasy ze źródłem danych ustawionym na **zdarzenia cyklu życia urządzenia**IoT Hub wysyła komunikat informujący o tym, czy urządzenie zostało usunięte lub utworzone. Na koniec w ramach [publicznej wersji zapoznawczej IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)deweloper może utworzyć trasy ze źródłem danych ustawionym na **cyfrowe sznurki zmiany** i IoT Hub wysyłać komunikaty za każdym razem, gdy [Właściwość](../iot-pnp/iot-plug-and-play-glossary.md) dwuosiowa jest ustawiona lub zmieniana, [dwuosiowa cyfra ](../iot-pnp/iot-plug-and-play-glossary.md)jest zastępowana lub gdy występuje zdarzenie zmiany w odniesieniu do podstawowego sznurka urządzenia.

[IoT Hub integruje się również z Azure Event Grid](iot-hub-event-grid.md) , aby publikować zdarzenia urządzeń w celu obsługi integracji w czasie rzeczywistym i automatyzacji przepływów pracy na podstawie tych zdarzeń. Zobacz podstawowe [różnice między routingiem komunikatów i Event Grid](iot-hub-event-grid-routing-comparison.md) , aby dowiedzieć się, co najlepiej sprawdza się w przypadku danego scenariusza.

## <a name="testing-routes"></a>Testowanie tras

Podczas tworzenia nowej trasy lub edytowania istniejącej trasy należy przetestować zapytanie trasy z przykładowym komunikatem. Możesz testować poszczególne trasy lub testować wszystkie trasy jednocześnie, a żadne komunikaty nie są kierowane do punktów końcowych podczas testu. Do testowania można używać Azure Portal, Azure Resource Manager, Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Wyniki ułatwiają określenie, czy przykładowy komunikat pasuje do zapytania, komunikat niezgodny z zapytaniem lub test nie mógł zostać uruchomiony z powodu niepoprawnej składni przykładowego komunikatu lub zapytania. Aby dowiedzieć się więcej, zobacz [testowanie trasy](/rest/api/iothub/iothubresource/testroute) i [testowanie wszystkich tras](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Określanie kolejności gwarancji z co najmniej raz na dostarczenie

IoT Hub Routing komunikatów gwarantuje uporządkowaną i co najmniej raz dostarczenie komunikatów do punktów końcowych. Oznacza to, że mogą istnieć zduplikowane komunikaty, a serie komunikatów mogą być ponownie przesyłane z zastosowaniem oryginalnej kolejności wiadomości. Na przykład jeśli oryginalna kolejność wiadomości to [1, 2, 3, 4], można otrzymać sekwencję komunikatów podobną do [1, 2, 1, 2, 3, 1, 2, 3, 4]. Gwarancja porządkowania polega na tym, że jeśli kiedykolwiek otrzymasz komunikat [1], zawsze będzie miało [2, 3, 4].

W celu obsługi duplikatów komunikatów Zalecamy umieszczenie unikatowego identyfikatora we właściwościach aplikacji komunikatu w punkcie pochodzenia, który jest zwykle urządzeniem lub modułem. Usługa korzystająca z komunikatów może obsługiwać duplikaty komunikatów przy użyciu tego identyfikatora.

## <a name="latency"></a>Opóźnienie

W przypadku przesyłania komunikatów telemetrycznych z urządzenia do chmury przy użyciu wbudowanych punktów końcowych istnieje niewielkie zwiększenie opóźnienia po utworzeniu pierwszej trasy.

W większości przypadków średni wzrost opóźnienia jest mniejszy niż 500 ms. Opóźnienie można monitorować za pomocą **routingu: opóźnienie komunikatów dla komunikatów/zdarzeń** lub **D2C. endpoints. opóźnienie. wbudowane. Events** IoT Hub metryki. Tworzenie lub usuwanie dowolnej trasy po pierwszej stronie nie ma wpływu na opóźnienie końca.

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

IoT Hub oferuje kilka metryk związanych z routingiem i punktami końcowymi, aby zapewnić przegląd kondycji wysyłanych centrów i komunikatów. Możesz połączyć informacje z wielu metryk, aby zidentyfikować główną przyczynę problemów. Na przykład użyj routingu metryk **: porzucone komunikaty** telemetryczne lub **D2C. telemetrię** . ruch wychodzący. upuszczone, aby określić liczbę komunikatów, które zostały porzucone, gdy nie pasują do zapytania na żadnej trasie, a trasa rezerwowa została wyłączona. [Metryki IoT Hub](iot-hub-metrics.md) wyświetlają wszystkie metryki, które są domyślnie włączone dla IoT Hub.

Aby uzyskać [stan kondycji](iot-hub-devguide-endpoints.md#custom-endpoints) punktów końcowych, można użyć interfejsu API REST [Uzyskaj kondycję punktu końcowego](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Zalecamy używanie [metryk IoT Hub](iot-hub-metrics.md) związanych z opóźnieniem komunikatów routingu w celu identyfikowania i debugowania błędów, gdy kondycja punktu końcowego jest martwa lub zła. Na przykład dla typu punktu końcowego Event Hubs można monitorować **D2C. endpoints. Opóźnij. eventHubs**. Stan punktu końcowego w złej kondycji zostanie zaktualizowany w dobrej kondycji, gdy IoT Hub ustanowił ostatecznie spójny stan kondycji.

Korzystając z dzienników diagnostycznych **trasy** w Azure monitor [ustawień diagnostycznych](../iot-hub/iot-hub-monitor-resource-health.md), można śledzić błędy występujące podczas obliczania zapytania routingu i kondycji punktu końcowego jako postrzegane przez IoT Hub, na przykład wtedy, gdy punkt końcowy jest martwy. Te dzienniki diagnostyczne mogą być wysyłane do dzienników Azure Monitor, Event Hubs lub Azure Storage na potrzeby przetwarzania niestandardowego.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak tworzyć trasy komunikatów, zobacz [proces IoT Hub komunikatów z urządzenia do chmury przy użyciu tras](tutorial-routing.md).

* [Jak wysyłać komunikaty z urządzenia do chmury](quickstart-send-telemetry-node.md)

* Aby uzyskać informacje o zestawach SDK, których można użyć do wysyłania komunikatów z urządzenia do chmury, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).
