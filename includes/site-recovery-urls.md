---
title: dołączanie pliku
description: dołączanie pliku
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183756"
---
Nazwa | Komercyjny adres URL | Adres URL instytucji rządowych | Opis
--- | --- | --- | ---
Usługa Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Służy do kontroli dostępu i zarządzania tożsamościami przy użyciu Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Służy do transferowania i koordynacji danych replikacji.
Replikacja | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Służy do wykonywania operacji i koordynacji zarządzania replikacją.
Magazyn | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Służy do uzyskiwania dostępu do konta magazynu przechowującego zreplikowane dane.
Dane telemetryczne (opcjonalnie) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Używany na potrzeby telemetrii.
Synchronizacja czasu | ``time.windows.com`` | ``time.nist.gov`` | Służy do sprawdzania synchronizacji czasu między systemem i czasem globalnym we wszystkich wdrożeniach.


