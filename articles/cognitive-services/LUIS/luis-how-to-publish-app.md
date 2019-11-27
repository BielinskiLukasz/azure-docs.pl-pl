---
title: Publikowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Po zakończeniu tworzenia i testowania aplikacją usługi LUIS active był dostępny do aplikacji klienckiej, publikując je do punktu końcowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221718"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji do tymczasowego lub produkcyjnego punktu końcowego

Po zakończeniu kompilowania, uczenia i testowania aktywnej aplikacji LUIS Udostępnij ją aplikacji klienckiej, publikując ją w punkcie końcowym. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publikowanie

1. Aby opublikować w punkcie końcowym, wybierz pozycję **Publikuj** w prawym górnym panelu. 

    ![Przycisk Publikuj na górze, prawy pasek nawigacyjny](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Wybierz ustawienia dla opublikowanego punktu końcowego przewidywania, a następnie wybierz pozycję **Publikuj**.

    ![Wybierz pozycję Publikuj ustawienia, a następnie wybierz przycisk Publikuj.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publikowanie miejsc

Zaznacz odpowiednie miejsce po wyświetleniu okna podręcznego: 

* Przygotowanie
* Produkcja 

Korzystając z obu miejsc publikowania, można w ten sposób mieć dwie różne wersje aplikacji dostępnych w opublikowanych punktach końcowych lub w tej samej wersji w dwóch różnych punktach końcowych. 

### <a name="publishing-regions"></a>Publikowanie regionów

Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami punktu końcowego przewidywania LUIS dodanych w portalu LUIS na stronie **zarządzaj** ->  **[zasobów platformy Azure](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** . 

Na przykład dla aplikacji utworzonej w usłudze [www.Luis.AI](https://www.luis.ai), jeśli UTWORZYSZ zasób Luis w dwóch regionach, **zachodnim** i **wschodnim**, a następnie dodasz je do aplikacji jako zasoby, aplikacja zostanie opublikowana w obu regionach. Aby uzyskać więcej informacji na temat regionów LUIS, zobacz [regiony](luis-reference-regions.md).

> [!TIP]
> Istnieją 3 regiony tworzenia. Należy utworzyć w regionie, w którym ma zostać opublikowana. Jeśli zachodzi potrzeba opublikowania we wszystkich regionach, należy zarządzać procesem tworzenia i modelem, który został przeszkolony, we wszystkich 3 regionach tworzenia. 


## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Po wybraniu miejsca Skonfiguruj ustawienia publikowania dla:

* Analiza tonacji
* Korekcja pisowni — tylko punkt końcowy przewidywania w wersji 2
* Napełnianiu mowy 

Po opublikowaniu te ustawienia są dostępne do przeglądu na stronie **ustawień publikowania** sekcji **Zarządzanie** . Można zmienić ustawienia za pomocą każdej publikacji. Jeśli anulujesz publikowanie, wszelkie zmiany wprowadzone podczas publikowania również zostaną anulowane. 

### <a name="when-your-app-is-published"></a>Po opublikowaniu aplikacji

Po pomyślnym opublikowaniu aplikacji w górnej części przeglądarki pojawia się powiadomienie o powodzeniu. Powiadomienie zawiera również link do punktów końcowych. 

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz łącze. Możesz również przejść do adresów URL punktów końcowych, wybierając pozycję **Zarządzaj** w górnym menu, a następnie wybierając pozycję **zasoby platformy Azure** w menu po lewej stronie. 

## <a name="sentiment-analysis"></a>Analiza tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia integrację LUIS z [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) w celu zapewnienia analizy tonacji i kluczowych fraz. 

Nie trzeba podać klucz analizy tekstu, a nie są pobierane opłaty rozliczeń dla tej usługi do konta platformy Azure. 

Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych. Etykieta tonacji `positive`, `neutral`i `negative` jest na obsługiwaną kulturę. Obecnie tylko język angielski obsługuje etykiety tonacji. 

Aby uzyskać więcej informacji na temat odpowiedzi punktu końcowego JSON z analizą tonacji, zobacz [tonacji Analysis](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Poprawianie pisowni

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Poprawki pisowni są wprowadzane przed przewidywaniem LUIS użytkownika wypowiedź. W odpowiedzi można zobaczyć zmiany w oryginalnym wypowiedź, w tym pisownię.

## <a name="speech-priming"></a>Napełnianiu mowy

Speech napełnianiu to proces polegający na wysyłaniu modelu LUIS do usługi mowy przed konwersją tekstu na mowę. Dzięki temu usługa mowy może dokładniej zapewnić konwersję mowy dla modelu. Umożliwia to bot LUIS i reagowanie żądań i odpowiedzi w jednym wywołaniu przez jedno wywołanie mowy i przejęcie odpowiedzi na LUIS. Zapewnia ona mniej czasu na ogół.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Zarządzanie](./luis-how-to-azure-subscription.md) kluczami, aby dodać klucze do klucza subskrypcji platformy Azure do Luis i jak ustawić klucz sprawdzanie pisowni Bing i uwzględnić wszystkie intencje w wynikach.
* Aby uzyskać instrukcje dotyczące testowania opublikowanej aplikacji w konsoli testowej, zobacz temat [uczenie i testowanie aplikacji](luis-interactive-test.md) .

