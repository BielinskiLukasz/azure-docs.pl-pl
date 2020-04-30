---
title: Skonfiguruj punkty końcowe usługi sieci wirtualnej dla Azure Service Bus
description: Ten artykuł zawiera informacje dotyczące sposobu dodawania punktu końcowego usługi Microsoft. ServiceBus do sieci wirtualnej.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: d1766ffb579bb1a86da91ac73a396ce0d008f89e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117628"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Skonfiguruj punkty końcowe usługi sieci wirtualnej dla Azure Service Bus

Integracja Service Bus z [punktami końcowymi usługi Virtual Network (VNET)][vnet-sep] umożliwia bezpieczny dostęp do funkcji obsługi komunikatów z obciążeń takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu do powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednie przestrzenie nazw Service Bus nie będą już akceptować ruchu z dowolnego miejsca, ale autoryzowanych sieci wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie przestrzeni nazw Service Bus z punktem końcowym usługi konfiguruje odizolowany tunel sieciowy z podsieci sieci wirtualnej do usługi obsługi komunikatów.

Wynikiem jest relacja między obciążeniami powiązanymi z podsiecią i odpowiadającą jej przestrzenią nazw Service Bus, w odróżnieniu od pozostałego adresu sieciowego punktu końcowego usługi obsługi komunikatów znajdującego się w zakresie publicznego adresu IP.

>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Service Bus.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane w przypadku implementacji sieci wirtualnych.
>
> Typowe scenariusze platformy Azure, które nie współpracują z sieciami wirtualnymi (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Poniższe usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane tylko w [warstwie Premium](service-bus-premium-messaging.md) Service Bus przestrzenie nazw.
> 
> W przypadku korzystania z punktów końcowych usługi sieci wirtualnej z Service Bus nie należy włączać tych punktów końcowych w aplikacjach, które mieszają warstwy Standardowa i Premium Service Bus przestrzenie nazw. Ponieważ warstwa standardowa nie obsługuje sieci wirtualnych. Punkt końcowy jest ograniczony tylko do przestrzeni nazw warstwy Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń obsługujące integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, zazwyczaj nadal potrzebują ścieżek komunikacji między usługami znajdującymi się w tych przedziałach.

Wszystkie bezpośrednie trasy IP między przedziałami, w tym przenoszące HTTPS za pośrednictwem protokołu TCP/IP, obejmują ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi komunikatów zapewniają całkowicie izolowane ścieżki komunikacyjne, w przypadku których komunikaty są nawet zapisywane na dysku podczas przejścia między stronami. Obciążenia w dwóch odrębnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem Service Bus mogą komunikować się efektywnie i niezawodnie za pośrednictwem komunikatów, podczas gdy jest zachowywana odpowiednia integralność granic izolacji sieci.
 
Oznacza to, że rozwiązania w chmurze dotyczące zabezpieczeń nie tylko uzyskują dostęp do wiodących i skalowalnych w branży asynchronicznych funkcji obsługi komunikatów, ale mogą teraz używać komunikatów do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązania, które są najbardziej bezpieczne, niż to, co jest osiągalne w trybie komunikacji równorzędnej, w tym protokołu HTTPS i innych protokołów gniazd zabezpieczonych za pomocą protokołu TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Powiązywanie Service Bus z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer Azure Service Bus akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie przestrzeni nazw Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi Virtual Network** w podsieci Virtual Network i włączyć ją dla elementu **Microsoft. ServiceBus** , zgodnie z opisem w temacie [Omówienie punktu końcowego usługi][vnet-sep]. Po dodaniu punktu końcowego usługi należy powiązać z nim przestrzeń nazw Service Bus z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Service Bus z podsiecią sieci wirtualnej. Chociaż reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają udzielony dostęp do przestrzeni nazw Service Bus. Sama Service Bus nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie udzielono dostępu do podsieci przez włączenie tej reguły.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak dodać punkt końcowy usługi sieci wirtualnej przy użyciu Azure Portal. Aby ograniczyć dostęp, należy zintegrować punkt końcowy usługi sieci wirtualnej dla tej Event Hubs przestrzeni nazw.

1. Przejdź do **przestrzeni nazw Service Bus** w [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć** . Domyślnie wybrana jest opcja **wszystkie sieci** . Przestrzeń nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zapora — wybrana opcja Wszystkie sieci](./media/service-endpoints/firewall-all-networks-selected.png)
1. Wybierz opcję **wybrane sieci** w górnej części strony.
2. W sekcji **Virtual Network** strony wybierz pozycję **+ Dodaj istniejącą sieć wirtualną**. 

    ![dodawanie istniejącej sieci wirtualnej](./media/service-endpoints/add-vnet-menu.png)
3. Wybierz sieć wirtualną z listy sieci wirtualnych, a następnie wybierz **podsieć**. Przed dodaniem sieci wirtualnej do listy musisz włączyć punkt końcowy usługi. Jeśli punkt końcowy usługi nie jest włączony, w portalu zostanie wyświetlony monit o jego włączenie.
   
   ![wybieranie podsieci](./media/service-endpoints/select-subnet.png)

4. Po włączeniu punktu końcowego usługi dla podsieci na potrzeby elementu **Microsoft. ServiceBus**powinien zostać wyświetlony następujący komunikat o powodzeniu. Wybierz pozycję **Dodaj** w dolnej części strony, aby dodać sieć. 

    ![wybieranie podsieci i włączanie punktu końcowego](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Jeśli nie możesz włączyć punktu końcowego usługi, możesz zignorować brakujący punkt końcowy usługi sieci wirtualnej przy użyciu szablonu Menedżer zasobów. Ta funkcja nie jest dostępna w portalu.
6. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut, aż potwierdzenie będzie widoczne w powiadomieniach portalu. Przycisk **Zapisz** powinien być wyłączony. 

    ![Zapisz sieć](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Poniższy szablon Menedżer zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącej Service Bus przestrzeni nazw.

Parametry szablonu:

* **przestrzeń nazw**: Service Bus przestrzeń nazw.
* **virtualNetworkingSubnetId**: w pełni kwalifikowana ścieżka Menedżer zasobów dla podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` dla domyślnej podsieci sieci wirtualnej.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
> 
> wniosek
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

Szablon:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz następujące linki:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vnet-sep]
- [Azure Service Bus filtrowanie adresów IP][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
