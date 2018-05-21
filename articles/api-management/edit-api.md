---
title: Edytowanie interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do edytowania interfejsu API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: b39259fcfc93cb0a2a1a2dc600e5235da8cc6930
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="edit-an-api"></a>Edytowanie interfejsu API

Kroki opisane w tym samouczku umożliwiają użycie usługi API Management (APIM) do edytowania interfejsu API. 

+ Można dodawać i usuwać operacje oraz zmieniać ich nazwy w wystąpieniu usługi APIM. 
+ Można edytować program Swagger interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
+ [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Edytowanie interfejsu API w usłudze APIM

![Edytowanie interfejsu API](./media/edit-api/edit-api001.png)

1. Kliknij kartę **Interfejsy API**.
2. Wybierz jeden z wcześniej zaimportowanych interfejsów API.
3. Wybierz kartę **Projekt**.
4. Wybierz operację, którą chcesz edytować.
5. Aby zmienić nazwę operacji, wybierz ikonę **ołówka** w oknie **Fronton**.

## <a name="update-the-swagger"></a>Aktualizowanie programu Swagger

Możesz zaktualizować interfejs API zaplecza w witrynie Azure Portal, wykonując następujące czynności:

1. Wybierz pozycję **Wszystkie operacje**.
2. Kliknij ikonę ołówka w oknie **Fronton**.

    ![Edytowanie interfejsu API](./media/edit-api/edit-api002.png)

    Zostanie wyświetlony program Swagger interfejsu API.

    ![Edytowanie interfejsu API](./media/edit-api/edit-api003.png)

3. Zaktualizuj program Swagger.
4. Naciśnij pozycję **Zapisz**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady zasad usługi APIM](policy-samples.md)
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)