---
title: Procesor zestawienia zmian w usłudze Azure Cosmos DB
description: Dowiedz się, jak użyć procesora Azure Cosmos DB zmian, aby odczytać Źródło zmian, składniki procesora źródła zmian
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.openlocfilehash: 4325f75ac8181e088d64e53d3f65e085a09c0224
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119413"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Procesor zestawienia zmian w usłudze Azure Cosmos DB

Procesor kanału informacyjnego zmian jest częścią [zestawu Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Upraszcza proces odczytywania źródła zmian i rozpowszechniania przetwarzania zdarzeń przez wielu użytkowników efektywnie.

Główną zaletą biblioteki procesora kanału informacyjnego jest zachowanie odporne na błędy, które gwarantuje, że dostarczanie wszystkich zdarzeń w strumieniu zmian zostanie zakończone "co najmniej raz".

## <a name="components-of-the-change-feed-processor"></a>Składniki procesora źródła zmian

Są cztery główne składniki implementacji procesora zestawienia zmian:

1. **Monitorowany kontener:** Monitorowany kontener zawiera dane, na podstawie których jest generowane zestawienie zmian. Wszystkie wstawienia i aktualizacje monitorowanego kontenera są odzwierciedlone w jego zestawieniu zmian.

1. **Kontener dzierżawy:** Kontener dzierżawy działa jako magazyn stanów i koordynuje przetwarzanie zestawienia zmian przez wielu pracowników. Kontener dzierżawy może być przechowywany na tym samym koncie co monitorowany kontener lub na osobnym koncie.

1. **Host:** Host to wystąpienie aplikacji korzystające z procesora zestawienia zmian do nasłuchiwania zmian. Wiele wystąpień z tą samą konfiguracją dzierżawy może działać równolegle, ale każde wystąpienie powinno mieć inną **nazwę wystąpienia**.

1. **Obiekt delegowany:** Obiekt delegowany to kod, który definiuje, co ty jako deweloper zrobisz z każdą partią zmian odczytywanych przez procesor zestawienia zmian. 

Aby dowiedzieć się więcej o tym, jak te cztery elementy procesora źródła zmian współpracują ze sobą, przyjrzyjmy się przykładowi na poniższym diagramie. Monitorowany kontener przechowuje dokumenty i używa jako klucza partycji "miasto". Zobaczymy, że wartości klucza partycji są dystrybuowane w zakresach zawierających elementy. Istnieją dwa wystąpienia hosta, a procesor kanału informacyjnego zmiany przypisuje różne zakresy wartości klucza partycji do każdego wystąpienia, aby zmaksymalizować rozkład obliczeń. Każdy zakres jest odczytywany równolegle, a jego postęp jest obsługiwany niezależnie od innych zakresów w kontenerze dzierżawy.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Przykładowy procesor źródła zmian" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementacja procesora źródła zmian

Punkt wejścia jest zawsze monitorowanym kontenerem z `Container` wystąpienia wywoływanego `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Gdzie pierwszy parametr jest odrębną nazwą opisującą cel tego procesora, a druga nazwa jest implementacją delegata, która będzie obsługiwać zmiany. 

Przykładem delegata będzie:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Na koniec zdefiniujesz nazwę tego wystąpienia procesora z `WithInstanceName` i który jest kontenerem do obsługi stanu dzierżawy `WithLeaseContainer` .

Wywołanie `Build` spowoduje udostępnienie wystąpienia procesora, które można uruchomić, wywołując metodę `StartAsync` .

## <a name="processing-life-cycle"></a>Cykl życia przetwarzania

Normalny cykl życiowy wystąpienia hosta wygląda następująco:

1. Przeczytaj Źródło zmian.
1. W przypadku braku zmian w stanie uśpienia przez wstępnie zdefiniowany czas (dostosowywalny `WithPollInterval` w programie w ramach konstruktora) i przejdź do #1.
1. Jeśli istnieją zmiany, wyślij je do **delegata**.
1. Po **pomyślnym**zakończeniu przetwarzania zmian przez delegata należy zaktualizować magazyn dzierżawy o ostatni przetworzony punkt w czasie i przejść do #1.

## <a name="error-handling"></a>Obsługa błędów

Procesor kanału informacyjnego zmiany jest odporny na błędy kodu użytkownika. Oznacza to, że jeśli w implementacji delegata występuje nieobsłużony wyjątek (krok #4), przetwarzanie wątku, w którym określona partia zmian zostanie zatrzymane i zostanie utworzony nowy wątek. Nowy wątek sprawdzi, który był ostatnim punktem w czasie, gdy magazyn dzierżawy ma dla tego zakresu wartości kluczy partycji, a następnie ponownie uruchomi się z tego powodu, wysyłając tę samą partię zmian do delegata. Takie zachowanie będzie kontynuowane do momentu poprawnego przetworzenia przez delegata zmian i jest to powód, dla którego procesor źródła zmian ma gwarancję "co najmniej raz", ponieważ jeśli kod delegata zgłasza wyjątek, ponowi próbę wykonania tej partii.

Aby zapobiec nieudanej próbie wykonania tej samej partii zmian przez procesor kanału informacyjnego zmian, należy dodać logikę w kodzie delegata do zapisu dokumentów, po wyjątku, do kolejki utraconych wiadomości. Ten projekt umożliwia śledzenie nieprzetworzonych zmian, gdy nadal można nadal przetwarzać przyszłe zmiany. Kolejka utraconych wiadomości może po prostu być innym kontenerem Cosmos. Dokładny magazyn danych nie ma znaczenia, po prostu, że nieprzetworzone zmiany są utrwalane.

Ponadto można użyć [szacowania źródła zmian](how-to-use-change-feed-estimator.md) do monitorowania postępu wystąpień procesora źródła zmian podczas odczytywania źródła zmian. Poza monitorowaniem, jeśli procesor źródła zmian nie zostanie "zablokowany" ciągle ponawianie tej samej partii zmian, można również zrozumieć, czy procesor kanału informacyjnego zmian jest opóźniony z powodu dostępnych zasobów, takich jak procesor CPU, pamięć i przepustowość sieci.

## <a name="deployment-unit"></a>Jednostka wdrożenia

Pojedyncza jednostka wdrożenia procesora źródła zmian składa się z jednego lub większej liczby wystąpień o tej samej `processorName` konfiguracji kontenera i dzierżawie. Istnieje wiele jednostek wdrożenia, w których każdy z nich ma inny przepływ biznesowy dla zmian i każda jednostka wdrożenia składająca się z jednego lub większej liczby wystąpień. 

Na przykład może istnieć jedna jednostka wdrożenia, która wyzwala zewnętrzny interfejs API w dowolnym momencie, gdy istnieje zmiana w Twoim kontenerze. Inna jednostka wdrożenia może przenosić dane w czasie rzeczywistym, za każdym razem, gdy istnieje zmiana. Gdy zmiana jest wykonywana w monitorowanym kontenerze, wszystkie jednostki wdrożenia zostaną powiadomione.

## <a name="dynamic-scaling"></a>Dynamiczne skalowanie

Jak wspomniano wcześniej, w jednostce wdrożenia może istnieć co najmniej jedno wystąpienie. Aby skorzystać z dystrybucji obliczeniowej w ramach jednostki wdrożenia, jedynymi wymaganiami są:

1. Wszystkie wystąpienia powinny mieć tę samą konfigurację kontenera dzierżawy.
1. Wszystkie wystąpienia powinny być takie same `processorName` .
1. Każde wystąpienie musi mieć inną nazwę (`WithInstanceName`).

Jeśli te trzy warunki mają zastosowanie, procesor kanału informacyjnego zmiany będzie używać algorytmu dystrybucji równej, dystrybuuje wszystkie dzierżawy w kontenerze dzierżawy do wszystkich uruchomionych wystąpień tej jednostki wdrożenia i obliczeń zrównoleglanie. Jedna dzierżawa może należeć tylko do jednego wystąpienia w danym momencie, więc Maksymalna liczba wystąpień jest równa liczbie dzierżaw.

Liczba wystąpień może się zwiększać i zmniejszać, a procesor kanału informacyjnego zmiany dynamicznie dostosowuje obciążenie przez ponowną dystrybucję.

Ponadto procesor kanału informacyjnego zmiany można dynamicznie dopasować do skalowania kontenerów z powodu wzrostu przepływności lub magazynu. Gdy rozmiar kontenera zostanie powiększony, procesor kanału informacyjnego zmian w sposób niewidoczny dla użytkownika przechwytuje te scenariusze poprzez dynamiczne zwiększanie dzierżaw i dystrybucję nowych dzierżaw między istniejącymi wystąpieniami.

## <a name="change-feed-and-provisioned-throughput"></a>Kanał informacyjny zmian i przepływność aprowizacji

Opłata jest naliczana za zużyte jednostek ru, ponieważ przenoszenie danych do i z kontenerów Cosmos zawsze zużywa jednostek ru. Opłata jest naliczana za jednostek ru zużyty przez kontener dzierżawy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Przykłady użycia w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz można dowiedzieć się więcej o procesorze źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Model ściągania zestawienia zmian](change-feed-pull-model.md)
* [Jak przeprowadzić migrację z biblioteki procesora źródła zmian](how-to-migrate-from-change-feed-library.md)
* [Korzystanie z estymatora zestawienia zmian](how-to-use-change-feed-estimator.md)
* [Czas rozpoczęcia procesora zestawienia zmian](how-to-configure-change-feed-start-time.md)
