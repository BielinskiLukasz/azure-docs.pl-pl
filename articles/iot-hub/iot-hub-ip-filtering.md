---
title: Filtry połączeń IP IoT Hub platformy Azure | Microsoft Docs
description: Jak używać filtrowania IP do blokowania połączeń z określonych adresów IP dla usługi Azure IoT Hub. Można blokować połączenia z poszczególnych adresów IP lub z nich.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 742706f4daa518faf06e5c8b735e679f345f1279
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849878"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT w oparciu o IoT Hub platformy Azure. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtr IP* umożliwia konfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, gdy warto zablokować IoT Hub punkty końcowe dla określonych adresów IP:

* Centrum IoT Hub powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystkie inne. Na przykład do tworzenia prywatnych połączeń między usługą IoT Hub a infrastrukturą lokalną można używać usługi IoT Hub w usłudze [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) .

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi IoT Hub.

## <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtrów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguły filtru IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu. Jednak klienci odczytujący się bezpośrednio z [wbudowanego punktu końcowego zgodnego z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) (nie za pośrednictwem parametrów połączenia IoT Hub) nie są powiązane z regułami filtrowania adresów IP. 

Wszystkie próby połączenia z adresu IP, które pasują do reguły odrzucenia adresu IP w centrum IoT, odbierają nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie zawiera wzmianki o regule adresu IP. Odrzucenie adresów IP może uniemożliwić korzystanie z innych usług platformy Azure, takich jak Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w Azure Portal ze współdziałania z usługą IoT Hub.

> [!NOTE]
> Jeśli musisz użyć Azure Stream Analytics (ASA) do odczytywania komunikatów z Centrum IoT z włączonym filtrem IP, użyj nazwy zgodnej z centrum zdarzeń i punktu końcowego Centrum IoT, aby ręcznie dodać [dane wejściowe strumienia Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do asa.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu Centrum IoT jest pusta. To ustawienie domyślne oznacza, że centrum akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0.

Aby przejść do strony Ustawienia filtru IP, wybierz pozycję **Sieć**, **dostęp publiczny**, a następnie wybierz pozycję **wybrane zakresy adresów IP**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub domyślnych ustawień filtru IP":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub Edytowanie reguły filtru IP

Aby dodać regułę filtrowania adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Dodawanie reguły filtru IP do centrum IoT Hub":::

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP**Wypełnij pola.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Po wybraniu opcji Dodaj regułę filtrowania adresów IP":::

* Podaj **nazwę** dla reguły filtru IP. Musi to być unikatowy ciąg alfanumeryczny bez uwzględniania wielkości liter, do 128 znaków. Akceptowane są tylko znaki alfanumeryczne ASCII 7-bitowe Plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` .

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255.

* Wybierz pozycję **Zezwalaj** lub **Blokuj** jako **akcję** dla reguły filtru IP.

Po wypełnieniu pól wybierz pozycję **Zapisz** , aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Powiadomienie o zapisaniu reguły filtru IP":::

Opcja **Dodaj** jest wyłączona, gdy zostanie osiągnięta maksymalna liczba reguł filtru IP.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz** , aby zapisać edycję.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana została zapisana.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Usuwanie reguły filtru IoT Hub IP":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu interfejsu wiersza polecenia platformy Azure

Filtry IP IoT Hub można pobrać i zaktualizować za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Aby pobrać bieżące filtry IP IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Spowoduje to zwrócenie obiektu JSON, w którym istniejące filtry IP są wymienione w `properties.ipFilterRules` kluczu:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Aby dodać nowy filtr IP dla IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Aby usunąć istniejący filtr IP w IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Należy pamiętać, że `<ipFilterIndexToRemove>` musi odpowiadać kolejności filtrów IP w IoT Hub `properties.ipFilterRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub mogą być pobierane i ustawiane przez [Azure PowerShell](/powershell/azure/overview).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizowanie reguł filtrów IP przy użyciu REST

Możesz również pobrać i zmodyfikować filtr IP IoT Hub przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w [metodzie metodę createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Obliczanie reguły filtru IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Na przykład, jeśli chcesz akceptować adresy w zakresie 192.168.100.0/22 i odrzucić wszystkie inne, pierwsza reguła w siatce powinna akceptować zakres adresów 192.168.100.0/22. Kolejna reguła powinna odrzucać wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Aby zmienić kolejność reguł filtrów IP w siatce, klikaj trzy pionowe kropki na początku wiersza i przy użyciu przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Zmienianie kolejności reguł filtrów IP Centrum IoT HUb":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Metryki IoT Hub](iot-hub-metrics.md)
