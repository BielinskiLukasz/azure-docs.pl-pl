---
title: Zbadaj zdarzenia za pomocą usługi Azure wskaźnikowej | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zbadać zdarzenia za pomocą platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 026689b68244052c3ad7cb50cd90d7319b6760ef
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71229419"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Samouczek: Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik

> [!IMPORTANT]
> Wykres badania jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ten samouczek ułatwia badanie zdarzeń za pomocą platformy Azure. Po nawiązaniu połączenia ze źródłami danych z platformą Azure — użytkownik chce otrzymywać powiadomienia o wystąpieniu podejrzanych sytuacji. Aby można było to zrobić, badanie wskaźnikowe platformy Azure umożliwia tworzenie zaawansowanych reguł alertów, które generują zdarzenia, które można przypisać i zbadać.

W tym artykule omówiono:
> [!div class="checklist"]
> * Badanie zdarzeń
> * Korzystanie z grafu badania
> * Reagowanie na zagrożenia

Zdarzenie może zawierać wiele alertów. Jest to agregacja wszystkich istotnych dowodów dla konkretnego badania. Zdarzenie jest tworzone na podstawie reguł analitycznych utworzonych na stronie **analizy** . Właściwości związane z alertami, takie jak ważność i stan, są ustawiane na poziomie incydentu. Gdy zezwolisz na platformę Azure, wiadomo, jakiego rodzaju zagrożeń szukasz, i jak je znaleźć, możesz monitorować wykryte zagrożenia, badając zdarzenia.

## <a name="prerequisites"></a>Wymagania wstępne
Po skonfigurowaniu reguły analitycznej można zbadać zdarzenie tylko w przypadku użycia pól mapowania jednostek. Wykres badania wymaga, aby oryginalne zdarzenie obejmowało jednostki.

## <a name="how-to-investigate-incidents"></a>Jak zbadać zdarzenia

1. Wybierz **zdarzenia**. Na ****  stronie incydenty są dostępne informacje o liczbie zdarzeń, liczbie otwartych, liczbie ustawionych **w toku**i liczbie zamkniętych. Dla każdego zdarzenia można zobaczyć czas jego wystąpienia i stan zdarzenia. Przyjrzyj się ważności, aby określić, które zdarzenia należy obsłużyć jako pierwsze.

    ![Wyświetl ważność zdarzenia](media/tutorial-investigate-cases/incident-severity.png)

1. Zdarzenia można filtrować zgodnie z wymaganiami, na przykład według stanu lub ważności.

1. Aby rozpocząć badanie, wybierz konkretne zdarzenie. Po prawej stronie można wyświetlić szczegółowe informacje o zdarzeniu, w tym jego ważność, podsumowanie liczby jednostek, zdarzenia pierwotne, które wyzwoliły to zdarzenie, oraz unikatowy identyfikator zdarzenia.

1. Aby wyświetlić więcej szczegółów na temat alertów i jednostek w zdarzeniu, wybierz pozycję **Wyświetl pełne szczegóły** na stronie incydentu i przejrzyj odpowiednie karty, które podsumowują informacje o zdarzeniu. Na karcie **alerty** Przejrzyj alert. Można wyświetlić wszystkie istotne informacje dotyczące alertu — zapytanie, które wyzwoliło alert, liczbę wyników zwróconych na zapytanie oraz możliwość uruchamiania elementy PlayBook na alertach. Aby przeanalizować jeszcze bardziej szczegółowe informacje o zdarzeniu, wybierz liczbę **zdarzeń**. Spowoduje to otwarcie kwerendy, która wygenerowała wyniki i zdarzenia, które wyzwoliły alert w Log Analytics. Na karcie **jednostki** można zobaczyć wszystkie jednostki, które zostały zmapowane w ramach definicji reguły alertu.

    ![Wyświetl szczegóły alertu](media/tutorial-investigate-cases/alert-details.png)

1. Jeśli aktywnie badasz zdarzenie, dobrym pomysłem jest ustawienie stanu zdarzenia na **w toku** do momentu jego zamknięcia.

