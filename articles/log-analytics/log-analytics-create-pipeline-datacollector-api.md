---
title: Tworzenie potoku danych przy użyciu interfejsu API usługi Azure Log Analytics Data Collector | Dokumentacja firmy Microsoft
description: Interfejs API modułu zbierającego dane HTTP analizy dziennika umożliwia dodawanie danych POST JSON do repozytorium usługi Log Analytics za pomocą dowolnego klienta, który można wywołać interfejsu API REST. W tym artykule opisano, jak przekazywać dane przechowywane w plikach w zautomatyzowany sposób.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 180f1a39b92dd699fa114cb98a5842b0ab0dc89a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190542"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Tworzenie potoku danych przy użyciu interfejsu API modułu zbierającego dane

[Interfejsu API modułu zbierającego dane usługi Log Analytics](log-analytics-data-collector-api.md) umożliwia zaimportowanie dowolnego niestandardowe dane do usługi Log Analytics. Jedynymi wymogami są na tym, że dane mają być sformatowanego JSON i dzielenie, do 30 MB lub mniej segmentów. Jest to całkowicie elastyczny mechanizm, który można podłączyć do na wiele sposobów: dane są wysyłane bezpośrednio z aplikacji do ad hoc jednorazowe przekazanie. W tym artykule opisują kilka punktów początkowych dla typowego scenariusza: trzeba przekazać dane przechowywane w plikach na podstawie regularnych, automatycznych. Chociaż przedstawione potok w tym miejscu nie będzie większość wydajne lub w przeciwnym razie jest zoptymalizowana, ma ona służyć jako punkt wyjścia do tworzenia potoku produkcji własne.

## <a name="example-problem"></a>Przykład problem
W pozostałej części tego artykułu będziemy sprawdzać dane dotyczące wyświetleń stron w usłudze Application Insights. W naszym scenariuszu hipotetyczny chcemy skorelować zbierane domyślnie zestaw SDK usługi Application Insights do danych niestandardowych, zawierających populacji każdego kraju w świecie, w celu określenia, gdzie firma powinna spędzać większość informacji geograficznych Marketing dolarów. 

