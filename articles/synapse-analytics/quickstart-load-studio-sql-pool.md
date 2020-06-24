---
title: Ładowanie zbiorcze danych za pomocą języka SQL Synapse
description: Ładowanie danych za pomocą programu Synapse Studio w programie SQL Server Synapse
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 4ae13dc51b4621dc3d8be39049dcade87c105cbc
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262620"
---
# <a name="bulk-loading-with-synapse-sql"></a>Ładowanie zbiorcze przy użyciu języka SQL Synapse

Ładowanie danych nigdy nie było łatwiejsze w przypadku korzystania z Kreatora ładowania zbiorczego w programie Synapse Studio. Ten Kreator przeprowadzi Cię przez proces tworzenia skryptu T-SQL za pomocą [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) do ładowania zbiorczego danych. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Punkty wejścia do kreatora ładowania zbiorczego

Teraz można łatwo ładować dane przy użyciu pul SQL z prostym kliknięciem prawym przyciskiem myszy w następujących obszarach w programie Synapse Studio:

- Plik lub folder z konta usługi Azure Storage dołączonego do obszaru roboczego ![ kliknij prawym przyciskiem myszy plik lub folder na koncie magazynu](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Ten Kreator generuje instrukcję COPY, która używa usługi AAD Pass-through dla authentcation. [Użytkownik usługi AAD musi mieć dostęp](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples#d-azure-active-directory-authentication-aad) do obszaru roboczego z co najmniej rolą RBAC współautor danych obiektów blob magazynu dla konta ADLS Gen2.

- Użytkownik musi mieć uprawnienia wymagane [do użycia instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) i CREATE TABLE, jeśli tworzysz nową tabelę do załadowania do programu.

- Połączona usługa skojarzona z kontem ADLS Gen2 **musi mieć dostęp do** / **folderu** plików do załadowania. Na przykład jeśli mechanizm uwierzytelniania połączonej usługi jest tożsamością zarządzaną, tożsamość zarządzana w obszarze roboczym musi mieć co najmniej uprawnienie czytelnik magazynu obiektów BLOB na koncie magazynu.

- Jeśli sieć wirtualna jest włączona w obszarze roboczym, upewnij się, że zintegrowane środowisko uruchomieniowe skojarzone z usługami połączonymi ADLS Gen2 konta dla danych źródłowych i w lokalizacji pliku błędu ma włączoną funkcję interaktywnego tworzenia. Funkcja interaktywnego tworzenia jest wymagana do automatycznego wykrywania schematu, wyświetlania podglądu zawartości pliku źródłowego i przeglądania ADLS Gen2 kont magazynu w kreatorze.

### <a name="steps"></a>Kroki

1. Wybierz konto magazynu i plik lub folder, który jest ładowany w panelu źródłowa lokalizacja magazynu: ![ Wybieranie lokalizacji źródłowej](./sql/media/bulk-load/bulk-load-source-location.png)

2. Wybierz ustawienia formatu pliku, w tym konto magazynu, w którym chcesz napisać odrzucone wiersze (plik błędu). Obecnie obsługiwane są tylko pliki CSV i Parquet.

    ![Wybieranie ustawień formatu pliku](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Możesz kliknąć pozycję "Podgląd danych", aby zobaczyć, jak instrukcja COPY przeanalizuje plik w celu ułatwienia konfigurowania ustawień formatu pliku. Kliknij pozycję "Podgląd danych" za każdym razem, gdy zmienisz ustawienie formatu pliku, aby zobaczyć, jak instrukcja COPY przeanalizuje plik ze zaktualizowanym ustawieniem: ![ Podgląd danych](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Wybierz pulę SQL, która będzie używana do ładowania, w tym czy obciążenie będzie dla istniejącej tabeli czy nowej tabeli: ![ Wybieranie lokalizacji docelowej](./sql/media/bulk-load/bulk-load-target-location.png)

5. Kliknij pozycję "Konfiguruj Mapowanie kolumn", aby upewnić się, że masz odpowiednie mapowanie kolumn. W przypadku nowych tabel Konfigurowanie mapowania kolumn ma krytyczne znaczenie dla aktualizowania typów danych kolumny docelowej: ![ Konfigurowanie mapowania kolumn](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Kliknij pozycję "Otwórz skrypt", a skrypt T-SQL zostanie wygenerowany przy użyciu instrukcji COPY do załadowania z usługi Data Lake: ![ otwieranie skryptu SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z artykułem [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) , aby uzyskać więcej informacji na temat możliwości kopiowania
- Zapoznaj się z artykułem [Omówienie ładowania danych](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt)
