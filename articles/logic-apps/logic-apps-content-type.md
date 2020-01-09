---
title: Obsługa typów zawartości
description: Dowiedz się, jak obsługiwać różne typy zawartości w przepływach pracy w czasie projektowania i czasie wykonywania w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666877"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Obsługa typów zawartości w Azure Logic Apps

Różne typy zawartości mogą przepływać za pomocą aplikacji logiki, na przykład JSON, XML, plików prostych i danych binarnych. Chociaż Logic Apps obsługuje wszystkie typy zawartości, niektóre mają natywną obsługę i nie wymagają rzutowania ani konwersji na aplikacje logiki. Inne typy mogą wymagać rzutowania lub konwersji w razie potrzeby. W tym artykule opisano, jak Logic Apps obsługuje typy zawartości i jak można prawidłowo rzutować lub konwertować te typy w razie potrzeby.

Aby określić odpowiedni sposób obsługi typów zawartości, Logic Apps opiera się `Content-Type` wartości nagłówka w wywołaniach HTTP, na przykład:

* [Application/JSON](#application-json) (typ natywny)
* [Text/zwykły](#text-plain) (typ natywny)
* [Aplikacja/XML i aplikacja/strumień oktetu](#application-xml-octet-stream)
* [Inne typy zawartości](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps przechowuje i obsługuje dowolne żądanie z typem zawartości *Application/JSON* jako obiektem notacji JavaScript (JSON). Domyślnie można analizować zawartość JSON bez żadnego rzutowania. Aby przeanalizować żądanie, które ma nagłówek z typem zawartości "Application/JSON", można użyć wyrażenia. Ten przykład zwraca wartość `dog` z tablicy `animal-type` bez rzutowania: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Jeśli pracujesz z danymi JSON, które nie określają nagłówka, możesz ręcznie rzutować te dane na kod JSON przy użyciu [funkcji JSON ()](../logic-apps/workflow-definition-language-functions-reference.md#json), na przykład: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Utwórz tokeny dla właściwości JSON

Logic Apps oferuje możliwość generowania tokenów przyjaznych dla użytkownika, które reprezentują właściwości w zawartości JSON, dzięki czemu można łatwiej odwoływać się do tych właściwości i używać ich w przepływie pracy aplikacji logiki.

* **Wyzwalacz żądania**

  W przypadku użycia tego wyzwalacza w Projektancie aplikacji logiki można podać schemat JSON, który opisuje oczekiwany ładunek. 
  Projektant analizuje zawartość JSON przy użyciu tego schematu i generuje tokeny przyjazne dla użytkownika, które reprezentują właściwości w zawartości JSON. 
  Następnie możesz łatwo odwoływać się do tych właściwości i używać ich w ramach przepływu pracy aplikacji logiki. 
  
  Jeśli nie masz schematu, możesz wygenerować schemat. 
  
  1. W wyzwalaczu żądania wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.  
  
  2. W obszarze **wprowadzanie lub wklejanie przykładowego ładunku JSON**Podaj przykładowy ładunek, a następnie wybierz pozycję **gotowe**. Przykład: 

     ![Podaj przykładowy ładunek JSON](./media/logic-apps-content-type/request-trigger.png)

     Wygenerowany schemat zostanie wyświetlony w wyzwalaczu.

     ![Podaj przykładowy ładunek JSON](./media/logic-apps-content-type/generated-schema.png)

     Oto podstawowa definicja wyzwalacza żądania w edytorze widoku kodu:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. W żądaniu upewnij się, że dołączysz nagłówek `Content-Type` i ustawisz wartość nagłówka na `application/json`.

* **Analiza akcji JSON**

  W przypadku korzystania z tej akcji w Projektancie aplikacji logiki można analizować dane wyjściowe JSON i generować tokeny przyjazne dla użytkownika, które reprezentują właściwości w zawartości JSON. 
  Następnie możesz łatwo odwoływać się do tych właściwości i używać ich w ramach przepływu pracy aplikacji logiki. Podobnie jak w przypadku wyzwalacza żądania, można dostarczyć lub wygenerować schemat JSON, który opisuje zawartość JSON, która ma zostać przeanalizowana. 
  Dzięki temu można łatwiej korzystać z danych z Azure Service Bus, Azure Cosmos DB i tak dalej.

  ![Przeanalizuj dane JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Gdy aplikacja logiki otrzymuje komunikaty HTTP z nagłówkiem `Content-Type` ustawionym na `text/plain`, aplikacja logiki przechowuje te komunikaty w postaci nieprzetworzonej. Jeśli te komunikaty są uwzględniane w kolejnych akcjach bez rzutowania, żądania są przekazywane z nagłówkiem `Content-Type` ustawionym na `text/plain`. 

Na przykład podczas pracy z plikiem prostym można uzyskać żądanie HTTP z nagłówkiem `Content-Type` ustawionym na `text/plain` typ zawartości:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Jeśli następnie wyślesz to żądanie w późniejszej akcji jako treść innego żądania, na przykład `@body('flatfile')`, to drugie żądanie ma także nagłówek `Content-Type`, który jest ustawiony na `text/plain`. Jeśli pracujesz z danymi, które są zwykłym tekstem, ale nie określono nagłówka, możesz ręcznie rzutować te dane na tekst za pomocą [funkcji String ()](../logic-apps/workflow-definition-language-functions-reference.md#string) , takiej jak to wyrażenie: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Aplikacja/XML i aplikacja/strumień oktetu

Logic Apps zawsze zachowuje `Content-Type` w odebranym żądaniu HTTP lub odpowiedzi. Dlatego jeśli aplikacja logiki otrzymuje zawartość z `Content-Type` ustawioną na `application/octet-stream`, a ta zawartość zostanie uwzględniona w późniejszej akcji bez rzutowania, żądanie wychodzące również ma `Content-Type` ustawione na `application/octet-stream`. Dzięki temu Logic Apps mogą zagwarantować, że dane nie zostaną utracone podczas przechodzenia przez przepływ pracy. Jednak stan akcji lub dane wejściowe i wyjściowe są przechowywane w obiekcie JSON, podczas gdy stan przechodzi przez przepływ pracy. 

## <a name="converter-functions"></a>Funkcje konwertera

W celu zachowania niektórych typów danych Logic Apps konwertuje zawartość na binarny ciąg szyfrowany algorytmem Base64 z odpowiednimi metadanymi, które zachowują zarówno ładunek `$content`, jak i `$content-type`, które są automatycznie konwertowane. 

Ta lista zawiera opis sposobu, w jaki Logic Apps konwertuje zawartość podczas korzystania z tych [funkcji](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: rzutuje dane na `application/json`
* `xml()`: rzutuje dane na `application/xml`
* `binary()`: rzutuje dane na `application/octet-stream`
* `string()`: rzutuje dane na `text/plain`
* `base64()`: konwertuje zawartość na ciąg zakodowany w formacie base64
* `base64toString()`: konwertuje ciąg zakodowany algorytmem Base64 na `text/plain`
* `base64toBinary()`: konwertuje ciąg zakodowany algorytmem Base64 na `application/octet-stream`
* `dataUri()`: konwertuje ciąg na identyfikator URI danych
* `dataUriToBinary()`: konwertuje identyfikator URI danych na ciąg binarny
* `dataUriToString()`: konwertuje identyfikator URI danych na ciąg

Na przykład, jeśli otrzymasz żądanie HTTP, w którym `Content-Type` ustawione na `application/xml`, takie jak ta zawartość:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Tę zawartość można rzutować za pomocą wyrażenia `@xml(triggerBody())` z funkcjami `xml()` i `triggerBody()`, a następnie później używać tej zawartości. Można też użyć wyrażenia `@xpath(xml(triggerBody()), '/CustomerName')` z funkcjami `xpath()` i `xml()`. 

## <a name="other-content-types"></a>Inne typy zawartości

Logic Apps współpracuje z innymi typami zawartości i obsługuje je, ale mogą wymagać ręcznego pobrania treści komunikatu przez odkodowanie zmiennej `$content`.

Załóżmy na przykład, że aplikacja logiki zostanie wyzwolona przez żądanie z typem zawartości `application/x-www-url-formencoded`. Aby zachować wszystkie dane, zmienna `$content` w treści żądania ma ładunek zakodowany jako ciąg Base64:

`CustomerName=Frank&Address=123+Avenue`

Ponieważ żądanie nie jest zwykłym tekstem ani JSON, żądanie jest przechowywane w akcji w następujący sposób:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps udostępnia funkcje natywne do obsługi danych formularza, na przykład: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Lub można ręcznie uzyskać dostęp do danych przy użyciu wyrażenia, takiego jak ten przykład:

`@string(body('formdataAction'))` 

Jeśli chcesz, aby żądanie wychodzące miało ten sam `application/x-www-url-formencoded` nagłówku typu zawartości, możesz dodać żądanie do treści akcji bez żadnego rzutowania przy użyciu wyrażenia, takiego jak `@body('formdataAction')`. Jednakże ta metoda działa tylko wtedy, gdy treść jest jedynym parametrem w danych wejściowych `body`. Jeśli spróbujesz użyć wyrażenia `@body('formdataAction')` w żądaniu `application/json`, zostanie wyświetlony błąd czasu wykonywania, ponieważ treść jest wysyłana jako zakodowana.
