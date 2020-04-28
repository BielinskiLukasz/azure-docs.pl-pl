---
title: Funkcje szablonu — Data
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do pracy z datami.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0c31b26361a262a502b2a9e0fb068391846cab4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192301"
---
# <a name="date-functions-for-arm-templates"></a>Funkcje daty dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z datami w szablonach Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Dodaje czas trwania do wartości podstawowej. Oczekiwano formatu ISO 8601.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base | Tak | ciąg | Wartość daty i godziny dla dodania. Użyj [formatu znacznika czasu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| czas trwania | Tak | ciąg | Wartość czasu, która ma zostać dodana do bazy. Może być wartością ujemną. Użyj [formatu czasu trwania ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Nie | ciąg | Format danych wyjściowych dla wyniku daty i godziny. Jeśli nie zostanie podany, zostanie użyty format wartości podstawowej. Użyj [standardowych ciągów formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowych ciągów formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Wartość zwracana

Wartość daty/godziny, która wynika z dodania wartości czasu trwania do wartości podstawowej.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje różne sposoby dodawania wartości czasu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Gdy poprzedni szablon zostanie wdrożony z czasem podstawowym programu `2020-04-07 14:53:14Z`, dane wyjściowe są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| add3Years | String | 4/7/2023 2:53:14 PM |
| subtract9Days | String | 3/29/2020 2:53:14 PM |
| add1Hour | String | 4/7/2020 3:53:14 PM |

Następny przykładowy szablon pokazuje, jak ustawić godzinę rozpoczęcia harmonogramu automatyzacji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Zwraca bieżącą wartość daty/godziny w określonym formacie. Jeśli format nie jest podany, używany jest format ISO 8601 (yyyyMMddTHHmmssZ). **Tej funkcji można użyć tylko w wartości domyślnej dla parametru.**

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| format |Nie |ciąg |Zakodowana wartość identyfikatora URI w celu przekonwertowania na ciąg. Użyj [standardowych ciągów formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowych ciągów formatu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Uwagi

Tej funkcji można użyć tylko w wyrażeniu dla wartości domyślnej parametru. Użycie tej funkcji w dowolnym miejscu w szablonie powoduje zwrócenie błędu. Funkcja nie jest dozwolona w innych częściach szablonu, ponieważ zwraca inną wartość za każdym razem, gdy jest wywoływana. Wdrożenie tego samego szablonu z tymi samymi parametrami nie da niezawodnego wygenerowania tych samych wyników.

Jeśli zostanie użyta [opcja ponownego wdrożenia wcześniejszego pomyślnego wdrożenia](rollback-on-error.md), a wcześniejsze wdrożenie zawiera parametr, który używa UtcNow, parametr nie zostanie ponownie oceniony. Zamiast tego wartość parametru z wcześniejszego wdrożenia zostanie automatycznie ponownie użyta w ramach wdrożenia wycofywania.

Należy zachować ostrożność wdrażania szablonu, który opiera się na funkcji utcNow dla wartości domyślnej. Po ponownym wdrożeniu i niepodaniu wartości parametru funkcja jest ponownie Szacowana. Jeśli chcesz zaktualizować istniejący zasób zamiast tworzyć nowe, przekaż wartość parametru z wcześniejszego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Bieżąca wartość DateTime czasu UTC.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje różne formaty wartości daty i godziny.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Dane wyjściowe z powyższego przykładu różnią się w zależności od wdrożenia, ale będą podobne do następujących:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| utcOutput | ciąg | 20190305T175318Z |
| utcShortOutput | ciąg | 03/05/2019 |
| utcCustomOutput | ciąg | 3 5 |

W następnym przykładzie pokazano, jak używać wartości z funkcji podczas ustawiania wartości tagu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie Azure Resource Manager można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
