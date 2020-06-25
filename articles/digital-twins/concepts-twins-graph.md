---
title: Bliźniacze reprezentacje cyfrowe i graf bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zrozumienie koncepcji wieloosiowej i sposobu tworzenia grafu przez ich relacje.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 79035a2fa2f7f3bed4890faa749661f025cea312
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362870"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Informacje na temat bliźniaczych reprezentacji cyfrowych i ich bliźniaczych wykresów

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

W rozwiązaniu Digital bliźniaczych reprezentacji na platformie Azure jednostki w Twoim środowisku są reprezentowane przez usługę Azure **Digital bliźniaczych reprezentacji**. Cyfrowa dwuosiowa to wystąpienie jednego z [modeli](concepts-models.md)zdefiniowanych przez użytkownika. Może być połączony z innym cyfrowym bliźniaczych reprezentacji poprzez **relacje** , aby utworzyć **Wykres dwuosiowy**: ten wykres dwuosiowy jest reprezentacją całego środowiska.

> [!TIP]
> "Usługa Azure Digital bliźniaczych reprezentacji" odnosi się do tej usługi platformy Azure jako całości. "Pojedyncze sznury cyfrowe" lub "same sznury" odnoszą się do pojedynczych węzłów bliźniaczych wewnątrz Twojego wystąpienia usługi.

## <a name="creating-digital-twins"></a>Tworzenie bliźniaczych reprezentacji cyfrowych

Aby można było utworzyć dwuosiową cyfrę w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, musisz mieć *model* przekazany do usługi. Model opisuje zestaw właściwości, wiadomości telemetrycznych i relacje, które mogą mieć poszczególne sznurki, między innymi. Aby uzyskać informacje o typach informacji, które są zdefiniowane w modelu, zobacz [pojęcia: modele niestandardowe](concepts-models.md).

Po utworzeniu i przekazaniu modelu aplikacja kliencka może utworzyć wystąpienie typu; to jest dwuosiowa cyfra. Na przykład po utworzeniu modelu *podłogi*można utworzyć jedną lub kilka bliźniaczych reprezentacji cyfrowych, które używają tego typu (takich jak sznury typu *podłogi*o nazwie *GroundFloor*, inne o nazwie *Floor2*itp.). 

Poniżej znajduje się fragment kodu klienta, który używa [interfejsów API DigitalTwins](how-to-use-apis-sdks.md) do tworzenia wycinków typu *pokoju*.

W bieżącej wersji zapoznawczej usługi Azure Digital bliźniaczych reprezentacji wszystkie właściwości sznurka muszą zostać zainicjowane przed utworzeniem sznurka. Jest to realizowane przez utworzenie dokumentu JSON, który zawiera wymagane wartości inicjalizacji.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

## <a name="relationships-creating-a-graph-of-digital-twins"></a>Relacje: Tworzenie grafu cyfrowego bliźniaczych reprezentacji

Bliźniaczych reprezentacji są połączone z wykresem bliźniaczym przez ich relacje. Relacje, które mogą mieć sznurek, są zdefiniowane jako część jego modelu.  

Na przykład model *podłogi* może definiować relację *zawierania* , która jest przeznaczona dla bliźniaczych reprezentacji typu *pokoju*. W przypadku tej definicji usługa Azure Digital bliźniaczych reprezentacji umożliwia tworzenie relacji między *dowolnym* *podłogą piętra* a dowolnym przędzą za *Pokój* (w tym bliźniaczych reprezentacji, *Room* które są podtypem). 

Poniżej przedstawiono przykładowy kod klienta korzystający z [interfejsów API DigitalTwins](how-to-use-apis-sdks.md) do tworzenia relacji między cyframi cyfrowymi typu *podłogi*o nazwie *GroundFloor* i sznurem cyfrowym typu *Room*o nazwie *Cafe*.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

Wynikiem tego procesu jest zestaw węzłów (Digital bliźniaczych reprezentacji) połączony za pośrednictwem krawędzi (ich relacji) w grafie.

## <a name="json-representations-of-graph-elements"></a>Reprezentacje JSON elementów grafu

