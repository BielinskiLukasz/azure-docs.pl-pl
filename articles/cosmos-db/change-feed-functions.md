---
title: Jak używać Azure Cosmos DB ze źródłem zmian Azure Functions
description: Użyj Azure Functions, aby nawiązać połączenie ze źródłem zmian Azure Cosmos DB. Później można tworzyć reaktywne usługi Azure Functions, które są wyzwalane dla każdego nowego zdarzenia.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 215ecc1e392f8e7051173fb6f589fb940c26f17d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872251"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury oparte na zdarzeniach bezserwerowych z Azure Cosmos DB i Azure Functions

Azure Functions zapewnia najprostszy sposób nawiązywania połączenia ze [źródłem zmian](change-feed.md). Można utworzyć małe Azure Functions reaktywne, które będą automatycznie wyzwalane dla każdego nowego zdarzenia w źródle zmian kontenera usługi Azure Cosmos.

![Funkcje oparte na zdarzeniach bezserwerowych działające z wyzwalaczem Azure Functions dla Cosmos DB](./media/change-feed-functions/functions.png)

Korzystając z [wyzwalacza Azure Functions dla Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), można użyć funkcji skalowania i niezawodnego wykrywania zdarzeń przez [procesor kanału informacyjnego](./change-feed-processor.md), bez konieczności zachowywania [infrastruktury procesów roboczych](./change-feed-processor.md). Wystarczy skupić się na logice funkcji platformy Azure bez obaw o resztę potoku pozyskania zdarzeń. Można nawet mieszać wyzwalacz z innymi [powiązaniami Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Obecnie wyzwalacz Azure Functions dla Cosmos DB jest obsługiwany wyłącznie z interfejsem API Core (SQL).

## <a name="requirements"></a>Wymagania

Aby zaimplementować przepływ oparty na zdarzeniach bezserwerowych, potrzebne są:

* **Monitorowany kontener**: monitorowany kontener jest monitorowanym kontenerem usługi Azure Cosmos i przechowuje dane, z których jest generowana podawanie zmian. Wszystkie operacje wstawiania, aktualizacje monitorowanego kontenera są odzwierciedlane w kanale zmian kontenera.
* **Kontener dzierżawy**: kontener dzierżawy zachowuje stan między wieloma i dynamicznymi wystąpieniami funkcji platformy Azure bez serwera i umożliwia dynamiczne skalowanie. Ten kontener dzierżawy może być ręcznie lub automatycznie tworzony przez wyzwalacz Azure Functions dla Cosmos DB. Aby automatycznie utworzyć kontener dzierżawy, Ustaw flagę *CreateLeaseCollectionIfNotExists* w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Kontenery z podzielonymi dzierżawami muszą mieć definicję klucza partycji `/id`.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Utwórz wyzwalacz Azure Functions dla Cosmos DB

Tworzenie funkcji platformy Azure z wyzwalaczem Azure Functions dla Cosmos DB jest teraz obsługiwane dla wszystkich Azure Functions IDE i integracji interfejsu wiersza polecenia:

* [Rozszerzenie programu Visual Studio](../azure-functions/functions-develop-vs.md) dla użytkowników programu Visual Studio.
* [Podstawowe rozszerzenie programu Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) dla użytkowników Visual Studio Code.
* A wreszcie [podstawowe narzędzia interfejsu wiersza polecenia](../azure-functions/functions-run-local.md#create-func) dla wieloplatformowego środowiska IDE niezależny od.

## <a name="run-your-trigger-locally"></a>Uruchamianie wyzwalacza lokalnie

[Funkcję platformy Azure](../azure-functions/functions-develop-local.md) można uruchamiać lokalnie za pomocą [emulatora Azure Cosmos DB](./local-emulator.md) , aby tworzyć i opracowywać przepływy oparte na zdarzeniach Bezserwerowych bez subskrypcji platformy Azure ani ponoszenia kosztów.

Jeśli chcesz przetestować scenariusze na żywo w chmurze, możesz [bezpłatnie Wypróbować Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez żadnej karty kredytowej lub subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz dalej dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Sposoby odczytywania źródła zmian](read-change-feed.md)
* [Za pomocą zmian źródła danych z biblioteką procesora](change-feed-processor.md)
* [Jak korzystać z biblioteki procesora źródła zmian](change-feed-processor.md)
* [Przetwarzanie baz danych bezserwerowe przy użyciu Azure Cosmos DB i Azure Functions](serverless-computing-database.md)
