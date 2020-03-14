---
title: Content Moderator szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Content Moderator szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372162"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator szyfrowanie danych magazynowanych

Content Moderator automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko w warstwie cenowej E0. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta Content moderator](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą Content Moderator należy utworzyć nowy zasób Content Moderator i wybrać pozycję E0 jako warstwę cenową. Po utworzeniu zasobu Content Moderator z użyciem warstwy cenowej E0 można użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Włącz szyfrowanie danych dla zespołu Content Moderatorowego

Aby włączyć szyfrowanie danych dla zespołu przeglądów Content Moderator, zobacz [Przewodnik Szybki Start: Try Content moderator w sieci Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Musisz podać _Identyfikator zasobu_ za pomocą warstwy cenowej Content moderator E0.


## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza zarządzanego przez klienta Content Moderator](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
