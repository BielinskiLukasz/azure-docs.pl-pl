---
title: Microsoft Azure urządzenie Data Box wymagania systemowe | Microsoft Docs
description: Informacje na temat wymagań dotyczących oprogramowania i sieci dla urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259957"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe Azure Data Box

W tym artykule opisano ważne wymagania systemowe dotyczące urządzenie Data Box Microsoft Azure i dla klientów nawiązujących połączenie z urządzenie Data Box. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem urządzenie Data Box, a następnie odwoływanie się do niego w razie potrzeby podczas wdrażania i kolejnej operacji.

Wymagania systemowe obejmują:

* **Wymagania dotyczące oprogramowania dla hostów łączących się z urządzenie Data Box** — opisuje obsługiwane platformy, przeglądarki dla lokalnego interfejsu użytkownika sieci Web, klientów protokołu SMB i dodatkowe wymagania dotyczące hostów, które mogą łączyć się z urządzenie Data Box.
* **Wymagania sieciowe dotyczące urządzenie Data Box** — zawiera informacje o wymaganiach sieciowych dla optymalnej operacji urządzenie Data Box.


## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania obejmują informacje w obsługiwanych systemach operacyjnych, obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web i klientów SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Obsługiwane systemy plików dla klientów z systemem Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Obsługiwane typy

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10-GbE nie jest dostępne, za pomocą linku danych 1 GbE można kopiować dane, ale mają wpływ na szybkości kopiowania.

### <a name="port-requirements"></a>Wymagania dotyczące portów

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby zezwalać na ruch SMB lub NFS. W tej tabeli *w programie lub w* *ruchu przychodzącym* odwołuje się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym urządzenie urządzenie Data Box wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzące do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Następne kroki

* [Wdrażanie Azure Data Box](data-box-deploy-ordered.md)
