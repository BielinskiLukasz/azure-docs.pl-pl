---
title: Schemat zdarzeń w usłudze Azure Event Grid Container Registry
description: Opisuje właściwości, które są dostarczane dla zdarzeń odmowa kontenera za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: 6f00d4f249543ece0eb8db4a8e040300d55b2de8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462848"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Schemat zdarzeń Azure Event Grid dla rejestru kontenerów

Ten artykuł zawiera właściwości i schematu zdarzeń rejestru kontenerów. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Magazyn obiektów blob emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Wywoływane, gdy obraz jest przekazywane. |
| Microsoft.ContainerRegistry.ImageDeleted | Wywoływane, gdy obraz zostanie usunięty. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu obraz wypchnięty zdarzeń: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia usunięto obraz jest podobne:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| temat | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Identyfikator zdarzenia. |
| sygnatura czasowa | ciąg | Czas, w którym wystąpiło zdarzenie. |
| action | ciąg | Akcja, która obejmuje podanego zdarzenia. |
| cel | obiekt | Obiekt docelowy zdarzenia. |
| żądanie | obiekt | Żądanie, który wygenerował zdarzenie. |

Obiekt docelowy ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| mediaType | ciąg | Typ MIME przywoływanego obiektu. |
| rozmiar | liczba całkowita | Liczba bajtów treści. Taka sama jak długość pola. |
| Podsumowanie | ciąg | Skrót zawartości, zgodnie z definicją w specyfikacji interfejsu API HTTP V2 rejestru. |
| Długość | liczba całkowita | Liczba bajtów treści. Taka sama jak rozmiar pola. |
| repozytorium | ciąg | Nazwa repozytorium. |
| tag | ciąg | Nazwa tagu. |

Obiekt żądania ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Identyfikator żądania, który zainicjował zdarzenie. |
| addr | ciąg | Adresu IP lub nazwę hosta i prawdopodobnie portu połączenia klienta, który zainicjował zdarzenie. Ta wartość jest RemoteAddr z żądania standardowego protokołu http. |
| host | ciąg | Dostępne z zewnątrz nazwę hosta wystąpienia rejestru, określony przez nagłówka hosta http na żądań przychodzących. |
| method | ciąg | Metoda żądania, który wygenerował zdarzenie. |
| useragent | ciąg | Nagłówek agenta użytkownika żądania. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
