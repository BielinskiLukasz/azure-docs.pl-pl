---
title: Samouczek — konfigurowanie reguł buforowania usługi Azure CDN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak konfigurować globalne i niestandardowe reguły buforowania usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 10d06d2e792b476a4c973029241d6cb98c0dd444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684305"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Samouczek: Ustawianie reguł buforowania w usłudze Azure CDN

> [!NOTE] 
> Reguły buforowania usługi Azure CDN są dostępne tylko w usłudze **Azure CDN w warstwie Standardowa firmy Verizon** oraz **Azure CDN w warstwie Standardowa firmy Akamai**. W przypadku usługi **Azure CDN w warstwie Premium firmy Verizon** użyj [aparatu reguł usługi Azure CDN](cdn-rules-engine.md) w portalu **Zarządzaj**, aby uzyskać podobną funkcjonalność.
 

W tym samouczku opisano, jak można użyć reguł buforowania usługi Azure Content Delivery Network (CDN) do ustawiania lub modyfikowania domyślnego zachowania wygaśnięcia pamięci podręcznej globalnie i w warunkach niestandardowych — np. w przypadku ścieżki adresu URL i rozszerzenia pliku. Usługa Azure CDN oferuje dwa typy reguł buforowania:
- Globalne reguły buforowania: Możesz ustawić jedną globalne reguły buforowania dla każdego punktu końcowego w profilu, który ma wpływ na wszystkie żądania do punktu końcowego. Globalna reguła buforowania zastępuje wszelkie nagłówki dyrektywy pamięci podręcznej HTTP, jeśli zostały one ustawione.

- Niestandardowe reguły buforowania: Można ustawić co najmniej jeden niestandardowe reguły buforowania dla każdego punktu końcowego w Twoim profilu. Niestandardowe reguły buforowania pasują do określonych ścieżek i rozszerzeń plików, są przetwarzane kolejno i zastępują globalną regułę buforowania, jeśli została ustawiona. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Otwórz stronę reguł buforowania.
> - Utwórz globalną regułę buforowania.
> - Utwórz niestandardową regułę buforowania.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć profil usługi CDN i co najmniej jeden punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz temat [Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Otwieranie strony reguł buforowania usługi Azure CDN

1. Wybierz profil usługi CDN w witrynie [Azure Portal](https://portal.azure.com), a następnie wybierz punkt końcowy.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona Reguły buforowania usługi CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Ustawianie globalnych reguł buforowania

Utwórz globalną regułę buforowania w następujący sposób:

1. W obszarze **Globalne reguły buforowania** ustaw **Zachowanie buforowania ciągu kwerendy** na **Ignoruj ciągi zapytań**.

2. Ustaw opcję **Zachowanie buforowania** na **Ustaw, jeśli brak**.
       
3. Dla opcji **Czas wygasania pamięci podręcznej** wprowadź wartość 10 w polu **Dni**.

    Globalna reguła buforowania ma wpływ na wszystkie żądania do punktu końcowego. Ta reguła będzie uwzględniać nagłówki dyrektywy pamięci podręcznej źródła, o ile takie istnieją (`Cache-Control` lub `Expires`). Jeśli nie są one określone, spowoduje to ustawienie pamięci podręcznej na 10 dni. 

    ![Globalne reguły buforowania](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Ustawianie niestandardowych reguł buforowania

Utwórz niestandardową regułę buforowania w następujący sposób:

1. W obszarze **Niestandardowe reguły buforowania** ustaw opcję **Warunek dopasowania** na **Ścieżka** i **Wartość dopasowania** na `/images/*.jpg`.
    
2. Ustaw **Zachowanie buforowania** na **Zastąp**, a następnie wprowadź wartość 30 w polu **Dni**.
       
    Ta niestandardowa reguła buforowania ustawia czas buforowania na 30 dni na dowolnym pliku obrazów `.jpg` w folderze `/images` punktu końcowego. Zastępuje wszystkie nagłówki HTTP `Cache-Control` lub `Expires` wysyłane przez serwer źródłowy.

    ![Niestandardowe reguły buforowania](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono reguły buforowania. Jeśli nie chcesz już ich używać, możesz je usunąć, wykonując następujące kroki:
 
1. Wybierz profil usługi CDN, a następnie punkt końcowy z regułami buforowania, które chcesz usunąć.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

3. W obszarze **Globalne reguły buforowania** ustaw opcję **Zachowanie buforowania** na **Nie ustawiono**.
 
4. W obszarze **Niestandardowe reguły buforowania** zaznacz pole wyboru obok reguły, którą chcesz usunąć.

5. Wybierz pozycję **Usuń**.

6. Kliknij przycisk **Zapisz** u góry strony.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Otwórz stronę reguł buforowania.
> - Utwórz globalną regułę buforowania.
> - Utwórz niestandardową regułę buforowania.

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować dodatkowe ustawienia reguły buforowania.

> [!div class="nextstepaction"]
> [Sterowanie zachowaniem buforowania w usłudze Azure CDN przy użyciu reguł buforowania](cdn-caching-rules.md)



