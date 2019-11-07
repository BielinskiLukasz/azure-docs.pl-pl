---
title: Przegląd definicji widoku w Azure Managed Applications | Microsoft Docs
description: Opisuje koncepcję tworzenia definicji widoku dla Azure Managed Applications.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: b23e844cb550a98328951bc6efae3c5039ff73bf
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607535"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Wyświetl artefakt definicji w Azure Managed Applications

Definicja widoku to opcjonalny artefakt w Azure Managed Applications. Umożliwia ona Dostosowywanie strony przegląd i Dodawanie kolejnych widoków, takich jak metryki i zasoby niestandardowe.

Ten artykuł zawiera omówienie artefaktu definicji widoku i jego możliwości.

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Artefakt definicji widoku musi mieć nazwę **galerii. JSON** i znajdować się na tym samym poziomie, co **createUiDefinition. JSON** i **mainTemplate. JSON** w pakiecie. zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicję aplikacji zarządzanej, zobacz temat [Publikowanie definicji aplikacji zarządzanej przez platformę Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Widok schematu definicji

Plik **galerii. JSON** ma tylko jedną właściwość `views` najwyższego poziomu, która jest tablicą widoków. Każdy widok jest wyświetlany w interfejsie użytkownika aplikacji zarządzanej jako osobny element menu w spisie treści. Każdy widok ma właściwość `kind`, która ustawia typ widoku. Musi być ustawiona na jedną z następujących wartości: [Overview](#overview), [Metrics](#metrics), [CustomResources](#custom-resources), [Associations](#associations). Aby uzyskać więcej informacji, zobacz bieżący [schemat JSON dla galerii. JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Przykładowy kod JSON dla definicji widoku:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Omówienie

`"kind": "Overview"`

Po udostępnieniu tego widoku w pliku **galerii. JSON**zastępuje on domyślną stronę przeglądu w aplikacji zarządzanej.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|nagłówek|Nie|Nagłówek strony przegląd.|
|description|Nie|Opis aplikacji zarządzanej.|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi na stronie Przegląd, zobacz [polecenia](#commands).|

![Omówienie](./media/view-definition/overview.png)

## <a name="metrics"></a>Metryki

`"kind": "Metrics"`

Widok metryki umożliwia zbieranie i agregowanie danych z zasobów aplikacji zarządzanych w ramach [metryk Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Nie|Wyświetlany tytuł widoku.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|schematy|Tak|Tablica wykresów na stronie metryk.|

### <a name="chart"></a>Wykres

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł wykresu.|
|wykrestype|Nie|Wizualizacja, która ma być używana na potrzeby tego wykresu. Domyślnie używa wykresu liniowego. Obsługiwane typy wykresów: `Bar, Line, Area, Scatter`.|
|metrics|Tak|Tablica metryk do wykreślenia na tym wykresie. Aby dowiedzieć się więcej o metrykach obsługiwanych w Azure Portal, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metryka

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|name|Tak|Nazwa metryki.|
|agregacja|Tak|Typ agregacji, który ma być używany dla tej metryki. Obsługiwane typy agregacji: `none, sum, min, max, avg, unique, percentile, count`|
|przestrzeń nazw|Nie|Dodatkowe informacje do użycia podczas określania poprawnego dostawcy metryk.|
|resourceTagFilter|Nie|Tablica tagów zasobów (zostanie oddzielona `or` Word), dla której będą wyświetlane metryki. Dotyczy filtru typu zasobu.|
|resourceType|Tak|Typ zasobu, dla którego będą wyświetlane metryki.|

![Metryki](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Zasoby niestandardowe

`"kind": "CustomResources"`

Można zdefiniować wiele widoków tego typu. Każdy widok przedstawia **unikatowy** niestandardowy typ zasobu od niestandardowego dostawcy zdefiniowanego w **mainTemplate. JSON**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](custom-providers-overview.md).

W tym widoku można wykonywać operacje GET, PUT, DELETE i POST dla niestandardowego typu zasobu. Operacje POST mogą być globalnymi akcjami niestandardowymi lub akcjami niestandardowymi w kontekście niestandardowego typu zasobu.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku CustomResources w pliku **galerii. JSON**.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|resourceType|Tak|Niestandardowy typ zasobu. Musi być **unikatowym** niestandardowym typem zasobu niestandardowego dostawcy.|
|ikona|Nie|Ikona widoku. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia Utwórz zasób niestandardowy. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md)|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi widoku CustomResources, zobacz [polecenia](#commands).|
|Columns|Nie|Tablica kolumn zasobu niestandardowego. Jeśli nie została zdefiniowana, kolumna `name` będzie wyświetlana domyślnie. Kolumna musi mieć `"key"` i `"displayName"`. W polu klucz Podaj klucz właściwości, która ma być wyświetlana w widoku. Jeśli jest zagnieżdżony, użyj kropki jako ogranicznika, na przykład `"key": "name"` lub `"key": "properties.property1"`. W polu Nazwa wyświetlana Podaj nazwę wyświetlaną właściwości, która ma być wyświetlana w widoku. Możesz również podać Właściwość `"optional"`. Po ustawieniu na wartość true kolumna jest domyślnie ukryta w widoku.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Polecenia

Polecenia to tablica dodatkowych przycisków paska narzędzi, które są wyświetlane na stronie. Każde polecenie reprezentuje akcję POST z niestandardowego dostawcy platformy Azure, która została zdefiniowana w pliku **mainTemplate. JSON**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie dostawców niestandardowych platformy Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlana nazwa przycisku polecenia.|
|ścieżka|Tak|Nazwa akcji dostawcy niestandardowego. Akcja musi być zdefiniowana w **mainTemplate. JSON**.|
|ikona|Nie|Ikona przycisku polecenia. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>związku

`"kind": "Associations"`

Można zdefiniować wiele widoków tego typu. Ten widok pozwala połączyć istniejące zasoby z zarządzaną aplikacją za pomocą dostawcy niestandardowego zdefiniowanego w **mainTemplate. JSON**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](custom-providers-overview.md).

W tym widoku można rozłożyć istniejące zasoby platformy Azure na podstawie `targetResourceType`. Po wybraniu zasobu zostanie utworzone żądanie dołączania do **publicznego** dostawcy niestandardowego, które może zastosować efekt uboczny do zasobu. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku skojarzenia w pliku **galerii. JSON**.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|targetResourceType|Tak|Docelowy typ zasobu. Jest to typ zasobu, który będzie wyświetlany na potrzeby dołączania do zasobów.|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia Utwórz zasób skojarzenia. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące Azure Managed Applications, spróbuj zadać pytanie na [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag `azure-managedapps`, aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
- Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie dostawców niestandardowych platformy Azure](custom-providers-overview.md).
- Aby utworzyć aplikację zarządzaną platformy Azure z dostawcami niestandardowymi platformy Azure, zobacz [Samouczek: Tworzenie aplikacji zarządzanej przy użyciu akcji dostawcy niestandardowego i typów zasobów](tutorial-create-managed-app-with-custom-provider.md)
