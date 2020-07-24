---
title: Terminologia dotycząca platformy Azure API Management | Microsoft Docs
description: Ten artykuł zawiera definicje terminów specyficznych dla API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 5bc76d2526c5585071a240af36b8a31e3de5708f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024932"
---
# <a name="azure-api-management-terminology"></a>Terminologia dotycząca API Management platformy Azure

Ten artykuł zawiera definicje terminów specyficznych dla API Management (APIM).

## <a name="term-definitions"></a>Definicje terminów

* **Interfejs API zaplecza** — usługa http IMPLEMENTUJĄCA interfejs API i jego operacje. 
* **Interfejs API** / frontonu **Interfejs API usługi APIM** — interfejs API APIM nie obsługuje interfejsów API, ale tworzy fasad dla interfejsów API, aby dostosować elewację zgodnie z potrzebami bez dotykania interfejsu API zaplecza. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie interfejsu API](import-and-publish.md).
* **Produkt APIM** — produkt zawiera jeden lub więcej interfejsów API, a także limit przydziału użycia i warunki użytkowania. Możesz dołączyć wiele interfejsów API i zaoferować je deweloperom za pomocą portalu dla deweloperów. Aby uzyskać więcej informacji, zobacz [Tworzenie i publikowanie produktu](api-management-howto-add-products.md).
* **Operacja interfejsu API APIM** — każdy interfejs API APIM reprezentuje zestaw operacji dostępnych dla deweloperów. Każdy interfejs API APIM zawiera odwołanie do usługi zaplecza implementującej interfejs API, a jej operacje są mapowane na operacje implementowane przez usługę zaplecza. Aby uzyskać więcej informacji, zobacz [symulacja odpowiedzi interfejsu API](mock-api-responses.md).
* **Wersja** — Czasami chcesz opublikować nowe lub inne funkcje interfejsu API dla niektórych użytkowników, podczas gdy inne chcą nawiązać z INTERFEJSem API, który jest aktualnie dla nich dostępny. Aby uzyskać więcej informacji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).
* **Poprawka** — gdy interfejs API jest gotowy do użycia przez deweloperów, zazwyczaj należy koniecznie wprowadzić zmiany w tym interfejsie API i w tym samym czasie nie zakłócają WYWOŁYWANIA interfejsu API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. Aby uzyskać więcej informacji, zobacz [Korzystanie z poprawek](api-management-get-started-revise-api.md).
* **Portal dla deweloperów** — klienci (deweloperzy) powinni korzystać z portalu dla deweloperów w celu uzyskiwania dostępu do interfejsów API. Portal dla deweloperów można dostosować. Aby uzyskać więcej informacji, zobacz [Dostosowywanie portalu dla deweloperów](api-management-customize-styles.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wystąpienia](get-started-create-service-instance.md)

