---
title: Zmień bibliotekę procesora kanału informacyjnego w Azure Cosmos DB
description: Dowiedz się, jak używać biblioteki procesora kanału zmian Azure Cosmos DB do odczytywania źródła zmian, składników procesora źródła zmian
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273303"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Zmień procesor kanału informacyjnego w Azure Cosmos DB 

Procesor kanału informacyjnego zmian jest częścią [zestawu Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Upraszcza proces odczytywania źródła zmian i rozpowszechniania przetwarzania zdarzeń przez wielu użytkowników efektywnie.

Główną zaletą biblioteki procesora kanału informacyjnego jest zachowanie odporne na błędy, które gwarantuje, że dostarczanie wszystkich zdarzeń w strumieniu zmian zostanie zakończone "co najmniej raz".

## <a name="components-of-the-change-feed-processor"></a>Składniki procesora źródła zmian

Istnieją cztery główne składniki implementacji procesora kanału informacyjnego zmiany: 

1. **Monitorowany kontener:** Monitorowany kontener zawiera dane, z których jest generowane strumieniowe źródło zmian. Wszystkie wstawienia i aktualizacje monitorowanego kontenera są odzwierciedlone w kanale zmian kontenera.

1. **Kontener dzierżawy:** Kontener dzierżawy działa jako magazyn stanów i koordynuje przetwarzanie źródła zmian przez wielu pracowników. Kontener dzierżawy może być przechowywany na tym samym koncie co monitorowany kontener lub na osobnym koncie. 

1. **Host:** Host jest wystąpieniem aplikacji korzystającym z procesora źródła zmian do nasłuchiwania zmian. Wiele wystąpień z tą samą konfiguracją dzierżawy może działać równolegle, ale każde wystąpienie powinno mieć inną **nazwę wystąpienia**. 

1. **Delegat:** Delegat to kod, który definiuje, co ty i dla deweloperów chce wykonać przy każdej partii zmian, które procesor kanału informacyjnego zmian odczytuje. 

Aby dowiedzieć się więcej o tym, jak te cztery elementy procesora źródła zmian współpracują ze sobą, przyjrzyjmy się przykładowi na poniższym diagramie. Monitorowany kontener przechowuje dokumenty i używa jako klucza partycji "miasto". Zobaczymy, że wartości klucza partycji są dystrybuowane w zakresach zawierających elementy. Istnieją dwa wystąpienia hosta, a procesor kanału informacyjnego zmiany przypisuje różne zakresy wartości klucza partycji do każdego wystąpienia, aby zmaksymalizować rozkład obliczeń. Każdy zakres jest odczytywany równolegle, a jego postęp jest obsługiwany niezależnie od innych zakresów w kontenerze dzierżawy.

![Przykładowy procesor źródła zmian](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementacja procesora źródła zmian

Punkt wejścia jest zawsze monitorowanym kontenerem z wystąpienia `Container`, które jest wywoływane `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Gdzie pierwszy parametr jest odrębną nazwą opisującą cel tego procesora, a druga nazwa jest implementacją delegata, która będzie obsługiwać zmiany. 

Przykładem delegata będzie:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Na koniec zdefiniujesz nazwę tego wystąpienia procesora z `WithInstanceName`, który jest kontenerem do obsługi stanu dzierżawy z `WithLeaseContainer`.

Wywołanie `Build` spowoduje udostępnienie wystąpienia procesora, które można uruchomić, wywołując `StartAsync`.

## <a name="processing-life-cycle"></a>Cykl życia przetwarzania

Normalny cykl życia wystąpienia hosta to:

1. Przeczytaj Źródło zmian.
1. W przypadku braku zmian w stanie uśpienia przez wstępnie zdefiniowany czas (dostosowywalny w przypadku `WithPollInterval` w konstruktorze) i przejdź do #1.
1. Jeśli istnieją zmiany, wyślij je do **delegata**.
1. Po **pomyślnym**zakończeniu przetwarzania zmian przez delegata należy zaktualizować magazyn dzierżawy o ostatni przetworzony punkt w czasie i przejść do #1.

## <a name="error-handling"></a>Obsługa błędów

Procesor kanału informacyjnego zmiany jest odporny na błędy kodu użytkownika. Oznacza to, że jeśli w implementacji delegata występuje nieobsłużony wyjątek (krok #4), przetwarzanie wątku, w którym określona partia zmian zostanie zatrzymane i zostanie utworzony nowy wątek. Nowy wątek sprawdzi, który był ostatnim punktem w czasie, gdy magazyn dzierżawy ma dla tego zakresu wartości kluczy partycji, a następnie ponownie uruchomi się z tego powodu, wysyłając tę samą partię zmian do delegata. Takie zachowanie będzie kontynuowane do momentu poprawnego przetworzenia przez delegata zmian i jest to powód, dla którego procesor źródła zmian ma gwarancję "co najmniej raz", ponieważ w przypadku zgłaszania kodu delegata zostanie ponowiona ta partia.

## <a name="dynamic-scaling"></a>Dynamiczne skalowanie

Jak wspomniano w trakcie wprowadzania, procesor kanału informacyjnego zmiany może automatycznie dystrybuować obliczenia w wielu wystąpieniach. Można wdrożyć wiele wystąpień aplikacji przy użyciu procesora źródła zmian i korzystać z niego, ale jedynymi wymaganiami są:

1. Wszystkie wystąpienia powinny mieć tę samą konfigurację kontenera dzierżawy.
1. Wszystkie wystąpienia powinny mieć taką samą nazwę przepływu pracy.
1. Każde wystąpienie musi mieć inną nazwę wystąpienia (`WithInstanceName`).

Jeśli te trzy warunki mają zastosowanie, procesor kanału informacyjnego zmiany będzie używać algorytmu dystrybucji równej, dystrybuuje wszystkie dzierżawy w kontenerze dzierżawy do wszystkich uruchomionych wystąpień i obliczeń zrównoleglanie. Jedna dzierżawa może należeć tylko do jednego wystąpienia w danym momencie, więc Maksymalna liczba wystąpień jest równa liczbie dzierżaw.

Liczba wystąpień może się zwiększać i zmniejszać, a procesor kanału informacyjnego zmiany dynamicznie dostosowuje obciążenie przez ponowną dystrybucję.

Ponadto procesor kanału informacyjnego zmiany można dynamicznie dopasować do skalowania kontenerów z powodu wzrostu przepływności lub magazynu. Gdy rozmiar kontenera zostanie powiększony, procesor kanału informacyjnego zmian w sposób niewidoczny dla użytkownika przechwytuje te scenariusze poprzez dynamiczne zwiększanie dzierżaw i dystrybucję nowych dzierżaw między istniejącymi wystąpieniami.

## <a name="change-feed-and-provisioned-throughput"></a>Kanał informacyjny zmian i przepływność aprowizacji

Opłata jest naliczana za zużyte jednostek ru, ponieważ przenoszenie danych do i z kontenerów Cosmos zawsze zużywa jednostek ru. Opłata jest naliczana za jednostek ru zużyty przez kontener dzierżawy.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Przykłady użycia w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz można dowiedzieć się więcej o procesorze źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Jak przeprowadzić migrację z biblioteki procesora źródła zmian](how-to-migrate-from-change-feed-library.md)
* [Korzystanie ze źródła zmian szacowania](how-to-use-change-feed-estimator.md)
* [Czas rozpoczęcia procesora kanału informacyjnego](how-to-configure-change-feed-start-time.md)
