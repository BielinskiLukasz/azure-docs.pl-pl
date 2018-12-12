---
title: Projekt aplikacji iteracji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Usługa LUIS uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e3472258cacea4ad0d9d8467d0cd06822a3acc6b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090503"
---
# <a name="authoring-cycle"></a>Cykl tworzenia
Usługa LUIS uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań. 

![Cykl tworzenia](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Budowanie modelu usługi LUIS
Model ma na celu ustalenie użytkownika pyta o (zamiar lub intencje) i które części pytanie podaj szczegóły (jednostki), pomagające ustalić odpowiedź. 

Model musi być specyficzne dla domeny aplikacji, aby określić słów i określającego, czy są odpowiednie, a także typowe word kolejności. 

Ten model zawiera intencje i podmioty. 

## <a name="add-training-examples"></a>Dodaj przykłady szkolenia
Usługa LUIS musi przykład wypowiedzi w intencji. Przykłady muszą wystarczająco dużo odmianą word wybór i kolejność słów, aby można było określić, który zamiar wypowiedź jest przeznaczona dla. Wypowiedź każdy przykład musi mieć wszystkie wymagane dane oznaczone jako jednostki. 

Poinstruowanie LUIS w celu ignorowania wypowiedzi, które nie mają znaczenia dla domeny Twojej aplikacji, przypisując wypowiedź do **Brak** intencji. Wszelkie słów i fraz, których nie potrzebujesz ściągane poza wypowiedź nie trzeba mieć etykietę. Brak jest etykiety słów i fraz, aby zignorować. 

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
Po utworzeniu wypowiedzi różnych 10 do 15 w każdym polu opcje przy użyciu wymaganych jednostek etykietą, uczenie i publikowanie. Z powiadomienie o pomyślnej publikacji użyj linku, aby pobrać punktów końcowych. Upewnij się, że tworzenie aplikacji i publikowanie aplikacji, dlatego, że jest on dostępny w [regionów punktu końcowego](luis-reference-regions.md) potrzebujesz. 

## <a name="https-endpoint-testing"></a>Testowanie punktu końcowego protokołu HTTPS
Możesz przetestować aplikacją usługi LUIS z punktu końcowego protokołu HTTPS. Testowanie z punktu końcowego umożliwia LUIS wybrać wypowiedzi o niskim poziomie pewności dla przeglądu.  

## <a name="recycle"></a>Odtwarzanie
Gdy skończysz, z cyklem tworzenia, możesz rozpocząć ponownie. Rozpoczynać przeglądanie wypowiedzi punktu końcowego, LUIS oznaczone pewnie niski. Sprawdź te wypowiedzi zarówno intencji i jednostki. Po przejrzeniu wypowiedzi, przejrzyj listę powinien być pusty.  

## <a name="batch-testing"></a>Testowanie wsadowe
Testowanie usługi Batch jest sposób, aby zobaczyć, ile wypowiedzi przykład są oceniane przez usługi LUIS. Przykłady powinno być jesteś nowym użytkownikiem usługi LUIS i powinna być poprawnie oznaczone intencji i chcesz, aby usługa LUIS można znaleźć jednostki. Wyniki testu wskazują, jak w tym zestawie wypowiedzi wykona usługi LUIS. 

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [współpracy](luis-concept-collaborator.md).