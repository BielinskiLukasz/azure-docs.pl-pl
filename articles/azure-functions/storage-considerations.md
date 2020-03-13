---
title: Zagadnienia dotyczące magazynu Azure Functions
description: Dowiedz się więcej o wymaganiach dotyczących magazynu Azure Functions i o szyfrowaniu przechowywanych danych.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276584"
---
# <a name="storage-considerations-for-azure-functions"></a>Zagadnienia dotyczące magazynu Azure Functions

Azure Functions wymaga konta usługi Azure Storage podczas tworzenia wystąpienia aplikacji funkcji. Aplikacja funkcji może używać następujących usług magazynu:


|Usługa magazynu  | Użycie funkcji  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Zachowaj stan powiązań i klucze funkcji.  <br/>Używane także przez [centra zadań w Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Udział plików służący do przechowywania i uruchamiania kodu aplikacji funkcji w [planie zużycia](functions-scale.md#consumption-plan). |
| [Usługa Azure queue storage](../storage/queues/storage-queues-introduction.md)     | Używane przez [centra zadań w Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Używane przez [centra zadań w Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Podczas korzystania z planu hostingu Zużycie kod funkcji i pliki konfiguracji powiązania są przechowywane w usłudze Azure File Storage na głównym koncie magazynu. Po usunięciu głównego konta magazynu ta zawartość zostanie usunięta i nie będzie można jej odzyskać.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Wynika to z faktu, że funkcje programu opierają się na usłudze Azure Storage w przypadku operacji takich jak zarządzanie wyzwalaczami i rejestrowanie wykonań funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel. Te konta obejmują konta magazynu tylko dla obiektów blob, Premium Storage platformy Azure i konta magazynu ogólnego przeznaczenia z replikacją ZRS. Te nieobsługiwane konta są filtrowane z bloku konto magazynu podczas tworzenia aplikacji funkcji.

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [Wprowadzenie do usług Azure Storage](../storage/common/storage-introduction.md#azure-storage-services). 

Chociaż możesz użyć istniejącego konta magazynu z aplikacją funkcji, musisz upewnić się, że spełnia ono wymagania. Konta magazynu utworzone w ramach przepływu tworzenia aplikacji funkcji mają gwarancję spełnienia wymagań dotyczących konta magazynu.  

## <a name="storage-account-guidance"></a>Wskazówki dotyczące konta magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, aplikacja funkcji nie zostanie uruchomiona. Rozwiązywanie problemów związanych z magazynem można znaleźć w temacie [How to rozwiązywanie problemów związanych z magazynem](functions-recover-storage-account.md). Poniższe zagadnienia dodatkowe dotyczą konta magazynu używanego przez aplikacje funkcji.

### <a name="storage-account-connection-setting"></a>Ustawienie połączenia z kontem magazynu

Połączenie konta magazynu jest obsługiwane w [ustawieniu aplikacji AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Parametry połączenia konta magazynu należy zaktualizować, gdy zostaną ponownie wygenerowane klucze magazynu. [Przeczytaj więcej na temat zarządzania kluczami magazynu tutaj](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Konta magazynu udostępnionego

Istnieje możliwość udostępnienia tego samego konta magazynu dla wielu aplikacji funkcji bez żadnych problemów. Na przykład w programie Visual Studio można opracowywać wiele aplikacji przy użyciu emulatora usługi Azure Storage. W takim przypadku emulator działa jak pojedyncze konto magazynu. To samo konto magazynu używane przez aplikację funkcji może być również używane do przechowywania danych aplikacji. Jednak takie podejście nie zawsze jest dobrym pomysłem w środowisku produkcyjnym.

### <a name="optimize-storage-performance"></a>Optymalizowanie wydajności magazynu

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Szyfrowanie danych magazynu

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).

Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania obiektów blob i danych plików. Te klucze muszą być obecne w Azure Key Vault, aby funkcje mogły uzyskiwać dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu Azure Portal](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Zainstaluj udziały plików (Linux)

Istniejące udziały Azure Files można zainstalować w aplikacjach funkcji systemu Linux. Instalując udział w aplikacji funkcji systemu Linux, możesz korzystać z istniejących modeli uczenia maszynowego lub innych danych w swoich funkcjach. Możesz użyć [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) polecenie, aby zainstalować istniejący udział w aplikacji funkcji systemu Linux. 

W tym poleceniu `share-name` jest nazwą istniejącego udziału Azure Files, a `custom-id` może być dowolnym ciągiem, który jednoznacznie definiuje udział w przypadku zamontowania w aplikacji funkcji. `mount-path` jest również ścieżką, z której uzyskuje się dostęp do udziału w aplikacji funkcji. `mount-path` musi mieć format `/dir-name`i nie może rozpoczynać się od `/home`.

Pełny przykład można znaleźć w artykule skrypty w temacie [Tworzenie aplikacji funkcji języka Python i Instalowanie udziału Azure Files](scripts/functions-cli-mount-files-storage-linux.md). 

Obecnie obsługiwane są tylko `storage-type` `AzureFiles`. Można zainstalować tylko pięć udziałów w danej aplikacji funkcji. Zainstalowanie udziału plików może wydłużyć czas zimnego uruchomienia o co najmniej 200-300ms lub jeszcze więcej, gdy konto magazynu znajduje się w innym regionie.

Zainstalowany udział jest dostępny dla kodu funkcji w określonym `mount-path`. Na przykład gdy `mount-path` jest `/path/to/mount`, można uzyskać dostęp do katalogu docelowego za pomocą interfejsów API systemu plików, jak w poniższym przykładzie w języku Python:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o opcjach hostingu Azure Functions.

> [!div class="nextstepaction"]
> [Skalowanie i hosting usługi Azure Functions](functions-scale.md)


