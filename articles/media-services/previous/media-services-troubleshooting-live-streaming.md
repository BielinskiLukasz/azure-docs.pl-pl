---
title: Przewodnik rozwiązywania problemów dotyczących przesyłania strumieniowego na żywo | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów związanych z Azure Media Services przesyłania strumieniowego na żywo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74885606"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo  

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z przesyłaniem strumieniowym na żywo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemy związane z koderami lokalnymi
Ta sekcja zawiera sugestie dotyczące rozwiązywania problemów związanych z koderami lokalnymi skonfigurowanymi do wysyłania strumienia o pojedynczej szybkości transmisji bitów do kanałów usługi AMS, dla których włączono obsługę kodowania na żywo.

### <a name="problem-would-like-to-see-logs"></a>Problem: Czy chcesz wyświetlić dzienniki
* **Potencjalny problem**: nie można znaleźć dzienników koderów, które mogą pomóc w debugowaniu problemów.
  
  * **Wirecast Telestream**: zwykle można znaleźć dzienniki w ścieżce C:\Users \{ username} \AppData\Roaming\Wirecast\ 
  * **Element aktywny**: można znaleźć linki do dzienników w portalu zarządzania. Kliknij przycisk **statystyki**, a następnie pozycję **dzienniki**. Na stronie **pliki dziennika** zostanie wyświetlona lista dzienników dla wszystkich elementów LiveEvent; Wybierz ten, który pasuje do bieżącej sesji. 
  * **Flash Media Live Encoder**: można znaleźć **katalog dziennika...** , przechodząc do karty **Dziennik kodowania** .

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: nie ma opcji wyprowadzania strumienia progresywnego
* **Potencjalny problem**: używany koder nie powoduje automatycznego odplatania. 
  
    **Kroki rozwiązywania problemów**: wyszukanie opcji deprzeploting w interfejsie kodera. Po włączeniu usuwania przeplotu ponownie sprawdź ustawienia danych wyjściowych progresywnych. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: podjęto próbę wykonania kilku ustawień wyjściowych kodera i nadal nie można nawiązać połączenia.
* **Potencjalny problem**: kanał kodowania platformy Azure nie został poprawnie zresetowany. 
  
    **Kroki rozwiązywania problemów**: Upewnij się, że koder nie jest już wypychany do AMS, Zatrzymaj i zresetuj kanał. Po ponownym uruchomieniu spróbuj połączyć koder z nowymi ustawieniami. Jeśli to nadal nie rozrozwiązuje problemu, spróbuj utworzyć nowy kanał całkowicie. Czasami kanały mogą stać się uszkodzone po kilku nieudanych próbach.  
* **Potencjalny problem**: ustawienia rozmiaru grupę GOP lub ramki klucza nie są optymalne. 
  
    **Kroki rozwiązywania problemów**: zalecany rozmiar grupę GOP lub interwał klatek kluczowych to 2 sekundy. Niektóre kodery obliczają to ustawienie w liczbie ramek, podczas gdy inne korzystają z sekund. Na przykład: podczas wyprowadzania 30 fps rozmiar grupę GOP będzie równy 60 klatek, co jest równoważne 2 sekund.  
* **Potencjalny problem**: zamknięte porty blokują strumień. 
  
    **Kroki rozwiązywania problemów**: podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP Sprawdź ustawienia zapory i/lub serwera proxy, aby potwierdzić, że porty wychodzące 1935 i 1936 są otwarte. 

> [!NOTE]
> Jeśli po wykonaniu kroków rozwiązywania problemów nadal nie można pomyślnie wykonać przesyłania strumieniowego, Prześlij bilet pomocy technicznej przy użyciu Azure Portal.
> 
> 

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

