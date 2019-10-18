---
title: Omówienie dedykowanych centrów zdarzeń — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera omówienie dedykowanych Event Hubs platformy Azure, które oferują wdrożenia z jedną dzierżawą centrów zdarzeń.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516749"
---
# <a name="overview-of-event-hubs-dedicated"></a>Omówienie Event Hubs — warstwa Dedykowana

*Klastry Event Hubs* oferują wdrożenia z jedną dzierżawą dla klientów z najbardziej wymaganymi potrzebami przesyłania strumieniowego. Ta oferta dla jednej dzierżawy jest objęta umową SLA gwarantującą dostępność na poziomie 99,99% i jest dostępna tylko w warstwie cenowej Dedykowana. Klaster Event Hubs może odbierać miliony zdarzeń na sekundę z gwarantowaną pojemnością i opóźnieniami podrzędnymi. Przestrzenie nazw i centra zdarzeń utworzone w dedykowanym klastrze obejmują wszystkie funkcje standardowej oferty i wiele innych, ale bez ograniczeń związanych z transferem danych przychodzących. Zawiera również popularne funkcje [przechwytywania Event Hubs](event-hubs-capture-overview.md) bez dodatkowych kosztów, co pozwala na automatyczne tworzenie partii i rejestrowanie strumieni danych w usłudze Azure Storage lub Azure Data Lake. 

Klastry są udostępniane i są rozliczane według **jednostek pojemności**, wstępnie przydzielonej ilości zasobów procesora i pamięci. W każdym klastrze można zakupić 1, 2, 4, 8, 12, 16 lub 20 jednostek. Ile można pozyskiwania i przesyłania strumieniowego na CU, zależy od różnych czynników, takich jak liczba producentów i odbiorców, kształt ładunku, szybkość ruchu wychodzącego (Zobacz wyniki testów porównawczych poniżej, aby uzyskać więcej informacji). 

> [!NOTE]
> Wszystkie klastry Event Hubs są domyślnie włączone i obsługują punkty końcowe Kafka, które mogą być używane przez istniejące aplikacje oparte na Kafka. Funkcja Kafka włączona w klastrze nie wpływa na przypadki użycia poza Kafkaem; nie ma opcji ani nie trzeba wyłączać Kafka w klastrze.

## <a name="why-dedicated"></a>Dlaczego są dedykowane?

Dedykowane Event Hubs oferują trzy atrakcyjne korzyści dla klientów, którzy potrzebują pojemności na poziomie przedsiębiorstwa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Gwarancja jednej dzierżawy w celu zapewnienia lepszej wydajności

Dedykowany klaster gwarantuje pojemność w pełnej skali i może przystąpić do gigabajtów danych przesyłanych strumieniowo za pomocą w pełni trwałego magazynu i opóźnień podrzędnych w celu uwzględnienia dowolnego obciążenia w ruchu. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Włączny i wyłączny dostęp do funkcji 
Oferta dedykowana obejmuje funkcje, takie jak przechwytywanie bez dodatkowych kosztów, a także wyłączny dostęp do przyszłych funkcji, takich jak Bring Your Own Key (BYOK). Usługa zarządza również równoważeniem obciążenia, aktualizacjami systemu operacyjnego, poprawkami zabezpieczeń i partycjonowaniem klienta, dzięki czemu można poświęcać mniej czasu na konserwację infrastruktury i więcej czasu na tworzenie funkcji po stronie klienta.  

#### <a name="cost-savings"></a>Oszczędności kosztów
W przypadku dużych ilości danych wejściowych (> 100 TUs) klaster postanowił znacznie mniej na godzinę niż zakup porównywalnej liczby jednostek przepływności w ramach oferty standardowej.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Przydziały i limity Event Hubs — warstwa Dedykowana

W przypadku oferty Event Hubs — warstwa Dedykowana jest naliczana stała cena miesięczna, a co najmniej 4 godziny użytkowania. Warstwa dedykowana oferuje wszystkie funkcje planu Standard, ale z możliwością skalowania w przedsiębiorstwie i limitami dla klientów wymagających obciążeń. 

| Funkcja | Standardowa (Standard) | Dedykowany |
| --- |:---:|:---:|
| Przepustowość | 20 TUs (do 40 TUs) | 20 jednostek |
| Przestrzenie nazw |  1 | 50 na CU |
| Centra zdarzeń |  10 na przestrzeń nazw | 1000 na przestrzeń nazw |
| Zdarzenia związane z transferem danych przychodzących | Płatność za milion zdarzeń | Zawarte |
| Rozmiar komunikatu | 1 000 000 bajtów | 1 000 000 bajtów |
| Partycje | 32 za centrum zdarzeń | 1024 za centrum zdarzeń |
| Grupy klientów | 20 na centrum zdarzeń | Brak limitu na wartość CU, 1000 na centrum zdarzeń |
| Połączenia obsługiwane przez brokera | 1 000, maksymalnie 5 000 | 100 K uwzględnione i maks. |
| Przechowywanie komunikatów | 7 dni, 84 GB uwzględnionych na jednostek PRZEPŁYWNOŚCI | 90 dni, 10 TB uwzględnionych na CU |
| Przechwytywanie | Płatność za godzinę | Zawarte |