Cyfrowe dane dwuosiowe i dane relacji są przechowywane w formacie JSON. Oznacza to, że po wykonaniu [zapytania dotyczącego grafu bliźniaczyego](how-to-query-graph.md) w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, wynik będzie reprezentacją JSON bliźniaczych reprezentacji i utworzonych relacji.

### <a name="digital-twin-json-format"></a>Format JSON cyfrowego przędzy

Gdy jest reprezentowany jako obiekt JSON, dwuosiowy symbol cyfrowy będzie wyświetlał następujące pola:

| Nazwa pola | Opis |
| --- | --- |
| `$dtId` | Ciąg dostarczony przez użytkownika reprezentujący identyfikator dwuosiowego |
| `$conformance` | Wyliczenie zawierające stan zgodności tej dwucyfrowej sznurka *(niezgodne,* *niezgodne*, *nieznany*) |
| `{propertyName}` | Wartość właściwości w formacie JSON ( `string` , typ numeru lub obiekt) |
| `$relationships` | Adres URL ścieżki do kolekcji Relationships. To pole jest nieobecne, jeśli cyfrowa dwuosiowa nie ma żadnych wychodzących krawędzi relacji. |
| `$metadata.$model` | Obowiązkowe Identyfikator interfejsu modelu, który charakteryzuje ten dwucyfrowego przędzy |
| `$metadata.{propertyName}.desiredValue` | [Tylko do zapisu właściwości] Wymagana wartość określonej właściwości |
| `$metadata.{propertyName}.desiredVersion` | [Tylko do zapisu właściwości] Wersja żądanej wartości |
| `$metadata.{propertyName}.ackVersion` | Wersja potwierdzona przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.ackCode` | [Tylko do zapisu właściwości] `ack`Kod zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.ackDescription` | [Tylko do zapisu właściwości] `ack`Opis zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `{componentName}` | Obiekt JSON zawierający wartości właściwości i metadanych składnika, podobnie jak w przypadku obiektu głównego. Ten obiekt istnieje, nawet jeśli składnik nie ma właściwości. |
| `{componentName}.{propertyName}` | Wartość właściwości składnika w formacie JSON ( `string` , typ numeru lub obiekt) |
| `{componentName}.$metadata` | Informacje o metadanych składnika, podobne do poziomu głównego`$metadata` |

Poniżej znajduje się przykład cyfrowego sznurka sformatowanego jako obiekt JSON:

```json
{
  "$dtId": "Cafe",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "$model": "dtmi:com:contoso:Table;1",
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Format JSON relacji

Gdy jest reprezentowany jako obiekt JSON, relacja z dwucyfrowego sznurka będzie wyświetlać następujące pola:

| Nazwa pola | Opis |
| --- | --- |
| `$edgeId` | Ciąg dostarczony przez użytkownika reprezentujący identyfikator tej krawędzi relacji. Ten ciąg jest unikatowy w kontekście źródłowej cyfrowej przędzy, co oznacza, że `sourceId`  +  `edgeId` jest unikatowy w kontekście wystąpienia usługi Azure Digital bliźniaczych reprezentacji. |
| `$sourceId` | Identyfikator źródłowej dwuosiowej |
| `$targetId` | Identyfikator docelowej przędzy cyfrowej |
| `$relationshipName` | Nazwa relacji |
| `{propertyName}` | Obowiązkowe Wartość właściwości tej relacji, w formacie JSON ( `string` , typ liczby lub obiekt) |

Oto przykład relacji sformatowanej jako obiekt JSON:

```json
{
  "$edgeId": "Edge-01",
  "$sourceId": "GroundFloor",
  "$relationship": "contains",
  "$targetId": "Cafe",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz, jak zarządzać elementami grafu za pomocą interfejsów API cyfrowych przędzy na platformie Azure:
* [Instrukcje: Zarządzanie bliźniaczych reprezentacji cyfrowym](how-to-manage-twin.md)
* [Instrukcje: Zarządzanie wykresem bliźniaczym z relacjami](how-to-manage-graph.md)

Lub Dowiedz się więcej na temat wykonywania zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure w celu uzyskania informacji:
* [Koncepcje: język zapytań](concepts-query-language.md)