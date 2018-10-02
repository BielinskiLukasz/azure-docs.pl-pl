---
title: Poznanie routingu komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — jak wysyłać komunikaty urządzenie chmura z za pomocą routingu komunikatów. Zawiera informacje na temat wysyłania danych telemetrycznych i danych telemetrycznych innych.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: a4ebb64d14b325680a65ffd52f6a07836c1e6ede
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585683"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Umożliwia routing komunikatów wysyłać komunikaty urządzenie chmura do różnych punktów końcowych

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Routing komunikatów umożliwia wysyłanie komunikatów z urządzenia do usług w chmurze w sposób zautomatyzowanych, skalowalnych i niezawodnych. Routing komunikatów może służyć do: 

* **Wysyłanie messsages telemetrii urządzenia, a także zdarzenia** , zdarzenia cyklu życia urządzenia i zdarzenia zmian do wbudowanych-endpoint i niestandardowych punktów końcowych bliźniaczej reprezentacji urządzenia. Dowiedz się więcej o [routingu punktów końcowych](##routing-endpoints).

* **Filtrowanie danych przed przesłaniem go do różnych punktów końcowych** przez zastosowanie zaawansowanych zapytań. Routing komunikatów umożliwia zapytania właściwości wiadomości oraz treść wiadomości, a także tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń. Dowiedz się więcej o korzystaniu z [zapytania w routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

Usługa IoT Hub wymaga dostępu do tych punktów końcowych usługi zapisu do rozsyłania wiadomości do pracy. Jeśli skonfigurujesz punktów końcowych za pośrednictwem witryny Azure portal, niezbędne uprawnienia są dodawane. Upewnij się, że konfigurowanie usługi do obsługi oczekiwanej przepływności. Podczas pierwszej konfiguracji rozwiązania IoT, może być konieczne monitorowanie dodatkowych punktów końcowych i wprowadź wymagane zmiany dotyczące rzeczywistego obciążenia.

Definiuje usługę IoT Hub [typowego formatu](iot-hub-devguide-messages-construct.md) dla wszystkich urządzeń do chmury, obsługi wiadomości dla interoperatbility różnych protokołów. Jeśli komunikat pasuje wiele tras, które wskazują na ten sam punkt końcowy, IoT Hub dostarczy komunikatu do określonego punktu końcowego tylko raz. W związku z tym nie trzeba polecenia Konfiguruj deduplikację na tematu lub kolejki usługi Service Bus. Partycjonowane kolejki koligacji partycji gwarantuje, kolejność komunikatów. Użyj tego samouczka, aby dowiedzieć się, jak [Konfigurowanie routingu komunikatów] (samouczek routing.md).

## <a name="routing-endpoints"></a>Punkty końcowe routingu

Centrum IoT hub ma domyślnego wbudowanych-w-punktu końcowego (**komunikaty/zdarzenia**) jest zgodna z usługą Event Hubs. Możesz utworzyć [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) do wyznaczania tras, łącząc innych usług w Twojej subskrypcji do Centrum IoT. Usługa IoT Hub obecnie obsługuje następujące usługi jako niestandardowe punkty końcowe:

### <a name="built-in-endpoint"></a>Wbudowany punkt końcowy

Można użyć standardowego [integracji usługi Event Hubs i zestawy SDK](iot-hub-devguide-messages-read-builtin.md) do odbierania komunikatów z urządzenia do chmury z wbudowanego punktu końcowego (**komunikaty/zdarzenia**). Należy pamiętać, że po utworzeniu trasy dane przestaną docierać do wbudowanych-w-punktu końcowego, chyba że trasa służy do tego punktu końcowego.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Usługa IoT Hub obsługuje tylko, zapisywanie danych w usłudze Azure Blob Storage w [Apache Avro](http://avro.apache.org/) formatu. Usługa IoT Hub partii komunikatów i zapisuje dane do obiektu blob, zawsze, gdy zadanie wsadowe osiągnie określony rozmiar lub upłynął określoną ilość czasu.

Usługa IoT Hub wartością domyślną jest następująca Konwencja nazewnictwa pliku:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Może używać dowolnego konwencję nazewnictwa plików, jednak należy użyć listy wszystkich tokenów. Usługi IoT Hub będzie zapisywać do pustego obiektu blob, jeśli nie ma żadnych danych do zapisu.

### <a name="service-bus-queues-and-service-bus-topics"></a>Kolejki usługi Service Bus i tematów usługi Service Bus

Kolejki usługi Service Bus i tematy, używane jako punktów końcowych usługi IoT Hub nie może mieć **sesje** lub **wykrywania duplikatów** włączone. Jeśli jedno z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w witrynie Azure portal.

### <a name="event-hubs"></a>Event Hubs

Oprócz wbudowanych — Event Hubs punkt końcowy zgodny można również kierować dane do niestandardowych punktów końcowych typu Event Hubs. 

Gdy używasz routingu, jak i niestandardowe punkty końcowe, wiadomości tylko są dostarczane do wbudowany punkt końcowy, jeśli nie pasują do żadnych reguł. W celu dostarczenia komunikatu do endpoint wbudowane i niestandardowe punkty końcowe, Dodaj trasę, która wysyła komunikaty do endpoint zdarzenia.

## <a name="reading-data-that-has-been-routed"></a>Odczyt danych, który został przesłany

Można skonfigurować trasę, postępując zgodnie z dokumentem [samouczek](tutorial-routing.md).

Użyj następujących samouczków dowiesz się, jak odczytać wiadomość z punktu końcowego.

* Odczytywanie z [wbudowanych-endpoint](quickstart-send-telemetry-node.md)

* Odczytywanie z [magazynu obiektów Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Odczytywanie z [usługi Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Odczytywanie z [kolejki usługi Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Odczytywanie [tematy usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Rezerwowy trasy

Rezerwowy trasa wysyła wszystkie komunikaty, które nie spełniają warunki zapytania na dowolnym istniejące trasy do wbudowanych w — Event Hubs (**komunikaty/zdarzenia**), która jest zgodna z [usługi Event Hubs](/azure/event-hubs/). Routing komunikatów jest włączona, po włączeniu możliwości rezerwowego trasy. Należy pamiętać, że po utworzeniu trasy dane przestaną docierać do wbudowanych-w-punktu końcowego, chyba że zostanie utworzona trasa tego punktu końcowego. Jeśli nie ma żadnych tras do wbudowanych-w-punktu końcowego i rezerwowego trasy jest włączona, tylko komunikaty, które nie są zgodne wszelkie warunki zapytania na trasach, będą wysyłane do wbudowanych-w-punktu końcowego. Również usunięcie wszystkich istniejących tras rezerwowego trasy musi mieć możliwość odbierania wszystkie dane w wbudowanych-w-punkcie końcowym. 

Możesz włączyć/wyłączyć rezerwowego trasy w witrynie Azure Portal -> Routing komunikatów bloku. Można również użyć usługi Azure Resource Manager dla [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) do użycia niestandardowego punktu końcowego dla trasy rezerwowego.

## <a name="non-telemetry-events"></a>Zdarzenia inne niż telemetrii

Oprócz danych telemetrycznych z urządzenia routing komunikatów umożliwia także wysyłanie zdarzeń zmiany bliźniaczych reprezentacji urządzeń i zdarzenia cyklu życia urządzenia. Na przykład, jeśli trasa jest tworzony ze źródłem danych, ustaw **zdarzenia zmiany w bliźniaczej reprezentacji urządzenia**, usługi IoT Hub wysyła wiadomości do punktu końcowego, które zawierają zmiany w bliźniaczej reprezentacji urządzenia. Podobnie jeśli trasa jest tworzony ze źródłem danych, ustaw **zdarzenia cyklu życia urządzenia**, usługi IoT Hub będzie wysyłać komunikat wskazujący, czy urządzenia został usunięty lub utworzony. 

[Usługa IoT Hub integruje się również z usługi Azure Event Grid](iot-hub-event-grid.md) publikowanie zdarzenia urządzenia w celu obsługi integracji w czasie rzeczywistym i automatyzacji przepływów pracy na podstawie tych zdarzeń. Klucz [różnice między routing komunikatów i Event Grid](iot-hub-event-grid-routing-comparison.md) Aby dowiedzieć się, w której działa najlepiej dla danego scenariusza.

## <a name="testing-routes"></a>Testowanie trasy

Podczas tworzenia nowej trasy lub Edytuj istniejącą trasę, należy przetestować zapytania trasy z przykładowy komunikat. Można przetestować indywidualnych tras lub przetestuj wszystkie trasy tylko raz, a żadne komunikaty są kierowane do punktów końcowych podczas testu. Azure Portal, usługi Azure Resource Manager, programu Azure PowerShell i wiersza polecenia platformy Azure może służyć do testowania. Wyniki pomagają ustalić, czy przykładowy komunikat wynik kwerendy, komunikat jest niezgodna z zapytania lub nie przebiegu testu, ponieważ składnia komunikatu lub zapytanie przykładowe są niepoprawne. Aby dowiedzieć się więcej, zobacz [trasy testu](/rest/api/iothub/iothubresource/testroute) i [Testuj wszystkie trasy](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Opóźnienie

Podczas rozsyłania komunikatów telemetrycznych przesyłanych z urządzenia do chmury za pomocą wbudowanych punktach końcowych po utworzeniu pierwszego trasy jest niewielki wzrost opóźnienia end-to-end.

W większości przypadków Średni czas oczekiwania jest mniej niż 500 MS. Można monitorować za pomocą opóźnienie **routingu: komunikat o opóźnieniu komunikaty/zdarzenia** lub **d2c.endpoints.latency.builtIn.events** metryki usługi IoT Hub. Tworzenie lub usuwanie jakiejkolwiek trasy po pierwszy z nich nie ma wpływu na opóźnienie end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

IoT Hub udostępnia kilka routingu i punktu końcowego powiązane metryki, aby Przegląd kondycji Centrum i wysłane wiadomości. Możesz połączyć informacje z wielu metryk, aby odkryć ich główną przyczynę problemów. Na przykład użyj metryki **routingu: porzucone komunikaty telemetryczne** lub **d2c.telemetry.egress.dropped** do identyfikowania liczbę wiadomości, które zostały usunięte, gdy nie odpowiadają one zapytań na żadnej trasy a trasy rezerwowego zostało wyłączone. [Metryki usługi IoT Hub](iot-hub-metrics.md) Wyświetla listę wszystkich metryk, które są domyślnie włączone dla usługi IoT Hub.

Za pomocą **trasy** dzienników diagnostycznych w usłudze Azure Monitor [ustawień diagnostycznych](../iot-hub/iot-hub-monitor-resource-health.md), możesz śledzi błędy podczas oceny routingu zapytania i punktu końcowego kondycji wyobrażenia Centrum IoT, na przykład gdy punkt końcowy nie istnieje. Te dzienniki diagnostyczne mogą być wysyłane do usługi Log Analytics, usługi Event Hubs lub usługi Azure Storage do niestandardowego przetwarzania.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się, jak utworzyć trasy wiadomości, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](tutorial-routing.md).

* [Jak wysyłać komunikaty urządzenie chmura](quickstart-send-telemetry-node.md)

* Aby uzyskać informacje dotyczące zestawów SDK, służy do wysyłania komunikatów urządzenie chmura, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).
