---
title: Pulpit nawigacyjny aplikacji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dowiedz się więcej na temat pulpitu nawigacyjnego aplikacji narzędziu do raportowania zwizualizowanego, która umożliwia monitorowanie aplikacji w jednym skrócie.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: fa5560c008270635d0d93b8004e017c810c9c27a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074422"
---
# <a name="application-dashboard"></a>Pulpit nawigacyjny aplikacji
Pulpit nawigacyjny aplikacji umożliwia monitorowanie aplikacji w jednym skrócie. **Pulpit nawigacyjny** są wyświetlane po otwarciu aplikacji, klikając nazwę aplikacji **Moje aplikacje** stronie wybierz **pulpit nawigacyjny** z górnym panelu. 

> [!CAUTION]
> Jeśli chcesz najbardziej aktualne metryki dla usługi LUIS, należy:
> * Użyj usługi LUIS [klucza punktu końcowego](luis-how-to-azure-subscription.md) tworzenia na platformie Azure
> * Użyj klucza punktu końcowego usługi LUIS dla wszystkich żądań punktu końcowego, w tym usługi LUIS [API](https://aka.ms/luis-endpoint-apis) i bot
> * Użyj klucza innym punktem końcowym dla każdej aplikacji usługi LUIS. Nie należy używać klucza pojedyncze punkty końcowe dla wszystkich aplikacji. Klucza punktu końcowego są śledzone na poziomie klucza, nie na poziomie aplikacji.  

**Pulpit nawigacyjny** strona zawiera omówienie aplikacji LUIS, w tym modelu bieżącego stanu, a także [punktu końcowego](luis-glossary.md#endpoint) użycia wraz z upływem czasu. 
  
## <a name="app-status"></a>Stan aplikacji
Pulpit nawigacyjny Wyświetla szkolenia aplikacji i stan, w tym datę i godzinę, kiedy aplikacja ostatniego publikowania wiedzę i opublikowane.  

![Pulpit nawigacyjny — stan aplikacji](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statystyki danych modelu
Pulpit nawigacyjny wyświetla łączną liczbę intencji, jednostek i etykietami wypowiedzi istniejące w aplikacji. 

![Statystyki danych aplikacji](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Trafienia punktu końcowego
Pulpit nawigacyjny Wyświetla trafień całkowita punktu końcowego, otrzymuje aplikacji usługi LUIS, które umożliwia do wyświetlenia: liczba trafień w przedziale czasu, określ. Całkowita liczba trafień, wyświetlany jest sumą trafień punktu końcowego, które używają [klucza punktu końcowego](./luis-concept-keys.md#endpoint-key) i punktu końcowego trafienia używające [Tworzenie klucza](./luis-concept-keys.md#authoring-key).

![Trafienia punktu końcowego](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Liczba trafień najbardziej bieżący punkt końcowy znajduje się w witrynie Azure portal w obszarze Przegląd usługi LUIS. 
 
### <a name="total-endpoint-hits"></a>Punkt końcowy łączna liczba trafień
Łączna liczba trafień punktu końcowego odebrane do swojej aplikacji od utworzenia aplikacji poprzedzającym bieżącą datę.

### <a name="endpoint-hits-per-period"></a>Trafienia punktu końcowego dla okresu
Liczba trafień otrzymał w ciągu ostatnich wyświetlanych na dzień. Punkty od daty rozpoczęcia i zakończenia reprezentują dni, w tym okresie. Umieść wskaźnik myszy nad każdego punktu, aby zobaczyć liczby trafień, w każdego dnia w okresie. 

Aby wybrać okres, aby wyświetlić na wykresie:
 
1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowych ustawień](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostępu do listy okresów. Możesz wybrać okresy od jednego tygodnia się na jeden rok. 

    ![Trafienia punktu końcowego dla okresu](./media/luis-how-to-use-dashboard/timerange.png)

2. Wybierz okres z listy, a następnie kliknij strzałkę Wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić wykres.

### <a name="key-usage"></a>Użycie klucza
Liczba trafień używane z klucza punktu końcowego aplikacji. Aby uzyskać więcej informacji na temat kluczy punktu końcowego, zobacz [kluczy w LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Podział intencji
**Podział intencji** przedstawia podział intencji na podstawie wypowiedzi etykietą lub trafienia punktu końcowego. Ten wykres podsumowania przedstawia względne znaczenie każdego intencji w aplikacji. Po umieszczeniu wskaźnika myszy nad wycinek zostanie wyświetlony intencji nazwy i którą reprezentuje procent całkowitej liczby trafień etykietami wypowiedzi/punktu końcowego. 

![Podział intencji](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Aby kontrolować, czy podział jest oparta na wypowiedzi etykietą lub trafienia punktu końcowego:

1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowych ustawień](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostępu do listy, jak na poniższej ilustracji:

    ![Lista opcji podziału](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Wybierz wartość z listy, a następnie kliknij przycisk strzałki wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić wykres.

## <a name="entity-breakdown"></a>Podział jednostki
Pulpit nawigacyjny przedstawia podział jednostki na podstawie wypowiedzi etykietą lub trafienia punktu końcowego. Ten wykres podsumowania przedstawia względnego każdej jednostki w aplikacji. Po umieszczeniu wskaźnika myszy nad wycinek zostanie wyświetlony, nazwa podmiotu i wartość procentowa w trafień etykietami wypowiedzi/punktu końcowego. 

![Podział jednostki](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Aby kontrolować, czy podział jest oparta na wypowiedzi etykietą lub trafienia punktu końcowego:

1. Kliknij przycisk **dodatkowe ustawienia** ![przycisk dodatkowych ustawień](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) dostępu do listy, jak na poniższej ilustracji:

    ![Lista podział jednostek](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Wybierz wartość z listy, a następnie kliknij przycisk strzałki wstecz ![Strzałka wstecz](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Aby wyświetlić wykres odpowiednio.
