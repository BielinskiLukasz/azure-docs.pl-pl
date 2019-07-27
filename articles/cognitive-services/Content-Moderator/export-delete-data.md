---
title: Eksportowanie lub usuwanie danych użytkownika — Content Moderator
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak eksportować lub usuwać dane w Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: b18bc2907c00ed1424db440f09c914faff6a9863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565564"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Eksportowanie lub usuwanie danych użytkownika w Content Moderator

Content Moderator zbiera dane użytkowników w celu obsługi usługi, ale klienci mają pełną kontrolę, aby wyświetlać, eksportować i usuwać swoje dane przy użyciu [Narzędzia Recenzja](https://contentmoderator.cognitive.microsoft.com/) oraz [interfejsów API moderowania i przeglądania](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat eksportowania i usuwania danych użytkownika w Content Moderator, zobacz poniższą tabelę.

| Data | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | ND | Usuń przy użyciu Azure Portal (subskrypcje platformy Azure). Lub użyj przycisku **Usuń zespół** na stronie ustawień zespołu [przeglądu interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) . |
| Obrazy do dopasowania niestandardowego | Wywołaj [interfejs API pobierania identyfikatorów obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrazy są przechowywane w jednokierunkowym formacie skrótu i nie ma możliwości wyodrębnienia rzeczywistych obrazów. | Wywołaj [interfejs API usuwania wszystkich obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Lub Usuń zasób Content Moderator przy użyciu Azure Portal. |
| Warunki dopasowania niestandardowego | Licencja CAL na [interfejs API pobierania wszystkich warunków](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Wywołaj [interfejs API usuwania wszystkich warunków](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Lub Usuń zasób Content Moderator przy użyciu Azure Portal. |
| `Tags` | ND | Użyj ikony **usuwania** dostępnej dla każdego tagu na stronie ustawień tagów interfejsu użytkownika przeglądu. Lub użyj przycisku **Usuń zespół** na stronie ustawień zespołu [przeglądu interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) . |
| Recenzje | Wywoływanie [interfejsu API uzyskiwania przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Użyj przycisku **Usuń zespół** na stronie ustawień zespołu [przeglądu interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) .
| Użytkownicy | ND | Użyj ikony **usuwania** dostępnej dla każdego użytkownika na stronie ustawień zespołu [przeglądu interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) . Lub użyj przycisku **Usuń zespół** na stronie ustawień zespołu [przeglądu interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) . |

