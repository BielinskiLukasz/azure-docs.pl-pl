---
title: 'Samouczek: implementowanie analizy przestrzennej IoT przy użyciu Azure Maps'
description: 'Samouczek: integracja IoT Hub z interfejsami API usługi Azure Maps.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6c35b52149e3c0117c727771d38d0f010180fc63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432813"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Samouczek: implementowanie analizy przestrzennej IoT przy użyciu Azure Maps

Śledzenie i przechwytywanie istotnych zdarzeń występujących w miejscu i czasie jest typowym scenariuszem IoT. Na przykład w przypadku zarządzania flotą, śledzenia zasobów, mobilności i aplikacji inteligentnych miast. Ten samouczek przeprowadzi Cię przez wzorzec rozwiązań służący do używania Azure Maps interfejsów API z odpowiednimi zdarzeniami przechwytywanymi przez IoT Hub przy użyciu modelu subskrypcji zdarzeń dostarczonego przez Event Grid.

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Tworzenie centrum IoT Hub.
> * Przekazywanie obszaru geofencingu w usłudze Azure Maps — usłudze danych przy użyciu interfejsu API przekazywania danych.
> * Utwórz funkcję w Azure Functions, implementując logikę biznesową opartą na Azure Maps analizie przestrzennej.
> * Subskrybuj zdarzenia telemetryczne urządzenia IoT z funkcji platformy Azure za pośrednictwem Event Grid.
> * Filtrowanie zdarzeń telemetrii za pomocą IoT Hub Routing komunikatów.
> * Utwórz konto magazynu, aby przechowywać odpowiednie dane zdarzenia.
> * Symulowanie urządzenia IoT w pojeździe.
    

## <a name="use-case"></a>Przypadek użycia

Firma Microsoft exemplify rozwiązanie w scenariuszu, w którym firma wynajmu samochodu planuje monitorować i rejestrować zdarzenia dotyczące dzierżawionych samochodów. Często samochody wynajmujące są wynajęcie samochodów w określonym regionie geograficznym i muszą śledzić ich miejsce pobytu podczas dzierżawy. Każde wystąpienie, które obejmuje samochód opuszczający wskazany region geograficzny, musi zostać zarejestrowane, aby zasady, opłaty i inne aspekty biznesowe mogły być prawidłowo obsługiwane.