Używamy publiczne źródło danych takich jak [ONZ World populacji perspektyw](https://esa.un.org/unpd/wpp/) do tego celu. Dane będą mieć poniższy prosty schemat:

![Przykład prosty schemat](./media/log-analytics-create-pipeline-datacollector-api/example-simple-schema-01.png)

W tym przykładzie przyjęto założenie, że firma Microsoft przekaże nowy plik z danymi ostatni rok tak szybko, jak staje się dostępna.

## <a name="general-design"></a>Projekt ogólny
Używamy klasycznego logiki typ ETL do projektowania nasz potok. Architektura będzie wyglądać w następujący sposób:

![Architektura procesu zbierania danych](./media/log-analytics-create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

W tym artykule nie opisano sposób tworzenia danych lub [przekazać go do konta usługi Azure Blob Storage](../storage/blobs/storage-upload-process-images.md). Przeciwnie firma Microsoft przejmą przepływ tak szybko, jak nowy plik zostanie przekazany do obiektu blob. W tym miejscu:

1. Proces wykryje, że nowe dane zostały przekazane.  Przedstawiony przykład używa [aplikacji logiki platformy Azure](../logic-apps/logic-apps-overview.md), która ma dostępne wyzwalacza do wykrywania nowych danych przekazywanych do obiektu blob.

2. Procesor odczytuje te nowe dane i konwertuje ją na format JSON, w formacie wymaganym przez usługę Log Analytics.  W tym przykładzie używamy [funkcji platformy Azure](../azure-functions/functions-overview.md) jako uproszczone, wydajne sposób wykonywania naszego kodu przetwarzania. Funkcja rozpocznie się kompilowanie przez samą aplikację logiki, używany do wykrywania w nowe dane.

3. Na koniec po udostępnieniu obiekt JSON jest wysyłana do usługi Log Analytics. Sama aplikacja logiki wysyła dane do usługi Log Analytics przy użyciu wbudowanych w działaniu Log Analytics Data Collector.

Gdy szczegółowe ustawienia magazynu obiektów blob, w aplikacji logiki lub funkcji platformy Azure nie jest opisane w tym artykule, uzyskać szczegółowe instrukcje są dostępne na stronach określonych produktów.

Aby monitorować ten potok, używamy usługi Application Insights do monitorowania naszej funkcji platformy Azure [szczegółowych informacji w tym miejscu](../azure-functions/functions-monitoring.md)i Log Analytics do monitorowania naszej aplikacji logiki [szczegółowych informacji w tym miejscu](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Konfigurowanie potoku
Aby ustawić potoku, najpierw upewnij się, że masz kontener obiektów blob utworzone i skonfigurowane. Podobnie upewnij się, czy został utworzony obszar roboczy usługi Log Analytics, gdzie chcesz wysyłać dane do.

## <a name="ingesting-json-data"></a>Wprowadzane dane JSON
Wprowadzane dane JSON jest proste dzięki usłudze Logic Apps, a ponieważ transformacji nie musi mieć miejsce, firma Microsoft obejmować cały potok w jednej aplikacji logiki. Po skonfigurowaniu kontenera obiektów blob i obszar roboczy usługi Log Analytics, Utwórz nową aplikację logiki i skonfigurować go w następujący sposób:

![Przykład przepływu pracy aplikacji logiki](./media/log-analytics-create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Zapisz aplikację logiki, a następnie przejść do testowania.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Wprowadzane XML, CSV lub innych formatów danych
Obecnie usługa Logic Apps nie ma wbudowanych funkcji łatwo przekształcić XML, CSV lub innych typów w formacie JSON. W związku z tym należy użyć innych metod do ukończenia tej transformacji. W tym artykule używamy możliwości obliczeniowych bez użycia serwera usługi Azure Functions jako bardzo lekka i koszt przyjaznego sposób działania. 

W tym przykładzie możemy przeanalizować pliku CSV, ale wszystkie inne typy plików mogą być przetwarzane podobnie. Po prostu zmodyfikuj deserializacji części funkcji platformy Azure, aby odzwierciedlić poprawne logiki dla danego typu danych specyficznych dla.

1.  Tworzenie nowej funkcji platformy Azure przy użyciu funkcji środowiska uruchomieniowego w wersji 1 i na podstawie użycia, gdy zostanie wyświetlony monit.  Wybierz **wyzwalacza HTTP** przeznaczone dla języka C# jako punktu wyjścia, konfigurującego wiązania wymagamy szablonu. 
2.  Z **Wyświetl pliki** karcie w okienku po prawej stronie, Utwórz nowy plik o nazwie **project.json** i wklej następujący kod z pakietów NuGet, które są używane:

    ![Usługa Azure Functions przykładowy projekt](./media/log-analytics-create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Przełącz się do **run.csx** w okienku po prawej stronie, a następnie Zastąp domyślny kod następującym kodem. 

    >[!NOTE]
    >Dla projektu musisz zastąpić modelu rekordu (klasy "PopulationRecord") przy użyciu schematu danych.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Zapisz swoją funkcję.
5. Przetestować funkcję, aby upewnić się, że kod działa poprawnie. Przełącz się do **Test** karty w okienku po prawej stronie, w następujący sposób konfigurowania testu. Umieść link do obiektu blob przy użyciu przykładowych danych **treść żądania** pola tekstowego. Po kliknięciu przycisku **Uruchom**, JSON powinny zostać wyświetlone dane wyjściowe w **dane wyjściowe** pole:

    ![Aplikacje funkcji testowania kodu](./media/log-analytics-create-pipeline-datacollector-api/functions-test-01.png)

Teraz należy wrócić i zmodyfikować aplikację logiki, Rozpoczęliśmy kompilowanie wcześniej, aby dołączyć dane pozyskane i przekonwertowane na JSON format.  Przy użyciu projektanta widoku, skonfigurować w następujący sposób, a następnie zapisz aplikację logiki:

![Pełny przykład przepływu pracy aplikacji logiki](./media/log-analytics-create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testowanie potoku
Teraz Przekaż nowy plik do obiektu blob skonfigurowane wcześniej i jest monitorowana przez aplikację logiki. Wkrótce powinien zobaczyć nowe wystąpienie klasy rozpoczęcia aplikacji logiki, wyłącz, wyróżnienia do funkcji platformy Azure i następnie pomyślnie wysłać dane do usługi Log Analytics. 

>[!NOTE]
>Może potrwać do 30 minut, zanim dane pojawią się w chwili usługi Log Analytics pierwszy przesyłania nowy typ danych.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelacji z innymi danymi w usłudze Log Analytics i usługi Application Insights
Aby ukończyć nasz cel korelacji usługi Application Insights dane dotyczące wyświetleń stron z danymi populacji, które firma Microsoft pozyskanych z naszych niestandardowego źródła danych, uruchom następujące zapytanie z okna analizy usługi Application Insights lub obszar roboczy usługi Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Dane wyjściowe powinny pokazywać dane z dwóch źródeł przyłączone.  

![Korelowanie odłączony danych w przykładzie wynik wyszukiwania](./media/log-analytics-create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Sugerowane ulepszenia dla potoku produkcji
W tym artykule prezentowane prototyp pracy, logiki mogą być stosowane do prawdziwe rozwiązania jakości produkcyjnej. Takie rozwiązanie jakości produkcyjnej zaleca się następujące ulepszenia:

* Dodawanie obsługi błędów i Logika ponawiania w swojej aplikacji logiki i funkcji.
* Dodaj logikę, aby upewnić się, że nie zostanie przekroczony limit 30MB/pojedynczego wywołania interfejsu API usługi Log Analytics pozyskiwania. Podzielić dane na mniejsze segmenty, jeśli to konieczne.
* Konfigurowanie zasad oczyszczania w magazynie obiektów blob. Po pomyślnie wysłane do usługi Log Analytics, chyba że chcesz zachować dane pierwotne, która jest dostępna w celu jego archiwizacji, nie ma powodu kontynuować przechowywanie go. 
* Sprawdź monitorowania jest włączona na pełny potok, dodając punkty śledzenia i alertów odpowiednio.
* Korzystać z kontroli źródła do zarządzania kodem funkcji i aplikacji logiki.
* Upewnij się, że występuje zasady zarządzania odpowiednie zmiany, takie, że w przypadku zmiany schematu, funkcji i Logic Apps są odpowiednio modyfikowane.
* Podczas przekazywania wielu różnych typów danych oddzielenie czynności związanych z ich do poszczególnych folderów w kontenerze obiektów blob, a następnie utwórz logikę wentylator logiki się na podstawie typu danych. 


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [interfejsu API modułu zbierającego dane](log-analytics-data-collector-api.md) można zapisać danych do usługi Log Analytics za pomocą dowolnego klienta interfejsu API REST.
