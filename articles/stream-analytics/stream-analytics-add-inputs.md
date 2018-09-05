---
title: Zrozumienie danych wejściowych usługi Azure Stream Analytics
description: W tym artykule opisano pojęcia danych wejściowych w zadaniu usługi Azure Stream Analytics, porównywanie przesyłania strumieniowego danych wejściowych wejściowych danych referencyjnych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 4a84e8f7460d3a339be783be6a12353770ced1b8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665827"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Zrozumienie danych wejściowych usługi Azure Stream Analytics

Zadania usługi Azure Stream Analytics nawiązać połączenia danych wejściowych. Każdy dane wejściowe definiuje połączenie się z istniejącym źródłem danych. Stream Analytics akceptuje przychodzące dane z kilku rodzajów źródeł zdarzeń, w tym usługi Event Hubs, IoT Hub i Blob storage. Dane wejściowe odwołuje się nazwa wymienionej w kwerendzie SQL przesyłania strumieniowego, które piszesz dla każdego zadania. W zapytaniu Dołącz do wielu danych wejściowych mieszania danych lub do porównywania danych przesyłanych strumieniowo z wyszukiwania z danymi referencyjnymi i przekazywać wyniki do danych wyjściowych. 

Stream Analytics ma najwyższej jakości Integracja z trzech rodzajów zasobów jako dane wejściowe:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby danych wejściowych może znajdować się w tej samej subskrypcji platformy Azure jako zadania usługi Stream Analytics lub z innej subskrypcji.

Możesz użyć [witryny Azure portal](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), i [programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md)do tworzenia, edytowania i przetestować dane wejściowe zadania usługi Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Stream i odwołanie do danych wejściowych
Ponieważ dane są wypychane do źródła danych, ma zużywane przez zadanie usługi Stream Analytics i przetwarzane w czasie rzeczywistym. Dane wejściowe są podzielone na dwa typy: dane strumienia danych wejściowych i odwoływać się do danych wejściowych.

### <a name="data-stream-input"></a>Dane wejściowe strumienia danych
Strumień danych jest niepowiązanego sekwencję zdarzeń, wraz z upływem czasu. Zadania usługi Stream Analytics mogą zawierać elementu wejściowego strumienia danych z co najmniej jeden. Usługa Event Hubs, IoT Hub i Blob storage są obsługiwane jako źródła wejściowego strumienia danych. Usługa Event Hubs są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Te strumienie mogą obejmować, kanałów informacyjnych w mediach społecznościowych działania, handlu podstawowe informacje lub dane z czujników. Centra IoT Hub są zoptymalizowane do zbierania danych z połączonych urządzeń w scenariuszach Internetu rzeczy (IoT).  Magazyn obiektów blob może służyć jako źródło danych wejściowych dla zbiorczego dane są wprowadzane jako strumień, takich jak pliki dziennika.  

Aby uzyskać więcej informacji na temat wejścia danych przesyłanych strumieniowo, zobacz [Stream dane jako dane wejściowe do usługi Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Wejściowych danych referencyjnych
Stream Analytics obsługuje również znane jako dane wejściowe *dane referencyjne*. Dane referencyjne jest całkowicie statycznych lub zmieniają się powoli. Zazwyczaj służy do wykonywania korelacji i wyszukiwania. Na przykład można sprzęgnąć danych w wejściowego strumienia danych z danymi w danych referencyjnych, na ile wykona sprzężenie SQL, aby wyszukać wartości statyczne. Usługa Azure Blob storage jest obecnie obsługiwane tylko źródło danych wejściowych danych referencyjnych. Obiekty BLOB źródła danych referencyjnych mogą zawierać maksymalnie 100 MB rozmiar.

Aby uzyskać więcej informacji na temat wejścia danych referencyjnych, zobacz [Using danych referencyjnych dla wyszukiwania w usłudze Stream Analytics](stream-analytics-use-reference-data.md)

Ten artykuł stanowi krok w [ścieżka szkoleniowa usługi Stream Analytics](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)