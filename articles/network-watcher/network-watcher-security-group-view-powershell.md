---
title: Analizowanie zabezpieczeń sieci — widok grupy zabezpieczeń-Azure PowerShell
titleSuffix: Azure Network Watcher
description: W tym artykule opisano, jak za pomocą programu PowerShell analizować zabezpieczenia maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3127d60263437a18e0c8d9a98ebdfad31049c58d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277914"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń umożliwia skonfigurowanie skonfigurowanych i obowiązujących reguł zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł skonfigurowanych na maszynie wirtualnej w celu zapewnienia, że ruch jest prawidłowo dozwolony lub odrzucany. W tym artykule przedstawiono sposób pobierania skonfigurowanych i obowiązujących reguł zabezpieczeń do maszyny wirtualnej przy użyciu programu PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu należy uruchomić polecenie cmdlet `Get-AzNetworkWatcherSecurityGroupView`, aby pobrać informacje o regule zabezpieczeń.

W tym scenariuszu założono, że wykonano już kroki opisane w temacie [tworzenie Network Watcher](network-watcher-create.md) w celu utworzenia Network Watcher.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule umożliwia pobranie skonfigurowanych i obowiązujących reguł zabezpieczeń dla danej maszyny wirtualnej.

## <a name="retrieve-network-watcher"></a>Retrieve Network Watcher

Pierwszym krokiem jest pobranie wystąpienia Network Watcher. Ta zmienna jest przenoszona do polecenia cmdlet `Get-AzNetworkWatcherSecurityGroupView`.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Pobierz maszynę wirtualną

Aby uruchomić polecenie cmdlet `Get-AzNetworkWatcherSecurityGroupView` w programie, wymagana jest maszyna wirtualna. Poniższy przykład pobiera obiekt maszyny wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład to skrócona odpowiedź na zwrócone wyniki. Wyniki pokazują wszystkie obowiązujące i stosowane reguły zabezpieczeń na maszynie wirtualnej podzielone na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**i **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Inspekcja sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) z Network Watcher](network-watcher-nsg-auditing-powershell.md) , aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności sieciowych grup zabezpieczeń.


