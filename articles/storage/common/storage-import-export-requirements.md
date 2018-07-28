---
title: Wymagania dotyczące usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Zrozumienie wymagań sprzętu i oprogramowania dla usługi Azure Import/Export.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: alkohli
ms.openlocfilehash: a86d5c1513594f5bc0df03b8ca7671a1f9541b4d
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308058"
---
# <a name="azure-importexport-system-requirements"></a>Wymagania dotyczące systemu Azure Import/Export

W tym artykule opisano ważne wymagania dotyczące usługi Azure Import/Export. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed korzystania z usługi Import/Export, a następnie wrócić do niego zgodnie z potrzebami podczas operacji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby przygotować dyski twarde, za pomocą narzędzia WAImportExport, następujące **64-bitowego systemu operacyjnego, który obsługuje szyfrowanie dysków funkcją BitLocker** są obsługiwane.


|Platforma |Wersja |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8.1 Pro, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |


## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Usługa Azure Import/Export obsługuje następujące [konta usługi Azure storage](storage-account-options.md).
- Ogólne v1 konta magazynu przeznaczenia (wdrożenia zarówno klasyczny, jak i usługi Azure Resource Manager)
- Konta usługi Blob Storage
- Kont magazynu ogólnego przeznaczenia v2

Każde zadanie może służyć do przesyłania danych do lub z tylko jednego konta magazynu. Innymi słowy zadanie importu/eksportu pojedynczej nie mogą rozciągać się na wielu kontach magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [sposób tworzenia konta magazynu](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Usługa Azure Import/Eksport obsługują kont magazynu, gdzie [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) funkcja została włączona. 

## <a name="supported-storage-types"></a>Obsługiwane typy

Poniższa lista typów magazynu jest obsługiwana przy użyciu usługi Azure Import/Export.


|Zadanie  |Usługa Storage |Obsługiwane  |Nieobsługiwane  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Usługa Azure File storage       | Blokowe obiekty BLOB i stronicowe obiekty BLOB, obsługiwane <br><br> Obsługiwane pliki          |
|Eksportowanie     |   Azure Blob Storage       | Blokowe obiekty BLOB, stronicowe obiekty BLOB i obiekty BLOB dołączania obsługiwane         | Usługa pliki systemu Azure nie jest obsługiwane


## <a name="supported-hardware"></a>Obsługiwane usługi sprzętowego 

Dla usługi Azure Import/Export potrzebne są obsługiwane dyski do kopiowania danych.

### <a name="supported-disks"></a>Obsługiwane dyski

Poniższa lista dysków jest obsługiwane do użytku z usługi Import/Export.


|Typ dysku  |Rozmiar  |Obsługiwane |Nieobsługiwane  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |         |         |
|HDD     |  2,5"<br>3,5"       |SATA II SATA III         |Zewnętrzny dysk twardy za pomocą wbudowanych adaptera USB <br> Dyskiem wewnątrz wielkość liter w wyrazie zewnętrzny dysk twardy         |


Zadania importu/eksportu pojedynczego może mieć:
- Maksymalnie 10 HDD/SSD.
- Kombinacja HDD/SSD o dowolnym rozmiarze.

Duża liczba dysków mogły być rozkładane na wiele zadań i nie ma nieograniczoną liczbę zadań, które mogą być tworzone. Zadań importu jest przetwarzany tylko pierwszy wolumin danych na dysku. Ilość danych muszą być sformatowane jako NTFS.

Podczas przygotowywania dysków twardych i kopiowanie danych przy użyciu narzędzia WAImportExport, można użyć zewnętrznej karty USB. Większość standardowych USB 3.0 lub nowszej adapterów powinny działać. 


## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST wyeksportować importu Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

