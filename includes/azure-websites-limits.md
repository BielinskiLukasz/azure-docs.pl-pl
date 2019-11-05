---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 1f08067768f2d4a0a0c2ab31b3db1c9f2c9e1521
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476928"
---
| Zasób | Bezpłatna | Udostępnione | Podstawowa | Standardowa | Premium (v2) | Izolowane </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplikacje internetowe, mobilne lub API](https://azure.microsoft.com/services/app-service/) na [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup> |Bez ograniczeń<sup>2</sup>|
| [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) |10 na region |10 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów |100 na grupę zasobów|
| Typ wystąpienia obliczeniowego |Udostępnione |Udostępnione |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup> |Dedykowane<sup>3</sup></p> |Dedykowane<sup>3</sup>|
| [Skalowanie w poziomie](../articles/app-service/manage-scale-up.md) (maksymalna liczba wystąpień) |1 udostępniony |1 udostępniony |3 dedykowane<sup>3</sup> |10 dedykowanych<sup>3</sup> |20 dedykowanych<sup>3</sup>|100 dedykowany<sup>4</sup>|
| Magazyn<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Czas procesora CPU (5 minut)<sup>6</sup> |3 minuty |3 minuty |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a>|
| Czas procesora CPU (dzień)<sup>6</sup> |60 minut |240 minut |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |Bez ograniczeń, płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/) standardowych</a> |
| Pamięć (1 godzina) |1 024 MB na plan App Service |1 024 MB na aplikację |Nie dotyczy |Nie dotyczy |Nie dotyczy |Nie dotyczy |
| Przepustowość |165 MB |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |Nieograniczone, [opłaty za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/) są naliczane |
| Architektura aplikacji |32 — bit |32 — bit |32-bitowa/64-bitowa |32-bitowa/64-bitowa |32-bitowa/64-bitowa |32-bitowa/64-bitowa |
| Gniazda sieci Web na wystąpienie<sup>7</sup> |5 |35 |350 |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
| Połączenia IP | 600 | 600 | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | Zależy od rozmiaru wystąpienia<sup>8</sup> | 64 000 |
| Współbieżne [połączenia debugera](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikację |1 |1 |1 |5 |5 |5 |
| App Service certyfikatów na subskrypcję<sup>9</sup>| Brak obsługi | Brak obsługi |10 |10 |10 |10 |
| Domeny niestandardowe na</a> aplikacji |0 (tylko poddomena azurewebsites.net)|500 |500 |500 |500 |500 |
| [Obsługa protokołu SSL](../articles/app-service/configure-ssl-certificate.md) domeny niestandardowej |Nieobsługiwane, certyfikat wieloznaczny dla *. azurewebsites.net jest domyślnie dostępny|Nieobsługiwane, certyfikat wieloznaczny dla *. azurewebsites.net jest domyślnie dostępny|Nieograniczone połączenia SNI SSL |Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń |Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń | Uwzględniono nieograniczone SNI SSL i 1 Połączenie SSL z adresu IP połączeń|
| Połączenia hybrydowe na plan | | | 5 | 25 | 200 | 200 |
| Zintegrowana usługa równoważenia obciążenia | |X |X |X |X |X<sup>10</sup> |
| [Zawsze włączone](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Zaplanowane kopie zapasowe](../articles/app-service/manage-backup.md) | | | | Zaplanowane kopie zapasowe co 2 godziny, maksymalnie 12 kopii zapasowych dziennie (ręczna + zaplanowana) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczna + zaplanowana) | Zaplanowane kopie zapasowe co godzinę, maksymalnie 50 kopii zapasowych dziennie (ręczna + zaplanowana) |
| [Skalowanie automatyczne](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Zadania WebJob](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| Obsługa [usługi Azure Scheduler](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitorowanie punktu końcowego](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Miejsca przejściowe](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Umowa SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup> Aplikacje i przydziały magazynu są na App Service plan, chyba że zaznaczono inaczej.  
<sup>2</sup> Rzeczywista liczba aplikacji, które można hostować na tych komputerach, zależy od aktywności aplikacji, rozmiaru wystąpień maszyn i odpowiedniego wykorzystania zasobów.  
<sup>3</sup> Wystąpienia dedykowane mogą mieć różne rozmiary. Aby uzyskać więcej informacji, zobacz [Cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Więcej informacji można uzyskać na żądanie.  
<sup>5</sup> Limit magazynowania to łączny rozmiar zawartości dla wszystkich aplikacji w ramach tego samego planu usługi App Service. Łączny rozmiar zawartości wszystkich aplikacji we wszystkich planach usługi App Service w ramach jednej grupy zasobów i regionu nie może przekroczyć 500 GB.  
<sup>6</sup> Te zasoby są ograniczone przez zasoby fizyczne w dedykowanych wystąpieniach (rozmiar wystąpienia i liczba wystąpień).  
<sup>7</sup> W przypadku skalowania aplikacji w warstwie Podstawowa do dwóch wystąpień dla każdego z tych dwóch wystąpień masz 350 połączeń współbieżnych. W przypadku warstwy Standardowa i wyższych nie ma teoretycznych limitów dla gniazd sieci Web, ale inne czynniki mogą ograniczyć liczbę gniazd sieci Web. Na przykład maksymalna dozwolona liczba równoczesnych żądań (zdefiniowanych przez `maxConcurrentRequestsPerCpu`) to: 7 500 na małą maszynę wirtualną, 15 000 na średnią maszynę wirtualną (7 500 x 2 rdzenie) i 75 000 za dużą maszynę wirtualną 18 750 (rdzenie x 4).  
<sup>8</sup> Maksymalna liczba połączeń IP jest zależna od wystąpienia i zależy od rozmiaru wystąpienia: 1 920 na wystąpienie B1/S1/P1V2, 3 968 per B2/S2/P2V2, 8 064 dla wystąpienia B3/S3/P3V2.  
<sup>9</sup> Limit przydziału Certyfikat usługi App Service na subskrypcję można zwiększyć przez żądanie obsługi do limitu maksymalnego 200.  
<sup>10</sup> Jednostki SKU izolowana usługa App Service mogą być funkcją wewnętrznego równoważenia obciążenia (ILB) z Azure Load Balancer, dzięki czemu nie ma połączenia publicznego z Internetu. Z tego powodu niektóre funkcje izolowanej usługi App Service z obciążeniem równoważonym wewnętrznie muszą być używane z poziomu maszyn mających bezpośredni dostęp do punktu końcowego sieci modułu wewnętrznego równoważenia obciążenia.  
<sup>11</sup> Uruchamiaj niestandardowe pliki wykonywalne i/lub skrypty na żądanie, zgodnie z harmonogramem lub w sposób ciągły jako zadanie w tle w ramach wystąpienia App Service. Do ciągłego wykonywania zadań w sieci Web jest wymagana opcja Zawsze włączona. Do wykonywania zadań w sieci Web według harmonogramu jest wymagana usługa Azure Scheduler w warstwie Bezpłatna lub Standardowa. Brak wstępnie zdefiniowanego limitu liczby zadań WebJob, które mogą być uruchamiane w wystąpieniu App Service. Istnieją praktyczne ograniczenia, które zależą od tego, co próbuje wykonać kod aplikacji.  