---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6dc846ab6af0ed4d6d48c6f6db0abbce06427c36
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035887"
---
Azure Data Factory to wielodostępna usługa, która ma następujące domyślne limity, aby upewnić się, że subskrypcje klientów są chronione przed innymi obciążeniami. Aby podnieść limity do wartości maksymalnej dla subskrypcji, skontaktuj się z pomocą techniczną.

### <a name="version-2"></a>Wersja 2

| Zasób | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w ramach subskrypcji platformy Azure | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba jednostek, takich jak potoki, zestawy danych, wyzwalacze, połączone usługi i środowiska Integration Runtime, w ramach fabryki danych | 5000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba rdzeni procesora dla środowiska Azure-SSIS Integration Runtime w ramach jednej subskrypcji | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia potoków na fabrykę danych, które są współużytkowane przez wszystkie potoki w fabryce | 10 000  | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia działań zewnętrznych na subskrypcję na [region Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Działania zewnętrzne są zarządzane w środowisku Integration Runtime, ale wykonywane przez połączone usługi, takie jak datakostki, procedura składowana, HDInsight i inne.</small> | 3000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia działań potoku na subskrypcję na [region Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Działania potoku wykonują w środowisku Integration Runtime, w tym wyszukiwanie, GetMetadata i DELETE.</small>| 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne operacje tworzenia na subskrypcję na [region Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Including Testuj połączenie, przeglądać listę folderów i listę tabel, Podgląd danych. | 200 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne użycie jednostek integracji danych<sup>1</sup> na subskrypcję na [region Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupa regionów 1<sup>2</sup>: 6000<br>Grupa regionów 2<sup>2</sup>: 3000<br>Grupa regionów 3<sup>2</sup>: 1500 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna liczba działań na potok, które obejmują działania wewnętrzne dla kontenerów | 40 | 40 |
| Maksymalna liczba połączonych środowisk Integration Runtime, które można utworzyć dla pojedynczego środowiska Integration Runtime (własne środowisko uruchomieniowe) | 100 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna liczba parametrów na potok | 50 | 50 |
| Elementy ForEach | 100 000 | 100 000 |
| Równoległość ForEach | 20 | 50 |
| Liczba znaków na wyrażenie | 8192 | 8192 |
| Minimalny interwał wyzwalania okna wirowania | 15 minut | 15 minut |
| Maksymalny limit czasu dla uruchomień działania potoku | 7 dni | 7 dni |
| Bajtów na obiekt dla obiektów potoku<sup>3</sup> | 200 KB | 200 KB |
| Bajtów na obiekt dla zestawu danych i połączonych obiektów usługi<sup>3</sup> | 100 KB | 2 000 KB |
| Jednostki integracji danych<sup>1</sup> na uruchomienie działania kopiowania | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wywołania interfejsu API zapisu | 1200/h<br/><br/> Ten limit jest nakładany przez Azure Resource Manager, a nie Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Odczytaj wywołania interfejsu API | 12500/h<br/><br/> Ten limit jest nakładany przez Azure Resource Manager, a nie Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zapytania monitorowane na minutę | 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba operacji jednostki CRUD na minutę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalny czas trwania sesji debugowania przepływu danych | 8 godzin | 8 godzin |
| Współbieżna liczba przepływów danych na fabrykę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżna liczba sesji debugowania przepływu danych na użytkownika na fabrykę | 3 | 3 |
| Limit czasu wygaśnięcia Azure IR przepływu danych | 4 godziny | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> jednostka integracji danych (DIU) jest używana w operacji kopiowania z chmury do chmury, Dowiedz się więcej z [jednostek integracji danych (wersja 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [Cennik usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) jest [dostępna globalnie](https://azure.microsoft.com/global-infrastructure/services/) w celu zapewnienia zgodności danych, wydajności i obniżenia kosztów ruchu wychodzącego w sieci. 

| Grupa regionów | Regiony | 
| -------- | ------ |
| Grupa regionów 1 | Środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia, zachodnie stany USA, zachodnie stany USA 2 |
| Grupa regionów 2 | Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Indie Środkowe, Japonia Wschodnia, Northcentral US, Southcentral US, Azja Południowo-Wschodnia, zachodnio-środkowe stany USA |
| Grupa regionów 3 | Kanada środkowa, Azja Wschodnia, Francja środkowa, Korea środkowa, Południowe Zjednoczone Królestwo |

<sup>3</sup> obiekty potoków, zestawów danych i połączone usługi reprezentują logiczne grupowanie obciążeń. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenosić i przetwarzać za pomocą Azure Data Factory. Data Factory jest zaprojektowany do skalowania do obsługi petabajtów danych.

### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Fabryki danych w ramach subskrypcji platformy Azure |50 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potoki w fabryce danych |2 500 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zestawy danych w ramach fabryki danych |5000 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wycinki współbieżne na zestaw danych |10 |10 |
| Bajtów na obiekt dla obiektów potoku<sup>1</sup> |200 KB |200 KB |
| Bajtów na obiekt dla zestawu danych i obiektów połączonych usług<sup>1</sup> |100 KB |2 000 KB |
| Rdzenie klastra na żądanie usługi Azure HDInsight w ramach subskrypcji<sup>2</sup> |60 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba jednostek przenoszenia danych w chmurze na działanie kopii w ramach uruchomienia<sup>3</sup> |32 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba ponownych prób dla uruchomień działania potoku |1000 |MaxInt (32 bit) |

<sup>1</sup> potok, zestaw danych i obiekty połączonej usługi reprezentują logiczne grupowanie obciążeń. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenosić i przetwarzać za pomocą Azure Data Factory. Data Factory jest zaprojektowany do skalowania do obsługi petabajtów danych.

<sup>2</sup> rdzenie usługi HDInsight na żądanie są przyłączone do subskrypcji zawierającej fabrykę danych. W związku z tym poprzedni limit to wymuszony w Data Factory limit rdzeni dla rdzeni usługi HDInsight na żądanie. Jest ona różna od limitu podstawowego skojarzonego z subskrypcją platformy Azure.

<sup>3</sup> jednostka przenoszenia danych w chmurze (DMU) dla wersji 1 jest używana w operacji kopiowania z chmury do chmury, Dowiedz się więcej z [jednostek przenoszenia danych w chmurze (wersja 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [Cennik usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Zasób** | **Dolny limit domyślny** | **Minimalny limit** |
| --- | --- | --- |
| Interwał planowania |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Wartość limitu czasu ponawiania |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity wywołań usługi sieci Web
Azure Resource Manager ma limity wywołań interfejsu API. Wywołania interfejsu API można wykonywać z częstotliwością [Azure Resource Manager limitów interfejsu API](../articles/azure-subscription-service-limits.md#resource-group-limits).
