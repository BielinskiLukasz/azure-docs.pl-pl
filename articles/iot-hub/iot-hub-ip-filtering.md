---
title: Filtry połączeń IP IoT Hub platformy Azure | Microsoft Docs
description: Jak używać filtrowania IP do blokowania połączeń z określonych adresów IP dla usługi Azure IoT Hub. Można blokować połączenia z poszczególnych adresów IP lub z nich.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414307"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT w oparciu o IoT Hub platformy Azure. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtr IP* umożliwia konfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, gdy warto zablokować IoT Hub punkty końcowe dla określonych adresów IP:

* Centrum IoT Hub powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystkie inne. Na przykład do tworzenia prywatnych połączeń między usługą IoT Hub a infrastrukturą lokalną można używać usługi IoT Hub w usłudze [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) .

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi IoT Hub.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguły filtru IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, które pasują do reguły odrzucenia adresu IP w centrum IoT, odbierają nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie zawiera wzmianki o regule adresu IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu Centrum IoT jest pusta. To ustawienie domyślne oznacza, że centrum akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

![IoT Hub domyślnych ustawień filtru IP](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub Edytowanie reguły filtru IP

Aby dodać regułę filtrowania adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

![Dodawanie reguły filtru IP do centrum IoT Hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP**Wypełnij pola.

![Po wybraniu opcji Dodaj regułę filtrowania adresów IP](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Podaj **nazwę** dla reguły filtru IP. Musi to być unikatowy ciąg alfanumeryczny bez uwzględniania wielkości liter, do 128 znaków. Akceptowane są tylko znaki alfanumeryczne ASCII 7- `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` bitowe Plus.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255.

* Wybierz pozycję **Zezwalaj** lub **Blokuj** jako **akcję** dla reguły filtru IP.

Po wypełnieniu pól wybierz pozycję **Zapisz** , aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

![Powiadomienie o zapisaniu reguły filtru IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Opcja **Dodaj** jest wyłączona, gdy zostanie osiągnięta maksymalna liczba reguł filtru IP.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz** , aby zapisać edycję.

> [!NOTE]
> Odrzucenie adresów IP może uniemożliwić korzystanie z usługi IoT Hub innym usługom platformy Azure (takim jak Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w portalu).

> [!WARNING]
> W przypadku korzystania z Azure Stream Analytics (ASA) do odczytywania komunikatów z usługi IoT Hub z włączonym filtrowaniem adresów IP należy użyć nazwy zgodnej z centrum zdarzeń i punktu końcowego IoT Hub w parametrach połączenia ASA.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana została zapisana.

![Usuwanie reguły filtru IoT Hub IP](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

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

Należy pamiętać `<ipFilterIndexToRemove>` , że musi odpowiadać kolejności filtrów IP w `properties.ipFilterRules`IoT Hub.

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

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Na przykład, jeśli chcesz akceptować adresy w zakresie 192.168.100.0/22 i odrzucić wszystkie inne, pierwsza reguła w siatce powinna akceptować zakres adresów 192.168.100.0/22. Następną regułę należy odrzucić wszystkie adresy, używając 0.0.0.0/0 zakresu.

Aby zmienić kolejność reguł filtrów IP w siatce, klikaj trzy pionowe kropki na początku wiersza i przy użyciu przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

![Zmiana kolejności reguł filtrowania IoT Hub IP](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Monitorowanie operacji](iot-hub-operations-monitoring.md)
* [Metryki IoT Hub](iot-hub-metrics.md)
