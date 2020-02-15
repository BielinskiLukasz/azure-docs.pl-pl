---
title: Wybierz odpowiednią warstwę cenową | Mapy Microsoft Azure
description: W tym artykule znajdziesz informacje o warstwach cenowych oferowanych przez Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 048ecdc9b96edcdebecb4cd7df0630aa991aa4d1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210076"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w Azure Maps

Azure Maps oferuje dwie warstwy cenowe, S0 i S1. Celem tego artykułu jest ułatwienie wyboru odpowiedniej warstwy cenowej dla potrzeb użytkownika. Aby wybrać odpowiednią warstwę cenową, należy zadać sobie poniższe dwa pytania.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ile współbieżnych użytkowników planuję obsługiwać? 
Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę**. Natomiast warstwa S1 obsługuje **więcej niż 50 zapytań na sekundę**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie możliwości geoprzestrzenne są planowane do użycia?
Warstwa cenowa S0 jest dla Ciebie odpowiednia, jeśli podstawowe interfejsy API geoprzestrzennej spełniają wymagania dotyczące usługi. Jeśli chcesz uzyskać bardziej zaawansowane możliwości aplikacji, rozważ rezygnację z warstwy cenowej S1. Zaawansowane możliwości obejmują: połączenie lotnicze oraz zdjęcia hybrydowe, uzyskiwanie zakresu tras i geokodowanie partii. Zapoznaj się z tabelą **możliwości warstwy cenowej** , aby wybrać warstwę cenową, która jest najbardziej odpowiednia dla Twojej aplikacji.

### <a name="pricing-tier-capabilities"></a>Możliwości warstwy cenowej

| Możliwości                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderowanie mapy                              | ✓                   | ✓       |
| Obraz satelitarny                       |                     | ✓        |
| Wyszukiwanie                                  | ✓                    | ✓        |
| Wyszukiwanie wsadowe                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Routing usługi Batch                            |                    | ✓        |
| Routing macierzy                          |                     | ✓        |
| Zakres tras (izochron)                |                     | ✓        |
| Ruch                                |✓                    |✓        |
| Strefa czasowa                               |✓                    |✓        |
| Geolokalizacja (wersja zapoznawcza)                    |✓                   |✓        |
| Operacje przestrzenne                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dane Azure Maps (wersja zapoznawcza)                |                     | ✓        |
| Mobilność (wersja zapoznawcza)                       |                     | ✓        |
| Pogoda (wersja zapoznawcza)                        |✓                    |✓        |

Należy wziąć pod uwagę następujące dodatkowe punkty:
* Jakiego typu przedsiębiorstwa masz?
* Jak jest krytyczna aplikacja?

### <a name="pricing-tier-targeted-customers"></a>Zamierzone odbiorcy warstwy cenowej

Zapoznaj się z tabelą **docelową dla klientów warstwy cenowej** , aby lepiej poznać warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Warstwa cenowa  |     Dokierowany klienci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 działa w przypadku aplikacji na wszystkich etapach produkcji: od projektowania rozwiązań do tworzenia koncepcji i wczesnego etapu testowania do produkcji i wdrażania aplikacji. Jednak ta warstwa została zaprojektowana na potrzeby rozwoju małych lub klientów, którzy mają mniej równoczesnych użytkowników lub obie te funkcje. <p>|
| S1            |    <p>Warstwa cenowa S1 jest przeznaczony dla klientów, którzy mają duże aplikacje dla przedsiębiorstw, aplikacje o znaczeniu krytycznym lub duże ilości równoczesnych użytkowników. Jest on również przeznaczony dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.</p>|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie wyświetlania i zmieniania warstw cenowych:

> [!div class="nextstepaction"] 
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
