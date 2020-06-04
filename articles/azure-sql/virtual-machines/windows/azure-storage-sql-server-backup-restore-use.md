---
title: Jak używać usługi Azure Storage do SQL Server kopii zapasowych i przywracania | Microsoft Docs
description: Dowiedz się, jak utworzyć kopię zapasową SQL Server w usłudze Azure Storage. W tym artykule wyjaśniono zalety tworzenia kopii zapasowych baz danych SQL w usłudze Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: e4235a2d0be141d8cb1af30f2c573e3bdfcdd817
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342905"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Tworzenie kopii zapasowych i przywracanie SQL Server przy użyciu usługi Azure Storage
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Począwszy od SQL Server 2012 SP1 ZASTOSUJESZ pakietu CU2 można teraz pisać SQL Server kopie zapasowe bezpośrednio w usłudze Azure Blob Storage. Za pomocą tej funkcji można tworzyć kopie zapasowe i przywracać z usługi Azure Blob Storage i bazy danych SQL Server. Tworzenie kopii zapasowych w chmurze zapewnia korzyści z dostępności, nieograniczonego magazynu poza lokacją i ułatwia migrację danych do i z chmury. Instrukcje tworzenia kopii zapasowej lub przywracania można wydać przy użyciu języka Transact-SQL lub SMO.

