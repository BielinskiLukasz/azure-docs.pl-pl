---
title: Wymagania dotyczące usługi Azure Import/Export | Microsoft Docs
description: Zapoznaj się z wymaganiami dotyczącymi oprogramowania i sprzętu usługi Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 6da9490d5d9a876922fb9c2ba5d908db411adb1a
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512420"
---
# <a name="azure-importexport-system-requirements"></a>Wymagania systemowe usługi Azure Import/Export

W tym artykule opisano ważne wymagania dotyczące usługi Azure Import/Export. Zalecamy dokładne zapoznanie się z informacjami przed użyciem usługi Import/Export, a następnie odwołują się do niej w miarę potrzeb podczas operacji.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Aby przygotować dyski twarde przy użyciu narzędzia WAImportExport, obsługiwane są następujące **64-bitowe systemy operacyjne, które obsługują szyfrowanie dysków funkcją BitLocker** .


|Platforma |Wersja |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 z dodatkiem R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Inne wymagane oprogramowanie dla klienta systemu Windows

|Platforma |Wersja |
|---------|---------|
|.NET Framework    | 4.5.1       |
| Funkcja BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

Usługa Azure Import/Export obsługuje następujące typy kont magazynu:

- Standardowe konta magazynu Ogólnego przeznaczenia v2 (zalecane w przypadku większości scenariuszy)
- Konta usługi Blob Storage
- Konta magazynu Ogólnego przeznaczenia V1 (zarówno w przypadku wdrożeń klasycznych, jak i Azure Resource Manager),

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie kont usługi Azure Storage](storage-account-overview.md).

Każde zadanie może służyć do transferowania danych do lub z tylko jednego konta magazynu. Innymi słowy, pojedyncze zadanie importu/eksportu nie może obejmować wielu kont magazynu. Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-account-create.md).

> [!IMPORTANT]
> Usługa eksport platformy Azure nie obsługuje kont magazynu, w których włączono funkcję [punktów końcowych usługi Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)   . 

## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

Poniższa lista typów magazynu jest obsługiwana przez usługę Azure Import/Export.


|Zadanie  |Usługa magazynu |Obsługiwane  |Nieobsługiwane  |
|---------|---------|---------|---------|
|Importuj     |  Azure Blob Storage <br><br> Azure File Storage       | Obsługiwane obiekty blob i stronicowe obiekty blob <br><br> Obsługiwane pliki          |
|Eksportowanie     |   Azure Blob Storage       | Obsługiwane są blokowe obiekty blob, stronicowe obiekty blob i dołączane obiekty blob         | Nieobsługiwane Azure Files


## <a name="supported-hardware"></a>Obsługiwany sprzęt

W przypadku usługi Azure Import/Export potrzebujesz obsługiwanych dysków do kopiowania danych.

### <a name="supported-disks"></a>Obsługiwane dyski

Następująca lista dysków umożliwia korzystanie z usługi Import/Export.


|Typ dysku  |Rozmiar  |Obsługiwane |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          |
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

Następujące typy dysków nie są obsługiwane:

- USBs.
- Zewnętrzny dysk twardy z wbudowaną adapterem USB.
- Dyski, które znajdują się w obudowie zewnętrznego dysku twardego.

Pojedyncze zadanie importu/eksportu może mieć następujące wartości:

- Maksymalnie 10 dysków twardych/dysków SSD.
- Mieszany dysk twardy/SSD dowolnego rozmiaru.

Duża liczba dysków można rozłożyć na wiele zadań, a liczba zadań, które można utworzyć, nie jest ograniczona. W przypadku zadań importu tylko pierwszy wolumin danych na dysku jest przetwarzany. Wolumin danych musi być sformatowany w systemie plików NTFS.

Podczas przygotowywania dysków twardych i kopiowania danych przy użyciu narzędzia WAImportExport można użyć zewnętrznych adapterów USB. Większość z przedziałów USB 3,0 lub nowszych powinna być w pełni zastosowana.

## <a name="next-steps"></a>Następne kroki

* [Transferowanie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
