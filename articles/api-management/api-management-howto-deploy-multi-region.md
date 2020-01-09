---
title: Wdrażanie usług API Management platformy Azure w wielu regionach platformy Azure
titleSuffix: Azure API Management
description: Dowiedz się, jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442666"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach platformy Azure

Usługa Azure API Management obsługuje wdrażanie w wielu regionach, co umożliwia wydawcom interfejsu API dystrybucję pojedynczej usługi Azure API Management w dowolnej liczbie obsługiwanych regionów platformy Azure. Funkcja wieloregionowa pomaga zmniejszyć opóźnienie żądania postrzegane przez geograficznie rozproszonych klientów interfejsu API i zwiększa dostępność usługi, jeśli jeden region przejdzie w tryb offline.

Nowa usługa API Management platformy Azure początkowo zawiera tylko jedną [jednostkę][unit] w jednym regionie platformy Azure, regionie podstawowym. Dodatkowe regiony można dodać do regionu podstawowego lub pomocniczego. Składnik bramy API Management jest wdrażany w każdym wybranym regionie podstawowym i pomocniczym. Przychodzące żądania interfejsu API są automatycznie kierowane do najbliższego regionu. Jeśli region przejdzie w tryb offline, żądania interfejsu API będą automatycznie kierowane wokół regionu zakończonego niepowodzeniem do następnej najbliższej bramy.

> [!NOTE]
> Tylko składnik bramy API Management jest wdrażany we wszystkich regionach. Składnik zarządzania usługami i Portal dla deweloperów są hostowane tylko w regionie podstawowym. W związku z tym, w przypadku awarii regionu podstawowego, dostęp do portalu dla deweloperów i możliwość zmiany konfiguracji (np. dodanie interfejsów API, zastosowanie zasad) zostanie ograniczony do momentu powrotu do trybu online w regionie podstawowym. Chociaż region podstawowy jest dostępny w trybie offline, regiony pomocnicze będą nadal obsługiwały ruch interfejsu API przy użyciu najnowszej dostępnej konfiguracji.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Wdróż wystąpienie usługi API Management w nowym regionie

> [!NOTE]
> Jeśli nie utworzono jeszcze wystąpienia usługi API Management, zobacz [Tworzenie wystąpienia usługi API Management][create an api management service instance].

W Azure Portal przejdź do strony **skalowanie i Cennik** dla swojego wystąpienia usługi API Management.

![Karta Skala][api-management-scale-service]

Aby wdrożyć w nowym regionie, kliknij pozycję **+ Dodaj region** na pasku narzędzi.

![Dodaj region][api-management-add-region]

Z listy rozwijanej wybierz lokalizację i ustaw liczbę jednostek dla suwaka.

![Określ jednostki][api-management-select-location-units]

Kliknij przycisk **Dodaj** , aby umieścić wybór w tabeli lokalizacji.

Powtórz ten proces, dopóki nie zostaną skonfigurowane wszystkie lokalizacje, a następnie kliknij przycisk **Zapisz** na pasku narzędzi, aby rozpocząć proces wdrażania.

## <a name="remove-region"> </a>Usuwanie wystąpienia usługi API Management z lokalizacji

W Azure Portal przejdź do strony **skalowanie i Cennik** dla swojego wystąpienia usługi API Management.

![Karta Skala][api-management-scale-service]

W przypadku lokalizacji, którą chcesz usunąć, otwórz menu kontekstowe za pomocą przycisku **...** na prawym końcu tabeli. Wybierz opcję **Usuń** .

Potwierdź usunięcie i kliknij przycisk **Zapisz** , aby zastosować zmiany.

## <a name="route-backend"> </a>Kierowanie wywołań interfejsu API do regionalnych usług zaplecza

Usługa Azure API Management zawiera tylko jeden adres URL usługi wewnętrznej bazy danych. Mimo że istnieją wystąpienia usługi Azure API Management w różnych regionach, Brama interfejsu API nadal będzie przesyłać żądania do tej samej usługi zaplecza, która jest wdrażana w tylko jednym regionie. W takim przypadku wzrost wydajności będzie występować tylko z odpowiedzi w pamięci podręcznej w systemie Azure API Management w regionie określonym dla żądania, ale kontaktowanie się z zapleczem na całym świecie może nadal spowodować duże opóźnienia.

Aby w pełni wykorzystać rozkład geograficzny systemu, należy wdrożyć usługi zaplecza w tych samych regionach co wystąpienia usługi Azure API Management. Następnie korzystając z zasad i właściwości `@(context.Deployment.Region)`, można kierować ruch do lokalnych wystąpień zaplecza.

1. Przejdź do wystąpienia usługi Azure API Management i kliknij pozycję **interfejsy API** w menu po lewej stronie.
2. Wybierz żądany interfejs API.
3. Kliknij przycisk **Edytor kodu** na liście rozwijanej strzałki w **przetwarzaniu przychodzącym**.

    ![Edytor kodu interfejsu API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Użyj `set-backend` połączone z zasadami `choose` warunkowych, aby utworzyć odpowiednie zasady routingu w sekcji `<inbound> </inbound>` pliku.

    Na przykład poniższy plik XML będzie działał w regionach zachodnie stany USA i Azja Wschodnia:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Możesz również połączyć usługi zaplecza z [platformą Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), skierować wywołania interfejsu API do Traffic Manager i umożliwić automatyczne rozwiązanie routingu.

## <a name="custom-routing"> </a>Używanie routingu niestandardowego do API Management bram regionalnych

API Management kieruje żądania do _bramy_ regionalnej na podstawie [najmniejszego opóźnienia](../traffic-manager/traffic-manager-routing-methods.md#performance). Chociaż nie jest możliwe przesłonięcie tego ustawienia w API Management, możesz użyć własnych Traffic Manager z niestandardowymi regułami routingu.

1. Utwórz własne [Traffic Manager platformy Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Jeśli używasz domeny niestandardowej, [Użyj jej z Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) zamiast usługi API Management.
1. [Skonfiguruj API Management regionalne punkty końcowe w Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionalne punkty końcowe są zgodne ze wzorcem adresu URL `https://<service-name>-<region>-01.regional.azure-api.net`, na przykład `https://contoso-westus2-01.regional.azure-api.net`.
1. [Skonfiguruj punkty końcowe API Management stanu regionalne w Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Punkty końcowe stanu regionalnego są zgodne ze wzorcem adresu URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, na przykład `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Określ [metodę routingu](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