## <a name="overview"></a>Omówienie
SQL Server 2016 wprowadza nowe możliwości; za pomocą [kopii zapasowej migawek plików](https://msdn.microsoft.com/library/mt169363.aspx) można wykonywać niemal chwilowo kopie zapasowe i niezwykle szybkie przywracanie.

W tym temacie opisano, dlaczego można użyć usługi Azure Storage do SQL Server kopii zapasowych, a następnie opisano składniki, których dotyczą. Możesz użyć zasobów podanych na końcu artykułu, aby uzyskać dostęp do przewodników i dodatkowych informacji, aby rozpocząć korzystanie z tej usługi z kopiami zapasowymi SQL Server.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Zalety korzystania z usługi Azure Blob Storage do tworzenia kopii zapasowych SQL Server
Istnieje kilka wyzwań, które należy uwzględnić podczas tworzenia kopii zapasowych SQL Server. Te wyzwania obejmują zarządzanie magazynem, ryzyko awarii magazynu, dostęp do magazynu poza lokacją i konfigurację sprzętu. Wiele z tych wyzwań zostało rozkierowanych za pomocą usługi Azure Blob Storage do SQL Server kopii zapasowych. Weź pod uwagę następujące korzyści:

* **Łatwość użycia**: przechowywanie kopii zapasowych w obiektach Blob platformy Azure może być wygodnym, elastycznym i łatwym w dostępie do opcji poza lokacją. Tworzenie magazynu poza lokacją dla SQL Server kopii zapasowych może być łatwe do zmodyfikowania istniejących skryptów/zadań, aby użyć składni **kopii zapasowej do adresu URL** . Magazyn poza lokacją powinien być zazwyczaj wystarczająco mały od lokalizacji produkcyjnej bazy danych, aby zapobiec pojedynczej awarii, która może mieć wpływ na Lokacje i produkcyjne bazy danych w trybie offline. Dzięki rozdzieleniu [geograficznie obiektów blob platformy Azure](../../../storage/common/storage-redundancy.md)masz dodatkową warstwę ochrony w przypadku awarii, która może wpływać na cały region.
* **Archiwum kopii zapasowych**: usługa Azure Blob Storage oferuje lepszą alternatywę dla często używanej opcji taśmy do archiwizowania kopii zapasowych. Magazyn taśm może wymagać fizycznego transportu do funkcji poza lokacją i środków ochrony multimediów. Przechowywanie kopii zapasowych w usłudze Azure Blob Storage zapewnia możliwość szybkiej, wysokiej dostępności i trwałego archiwizowania.
* **Zarządzany sprzęt**: nie ma żadnych nakładów związanych z zarządzaniem sprzętem w ramach usług platformy Azure. Usługi platformy Azure zarządzają sprzętem i zapewniają replikację geograficzną w celu zapewnienia nadmiarowości i ochrony przed awariami sprzętu.
* **Nieograniczony magazyn**: dzięki włączeniu bezpośredniej kopii zapasowej obiektów blob platformy Azure masz dostęp do praktycznie nieograniczonego magazynu. Alternatywnie tworzenie kopii zapasowej na dysku maszyny wirtualnej platformy Azure ma limity na podstawie rozmiaru maszyny. Istnieje ograniczenie liczby dysków, które można dołączyć do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych. Ten limit wynosi 16 dysków dla bardzo dużego wystąpienia i mniej dla mniejszych wystąpień.
* **Dostępność kopii zapasowych**: kopie zapasowe przechowywane w obiektach Blob platformy Azure są dostępne z dowolnego miejsca i w dowolnym czasie i mogą być łatwo dostępne dla przywrócenia do wystąpienia SQL Server, bez konieczności dołączania/odłączania ani pobierania i dołączania dysku VHD.
* **Koszt**: Płatność wyłącznie za usługę, która jest używana. Może być opłacalne jako opcja archiwum poza lokacją i kopiami zapasowymi. Aby uzyskać więcej informacji, zobacz [Kalkulator cen platformy Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Kalkulator cen")i artykuł dotyczący [cen platformy Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Artykuł dotyczący cen") .
* **Migawki magazynu**: gdy pliki bazy danych są przechowywane w obiekcie blob platformy Azure i używasz SQL Server 2016, możesz użyć [kopii zapasowej migawek plików](https://msdn.microsoft.com/library/mt169363.aspx) , aby wykonywać niemal chwilowe kopie zapasowe i niezwykle szybkie przywracanie.

Aby uzyskać więcej informacji, zobacz [SQL Server kopia zapasowa i przywracanie za pomocą usługi Azure Blob Storage](https://go.microsoft.com/fwlink/?LinkId=271617).

W poniższych dwóch sekcjach wprowadzono usługę Azure Blob Storage, w tym wymagane składniki SQL Server. Ważne jest, aby zrozumieć składniki i ich interakcje w celu pomyślnego użycia kopii zapasowych i przywracania z usługi Azure Blob Storage.

## <a name="azure-blob-storage-components"></a>Składniki usługi Azure Blob Storage
Poniższe składniki platformy Azure są używane podczas tworzenia kopii zapasowych w usłudze Azure Blob Storage.

| Składnik | Opis |
| --- | --- |
| **Konto magazynu** |Konto magazynu jest punktem początkowym dla wszystkich usług magazynu. Aby uzyskać dostęp do usługi Azure Blob Storage, należy najpierw utworzyć konto usługi Azure Storage. Aby uzyskać więcej informacji na temat usługi Azure Blob Storage, zobacz [jak korzystać z usługi Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Kontener** |Kontener zawiera grupowanie zestawu obiektów blob i może przechowywać nieograniczoną liczbę obiektów BLOB. Aby napisać SQL Server kopię zapasową do magazynu obiektów blob platformy Azure, musisz mieć co najmniej utworzony kontener główny. |
| **Tworzenia** |Plik o dowolnym typie i rozmiarze. Adresy obiektów BLOB są adresowane przy użyciu następującego formatu adresu URL: **https://[konto magazynu]. blob. Core. Windows. NET/[Container]/[BLOB]**. Aby uzyskać więcej informacji na temat stronicowych obiektów blob, zobacz [Opis bloków i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Składniki SQL Server
Poniższe składniki SQL Server są używane podczas tworzenia kopii zapasowych w usłudze Azure Blob Storage.

| Składnik | Opis |
| --- | --- |
| **Adres URL** |Adres URL określa Uniform Resource Identifier (URI) do unikatowego pliku kopii zapasowej. Adres URL służy do dostarczania lokalizacji i nazwy pliku kopii zapasowej SQL Server. Adres URL musi wskazywać na rzeczywisty obiekt BLOB, a nie tylko kontener. Jeśli obiekt BLOB nie istnieje, zostanie utworzony. W przypadku określenia istniejącego obiektu BLOB kopia ZAPASowa kończy się niepowodzeniem, chyba że zostanie określona > z FORMATOWANIEm. Poniżej znajduje się przykładowy adres URL określony w poleceniu BACKUP: **http [s]://[storageaccount]. blob. Core. Windows. NET/[Container]/[filename. bak]**. Protokół HTTPS jest zalecany, ale nie jest wymagany. |
| **Poświadczenie** |Informacje wymagane do nawiązania połączenia i uwierzytelnienia w usłudze Azure Blob Storage są przechowywane jako poświadczenia. Aby SQL Server zapisywania kopii zapasowych do obiektu blob platformy Azure lub przywracania z niego, należy utworzyć SQL Server poświadczenia. Aby uzyskać więcej informacji, zobacz [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> SQL Server 2016 został zaktualizowany do obsługi blokowych obiektów BLOB. Aby uzyskać więcej informacji, zobacz [Samouczek: używanie Microsoft Azure Blob Storage z bazami danych SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) .
> 
> 

## <a name="next-steps"></a>Następne kroki
1. Utwórz konto platformy Azure, jeśli jeszcze go nie masz. Jeśli oceniasz platformę Azure, weź pod uwagę [bezpłatną wersję próbną](https://azure.microsoft.com/free/).
2. Następnie przejdź do jednego z następujących samouczków, które przeprowadzą Cię przez proces tworzenia konta magazynu i przywracania.
   
   * **SQL Server 2014**: [samouczek: SQL Server 2014 kopia zapasowa i przywracanie do Microsoft Azure magazynu obiektów BLOB](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [samouczek: używanie Microsoft Azure magazynu obiektów blob z bazami danych SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Zapoznaj się z dodatkową dokumentacją, rozpoczynając od [SQL Server kopii zapasowej i przywracania za pomocą Microsoft Azure magazynu obiektów BLOB](https://msdn.microsoft.com/library/jj919148.aspx).

Jeśli masz jakieś problemy, zapoznaj się z tematem [SQL Server wykonywania kopii zapasowych w ramach najlepszych rozwiązań dotyczących adresów URL i rozwiązywania problemów](https://msdn.microsoft.com/library/jj919149.aspx).

Inne SQL Server opcje tworzenia kopii zapasowej i przywracania znajdują się w temacie [Tworzenie kopii zapasowych i przywracanie SQL Server na platformie Azure Virtual Machines](backup-restore.md).

