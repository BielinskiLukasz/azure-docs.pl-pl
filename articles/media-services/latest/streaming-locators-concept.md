---
title: Przesyłanie strumieniowe lokalizatorów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis Lokalizatory przesyłania strumieniowego są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317480"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby wprowadzić filmów wideo w danych wyjściowych dostępne dla klientów do odtwarzania elementu zawartości, należy utworzyć [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i późniejszego kompilowania adresów URL przesyłania strumieniowego. Aby uzyskać przykład .NET, zobacz [uzyskać Lokalizator przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**, należy określić [zasobów](https://docs.microsoft.com/rest/api/media/assets) nazwy i [przesyłania strumieniowego zasad](https://docs.microsoft.com/rest/api/media/streamingpolicies) nazwy. Można użyć jednej z wstępnie zdefiniowane zasady przesyłania strumieniowego lub utworzenia niestandardowych zasad. Wstępnie zdefiniowane zasady obecnie dostępne są: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" i "Predefined_MultiDrmStreaming". Korzystając z niestandardowego przesyłania strumieniowego zasady, należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi Media i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje i protokołów. 

Jeśli chcesz określić opcje szyfrowania na strumień, należy utworzyć [zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) , konfiguruje sposób dostarczania klucza zawartości dla klientów za pośrednictwem składnika dostarczania klucza usługi Media Services końcowych. Skojarzenia usługi lokalizatora przesyłania strumieniowego za pomocą **zasad klucza zawartości** i klucza zawartości. Usługi Media Services można pozostawić opcję automatycznego generowania klucza. W poniższym przykładzie .NET zawiera instrukcje dotyczące konfigurowania szyfrowania AES z tokenu ograniczeń w wersji 3 usługa Media Services: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Zasady kluczy zawartości** są aktualizowalne, możesz chcieć zaktualizować zasady, jeśli musisz przeprowadzić rotację kluczy. Może upłynąć do 15 minut w przypadku pamięci podręcznych dostarczania klucza do aktualizacji i wybierze zaktualizowane zasady. Zalecane jest, aby nie tworzyć nowe zasady klucz zawartości dla każdego lokalizatora przesyłania strumieniowego. Należy spróbować ponownie użyć istniejących zasad, zawsze wtedy, gdy potrzebne są te same opcje.

> [!IMPORTANT]
> * Właściwości **Lokalizatory przesyłania strumieniowego** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. 

## <a name="associate-filters-with-streaming-locators"></a>Kojarzenie filtrów z Lokalizatory przesyłania strumieniowego

Można określić listę [aktywów lub filtry](filters-concept.md), która będzie stosowana dla Twojego [lokalizatora przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). [Funkcji dynamicznego pakowania](dynamic-packaging-overview.md) ma zastosowanie ta lista filtrów wraz z tymi klienta określa się w adresie URL. Ta kombinacja generuje [dyanamic manifest](filters-dynamic-manifest-overview.md), która jest oparta na filtry w adresie URL i filtry, możesz określić na lokalizatora przesyłania strumieniowego. Zaleca się korzystania z tej funkcji, jeśli chcesz zastosować filtry, ale nie należy udostępniać nazwy filtru w adresie URL.

## <a name="filter-order-page-streaming-locator-entities"></a>Filtr, zamówienia, strona lokalizatora przesyłania strumieniowego jednostek

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo przy użyciu platformy .NET](stream-files-tutorial-with-api.md)
* [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
