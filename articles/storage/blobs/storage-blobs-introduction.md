---
title: Wprowadzenie do magazynu obiektów BLOB (Object) — Azure Storage
description: Magazyn obiektów Blob Azure przechowuje olbrzymie ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Magazyn obiektów Blob Azure jest wysoce skalowalny i dostępny. Klienci mają dostęp do obiektów danych w magazynie obiektów Blob za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, w sposób programistyczny za pomocą bibliotek klienta magazynu Azure lub przy użyciu interfejsu REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/04/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: aeb6971d37601367e62b8f883ccbc576e9534cdb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356483"
---
# <a name="introduction-to-azure-blob-storage"></a>Wprowadzenie do usługi Azure Blob Storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Zasoby magazynu obiektów blob

Magazyn obiektów blob oferuje trzy typy zasobów:

- **Konto magazynu**
- **Kontener** na koncie magazynu
- **Obiekt blob** w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Relacja między obiektem BLOB konta a zasobem kontenera](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Konta magazynu

Konto magazynu zapewnia unikatową przestrzeń nazw na platformie Azure dla danych użytkownika. Każdy obiekt przechowywany w usłudze Azure Storage ma adres, który zawiera unikatową nazwę konta. Kombinacja nazwy konta i punktu końcowego obiektu blob usługi Azure Storage stanowi adres podstawowy dla obiektów w ramach konta magazynu.

Jeśli na przykład konto magazynu ma nazwę *mystorageaccount*, domyślnym punktem końcowym magazynu obiektów blob jest:

```
http://mystorageaccount.blob.core.windows.net 
```

Aby utworzyć konto magazynu, zobacz temat [Tworzenie konta magazynu](../common/storage-account-create.md). Aby dowiedzieć się więcej o kontach magazynu, zobacz [Omówienie konta usługi Azure Storage](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Kontener porządkuje zestaw obiektów blob, pełniąc funkcję podobną do katalogu w systemie plików. Konto magazynu może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może zawierać nieograniczoną liczbę obiektów blob. 

  > [!NOTE]
  > Wszystkie litery w nazwie kontenera muszą być małymi literami. Aby uzyskać dodatkowe informacje o nazewnictwie kontenerów, zobacz [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych).

### <a name="blobs"></a>Obiekty blob
 
Usługa Azure Storage obsługuje trzy typy obiektów blob:

- **Blokowe obiekty blob** mogą zawierać dane tekstowe i binarne o rozmiarze do około 4,7 TB. Blokowe obiekty blob składają się z bloków danych, którymi można zarządzać indywidualnie.
- **Uzupełnialne obiekty blob** również składają się z bloków, podobnie jak blokowe obiekty blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialne obiekty blob doskonale sprawdzają się w sytuacjach takich jak zapisywanie danych dzienników z maszyn wirtualnych.
- **Stronicowe obiekty blob** umożliwiają przechowywanie plików o dostępie swobodnym o rozmiarze do 8 TB. Stronicowe obiekty blob przechowują pliki wirtualnych dysków twardych (VHD) i stanowią dyski dla maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji na temat stronicowych obiektów blob, zobacz [Overview of Azure page blobs (Omówienie stronicowych obiektów blob platformy Azure)](storage-blob-pageblob-overview.md)

Aby uzyskać więcej informacji na temat różnych typów obiektów blob, zobacz temat [Understanding Block Blobs, Append Blobs, and Page Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Omówienie blokowych, uzupełnialnych i stronicowych obiektów blob).

## <a name="move-data-to-blob-storage"></a>Przenoszenie danych do magazynu obiektów blob

Istnieje wiele rozwiązań do migrowania istniejących danych do magazynu obiektów blob:

- **AzCopy** to łatwe w użyciu narzędzie wiersza polecenia dla systemu Windows i Linux, które służy do kopiowania danych do i z magazynu obiektów blob, między kontenerami oraz między kontami magazynu. Aby uzyskać więcej informacji na temat narzędzia AzCopy, zobacz temat [Transfer data with the AzCopy v10 (Preview)](../common/storage-use-azcopy-v10.md) (Przenoszenie danych przy użyciu narzędzia AzCopy w wersji 10 [wersja zapoznawcza]). 
- **Biblioteka przenoszenia danych usługi Azure Storage** jest biblioteką .NET służącą do przenoszenia danych między usługami Azure Storage. Narzędzie AzCopy zostało utworzone przy użyciu biblioteki przenoszenia danych. Więcej informacji można znaleźć w [dokumentacji referencyjnej](/dotnet/api/microsoft.azure.storage.datamovement) biblioteki przenoszenia danych. 
- **Azure Data Factory** obsługuje kopiowanie danych do i z usługi BLOB Storage przy użyciu klucza konta, sygnatury dostępu współdzielonego, nazwy głównej usługi lub tożsamości zarządzanych dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz temat [Copy data to or from Azure Blob storage by using Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Kopiowanie danych do i z usługi Azure Blob Storage za pomocą usługi Azure Data Factory). 
- **Blobfuse** jest sterownikiem wirtualnego systemu plików usługi Azure Blob Storage. Sterownik blobfuse umożliwia dostęp do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. Aby uzyskać więcej informacji, zobacz temat [How to mount Blob storage as a file system with blobfuse](storage-how-to-mount-container-linux.md) (Jak zainstalować magazyn obiektów blob jako system plików przy użyciu sterownika blobfuse).
- Usługa **Azure Data Box** jest dostępna do transferowania danych lokalnych do magazynu obiektów blob, gdy duże zestawy danych lub ograniczenia sieci umożliwiają przekazywanie danych przez nierealistyczny czas. W zależności od rozmiaru danych można żądać [Azure Data Box Disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md)lub [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) urządzeń od firmy Microsoft. Następnie możesz skopiować dane na te urządzenia i przesłać je z powrotem do firmy Microsoft w celu przekazania ich do magazynu obiektów BLOB.
- **Usługa Azure Import/Export** umożliwia importowanie i eksportowanie dużych ilości danych do i z konta magazynu przy użyciu podanych dysków twardych. Aby uzyskać więcej informacji, zobacz [Use the Microsoft Azure Import/Export service to transfer data to Blob Storage](../common/storage-import-export-service.md) (Przesyłanie danych do usługi Blob Storage za pomocą usługi Microsoft Azure Import/Export).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Cele skalowalności i wydajności dla usługi BLOB Storage](scalability-targets.md)
