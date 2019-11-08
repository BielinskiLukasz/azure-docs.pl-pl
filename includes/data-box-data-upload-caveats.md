---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800011"
---
- Nie należy kopiować plików bezpośrednio do żadnego ze współtworzonych udziałów. Należy utworzyć folder w udziale, a następnie skopiować pliki do tego folderu.
- Folder w *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest kontenerem. Na przykład kontenery są tworzone jako *StorageAccount_BlockBlob/Container* i *StorageAccount_PageBlob/Container*.
- Każdy folder utworzony bezpośrednio w obszarze *StorageAccount_AzureFiles* jest tłumaczony na udział plików platformy Azure.
- Jeśli masz istniejący obiekt platformy Azure (np. obiekt BLOB lub plik) w chmurze o takiej samej nazwie jak kopiowany obiekt, urządzenie Data Box zastąpi plik w chmurze.
- Każdy plik zapisany w udziałach *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* jest przekazywany jako blokowy obiekt BLOB i stronicowy obiekt BLOB.
- Usługa Azure Blob Storage nie obsługuje katalogów. Jeśli utworzysz folder w folderze *StorageAccount_BlockBlob* , w nazwie obiektu BLOB zostaną utworzone foldery wirtualne. W przypadku Azure Files zachowywana jest rzeczywista struktura katalogów.
- Nie przekazano żadnych pustych hierarchii katalogów (bez żadnych plików) utworzonych w folderach *StorageAccount_BlockBlob* i *StorageAccount_PageBlob* .
- Jeśli wystąpią błędy podczas przekazywania danych do platformy Azure, na docelowym koncie magazynu zostanie utworzony dziennik błędów. Ścieżka do tego dziennika błędów jest dostępna po zakończeniu przekazywania i można przejrzeć dziennik, aby podjąć działania naprawcze. Nie usuwaj danych ze źródła bez weryfikowania przekazanych danych.
- Metadane plików i uprawnienia NTFS nie są zachowywane, gdy dane są przekazywane do Azure Files. Na przykład *ostatni zmodyfikowany atrybut* plików nie będzie przechowywany podczas kopiowania danych.
