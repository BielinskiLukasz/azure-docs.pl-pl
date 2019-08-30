---
title: Tworzenie, edytowanie lub rozszerzone dane JSON dla definicji aplikacji logiki — Azure Logic Apps | Microsoft Docs
description: Tworzenie i rozszerzone dane JSON dla definicji aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: a96cc56c85db6726bf2cdaff72904e76ecbaf087
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164651"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Tworzenie, edytowanie lub rozszerzone dane JSON dla definicji aplikacji logiki w Azure Logic Apps

W przypadku tworzenia rozwiązań integracji dla przedsiębiorstw przy użyciu zautomatyzowanych przepływów pracy w [Azure Logic Apps](../logic-apps/logic-apps-overview.md), podstawowe definicje aplikacji logiki używają prostych i deklaratywnych JavaScript Object Notation (JSON) wraz z [językiem definicji przepływu pracy (WDL) Schemat](../logic-apps/logic-apps-workflow-definition-language.md) opisu i walidacji. Te formaty umożliwiają łatwiejsze odczytywanie i zrozumienie definicji aplikacji logiki, nie wiedząc o kodzie. Aby zautomatyzować tworzenie i wdrażanie aplikacji logiki, możesz uwzględnić definicje aplikacji logiki jako [zasoby platformy Azure](../azure-resource-manager/resource-group-overview.md) w ramach [szablonów Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md). Aby tworzyć i wdrażać aplikacje logiki oraz zarządzać nimi, można użyć [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)lub [interfejsów API REST Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Aby pracować z definicjami aplikacji logiki w formacie JSON, Otwórz Edytor widoku kodu podczas pracy w Azure Portal lub w programie Visual Studio lub skopiuj definicję do dowolnego edytora, który chcesz. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem jak utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Niektóre Azure Logic Apps możliwości, takie jak Definiowanie parametrów i wiele wyzwalaczy w definicjach aplikacji logiki, są dostępne tylko w formacie JSON, a nie w projektancie Logic Apps.
> W tym celu należy korzystać z widoku kodu lub innego edytora.

## <a name="edit-json---azure-portal"></a>Edytuj plik JSON — Azure Portal

1. Zaloguj się w witrynie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Z menu po lewej stronie wybierz pozycję **wszystkie usługi**. W polu wyszukiwania Znajdź pozycję "Aplikacje logiki", a następnie z wyników wybierz aplikację logiki.

3. W menu aplikacji logiki w obszarze **Narzędzia programistyczne**wybierz pozycję **Widok kodu aplikacji logiki**.

   Zostanie otwarty Edytor widoku kodu z definicją aplikacji logiki w formacie JSON.

## <a name="edit-json---visual-studio"></a>Edytowanie JSON — Visual Studio

Przed rozpoczęciem pracy z definicją aplikacji logiki w programie Visual Studio upewnij się, że zainstalowano [wymagane narzędzia](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Aby utworzyć aplikację logiki przy użyciu programu Visual Studio, [zapoznaj się z przewodnikiem Szybki Start: Automatyzowanie zadań i procesów przy użyciu Azure Logic Apps —](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)Visual Studio.

W programie Visual Studio można otwierać aplikacje logiki, które zostały utworzone i wdrożone bezpośrednio w Azure Portal lub jako projekty Azure Resource Manager z programu Visual Studio.

1. Otwórz rozwiązanie Visual Studio lub projekt [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) , który zawiera aplikację logiki.

2. Znajdź i Otwórz definicję aplikacji logiki, która domyślnie jest wyświetlana w [szablonie Menedżer zasobów](../azure-resource-manager/template-deployment-overview.md)o nazwie **LogicApp. JSON**. Można użyć i dostosować ten szablon do wdrożenia w różnych środowiskach.

3. Otwórz menu skrótów dla definicji i szablonu aplikacji logiki. Wybierz pozycję **Otwórz przy użyciu Projektanta aplikacji logiki**.

   ![Otwieranie aplikacji logiki w rozwiązaniu programu Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Jeśli nie masz tego polecenia w programie Visual Studio 2019, sprawdź, czy masz najnowsze aktualizacje programu Visual Studio.

4. W dolnej części projektanta wybierz **Widok kod**. 

   Zostanie otwarty Edytor widoku kodu z definicją aplikacji logiki w formacie JSON.

5. Aby powrócić do widoku projektanta, w dolnej części edytora widoku kodu wybierz **projekt**.

## <a name="parameters"></a>Parametry

Cykl życia wdrożenia ma zwykle różne środowiska do projektowania, testowania, przemieszczania i produkcji. Jeśli masz wartości, które chcesz ponownie użyć w całej aplikacji logiki bez zakodowana lub które różnią się w zależności od potrzeb wdrażania, możesz utworzyć [szablon Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) dla definicji przepływu pracy, aby można było również zautomatyzować aplikację logiki mieszczeniu. 

Postępuj zgodnie z tymi ogólnymi krokami, aby *Sparametryzuj*lub definiować i używać parametrów dla tych wartości. Następnie można podać wartości w osobnym pliku parametrów, który przekazuje te wartości do szablonu. Dzięki temu można łatwiej zmieniać te wartości bez konieczności aktualizacji i ponownego wdrażania aplikacji logiki. Aby uzyskać szczegółowe informacje, [Zobacz Omówienie: Automatyzowanie wdrażania aplikacji logiki za pomocą szablonów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)Azure Resource Manager.

1. W szablonie Zdefiniuj parametry szablonu i parametry definicji przepływu pracy służące do akceptowania wartości używanych odpowiednio do wdrożenia i środowiska uruchomieniowego.

   Parametry szablonu są zdefiniowane w sekcji parametrów, która znajduje się poza definicją przepływu pracy, a parametry definicji przepływu pracy są zdefiniowane w sekcji parametrów, która znajduje się w definicji przepływu pracy.

1. Zastąp wartości stałe wartościami wyrażeń, które odwołują się do tych parametrów. Wyrażenia szablonów używają składni, która różni się od wyrażeń definicji przepływu pracy.

   Unikaj skomplikowanego kodu, nie używając wyrażeń szablonów, które są oceniane we wdrożeniu w wyrażeniach definicji przepływu pracy, które są oceniane w czasie wykonywania. Używaj tylko wyrażeń szablonu poza definicją przepływu pracy. W definicji przepływu pracy używaj tylko wyrażeń definicji przepływu pracy.

   Podczas określania wartości parametrów definicji przepływu pracy można odwoływać się do parametrów szablonu za pomocą sekcji parametrów, która znajduje się poza definicją przepływu pracy, ale nadal w definicji zasobu dla aplikacji logiki. Dzięki temu można przekazać wartości parametrów szablonu do parametrów definicji przepływu pracy.

1. Przechowuj wartości parametrów w osobnym [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) i Uwzględnij ten plik we wdrożeniu.

## <a name="process-strings-with-functions"></a>Przetwarzanie ciągów przy użyciu funkcji

Logic Apps ma różne funkcje do pracy z ciągami. Załóżmy na przykład, że chcesz przekazać nazwę firmy z zamówienia do innego systemu. Jednak nie masz pewności co do prawidłowego obsłudze kodowania znaków. W tym ciągu można wykonać kodowanie Base64, ale aby uniknąć ucieczki w adresie URL, można zamienić kilka znaków. Ponadto potrzebny jest tylko podciąg dla nazwy firmy, ponieważ pięć pierwszych znaków nie jest używanych.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

W tych krokach opisano, jak ten przykład przetwarza ten ciąg, pracując od wewnątrz do zewnętrznego:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) Pobierz nazwę firmy, aby uzyskać całkowitą liczbę znaków.

2. Aby uzyskać krótszy ciąg, Odejmij `5`.

3. Uzyskaj [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)teraz. Rozpocznij od indeksu `5`i przejdź do pozostałej części ciągu.

4. Przekonwertuj ten podciąg na [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) ciąg.

5. Teraz [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) wszystkie`+` znaki z`-` znakami.

6. `_` Na [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) koniecwszystkieznakizznakami`/` .

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapuj elementy listy na wartości właściwości, a następnie użyj map jako parametrów

Aby uzyskać różne wyniki na podstawie wartości właściwości, można utworzyć mapę, która dopasowuje poszczególne wartości właściwości do wyniku, a następnie użyć tej mapy jako parametru.

Na przykład ten przepływ pracy definiuje niektóre kategorie jako parametry i mapę, która pasuje do tych kategorii z określonym adresem URL. Pierwszy przepływ pracy pobiera listę artykułów. Następnie przepływ pracy używa mapy, aby znaleźć adres URL zgodny z kategorią dla każdego artykułu.

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) Funkcja sprawdza, czy kategoria pasuje do znanej zdefiniowanej kategorii.

*   Po otrzymaniu pasującej kategorii przykład ściąga element z mapy przy użyciu nawiasów kwadratowych:`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Pobieranie danych za pomocą funkcji daty

Aby uzyskać dane ze źródła danych, które nie obsługuje natywnie *wyzwalaczy*, można użyć funkcji daty do pracy z godzinami i datami. Na przykład to wyrażenie stwierdza, jak długo trwa wykonywanie kroków tego przepływu pracy z wnętrza do zewnątrz:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. `order` Z akcji Wyodrębnij `startTime`.
2. Pobierz bieżącą godzinę w usłudze `utcNow()`.
3. Odejmij jedną sekundę:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Możesz użyć innych jednostek czasu, takich jak `minutes` lub. `hours`

3. Teraz można porównać te dwie wartości. 

   Jeśli pierwsza wartość jest mniejsza od drugiej wartości, to więcej niż jedna sekunda została przeniesiona od momentu pierwszego umieszczenia kolejności.

Aby sformatować daty, można użyć elementów formatujących ciągi. Na przykład, aby uzyskać RFC1123, użyj [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). Dowiedz się więcej na temat [formatowania dat](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Następne kroki

* [Wykonaj kroki na podstawie warunku (instrukcje warunkowe)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Wykonaj kroki na podstawie różnych wartości (przełącznik instrukcji switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Uruchom i powtórz kroki (pętle)](../logic-apps/logic-apps-control-flow-loops.md)
* [Uruchamianie lub scalanie równoległych kroków (gałęzie)](../logic-apps/logic-apps-control-flow-branches.md)
* [Wykonaj kroki na podstawie pogrupowanego stanu akcji (zakresy)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Dowiedz się więcej o [schemacie języka definicji przepływu pracy dla Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [akcjach i wyzwalaczach przepływu pracy dla Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
