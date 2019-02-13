---
title: Wybrać właściwą warstwę cenową dla usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oferowanych przez usługi Azure Maps warstw cenowych
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 56f9226f1bee630895725eb0b3806e294e9a5b51
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218146"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybrać właściwą warstwę cenową w usługi Azure Maps

Usługi Azure Maps oferuje dwie warstwy cenowe. Ten artykuł ma na celu pomóc wybrać właściwą ceny dla Twoich potrzeb warstwę. Aby wybrać właściwą warstwę cenową, spróbuj odpowiedzieć sobie następujące dwa pytania.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie funkcje geoprzestrzenne chcę użyć?
Warstwa cenowa S0 jest dla Ciebie odpowiednia, jeśli dane geograficzne podstawowych interfejsów API spełniają Twoje wymagania usługi. Jeśli chcesz bardziej zaawansowane funkcje dla aplikacji, należy wziąć pod uwagę sposób na warstwę cenową S1. Przykład możliwości są areal plus obrazach hybrydowych, uzyskiwanie zakres z trasy i geokodowania usługi batch. **Ceny warstwy możliwości** tabeli znajdującej się poniżej pozwala lepiej zrozumieć wymagania Twojej aplikacji. Ułatwia on również wybrać warstwę cenową najbardziej odpowiedni dla aplikacji.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Jak wiele równoczesnych użytkowników zaplanować do obsługi? 
Warstwy cenowe S0 i S1 obsługiwać różne ilości przepływność danych. Przed wybraniem usługi Azure Maps warstwy cenowej, spróbuj odpowiedzieć sobie kilka pytań. Przykładem jest "jak wielu równoczesnych użytkowników I chcesz do pomocy technicznej?" Warstwa cenowa S0 obsługuje maksymalnie **50 zapytań na sekundę**. Uchwyty warstwę cenową S1 **więcej niż 50 zapytań na sekundę**.

### <a name="pricing-tier-capabilities"></a>Cennik warstwy możliwości

| Możliwości                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Wyszukiwanie                                  |        ✓           |     ✓    |
| Routing                                 |        ✓           |     ✓    |
| Renderowanie                                  |        ✓           |     ✓    |
| Ruch                                 |        ✓           |     ✓    |
| Strefy czasowe                              |        ✓           |     ✓    |
| Zdjęcia i obrazach hybrydowych    |            |     ✓    |
| Zakres z trasy                    |                   |     ✓    |
| Lokalizacja adresu IP 2 (wersja zapoznawcza)                |        ✓           |     ✓    |
| Wielokąty z wyszukiwania          |                   |     ✓    |
| Geokodowanie usługi Batch (wersja zapoznawcza)              |                   |     ✓    |
| Batch routingu (wersja zapoznawcza)                |                   |     ✓    |
| Tabela routingu (wersja zapoznawcza)               |                   |     ✓    |


Warto wspomnieć biorąc pod uwagę te dodatkowych punktów danych:
* Jakiego rodzaju przedsiębiorstwa mają?
* Jak bardzo krytyczna jest aplikacja kompilowanego na bieżąco?

Zobacz **warstwy cenowej docelowych klientów** tabeli, aby lepiej poznać S0 i S1 warstw cenowych. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Warstwa cenowa docelowych klientów

| Warstwa cenowa  |     Klientów docelowych                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 jest przeznaczona dla klientów, którzy przedsiębiorstwom małych i średnich. To prawo warstwy cenowej dla Ciebie, jeśli nie spodziewasz się dużej liczby równoczesnych użytkowników. Jest również odpowiednie, jeśli dane geograficzne podstawowych interfejsów API wyświetlane w powyższej tabeli spełniają Twoje wymagania usługi. Ta warstwa jest ogólnie dostępna. Działa to w przypadku aplikacji na wszystkich etapach produkcji: opracowywania weryfikacji koncepcji i wczesnym etapie testowania do produkcyjnych aplikacji i wdrożenia.<p>|
| S1            |    <p>Warstwę cenową S1 jest klientom potrzebującym obsługę dużych przedsiębiorstw, aplikacji o znaczeniu krytycznym lub dużej liczby równoczesnych użytkowników. Jest również klienci, którzy wymagają usług geoprzestrzennych zaawansowane.</p>|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tym, jak można wyświetlać i zmieniać warstwy cenowe:

> [!div class="nextstepaction"] 
> [Warstwa cenowa zarządzania](how-to-manage-pricing-tier.md)
