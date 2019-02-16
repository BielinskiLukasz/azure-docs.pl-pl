---
title: Eksportowanie lub usunąć swoje dane — Content Moderator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak eksportować i usuwać dane w pakiecie Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 15a59bbdc4c93202f8906689100c24ba713ee487
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311260"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Eksportowanie lub usuwanie danych użytkownika w pakiecie Content Moderator

Content Moderator zbiera dane użytkownika w celu oferowania usługi, ale klienci mają pełną kontrolę nad wyświetlić, eksportować i usuwać ich dane przy użyciu [narzędzie do przeglądu](https://contentmoderator.cognitive.microsoft.com/) i [Moderowanie i interfejsy API przeglądu](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby uzyskać więcej informacji na temat sposobu eksportowania i usuwania danych użytkownika w pakiecie Content Moderator Zobacz poniższej tabeli.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | ND | Usuwanie w witrynie Azure portal (subskrypcje platformy Azure). Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Obrazy niestandardowe dopasowanie | Wywołaj [Get-image identyfikatory interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Obrazy są przechowywane w formacie jednokierunkowego skrótu własności, a nie istnieje sposób wyodrębnić rzeczywistych obrazów. | Wywołaj [usunąć wszystkie obrazy interfejs API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Lub usuwanie zasobu usługi Content Moderator, przy użyciu witryny Azure portal. |
| Warunki niestandardowe dopasowanie | Licencja CAL [Pobierz wszystkie warunki interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Wywołaj [usunąć wszystkie terminy interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Lub usuwanie zasobu usługi Content Moderator, przy użyciu witryny Azure portal. |
| Tagi | ND | Użyj **Usuń** ikonę dostępne dla każdego tagu na stronie ustawień Przegląd interfejsu użytkownika tagu. Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |
| Przeglądy | Wywołaj [API przeglądu Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu.
| Użytkownicy | ND | Użyj **Usuń** ikonę dostępne dla każdego użytkownika w [Przegląd interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. Lub użyj **usuwanie zespołu** znajdujący się w [Przegląd interfejsu użytkownika](https://contentmoderator.cognitive.microsoft.com/) strony ustawień zespołu. |

