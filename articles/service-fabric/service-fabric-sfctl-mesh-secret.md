---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — wpis tajny siatki sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do pobierania i usuwania Service Fabric siatki Secret.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905988"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Pobierz i Usuń zasoby z siatką kluczy tajnych.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób tajny. |
| list | Wyświetla listę wszystkich zasobów tajnych. |
| pokaż | Pobiera tajny zasób o podaną nazwę. |

## <a name="sfctl-mesh-secret-delete"></a>Usuwanie wpisu tajnego siatki sfctl
Usuwa zasób tajny.

Usuwa określony zasób tajny i wszystkie jego nazwane wartości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa zasobu tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secret-list"></a>Lista wpisów tajnych sfctl siatki
Wyświetla listę wszystkich zasobów tajnych.

Pobiera informacje o wszystkich zasobach tajnych w danej grupie zasobów. Informacje obejmują opis i inne właściwości wpisu tajnego.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secret-show"></a>Pokaż wpis tajny siatki sfctl
Pobiera tajny zasób o podaną nazwę.

Pobiera informacje o zasobie tajnym o podanej nazwie. Informacje obejmują opis i inne właściwości wpisu tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa zasobu tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).