---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — kod siatki sfctl-Package-log
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do uzyskiwania dzienników dla określonego pakietu kodu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906051"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Pobierz dzienniki dla kontenera określonego pakietu kodu dla danej repliki usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| get | Pobiera dzienniki z kontenera. |

## <a name="sfctl-mesh-code-package-log-get"></a>kod siatki sfctl-Package-log Get
Pobiera dzienniki z kontenera.

Pobiera dzienniki dla kontenera określonego pakietu kodu repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |
| --Code-package-name [wymagane] | Nazwa pakietu kodu usługi. |
| --Replica-Name [wymagane] | Nazwa repliki Service Fabric. |
| --Service-Name [wymagane] | Nazwa usługi. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienników. Wartość domyślna to 100. "wszystkie", aby wyświetlić kompletne dzienniki. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).