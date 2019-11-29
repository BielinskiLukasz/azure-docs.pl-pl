---
title: Tworzenie zasad dla właściwości tablicy zasobów
description: Dowiedz się, jak korzystać z parametrów tablicy i wyrażeń języka tablicowego, oszacować alias [*] i dołączać elementy z regułami definicji Azure Policy.
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: 035f300d01efe80cc44687d3779d7a5fb6be2fc3
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555166"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Tworzenie zasad dla właściwości tablicy zasobów platformy Azure

Właściwości Azure Resource Manager są zwykle określane jako ciągi i wartości logiczne. Gdy istnieje relacja jeden do wielu, właściwości złożone są raczej zdefiniowane jako tablice. W Azure Policy tablice są używane na kilka różnych sposobów:

- Typ [parametru definicji](../concepts/definition-structure.md#parameters)w celu zapewnienia wielu opcji
- Część [reguły zasad](../concepts/definition-structure.md#policy-rule) z zastosowaniem warunków **w** lub **notIn**
- Część reguły zasad, która szacuje [\[\*alias \]](../concepts/definition-structure.md#understanding-the--alias) do oszacowania:
  - Scenariusze takie jak **none**, **any**lub **All**
  - Złożone scenariusze z **liczbą**
- W [efekcie dołączania](../concepts/effects.md#append) , aby zastąpić lub dodać do istniejącej tablicy

W tym artykule opisano każde użycie programu według Azure Policy i przedstawiono kilka przykładów definicji.

## <a name="parameter-arrays"></a>Tablice parametrów

### <a name="define-a-parameter-array"></a>Zdefiniuj tablicę parametrów

Zdefiniowanie parametru jako tablicy umożliwia elastyczność zasad, gdy wymagana jest więcej niż jedna wartość.
Ta definicja zasad umożliwia określenie dowolnej pojedynczej lokalizacji parametru **allowedLocations** i wartości domyślnych _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Jako **Typ** to _ciąg_, podczas przypisywania zasad można ustawić tylko jedną wartość. Jeśli te zasady są przypisane, zasoby w zakresie są dozwolone tylko w ramach jednego regionu świadczenia usługi Azure. Większość definicji zasad musi zezwalać na listę zatwierdzonych opcji, takich jak Zezwalanie na _eastus2_, _Wschodnie_i _westus2_.

Aby utworzyć definicję zasad w celu zezwolenia na wiele opcji, należy użyć **typu** _tablicy_ . Te same zasady można napisać ponownie w następujący sposób:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Po zapisaniu definicji zasad nie można zmienić właściwości **typu** dla parametru.

Ta nowa definicja parametru przyjmuje więcej niż jedną wartość podczas przypisywania zasad. Po zdefiniowaniu właściwości array **allowedValues** wartości dostępne podczas przypisywania są dodatkowo ograniczone do wstępnie zdefiniowanej listy opcji. Użycie **allowedValues** jest opcjonalne.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Przekazywanie wartości do tablicy parametrów podczas przypisywania

Podczas przypisywania zasad przy użyciu Azure Portal parametr **typu** _Array_ jest wyświetlany jako pojedyncze pole tekstowe. Wskazówka mówi "Use; do oddzielania wartości. (np. Londyn; Nowy Jork) ". Aby przekazać dozwolone wartości lokalizacji _eastus2_, _Wschodnie_i _westus2_ do parametru, użyj następującego ciągu:

`eastus2;eastus;westus2`

Format wartości parametru jest różny w przypadku korzystania z interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub interfejsu API REST. Wartości są przesyłane przez ciąg JSON, który zawiera również nazwę parametru.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Aby użyć tego ciągu z każdym zestawem SDK, użyj następujących poleceń:

- Interfejs wiersza polecenia platformy Azure: polecenie [AZ Policy przypisanie Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) **z parametrem** Parameter
- Azure PowerShell: polecenie cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) z parametrem **PolicyParameter**
- Interfejs API REST: w operacji _Put_ [Create](/rest/api/resources/policyassignments/create) jako część treści żądania jako wartość właściwości **Properties. Parameters**

## <a name="policy-rules-and-arrays"></a>Reguły i tablice zasad

### <a name="array-conditions"></a>Warunki tablicy

[Warunki](../concepts/definition-structure.md#conditions) reguły zasad, które mogą być używane w _tablicy_
**Typ** parametru, są ograniczone do `in` i `notIn`. Wykonaj następującą definicję zasad z warunkiem `equals` na przykład:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Próba utworzenia tej definicji zasad za pomocą Azure Portal prowadzi do błędu, takiego jak ten komunikat o błędzie:

- "Zasad" {GUID} "nie można sparametryzowane z powodu błędów walidacji. Sprawdź, czy parametry zasad są prawidłowo zdefiniowane. Wynik oceny wyjątku wewnętrznego "[Parameters (" allowedLocations ")]" jest typem "Array", oczekiwano typu "String". "."

Oczekiwany **Typ** warunku `equals` to _ciąg_. Ponieważ **allowedLocations** jest zdefiniowany jako _Tablica_typów, aparat zasad szacuje wyrażenie języka i zgłosi błąd. Ze warunkiem `in` i `notIn` aparat zasad oczekuje _tablicy_ **typów** w wyrażeniu języka. Aby rozwiązać ten komunikat o błędzie, Zmień `equals` na `in` lub `notIn`.

### <a name="evaluating-the--alias"></a>Ocenianie aliasu [*]

Aliasy, które mają **\[\*\]** dołączone do ich nazwy wskazują, że **Typ** jest _tablicą_. Zamiast oceniać wartość całej tablicy, **\[\*\]** umożliwia ocenę każdego elementu tablicy. Istnieją trzy standardowe scenariusze, które dla każdej oceny elementu są przydatne w: brak, dowolne i wszystkie. W przypadku złożonych scenariuszy należy użyć [Count](../concepts/definition-structure.md#count).

Aparat zasad wyzwala **efekt** w **then** tylko wtedy, gdy reguła **if** ma wartość true.
Jest to ważne, aby zrozumieć w kontekście sposobu, w jaki **\[\*\]** oblicza każdy pojedynczy element tablicy.

Przykładowa reguła zasad dla poniższej tabeli scenariusza:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Tablica **ipRules** jest następująca dla poniższej tabeli scenariusza:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Dla każdego poniższego przykładu warunku Zastąp `<field>` z `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Poniższe wyniki są wynikiem kombinacji warunku i przykładową regułę zasad i tablicę istniejących wartości powyżej:

|Warunek |Wynik |Wyjaśnienie |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Wartość |Jeden element tablicy ma wartość false (127.0.0.1! = 127.0.0.1) i jeden jako true (127.0.0.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość false_ , a efekt nie jest wyzwalany. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efekt zasad |Oba elementy tablicy są oceniane jako prawdziwe (10.0.4.1! = 127.0.0.1 i 10.0.4.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość true_ i zostanie wyzwolony efekt. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efekt zasad |Jeden element tablicy ma wartość true (127.0.0.1 = = 127.0.0.1) i jeden jako wartość false (127.0.0.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efekt zasad |Oba elementy tablicy są oceniane jako false (10.0.4.1 = = 127.0.0.1 i 10.0.4.1 = = 192.168.1.1), więc warunek **Equals** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efekt zasad |Jeden element tablicy ma wartość false (127.0.0.1! = 127.0.0.1) i jeden jako true (127.0.0.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Wartość |Oba elementy tablicy są oceniane jako prawdziwe (10.0.4.1! = 127.0.0.1 i 10.0.4.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość true_. Operator logiczny ma wartość false (**nie** _true_), więc efekt nie zostanie wyzwolony. |
|`{<field>,"Equals":"127.0.0.1"}` |Wartość |Jeden element tablicy ma wartość true (127.0.0.1 = = 127.0.0.1) i jeden jako wartość false (127.0.0.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_ , a efekt nie jest wyzwalany. |
|`{<field>,"Equals":"10.0.4.1"}` |Wartość |Oba elementy tablicy są oceniane jako false (10.0.4.1 = = 127.0.0.1 i 10.0.4.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_ , a efekt nie jest wyzwalany. |

## <a name="the-append-effect-and-arrays"></a>Efekt dołączania i tablice

[Efekt dołączania](../concepts/effects.md#append) zachowuje się inaczej w zależności od tego, czy **pole Details** ma **\[\*alias \]** .

- Gdy nie jest **\[\*alias \]** , Append zastępuje całą tablicę właściwością **Value**
- Gdy **\[\*alias \]** , Append dodaje właściwość **Value** do istniejącej tablicy lub tworzy nową tablicę

Aby uzyskać więcej informacji, zobacz [przykłady dołączania](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).