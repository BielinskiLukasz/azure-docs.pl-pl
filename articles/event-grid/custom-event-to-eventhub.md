---
title: Wysyłanie zdarzeń niestandardowych dla usługi Azure Event Grid do usługi Event Hubs | Microsoft Docs
description: Za pomocą usługi Azure Event Grid i interfejsu wiersza polecenia platformy Azure można opublikować temat i zasubskrybować dane zdarzenie. Centrum zdarzeń jest używane dla punktu końcowego.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 06/06/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 4233884d02377f54fa7c12b4a4a016c283f66a8d
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851019"
---
# <a name="route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Kierowanie zdarzeń niestandardowych do usługi Azure Event Hubs za pomocą interfejsu wiersza polecenia platformy Azure i usługi Event Grid

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Usługa Azure Event Hubs jest jedną z obsługiwanych procedur obsługi zdarzeń. W tym artykule omówiono tworzenie tematu niestandardowego, subskrybowanie go i wyzwalanie zdarzenia w celu wyświetlenia wyniku za pomocą interfejsu wiersza polecenia platformy Azure. Zdarzenia są wysyłane do centrum zdarzeń.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *gridResourceGroup* w lokalizacji *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Tworzenie tematu niestandardowego

Temat usługi Event Grid udostępnia zdefiniowany przez użytkownika punkt końcowy, w którym publikowane są zdarzenia. Poniższy przykład obejmuje tworzenie tematu niestandardowego w grupie zasobów. Zamień `<your-topic-name>` na unikatową nazwę tematu. Nazwa tematu musi być unikatowa, ponieważ jest reprezentowana przez wpis DNS.

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Tworzenie centrum zdarzeń

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Utworzysz centrum zdarzeń w celu zbierania zdarzeń.

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>Subskrybowanie tematu

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić. Poniższy przykład ilustruje subskrybowanie utworzonego tematu i przekazywanie identyfikatora zasobu do centrum zdarzeń dla punktu końcowego. Punkt końcowy ma następujący format:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

Poniższy skrypt pobiera identyfikator zasobu dla centrum zdarzeń i subskrybuje temat usługi Event Grid. Ustawia on typ punktu końcowego na `eventhub` i używa identyfikatora centrum dla punktu końcowego.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --topic-name $topicname \
  -g gridResourceGroup \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

## <a name="send-an-event-to-your-topic"></a>Wysyłanie zdarzenia do tematu

Wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Po pierwsze uzyskajmy adres URL i klucz tematu niestandardowego.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

Aby uprościć ten artykuł, użyj przykładowych danych zdarzenia do wysłania do tematu. Zwykle dane zdarzenia są wysyłane przez aplikację lub usługę platformy Azure. CURL to narzędzie, które wysyła żądania HTTP. W tym artykule do wysłania zdarzenia do tematu używane jest narzędzie CURL.  W poniższym przykładzie są wysyłane trzy zdarzenia do tematu usługi Event Grid:

```azurecli-interactive
for i in 1 2 3
do
   body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
   curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
done
```

Przejdź do centrum zdarzeń w portalu i zwróć uwagę, że usługa Event Grid wysłała te trzy zdarzenia do centrum zdarzeń.

![Wyświetlanie komunikatów](./media/custom-event-to-eventhub/show-result.png)

Zazwyczaj jest tworzona aplikacja, która pobiera zdarzenia z centrum zdarzeń. Aby utworzyć aplikację, która odbiera komunikaty z centrum zdarzeń, zobacz:

* [Wprowadzenie do odbierania komunikatów za pomocą hosta procesora zdarzeń z biblioteki .NET Standard](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą języka Java](../event-hubs/event-hubs-java-get-started-receive-eph.md)
* [Odbieranie zdarzeń z usługi Event Hubs za pomocą usługi Apache Storm](../event-hubs/event-hubs-storm-getstarted-receive.md)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zamierzasz kontynuować pracę z tym zdarzeniem, nie usuwaj zasobów utworzonych w tym artykule. W przeciwnym razie użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Event Grid — informacje](overview.md)
- [Kierowanie zdarzeń usługi Blob Storage do niestandardowego internetowego punktu końcowego](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorowanie zmian maszyn wirtualnych za pomocą usług Azure Event Grid i Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Przesyłanie strumieniowe danych Big Data do magazynu danych](event-grid-event-hubs-integration.md)
