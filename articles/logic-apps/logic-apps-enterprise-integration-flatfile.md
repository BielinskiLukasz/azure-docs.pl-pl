---
title: Kodować i dekodować pliki proste w usłudze Azure logic apps | Dokumentacja firmy Microsoft
description: Jak używać pliku pliku koder i dekoder w pakiet integracyjny dla przedsiębiorstw usługi logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: 4c8ac7aa3ccafaf14c0399860f3576430dd6ba1a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430002"
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Omówienie integracji dla przedsiębiorstw przy użyciu plików prostych

Można zakodować zawartość XML, zanim zostanie wysłany do partnera biznesowego w scenariuszu business-to-business (B2B). W aplikacji logiki można użyć łącznika kodowania pliku prostego, aby to zrobić. Aplikacja logiki, które tworzysz może pobierać jego XML zawartość z różnych źródeł, w tym z wyzwalacza żądania HTTP, z innej aplikacji lub nawet jednego z wielu [łączników](../connectors/apis-list.md). Aby uzyskać więcej informacji o usłudze logic apps, zobacz [dokumentację usługi logic apps](logic-apps-overview.md "Dowiedz się więcej o usłudze Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Tworzenie łącznika kodowania pliku prostego
Wykonaj następujące kroki, aby dodać plik prosty kodowanie łącznika aplikacji logiki.

1. Tworzenie aplikacji logiki i [połączyć go z kontem integracji](logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji aplikacji logiki"). To konto zawiera schemat, które będą używane w celu zakodowania danych XML.  
1. Dodaj **żądania — zostanie odebrane żądanie HTTP podczas** wyzwalacz aplikacji logiki.  
   ![Zrzut ekranu wyzwalacza, aby wybrać](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Dodawanie pliku prostego kodowanie akcji, w następujący sposób:
   
    a. Wybierz **oraz** logowania.
   
    b. Wybierz **Dodaj akcję** link (pojawia się po wybraniu znak plus).
   
    c. W polu wyszukiwania wprowadź *prostego* filtrować wszystkie akcje do tego, którego chcesz używać.
   
    d. Wybierz **kodowania pliku prostego** opcję z listy.   
   ![Zrzut ekranu z prostego kodowanie pliku — opcja](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Na **kodowania pliku prostego** okno dialogowe, wybierz opcję **zawartości** pola tekstowego.  
   ![Zrzut ekranu zawartości pola tekstowego](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Wybierz tag treści jako zawartości, który chcesz zakodować. Tag treści spowoduje wypełnienie pola zawartość.     
   ![Zrzut ekranu przedstawiający tagu treści](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Wybierz **Nazwa schematu** pola listy, a następnie wybierz schemat, którego chcesz użyć, aby zakodować zawartość danych wejściowych.    
   ![Pole listy zrzucie ekranu Nazwa schematu](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Zapisz pracę.   
   ![Ikona Zapisz zrzut ekranu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

W tym momencie po zakończeniu konfigurowania łącznika kodowania pliku prostego. W przypadku aplikacji rzeczywistych można przechowywać dane zakodowane w aplikacji line-of-business, takich jak usługa Salesforce. Lub możesz wysłać, czy dane zakodowane do obrotu partnera. Można łatwo dodać akcję wysłania do usługi Salesforce lub z partnerem handlowym danych wyjściowych kodowania akcji przy użyciu dowolnego z łączników, pod warunkiem.

Teraz możesz przetestować łącznika zgłaszającą żądanie do punktu końcowego HTTP, a w tym zawartości XML w treści żądania.  

## <a name="create-the-flat-file-decoding-connector"></a>Tworzenie łącznika dekodowania pliku prostego

> [!NOTE]
> Aby wykonać te kroki, musisz mieć plik schematu już przekazany na koncie integracji.

1. Dodaj **żądania — zostanie odebrane żądanie HTTP podczas** wyzwalacz aplikacji logiki.  
   ![Zrzut ekranu wyzwalacza, aby wybrać](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Dodawanie pliku prostego dekodowanie akcji, w następujący sposób:
   
    a. Wybierz **oraz** logowania.
   
    b. Wybierz **Dodaj akcję** link (pojawia się po wybraniu znak plus).
   
    c. W polu wyszukiwania wprowadź *prostego* filtrować wszystkie akcje do tego, którego chcesz używać.
   
    d. Wybierz **dekodowania pliku prostego** opcję z listy.   
   ![Zrzut ekranu z prostego dekodowanie pliku — opcja](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Wybierz **zawartości** kontroli. Tworzy to lista zawartości z poprzednich krokach, które służą jako zawartość do odkodowania. Należy zauważyć, że *treści* z HTTP przychodzące żądanie jest dostępne do użycia jako zawartość do odkodowania. Możesz też wprowadzić zawartości do zdekodowania bezpośrednio do **zawartości** kontroli.     
1. Wybierz *treści* tagu. Zwróć uwagę, tag treści znajduje się teraz w **zawartości** kontroli.
1. Wybierz nazwę schematu, który chcesz użyć do dekodowania zawartości. Poniższy zrzut ekranu pokazuje, że *OrderFile* jest nazwą wybranego schematu. Ta nazwa schematu miał wcześniej przekazane na koncie integracji.
   
   ![Zrzut ekranu z prostego dekodowanie pliku okno dialogowe](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Zapisz pracę.  
   ![Ikona Zapisz zrzut ekranu](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

W tym momencie po zakończeniu konfigurowania Twojego pliku prostego dekodowanie łącznika. W przypadku aplikacji rzeczywistych można przechowywać dekodowane dane w aplikacji line-of-business, takich jak usługa Salesforce. Możesz łatwo dodać akcję, aby wysłać dane wyjściowe akcji dekodowania do usługi Salesforce.

Teraz możesz przetestować łącznika zgłaszającą żądanie do punktu końcowego HTTP, a w tym zawartości XML, który ma zostać zdekodowane w treści żądania.  

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw").  

