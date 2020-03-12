---
title: Migrowanie Azure Data Lake Storage z Gen1 do Gen2
description: Przeprowadź migrację Azure Data Lake Storage z Gen1 do Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e8266e5750a14542e7f115e021daa40b2b0bf8f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130100"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrowanie Azure Data Lake Storage z Gen1 do Gen2

Dane, obciążenia i aplikacje można migrować z Data Lake Storage Gen1 do Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 jest oparta na [usłudze Azure Blob Storage](storage-blobs-introduction.md) i oferuje zestaw funkcji przeznaczonych do analizy danych Big Data. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) łączy funkcje [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takie jak semantyka systemu plików, katalog i zabezpieczenia na poziomie pliku oraz skalowanie przy użyciu niskich kosztów, magazynu warstwowego, wysokiej dostępności/możliwości odzyskiwania po awarii z [usługi Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Aby ułatwić odczytywanie, w tym artykule jest stosowany termin *Gen1* do odwoływania się do Azure Data Lake Storage Gen1 i termin *Gen2* do odwoływania się do Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Zalecane podejście

Aby przeprowadzić migrację do Gen2, zalecamy wykonanie poniższych czynności.

: heavy_check_mark: Krok 1: Ocena gotowości

: heavy_check_mark: Krok 2: przygotowanie do migracji

: heavy_check_mark: Krok 3: Migrowanie obciążeń danych i aplikacji

: heavy_check_mark: Krok 4: uruchomienie produkcyjne z Gen1 do Gen2

> [!NOTE]
> Gen1 i Gen2 są różnymi usługami, ale nie ma możliwości uaktualnienia w miejscu, ale wymaganego wysiłku migracji. 

### <a name="step-1-assess-readiness"></a>Krok 1. Ocena gotowości

1. Dowiedz się więcej o [ofercie Data Lake Storage Gen2ej](https://azure.microsoft.com/services/storage/data-lake-storage/); korzyści, koszty i ogólna architektura. 

2. [PORÓWNAJ możliwości](#gen1-gen2-feature-comparison) Gen1 z tymi z Gen2. 

3. Przejrzyj listę [znanych problemów](data-lake-storage-known-issues.md) , aby ocenić wszelkie luki w działaniu.

4. Gen2 obsługuje funkcje magazynu obiektów blob, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia magazynu obiektów BLOB](storage-lifecycle-management-concepts.md). Jeśli interesuje Cię korzystanie z dowolnej z tych funkcji, przejrzyj [bieżący poziom wsparcia](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-feature-support).

5. Zapoznaj się z bieżącym stanem [obsługi ekosystemu platformy Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-ecosystem-support) , aby upewnić się, że Gen2 obsługuje wszystkie usługi, od których zależą Twoje rozwiązania.

### <a name="step-2-prepare-to-migrate"></a>Krok 2. Przygotowanie do migracji

1. Zidentyfikuj zestawy danych, które zostaną zmigrowane.

   Skorzystaj z tej możliwości, aby wyczyścić zestawy danych, które nie są już używane. Jeśli nie planujesz migrować wszystkich danych jednocześnie, poświęć ten czas, aby zidentyfikować logiczne grupy danych, które można migrować w fazach.
   
2. Określ wpływ migracji na firmę.

   Rozważmy na przykład, czy możesz zapewnić jakikolwiek przestój w czasie migracji. Te zagadnienia mogą pomóc w zidentyfikowaniu odpowiedniego wzorca migracji i wybraniu najbardziej odpowiednich narzędzi.

3. Utwórz plan migracji. 

   Zalecamy stosowanie tych [wzorców migracji](#migration-patterns). Można wybrać jeden z tych wzorców, połączyć je ze sobą lub zaprojektować niestandardowy wzorzec własny.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Krok 3. Migrowanie danych, obciążeń i aplikacji

Migrowanie danych, obciążeń i aplikacji przy użyciu preferowanego wzorca. Zalecamy Weryfikowanie scenariuszy przyrostowo.

1. [Utwórz konto magazynu](data-lake-storage-quickstart-create-account.md) i Włącz funkcję hierarchicznej przestrzeni nazw. 

2. Migruj dane. 

3. Skonfiguruj [usługi w swoich obciążeniach](data-lake-storage-integrate-with-azure-services.md) , aby wskazywały punkt końcowy usługi Gen2. 
   
4. Aktualizowanie aplikacji do używania interfejsów API Gen2. Zobacz przewodniki dla [platform .NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) i [rest](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aktualizuj skrypty, aby używać poleceń [cmdlet programu PowerShell](data-lake-storage-directory-file-acl-powershell.md)i [interfejsu wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)Data Lake Storage Gen2.
   
6. Wyszukaj odwołania do identyfikatorów URI, które zawierają ciąg `adl://` w plikach kodu lub w notesach datacegły, Apache Hive pliki HQL lub inny plik używany jako część obciążeń. Zamień te odwołania na [Gen2 sformatowany identyfikator URI](data-lake-storage-introduction-abfs-uri.md) nowego konta magazynu. Na przykład: identyfikator URI Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` może stać się `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Skonfiguruj zabezpieczenia na koncie, aby obejmowały [role kontroli dostępu opartej na rolach (RBAC)](../common/storage-auth-aad-rbac-portal.md), [zabezpieczenia na poziomie plików i folderów](data-lake-storage-access-control.md)oraz [zapory i sieci wirtualne usługi Azure Storage](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Krok 4: uruchomienie produkcyjne z Gen1 do Gen2

Po upewnieniu się, że Twoje aplikacje i obciążenia są stabilne w Gen2, możesz zacząć korzystać z Gen2, aby zaspokoić scenariusze biznesowe. Wyłącz wszystkie pozostałe potoki, które działają w Gen1 i likwidowanie konta Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Możliwości Gen1 vs Gen2

W tej tabeli porównano możliwości Gen1 z Gen2.

|Obszar |Gen1   |Gen2 |
|---|---|---|
|Dane organizacji|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa plików i folderów|[Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)<br>Obsługa kontenera, plików i folderów |
|Nadmiarowość geograficzna| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Uwierzytelnianie|[Tożsamość zarządzana usługi AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Nazwy główne usług](../../active-directory/develop/app-objects-and-service-principals.md)|[Tożsamość zarządzana usługi AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Nazwy główne usług](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Współużytkowany klucz dostępu](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autoryzacja|Zarządzanie — [RBAC](../../role-based-access-control/overview.md)<br>Dane — [listy ACL](data-lake-storage-access-control.md)|Zarządzanie — [RBAC](../../role-based-access-control/overview.md)<br>Dane — [listy ACL](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Szyfrowanie — dane przechowywane w spoczynku|Po stronie serwera — z [zarządzanymi usługami](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) lub kluczami [zarządzanymi przez klienta](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)|Po stronie serwera — z [zarządzanymi usługami](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) lub kluczami [zarządzanymi przez klienta](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)|
|Obsługa sieci wirtualnej|[Integracja z siecią wirtualną](../../data-lake-store/data-lake-store-network-security.md)|[Punkty końcowe usługi](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [prywatne punkty końcowe (publiczna wersja zapoznawcza)](../common/storage-private-endpoints.md)|
|Środowisko deweloperskie|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [interfejs wiersza polecenia platformy Azure](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[Rest](https://review.docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2), [.NET](/data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md) (w publicznej wersji zapoznawczej)|
|Dzienniki diagnostyczne|Dzienniki klasyczne<br>[Azure Monitor zintegrowany](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Dzienniki klasyczne](../common/storage-analytics-logging.md) (w publicznej wersji zapoznawczej)<br>Integracja ze monitorem platformy Azure — do opracowania na osi czasu|
|Ekosystem|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 i nowsze)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 i nowsze)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 Gen2 wzorców

Wybierz wzorzec migracji, a następnie zmodyfikuj ten wzorzec zgodnie z wymaganiami.

|||
|---|---|
|**Unieś i Przenieś**|Najprostszym wzorcem. Idealne rozwiązanie, jeśli potoki danych mogą zapewnić przestoje.|
|**Kopia przyrostowa**|Podobnie jak w przypadku *podnoszenia i przesunięcia*, ale z mniejszym przestojem. Idealne rozwiązanie w przypadku dużych ilości danych, które trwają dłużej niż kopiowanie.|
|**Podwójny potok**|Idealne rozwiązanie w przypadku potoków, które nie zapewniają przestojów.|
|**Synchronizacja dwukierunkowa**|Podobnie jak *podwójny potok*, ale z bardziej stopniowanym podejściem, które jest odpowiednie dla bardziej złożonych potoków.|

Przyjrzyjmy się bliżej każdej z wzorców.
 
### <a name="lift-and-shift-pattern"></a>Wzorzec podnoszenia i przesunięcia

Jest to najprostszy wzorzec.

1. Zatrzymaj wszystkie zapisy w Gen1.

2. Przenieś dane z Gen1 do Gen2. Zalecamy [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL są kopiowane z danymi.

3. Wypróbowanie operacji pozyskiwania i obciążeń do Gen2.

4. Likwidowanie Gen1.

![Wzorzec podnoszenia i przesunięcia](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Zagadnienia dotyczące używania wzorca dźwigu i przesunięcia

: heavy_check_mark: uruchomienie produkcyjne od Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

: heavy_check_mark: oczekuje przestoju podczas migracji i okresu uruchomienie produkcyjne.

: heavy_check_mark: idealne dla potoków, które mogą zapewnić przestoje i można uaktualnić jednocześnie wszystkie aplikacje.

### <a name="incremental-copy-pattern"></a>Wzorzec kopiowania przyrostowego

1. Rozpocznij przeniesienie danych z Gen1 do Gen2. Zalecamy [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL są kopiowane z danymi.

2. Przyrostowe kopiowanie nowych danych z Gen1.

3. Po skopiowaniu wszystkich danych Zatrzymaj wszystkie operacje zapisu do Gen1 i punkty obciążenia do Gen2.

4. Likwidowanie Gen1.

![Wzorzec kopiowania przyrostowego](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Zagadnienia dotyczące używania wzorca kopiowania przyrostowego:

: heavy_check_mark: uruchomienie produkcyjne od Gen1 do Gen2 dla wszystkich obciążeń w tym samym czasie.

: heavy_check_mark: oczekiwano przestoju w okresie uruchomienie produkcyjne.

: heavy_check_mark: idealne dla potoków, w których wszystkie aplikacje są uaktualnione w tym samym czasie, ale kopiowanie danych wymaga więcej czasu.

### <a name="dual-pipeline-pattern"></a>Podwójny wzorzec potoku

1. Przenieś dane z Gen1 do Gen2. Zalecamy [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Listy ACL są kopiowane z danymi.

2. Pozyskiwanie nowych danych zarówno w Gen1, jak i w Gen2.

3. Punkty obciążeń do Gen2.

4. Zatrzymaj wszystkie zapisy w Gen1, a następnie likwidowanie Gen1.

![Podwójny wzorzec potoku](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Zagadnienia dotyczące korzystania z podwójnego wzorca potoku:

: heavy_check_mark: potoki Gen1 i Gen2 są uruchamiane obok siebie.

: heavy_check_mark: obsługuje zero przestojów.

: heavy_check_mark: idealne w sytuacjach, gdy obciążenia i aplikacje nie mogą zapewnić przestoju i można pozyskać je na obu kontach magazynu.

### <a name="bi-directional-sync-pattern"></a>Wzorzec synchronizacji dwukierunkowej

1. Skonfiguruj replikację dwukierunkową między Gen1 i Gen2. Zalecamy [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Oferuje funkcję naprawy dla istniejących danych.

3. Po zakończeniu wszystkich operacji przenoszenia Zatrzymaj wszystkie zapisy do Gen1 i Wyłącz replikację dwukierunkową.

4. Likwidowanie Gen1.

![Wzorzec dwukierunkowy](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Zagadnienia dotyczące korzystania ze wzorca synchronizacji dwukierunkowej:

: heavy_check_mark: idealne dla złożonych scenariuszy obejmujących dużą liczbę potoków i zależności, w przypadku których podejście etapowe może być bardziej zrozumiałe.  

: heavy_check_mark: nakład migracji jest wysoki, ale zapewnia obsługę równoczesną dla Gen1 i Gen2.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat różnych części konfigurowania zabezpieczeń dla konta magazynu. Zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../common/storage-security-guide.md).
- Zoptymalizuj wydajność Data Lake Store. Zobacz [optymalizacja Azure Data Lake Storage Gen2 pod kątem wydajności](data-lake-storage-performance-tuning-guidance.md)
- Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi zarządzania Data Lake Store. Zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi używania Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

