---
title: Wyświetlanie stanu zadania usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić stan zadania importu/eksportu i dysków używanych.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: 49a2c03664ba39a624871b24c0b86a968a67eddb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521527"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Wyświetlanie stanu zadania usługi Azure Import/Export

Ten artykuł zawiera informacje na temat wyświetlić stan dysku i zadania dla zadań Azure Import/Export. Usługa Azure Import/Export umożliwia bezpieczne przesyłanie dużych ilości danych do usługi Azure Files i obiektów blob Azure. Usługa umożliwia również eksportować dane z usługi Azure Blob storage.  

## <a name="view-job-and-drive-status"></a>Widok zadania i stan dysku
Można śledzić stan importu lub eksportu zadania w witrynie Azure portal. Kliknij przycisk **Import/Export** kartę. Na stronie zostanie wyświetlona lista zadań.

![Widok stanu zadania](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Zostanie wyświetlony jeden z następujących stanów zadań w zależności od tego, gdzie Twój dysk jest w trakcie procesu.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania, jego stan jest ustawiony na **tworzenie**. Podczas wykonywania zadania w **tworzenie** stanu usługi Import/Export przyjęto założenie, dyski nie zostały wysłane do centrum danych. Zadanie może pozostać w tym stanie przez maksymalnie dwa tygodnie, po których jest on automatycznie usunięty przez usługę. |
| Wysyłka | Po wysyłasz pakietu, należy zaktualizować informacje o śledzeniu w witrynie Azure portal.  Spowoduje to włączenie zadania do **wysyłania** stanu. Zadanie pozostanie w **wysyłania** stanie do dwóch tygodni. 
| Odebrano | Po wszystkie dyski są odbierane w centrum danych, stan zadania jest ustawiana na **odebrane**. |
| Transferowanie | Po co najmniej jeden dysk rozpoczął przetwarzanie, stan zadania jest ustawiony na **transferowanie**. Aby uzyskać więcej informacji, przejdź do [stany stacji](#view-drive-status). |
| Pakowanie | Po wszystkich dysków przetwarzania, zadanie jest umieszczona w **pakowania** stan do momentu dyski są wysyłane do Ciebie. |
| Zakończone | Po wszystkie dyski są dostarczane do Ciebie, jeśli zadanie zostało ukończone bez błędów, następnie zadanie jest ustawiana na **Ukończono**. Zadania są automatycznie usuwane po 90 dniach w **Ukończono** stanu. |
| Zamknięte | Po wszystkie dyski są dostarczane do Ciebie, jeśli wystąpiły błędy podczas przetwarzania zadania, zadanie jest ustawiana na **zamknięte**. Zadania są automatycznie usuwane po 90 dniach w **zamknięte** stanu. |

## <a name="view-drive-status"></a>Wyświetlanie stanu dysku

W poniższej tabeli opisano cyklu życia poszczególnych dyskach przechodzi przez zadania importu lub eksportu. Bieżący stan każdego dysku, w ramach zadania jest widoczny w witrynie Azure portal.

W poniższej tabeli opisano każdy stan, który każdego dysku, w ramach zadania mogą przechodzić przez.

| Stan stacji | Opis |
|:--- |:--- |
| Określona | Do zadania importu, gdy zadanie jest tworzone w witrynie Azure portal, stan początkowy dla dysku jest **określone**. Przez zadanie eksportu, ponieważ dysk nie zostanie określony po utworzeniu zadania dysk początkowy stan jest **odebrane**. |
| Odebrano | Dysk przechodzi do **odebrane** stanu, gdy usługa Import/Export został przetworzony dysków, które zostały odebrane z firmą transportową do zadania importu. Przez zadanie eksportu jest stan stacji początkowej **odebrane** stanu. |
| NeverReceived | Dysk przechodzi do **NeverReceived** stanu po odebraniu pakietu do danego zadania, ale pakiet nie zawiera dysku. Dysk zostanie przesunięta w ten stan została już dwa tygodnie, ponieważ usługa otrzymała informacje o wysyłce, ale pakiet nie dotarła jeszcze w centrum danych. |
| Transferowanie | Dysk przechodzi do **transferowanie** stan, kiedy usługa zaczyna się na przesyłanie danych z dysku do usługi Azure Storage. |
| Zakończone | Dysk przechodzi do **Ukończono** stanu, gdy usługa została pomyślnie przeniesiona wszystkie dane bez błędów.
| CompletedMoreInfo | Dysk przechodzi do **CompletedMoreInfo** stanu, gdy Usługa napotkała problemy podczas kopiowania danych z lub na dysku. Informacje mogą zawierać błędy, ostrzeżenia lub komunikaty informacyjne o zastępowaniu obiektów blob.
| ShippedBack | Dysk przechodzi do **ShippedBack** stan, gdy zostały wysłane z centrum danych na powrót do adres zwrotny. |

Ten obraz z witryny Azure portal Wyświetla stan dysku zadania przykład:

![Wyświetl stan dysku](./media/storage-import-export-service/drivestate.png)

W poniższej tabeli opisano stanów awarii dysku i akcje wykonywane dla każdego stanu.

| Stan stacji | Wydarzenie | Rozpoznawanie / następny krok |
|:--- |:--- |:--- |
| NeverReceived | Dysk, który jest oznaczony jako **NeverReceived** (ponieważ nie została odebrana jako część zadania wydania) dociera do innego wydania. | Zespół operacyjny przenosi dysku **odebrane**. |
| ND | Dysk, który nie jest częścią dowolnego zadania dociera do centrum danych jako część innego zadania. | Dysk jest oznaczony jako dodatkowy dysk i jest zwracane po zakończeniu zadania skojarzone z oryginalnym pakietem. |

## <a name="time-to-process-job"></a>Czas procesu zadania
Ilość czasu potrzebnego do przetworzenia zadania importu/eksportu zależy od wielu czynników takich jak:

-  Czas dostawy
-  Ładowanie w centrum danych
-  Typ zadania i rozmiaru danych, w której są kopiowane
-  Liczba dysków w ramach zadania. 

Usługa Import/Export nie ma umowy SLA, ale usługa dokłada starań, aby wykonać kopię w 7 – 10 dni, po otrzymaniu dyski. Oprócz stanu opublikowane w witrynie Azure Portal interfejsów API REST może służyć do śledzenia postępu zadań. Wykonano parametru w [listy zadań]() wywołania operacji interfejsu API zapewnia postępy wartości procentowej.


## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transferowanie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST wyeksportować importu Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

