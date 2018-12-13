---
title: Sklepu systemu Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl magazynu.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 034ae29b5fabae15aa2b6b96e7fefaef23c1c5a1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275231"
---
# <a name="sfctl-store"></a>sfctl store
Wykonaj podstawowy plik operacji na poziomie magazynu obrazów klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa istniejące obraz przechowywanie zawartości. |
| root-info | Pobiera informacje o zawartości w folderze głównym magazynu obrazów. |
| stat | Pobiera informacje o zawartości magazynu obrazów. |

## <a name="sfctl-store-delete"></a>Usuwanie magazynu sfctl
Usuwa istniejące obraz przechowywanie zawartości.

Usuwa istniejące zawartość magazynu obrazu znalezionych w ramach danego obrazu przechowywać ścieżkę względną. To polecenie może służyć do usuwania pakietów aplikacji przekazany, po ich zainicjowaniu obsługi administracyjnej.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Ścieżka zawartości, [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów z jego katalogu głównego. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-store-root-info"></a>Interfejs sfctl magazynu z informacjami głównymi
Pobiera informacje o zawartości w folderze głównym magazynu obrazów.

Zwraca informacje o magazynie obrazu zawartości w folderze głównym magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-store-stat"></a>stat magazynu sfctl
Pobiera informacje o zawartości magazynu obrazów.

Zwraca informacje o zawartości ze sklepu obraz w określonym contentPath. Element contentPath jest określana względem katalogu głównego magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Ścieżka zawartości, [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów z jego katalogu głównego. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) usługi Service Fabric CLI.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).