---
title: Informacje o danych wejściowych dla Azure Stream Analytics
description: W tym artykule opisano koncepcję danych wejściowych w zadaniu Azure Stream Analytics, porównując dane wejściowe przesyłania strumieniowego z danymi wejściowymi.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426429"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Informacje o danych wejściowych dla Azure Stream Analytics

Azure Stream Analytics zadania łączą się z jednym lub wieloma danymi wejściowymi. Każde wejście definiuje połączenie z istniejącym źródłem danych. Stream Analytics akceptuje dane przychodzące z kilku rodzajów źródeł zdarzeń, w tym Event Hubs, IoT Hub i magazynu obiektów BLOB. Dane wejściowe są przywoływane przez nazwę w kwerendzie SQL przesyłania strumieniowego, które należy napisać dla każdego zadania. W zapytaniu można sprzęgać wiele danych wejściowych z danymi mieszanymi lub porównywać dane przesyłane strumieniowo z wyszukiwaniem w dane referencyjne i przekazywać wyniki do danych wyjściowych. 

Stream Analytics ma integrację pierwszej klasy z trzema rodzajami zasobów jako danymi wejściowymi:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Te zasoby wejściowe mogą być aktywne w ramach tej samej subskrypcji platformy Azure co zadanie Stream Analytics lub z innej subskrypcji.

Za pomocą [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [interfejsu API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)i [programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) można tworzyć, edytować i testować dane wejściowe zadań Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Dane wejściowe strumienia i odwołania
Gdy dane są wypychane do źródła danych, są używane przez zadanie Stream Analytics i przetwarzane w czasie rzeczywistym. Istnieją dwa typy danych wejściowych: dane strumienia danych i dane referencyjne.

### <a name="data-stream-input"></a>Dane wejściowe strumienia danych
Strumień danych to nieograniczona sekwencja zdarzeń w czasie. Zadania usługi Stream Analytics musza zawierać co najmniej jedne dane wejściowe strumienia danych. Jako źródła danych wejściowych strumienia danych są obsługiwane usługi Event Hubs, IoT Hub i Blob Storage. Event Hubs są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Te strumienie mogą obejmować źródła działań mediów społecznościowych, informacje o handlu giełdowe lub dane z czujników. Centra IoT są zoptymalizowane pod kątem zbierania danych z połączonych urządzeń w scenariuszach Internet rzeczy (IoT).  Magazyn obiektów BLOB może być używany jako źródło danych wejściowych do pozyskiwania zbiorczych plików jako strumień, takich jak pliki dziennika.  

Aby uzyskać więcej informacji na temat danych wejściowych przesyłania strumieniowego, zobacz [przesyłanie strumieniowe danych jako dane wejściowe do Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Dane wejściowe odwołania
Stream Analytics obsługuje również dane wejściowe znane jako *informacje referencyjne*. Dane referencyjne są całkowicie statyczne lub zmieniają się powoli. Jest zazwyczaj używany do przeprowadzenia korelacji i wyszukiwania. Na przykład można przyłączyć dane danych wejściowych strumienia danych do danych w danych referencyjnych, podobnie jak w przypadku wykonywania sprzężenia SQL w celu wyszukania wartości statycznych. Magazyn obiektów blob platformy Azure i Azure SQL Database są obecnie obsługiwane jako źródła danych wejściowych. Źródłowe źródła danych referencyjne mają limit równy 300 MB, w zależności od złożoności zapytania i przydzieloną liczbę jednostek przesyłania strumieniowego (zobacz sekcję [ograniczenie rozmiaru](stream-analytics-use-reference-data.md#size-limitation) w dokumentacji danych referencyjnych, aby uzyskać więcej informacji).

Aby uzyskać więcej informacji na temat danych wejściowych referencyjnych, zobacz [Korzystanie z danych referencyjnych dla odnośników w Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure portal](stream-analytics-quick-create-portal.md)
