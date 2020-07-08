---
title: Zarządzaj twórcą Azure Maps
description: W tym artykule dowiesz się, jak zarządzać twórcą Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83677952"
---
# <a name="manage-azure-maps-creator"></a>Zarządzaj twórcą Azure Maps

Kreator Azure Maps umożliwia tworzenie prywatnych danych mapy pomieszczeń. Korzystając z interfejsu API Azure Maps i modułu Maps (mapy wewnętrzne), można tworzyć interaktywne i dynamiczne aplikacje sieci Web z zamapowanej mapy. Obecnie Kreator jest dostępny tylko w Stany Zjednoczone przy użyciu warstwy cenowej S1.

W tym artykule opisano kroki tworzenia i usuwania zasobu twórcy na koncie Azure Maps.

## <a name="create-creator-resource"></a>Utwórz zasób twórcy

1. Zaloguj się do [Azure Portal](https://portal.azure.com)

2. Wybierz konto Azure Maps. Jeśli nie widzisz konta Azure Maps w obszarze **ostatnie zasoby**, przejdź do menu Azure Portal. Wybierz pozycję **Wszystkie zasoby**. Znajdź i wybierz swoje konto Azure Maps.

    ![Strona główna portalu Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. Gdy jesteś na stronie konta Azure Maps, przejdź do opcji **Przegląd** w obszarze **Creator**(Tworzenie). Kliknij przycisk **Utwórz** , aby utworzyć zasób twórcy Azure Maps.

    ![Tworzenie strony kreatora Azure Maps](./media/how-to-manage-creator/creator-blade-settings.png)

4. Wprowadź nazwę i lokalizację zasobu twórcy. Obecnie Kreator jest obsługiwany tylko w Stany Zjednoczone. Kliknij pozycję **Przegląd + utwórz**.

   ![Wprowadź stronę informacji o koncie twórcy](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.

    ![Strona Potwierdzanie ustawień konta twórcy](./media/how-to-manage-creator/creator-create-dialog.png)

6. Po zakończeniu wdrożenia zostanie wyświetlona strona z komunikatem o powodzeniu lub niepowodzeniu.

   ![Strona stanu wdrożenia zasobu](./media/how-to-manage-creator/creator-resource-created.png)

7. Kliknij pozycję **Przejdź do zasobu**. Na stronie widok zasobów twórcy zostanie wyświetlony stan zasobu twórcy i wybrany region demograficzny.

    ![Strona stanu twórcy](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Na stronie zasobów twórcy można wrócić do konta Azure Maps, do którego należy, klikając pozycję Azure Maps Account (konto).

## <a name="delete-creator-resource"></a>Usuwanie zasobu twórcy

Aby usunąć zasób twórcy, przejdź do swojego konta Azure Maps. Wybierz pozycję **Przegląd** w obszarze **twórca**. Kliknij przycisk **Usuń**.

>[!WARNING]
>Usunięcie zasobu twórcy konta Azure Maps spowoduje również usunięcie zestawów danych, tilesets i funkcji statesets utworzonych przy użyciu usług Creator Services.

![Strona kreatora z przyciskiem Usuń](./media/how-to-manage-creator/creator-delete.png)

Kliknij przycisk **Usuń** i wpisz swoją nazwę twórcy, aby potwierdzić usunięcie. Po usunięciu zasobu zostanie wyświetlona strona potwierdzenia, jak na poniższej ilustracji:

![Strona twórcy z potwierdzeniem usunięcia](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Authentication

Twórca dziedziczy ustawienia Azure Maps Access Control (IAM). Wszystkie wywołania interfejsu API do uzyskiwania dostępu do danych muszą być wysyłane z regułami uwierzytelniania i autoryzacji.

Dane użycia twórcy są zawarte w Azure Maps wykresy użycia i dziennik aktywności.  Aby uzyskać więcej informacji, zobacz [Zarządzanie uwierzytelnianiem w Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Dostęp do usług Creator Services

Usługi Creator Services są dostępne tylko w lokalizacji wybranej podczas tworzenia. Jeśli wywołania są wykonywane w ramach usług Creator spoza wybranej lokalizacji, zostanie zwrócony komunikat o błędzie użytkownika. Aby nawiązywać wywołania spoza wybranej lokalizacji, adres URL usługi musi zawierać prefiks geograficzny dla wybranych lokalizacji. Na przykład jeśli utworzono twórcę w Stany Zjednoczone, wszystkie wywołania do usługi konwersji muszą zostać przesłane do `us.atlas.microsoft.com/conversion/convert` .

Ponadto wszystkie dane zaimportowane do twórcy powinny zostać przekazane do tej samej lokalizacji geograficznej co zasób twórcy. Na przykład jeśli twórca jest zainicjowany w podanej Zjednoczonym, wszystkie dane pierwotne powinny zostać przekazane za pośrednictwem `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do kreatora dla mapowania pomieszczeń:

> [!div class="nextstepaction"]
> [Przekazywanie danych](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Konwersja danych](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Zestaw stanu funkcji](creator-indoor-maps.md#feature-statesets)

Dowiedz się, jak używać twórcy, aby renderować mapy wewnętrzne w aplikacji:

> [!div class="nextstepaction"]
> [Samouczek kreatora Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamiczne style mapy](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Korzystanie z modułu Maps (mapy wewnętrzne)](how-to-use-indoor-module.md)