---
title: Tworzenie nowej aplikacji
titleSuffix: Language Understanding - Azure Cognitive Services
description: Utwórz aplikacje i zarządzaj nimi na stronie sieci Web Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 2d269a5ea012275b358cb177fc0b1d26a124c6d8
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887602"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Utwórz nową aplikację usługi LUIS w portalu usługi LUIS
Istnieje kilka sposobów, aby utworzyć aplikację usługi LUIS. Można utworzyć aplikację usługi LUIS w [LUIS](https://www.luis.ai) portalu lub za pomocą usługi LUIS tworzenia [interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Za pomocą portalu usługi LUIS
Można utworzyć nową aplikację w portalu usługi LUIS na kilka sposobów:

* Rozpocznij od pusta aplikacja i tworzyć intencji, wypowiedzi i jednostek.
* Start z pustą aplikacją, a następnie dodaj [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md).
* Importowanie aplikacji usługi LUIS z pliku JSON, który zawiera już intencji, wypowiedzi i jednostek.

## <a name="using-the-authoring-apis"></a>Za pomocą tworzenia interfejsów API
Można utworzyć nową aplikację za pomocą tworzenia interfejsów API na kilka sposobów:

* [Rozpocznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) z pustą aplikację i utworzyć intencji, wypowiedzi i jednostek.
* [Rozpocznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) przy użyciu wbudowanych domeny.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Utwórz nową aplikację w usługi LUIS

1. Na **Moje aplikacje** wybierz opcję **Utwórz nową aplikację**.

    ![Lista aplikacji LUIS](./media/luis-create-new-app/apps-list.png)


2. W oknie dialogowym Nazwa aplikacji "TravelAgent".

    ![Utwórz nowe okno dialogowe aplikacji](./media/luis-create-new-app/create-app.png)

3. Wybierz Twojej kulturze aplikacji (TravelAgent aplikacji, wybierz język angielski), a następnie wybierz pozycję **gotowe**. 

    > [!NOTE]
    > Kultury nie można zmienić po utworzeniu aplikacji. 


## <a name="next-steps"></a>Kolejne kroki

Pierwsze zadanie w aplikacji jest [Dodawanie intencji](luis-how-to-add-intents.md).