## <a name="how-to-onboard"></a>Jak dołączyć

Samoobsługowe środowisko [tworzenia klastra Event Hubs](event-hubs-dedicated-cluster-create-portal.md) za pomocą witryny [Azure Portal](https://aka.ms/eventhubsclusterquickstart) jest teraz w wersji zapoznawczej. Jeśli masz jakieś pytania lub potrzebujesz pomocy przy dołączaniu do Event Hubs — warstwa Dedykowana, skontaktuj się z [zespołem Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Często zadawane pytania

#### <a name="what-can-i-achieve-with-a-cluster"></a>Co można osiągnąć przy użyciu klastra?

W przypadku klastra Event Hubs, jak dużo możliwości pozyskiwania i przesyłania strumieniowego zależy od różnych czynników, takich jak producenci, konsumenci, szybkość pozyskiwania i przetwarzania oraz wiele innych. 

W poniższej tabeli przedstawiono wyniki testów porównawczych, które zostały osiągnięte podczas testowania:

| Kształt ładunku | Odbiorników | Przepustowość ruchu przychodzącego| Komunikaty dotyczące transferu danych przychodzących | Przepustowość ruchu wychodzącego | Komunikaty wychodzące | Łącznie TUs | TUs na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | komunikaty 400 000/s | 800 MB/s | komunikaty 800k/s | 400 TUs | 100 TUs | 
| Partie 10x10KB | 2 | 666 MB/s | 66.6 k komunikatów/s | 1,33 GB/s | komunikaty 133k/s | 666 TUs | 166 TUs |
| Partie 6x32KB | 1 | 1,05 GB/s | komunikaty 34k/s | 1,05 GB/s | komunikaty 34k/s | 1000 TUs | 250 TUs |

Podczas testowania użyto następujących kryteriów:

- Użyto dedykowanego klastra Event Hubs z czterema jednostkami pojemności (CUs). 
- Centrum zdarzeń używane na potrzeby pozyskiwania ma 200 partycji. 
- Dane, które zostały odebrane, są odbierane przez dwie aplikacje odbiornika odbierane ze wszystkich partycji.

#### <a name="can-i-scale-updown-my-cluster"></a>Czy mogę skalować w górę i w dół mój klaster?

Po utworzeniu opłaty za klastry są naliczane co najmniej 4 godziny użytkowania. W wersji zapoznawczej środowiska samoobsługowego można przesłać [żądanie pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) do zespołu Event Hubs w obszarze *przydziały > technicznych > żądania skalowania* klastra w górę lub w dół w celu skalowania klastra w górę lub w dół. Ukończenie żądania skalowania klastra może potrwać do 7 dni. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Jak będzie współdziałać z moim klastrem za pomocą programu Geo-DR?

Można sparować geograficznie przestrzeń nazw w klastrze dedykowanym z inną przestrzenią nazw w klastrze dedykowanym. Nie zachęcamy do kojarzenia przestrzeni nazw dedykowanej warstwy z przestrzenią nazw w naszej ofercie standardowej, ponieważ ograniczenie przepływności będzie niezgodne, co spowoduje błędy. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Czy mogę migrować moje standardowe przestrzenie nazw, które mają należeć do klastra warstwy dedykowanej?
Obecnie nie obsługujemy zautomatyzowanego procesu migracji danych centrów zdarzeń z standardowej przestrzeni nazw do dedykowanej. 

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z przedstawicielem handlowym firmy Microsoft lub pomoc techniczna firmy Microsoft, aby uzyskać dodatkowe informacje dotyczące Event Hubs — warstwa Dedykowana. Możesz również utworzyć klaster lub dowiedzieć się więcej na temat Event Hubs warstw cenowych, odwiedzając następujące linki:

- [Tworzenie klastra Event Hubs za pomocą witryny Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Cennik Event Hubs — warstwa dedykowana](https://azure.microsoft.com/pricing/details/event-hubs/). Możesz również skontaktować się z przedstawicielem handlowym firmy Microsoft lub pomoc techniczna firmy Microsoft, aby uzyskać dodatkowe informacje dotyczące Event Hubs — warstwa Dedykowana pojemności.
- [Event Hubs często zadawane pytania](event-hubs-faq.md) zawierają informacje o cenach i odpowiedzi na kilka często zadawanych pytań dotyczących Event Hubs.