1. Zdarzenia mogą być przypisane do określonego użytkownika. Dla każdego zdarzenia można przypisać właściciela, ustawiając pole **właściciel zdarzenia** . Wszystkie incydenty są uruchamiane jako nieprzypisane. Możesz również dodawać komentarze, aby inni analitykowie mogli zrozumieć, co zostało zbadane i czego dotyczą zdarzenia.

    ![Przypisywanie zdarzenia do użytkownika](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Wybierz pozycję **Zbadaj** , aby wyświetlić mapę badania.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Użyj grafu badania do głębokiej szczegółowe

Wykres badania umożliwia analitykom zaproszenie odpowiednich pytań dotyczących każdego badania. Wykres badania ułatwia zrozumienie zakresu i zidentyfikowanie głównej przyczyny potencjalnego zagrożenia bezpieczeństwa przez skorelowanie odpowiednich danych z każdą jednostką. Możesz szczegółowe głębie i badać każdą jednostkę prezentowaną na grafie, wybierając ją i wybierając różne opcje ekspansji.  
  
Wykres badania zawiera:

- **Kontekst wizualny z danych pierwotnych**: Na żywo, Visual Graph wyświetla relacje jednostek wyodrębnione automatycznie z danych pierwotnych. Dzięki temu można łatwo wyświetlać połączenia między różnymi źródłami danych.

- **Odnajdywanie zakresu pełnego badania**: Rozszerz zakres badania przy użyciu wbudowanych zapytań eksploracji, aby wyrównać pełen zakres naruszenia.

- **Wbudowane kroki badania**: Użyj wstępnie zdefiniowanych opcji eksploracji, aby upewnić się, że zażądasz odpowiednich pytań na początku zagrożenia.

Aby użyć grafu badania:

1. Wybierz zdarzenie, a następnie wybierz pozycję **Zbadaj**. Spowoduje to przejście do grafu badania. Wykres przedstawia mapę ilustracyjnyą jednostek, które są bezpośrednio połączone z alertem, a każdy zasób jest podłączany w dalszej części.

   > [!IMPORTANT] 
   > Po skonfigurowaniu reguły analitycznej można zbadać zdarzenie tylko w przypadku użycia pól mapowania jednostek. Wykres badania wymaga, aby oryginalne zdarzenie obejmowało jednostki.

   ![Wyświetl mapę](media/tutorial-investigate-cases/map1.png)

1. Wybierz jednostkę, aby otworzyć okienko **jednostki** , aby można było przejrzeć informacje o tej jednostce.

    ![Wyświetl jednostki na mapie](media/tutorial-investigate-cases/map-entities.png)
  
1. Rozszerz swoje badanie, umieszczając kursor nad każdą jednostką, aby odsłonić listę pytań, które zostały zaprojektowane przez ekspertów ds. zabezpieczeń i analityków na jednostkę typu w celu pogłębienia badania. Te opcje są wywoływane w przypadku **zapytań**.

    ![Więcej szczegółów](media/tutorial-investigate-cases/exploration-cases.png)

   Na przykład na komputerze można zażądać powiązanych alertów. W przypadku wybrania zapytania eksploracji uzyskane uprawniania są dodawane z powrotem do grafu. W tym przykładzie wybranie **powiązanych alertów** zwróciło następujące alerty do grafu:

    ![Wyświetl pokrewne alerty](media/tutorial-investigate-cases/related-alerts.png)

1. Dla każdej kwerendy eksploracji można wybrać opcję otwarcia nieprzetworzonych wyników zdarzeń i zapytania używanego w Log Analytics, wybierając pozycję **zdarzenia\>** .

1. Aby zrozumieć zdarzenie, wykres zawiera równoległą oś czasu.

    ![Wyświetlanie osi czasu na mapie](media/tutorial-investigate-cases/map-timeline.png)

1. Umieść wskaźnik myszy na osi czasu, aby zobaczyć, które elementy grafu wystąpiły w tym czasie.

    ![Użyj osi czasu na mapie, aby zbadać alerty](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka nauczysz się rozpocząć badanie zdarzeń przy użyciu platformy Azure — wskaźnik. Przejdź do samouczka dotyczącego [sposobu reagowania na zagrożenia przy użyciu zautomatyzowanego elementy PlayBook](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) w celu zautomatyzowania swoich odpowiedzi na zagrożenia.