W naszym przypadku użycia samochody najmu są wyposażone w urządzenia IoT, które regularnie wysyłają dane telemetryczne do usługi Azure IoT Hub. Dane telemetryczne obejmują bieżącą lokalizację i wskazuje, czy silnik samochodu jest uruchomiony. Schemat lokalizacji urządzenia jest zgodny ze [schematem Plug and Play IoT dla danych geoprzestrzennych](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Schemat telemetrii urządzenia dla samochodu wynajmu:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": { 
            "location": { 
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            } 
        } 
    }
}
```

Aby osiągnąć cel, można użyć telemetrii urządzenia w pojeździe. Naszym celem jest wykonanie reguł geofencingu i odpowiednie kolejne czynności przy każdym zdarzeniu informującym o przeniesieniu samochodu do lokalizacji. W tym celu zasubskrybujemy zdarzenia telemetryczne urządzenia z IoT Hub za pośrednictwem Event Grid, dzięki czemu można wykonać żądaną logikę biznesową klienta tylko wtedy, gdy jest to konieczne. Istnieje kilka sposobów subskrybowania Event Grid. w tym samouczku będziemy korzystać z Azure Functions. Azure Functions reaguje na zdarzenia publikowane w usłudze Event Grid i implementuje logikę biznesową wynajmu samochodów na podstawie Azure Maps analiz przestrzennych. Funkcja platformy Azure składa się z sprawdzenia, czy pojazd opuścił ogrodzenie i, jeśli tak, zbiera dodatkowe informacje, takie jak adres skojarzony z bieżącą lokalizacją. Funkcja implementuje również logikę do przechowywania znaczących danych zdarzeń w magazynie obiektów BLOB danych, która pomaga zapewnić dokładny opis okoliczności zdarzeń dla analityków wynajmu samochodu oraz wynajmujących klientów.

Poniższy diagram zawiera ogólne omówienie systemu.

 
  <center>

  ![przegląd systemu](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

Poniższa ilustracja przedstawia obszar geoogrodzenia wyróżniony w trasie na niebieską i wynajmu pojazdu jako zieloną linię.

  ![Trasa geoogrodzenia](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Wymagania wstępne 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby wykonać kroki opisane w tym samouczku, musisz najpierw utworzyć grupę zasobów w Azure Portal. Aby utworzyć grupę zasobów, wykonaj następujące czynności:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **grupy zasobów**.
    
   ![Grupy zasobów](./media/tutorial-iot-hub-maps/resource-group.png)

3. W obszarze grupy zasobów wybierz pozycję **Dodaj**.
    
   ![Dodaj grupę zasobów](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Wprowadź następujące wartości właściwości:
    * **Subskrypcja:** Wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** Wprowadź wartość "ContosoRental" jako nazwę grupy zasobów.
    * **Region:** Wybierz region dla grupy zasobów.  

    ![Szczegóły grupy zasobów](./media/tutorial-iot-hub-maps/resource-details.png)

    Kliknij pozycję **Przegląd + Utwórz** i wybierz pozycję **Utwórz** na następnej stronie.

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Aby zaimplementować logikę biznesową na podstawie Azure Maps analiz przestrzennych, należy utworzyć konto Azure Maps w utworzonej grupie zasobów. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) , aby utworzyć subskrypcję konta usługi Azure Maps z warstwą cenową S1, i wykonaj kroki opisane w sekcji [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby można było rejestrować dane zdarzeń, utworzymy konto **v2storage** ogólnego przeznaczenia w grupie zasobów "ContosoRental" do przechowywania danych jako obiektów BLOB. Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Następnie będziemy musieli utworzyć kontener do przechowywania obiektów BLOB. Wykonaj poniższe czynności, aby to zrobić:

1. Na koncie magazynu przejdź do obiektów BLOB.

    ![Liczba](./media/tutorial-iot-hub-maps/blobs.png)

2. Kliknij przycisk kontener w lewym górnym rogu i nadaj nazwę kontenerowi "contoso-Wynajem dzienników" i kliknij przycisk "OK".

    ![Obiekt BLOB — kontener](./media/tutorial-iot-hub-maps/blob-container.png)

3. Przejdź do bloku **klucze dostępu** na koncie magazynu i skopiuj nazwę konta i klucz dostępu, użyjemy ich później.

    ![klucze dostępu](./media/tutorial-iot-hub-maps/access-keys.png)


Teraz, gdy mamy już konto magazynu i kontener służący do rejestrowania danych zdarzeń, następnie utworzysz Centrum IoT Hub.

### <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

IoT Hub to zarządzana usługa w chmurze, która działa jako centralny centrum komunikatów w celu komunikacji dwukierunkowej między aplikacją IoT a urządzeniami, które są przez nią zarządzane. Aby można było kierować komunikaty telemetryczne urządzenia do Event Grid, utworzymy IoT Hub w grupie zasobów "ContosoRental" i ustawisz integrację trasy komunikatów, w której będziemy filtrować komunikaty na podstawie stanu aparatu samochodu i wysyłać dane telemetryczne urządzenia komunikaty do Event Grid za każdym razem, gdy nastąpi przeniesienie samochodu. 

> [!Note] 
> W publicznej wersji zapoznawczej IoT Hub funkcje publikowania zdarzeń telemetrii urządzeń w Event Grid. Funkcja publiczna w wersji zapoznawczej jest dostępna we wszystkich regionach z wyjątkiem **Wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, Azure Government, Chiny z Chin** i **Azure (Niemcy**). 

Utwórz Centrum IoT Hub, wykonując czynności opisane w [sekcji tworzenie IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Rejestrowanie urządzenia 

Aby można było nawiązać połączenie z IoT Hub, urządzenie musi być zarejestrowane. Aby zarejestrować urządzenie w usłudze IoT Hub, wykonaj następujące czynności:

1. W IoT Hub Kliknij blok "IoT Devices", a następnie kliknij pozycję "nowy".

    ![Dodaj urządzenie](./media/tutorial-iot-hub-maps/add-device.png)

2. Na stronie Tworzenie urządzenia Nadaj nazwę urządzeniu IoT, a następnie kliknij pozycję "Zapisz".
    
    ![Rejestr-urządzenie](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Przekaż geoogrodzenie

Będziemy używać [aplikacji Poster](https://www.getpostman.com) do [przekazywania geoogrodzenia](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) do usługi Azure Maps przy użyciu interfejsu API przekazywania danych Azure Maps. Każde zdarzenie, gdy kabina znajduje się poza tym geoogrodzeniem, zostanie zarejestrowane.

Otwórz aplikację Poster i postępuj zgodnie z poniższymi instrukcjami, aby przekazać geoogrodzenie przy użyciu Azure Maps, interfejsu API przekazywania danych.  

1. W aplikacji Poster kliknij pozycję Nowy | Utwórz nową, a następnie wybierz pozycję żądanie. Wprowadź nazwę żądania na potrzeby przekazywania danych geofencing, wybierz docelową kolekcję lub folder zapisywania, a następnie kliknij pozycję Save (Zapisz).

    ![Przekazywanie geofencingu przy użyciu narzędzia Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Na karcie konstruktora wybierz metodę POST HTTP i wprowadź poniższy adres URL, aby utworzyć żądanie POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Wartość "GEOJSON" dotycząca parametru `dataFormat` w ścieżce URL reprezentuje Format przekazywanych danych.

3. Kliknij pozycję **Params** (Parametry), a następnie wprowadź poniższą parę klucz-wartość, która będzie używana w przypadku adresu URL żądania POST. Zastąp wartość klucza subskrypcji kluczem Azure Maps.
   
    ![Parametry klucz-wartość narzędzia Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Kliknij pozycję **treść** , a następnie wybierz pozycję **nieprzetworzony** Format wejściowy, a następnie wybierz pozycję **JSON (aplikacja/tekst)** jako format danych wejściowych z listy rozwijanej. Otwórz plik danych JSON w [tym miejscu](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)i skopiuj kod JSON do sekcji treść w ogłoszeniu jako dane do przekazania, a następnie kliknij pozycję **Wyślij**.
    
    ![Opublikuj dane](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Przejrzyj nagłówki odpowiedzi. Po pomyślnym żądaniu nagłówek **lokalizacji** będzie zawierać identyfikator URI stanu, aby sprawdzić bieżący stan żądania przekazywania. Identyfikator URI stanu będzie mieć następujący format. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Skopiuj identyfikator URI stanu i Dołącz do niego parametr `subscription-key`, podając jego wartość klucza subskrypcji konta usługi Azure Maps. Format identyfikatora URI stanu powinien wyglądać podobnie do przedstawionego poniżej:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Aby uzyskać `udId` Otwórz nową kartę w aplikacji Poster i wybierz pozycję Pobierz metodę HTTP na karcie Konstruktor i wprowadź żądanie GET w identyfikatorze URI stanu. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz udId w treści odpowiedzi. Skopiuj udId do późniejszego użycia.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Następnie utworzymy funkcję platformy Azure w grupie zasobów "ContosoRental", a następnie skonfigurujesz trasę komunikatów w IoT Hub do filtrowania komunikatów telemetrycznych urządzenia.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Tworzenie funkcji platformy Azure i Dodawanie subskrypcji Event Grid

Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego udostępniania infrastruktury obliczeniowej ani zarządzania nią. Aby dowiedzieć się więcej na temat Azure Functions, zapoznaj się z dokumentacją [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) . Logika zaimplementowana w funkcji wykorzystuje dane lokalizacji pochodzące z telemetrii urządzenia w pojeździe w celu oceny stanu geoogrodzenia. W przypadku, gdy dany pojazd wykracza poza ogrodzenie, funkcja będzie zbierać więcej informacji, takich jak adres lokalizacji za pośrednictwem [interfejsu API odwracania adresów wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , który tłumaczy daną współrzędną lokalizacji na adres ulicy zrozumiałej dla człowieka. Wszystkie istotne informacje o zdarzeniu są następnie przechowywane w magazynie obiektów BLOB. Krok 5 poniżej wskazuje kod wykonywalny implementujący taką logikę. Wykonaj poniższe kroki, aby utworzyć funkcję platformy Azure, która wysyła dzienniki danych do kontenera obiektów BLOB na koncie magazynu, i Dodaj do niej subskrypcję Event Grid.

1. Na pulpicie nawigacyjnym Azure Portal wybierz pozycję Utwórz zasób. Wybierz pozycję **obliczenia** z listy dostępnych typów zasobów, a następnie wybierz pozycję **aplikacja funkcji**.

    ![Utwórz zasób](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na stronie Tworzenie aplikacji funkcji Nazwij aplikację funkcji, w obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję "ContosoRental" z listy rozwijanej. Wybierz pozycję ".NET Core" jako stos środowiska uruchomieniowego, w obszarze **Magazyn** wybierz pozycję **Użyj istniejącej** i wybierz pozycję "contosorentaldata" z listy rozwijanej i kliknij przycisk **Utwórz**.
    
    ![Tworzenie aplikacji](./media/tutorial-iot-hub-maps/rental-app.png)

3. Po utworzeniu aplikacji musimy dodać do niej funkcję. Przejdź do aplikacji funkcji i kliknij pozycję **Nowa funkcja** , aby dodać funkcję, wybierz **w portalu** jako środowisko programistyczne, a następnie wybierz pozycję **Kontynuuj**.

    ![CREATE-FUNCTION](./media/tutorial-iot-hub-maps/function.png)

4. Wybierz pozycję **więcej szablonów** , a następnie kliknij przycisk **Zakończ i Wyświetl szablony**. 

5. Wybierz szablon z **wyzwalaczem Azure Event Grid**. Zainstaluj rozszerzenia, jeśli zostanie wyświetlony monit, nazwij funkcję i naciśnij przycisk **Utwórz**.

    ![Funkcja — szablon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Skopiuj [kod c#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do funkcji, a następnie kliknij przycisk **Zapisz**.
 
7. W skrypcie języka c# Zastąp następujące parametry:
    * Zastąp **SUBSCRIPTION_KEY** podstawowym kluczem subskrypcji konta usługi Azure Maps.
    * Zastąp **UDID** z UDIDem przekazana geoogrodzenia, 
    * Funkcja **CreateBlobAsync** w skrypcie tworzy obiekt BLOB dla zdarzenia na koncie magazynu danych. Zastąp **ACCESS_KEY**, **ACCOUNT_NAME** i **STORAGE_CONTAINER_NAME** przy użyciu klucza dostępu konta magazynu oraz nazwy konta i kontenera magazynu danych.

10. Kliknij pozycję **Dodaj subskrypcję Event Grid**.
    
    ![Dodaj zdarzenie do siatki](./media/tutorial-iot-hub-maps/add-egs.png)

11. Wypełnij szczegóły subskrypcji w obszarze **szczegóły subskrypcji zdarzeń** Twoja subskrypcja i schemat zdarzeń wybierz pozycję "Event Grid schemat". W obszarze **Szczegóły tematu** wybierz pozycję "konta usługi Azure IoT Hub" jako typ tematu, wybierz tę samą subskrypcję, która została użyta do utworzenia grupy zasobów, wybierz pozycję "ContosoRental" jako "grupę zasobów" i wybierz IoT Hub utworzone jako "zasób". Wybierz dane **telemetryczne urządzenia** jako typ zdarzenia. Po wybraniu tych opcji w polu "typ tematu" zostanie wyświetlona automatyczna zmiana "IoT Hub".

    ![zdarzenie — siatka — subskrypcja](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrowanie zdarzeń przy użyciu IoT Hub Routing komunikatów

Po dodaniu subskrypcji Event Grid do funkcji platformy Azure będzie można wyświetlić domyślną trasę komunikatów do Event Grid w bloku **routingu wiadomości** IoT Hub. Routing komunikatów umożliwia kierowanie różnych typów danych, takich jak komunikaty telemetryczne urządzenia, zdarzenia cyklu życia urządzenia i zdarzenia zmiany sznurka urządzenia do różnych punktów końcowych. Aby dowiedzieć się więcej na temat routingu komunikatów usługi IoT Hub, zobacz [używanie IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![koncentrator-EG-Route](./media/tutorial-iot-hub-maps/hub-route.png)

W naszym przykładowym scenariuszu chcemy odfiltrować wszystkie komunikaty, w przypadku których trwa przeniesienie pojazdu najmu. Aby opublikować takie zdarzenia telemetrii urządzenia do Event Grid, będziemy używać zapytania routingu do filtrowania zdarzeń, w których właściwość `Engine` ma wartość **"on"** . Istnieją różne sposoby wykonywania zapytań dotyczących komunikatów przesyłanych z urządzeń do chmury, aby dowiedzieć się więcej na temat składni routingu wiadomości, zobacz [IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Aby utworzyć zapytanie routingu, kliknij trasę **RouteToEventGrid** i Zastąp **zapytanie routingu** z **"aparat =" na ""** , a następnie kliknij przycisk **Zapisz**. Teraz Centrum IoT będzie publikować dane telemetryczne urządzenia, na których jest włączony aparat.

![koncentrator — EG — filtr](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Wyślij dane telemetryczne do IoT Hub

Gdy nasza funkcja platformy Azure zostanie uruchomiona, będziemy teraz wysyłać dane telemetryczne do IoT Hub, co spowoduje skierowanie go do Event Grid. Użyjemy aplikacji języka c# w celu symulowania danych lokalizacji na urządzeniu z pojazdem samochodu. Aby uruchomić aplikację, musisz mieć zestaw .NET Core SDK 2.1.0 na komputerze deweloperskim lub nowszym. Wykonaj poniższe kroki, aby wysłać symulowane dane telemetryczne do IoT Hub.

1. Pobierz projekt języka c# [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) . 

2. Otwórz plik simulatedCar.cs w wybranym edytorze tekstów i Zastąp wartość `connectionString` wartością zapisaną podczas rejestrowania urządzenia i Zapisz zmiany w pliku.
 
3. W oknie terminalu lokalnego przejdź do folderu głównego C# projektu i uruchom następujące polecenie, aby zainstalować wymagane pakiety dla aplikacji symulowanego urządzenia:
    
    ```cmd/sh
    dotnet restore
    ```

4. W tym samym terminalu uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulacji samochodu wynajmu:

    ```cmd/sh
    dotnet run
    ```

  Terminal lokalny powinien wyglądać podobnie do przedstawionego poniżej.

  ![Dane wyjściowe terminalu](./media/tutorial-iot-hub-maps/terminal.png)

Jeśli teraz otworzysz kontener usługi BLOB Storage, powinny być widoczne cztery obiekty blob dla lokalizacji, w których pojazd znajduje się poza ogrodzeniem.

![Wprowadź obiekt BLOB](./media/tutorial-iot-hub-maps/blob.png)

Mapa poniżej przedstawia cztery punkty, w których pojazd był poza ogrodzeniem, rejestrowany w regularnych odstępach czasu.

![Mapa naruszenia](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Następne kroki

Aby poznać Azure Maps interfejsów API używanych w tym samouczku, zobacz:

* [Uzyskaj zwrotny adres wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Pobierz geoogrodzenie](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz:

* [Interfejsy API REST Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Aby dowiedzieć się więcej na temat Plug and Play IoT, zobacz:

* [Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Aby uzyskać listę urządzeń z certyfikatem platformy Azure dla IoT, odwiedź stronę:

* [Certyfikowane urządzenia platformy Azure](https://catalog.azureiotsolutions.com/)

Aby dowiedzieć się więcej o sposobach wysyłania urządzenia do telemetrii w chmurze i na odwrót, zobacz:

* [Wysyłanie danych telemetrycznych z urządzenia](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
