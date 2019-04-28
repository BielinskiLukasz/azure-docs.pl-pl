---
title: Przykłady interfejsu wiersza polecenia platformy Azure — App Service | Microsoft Docs
description: Przykłady interfejsu wiersza polecenia platformy Azure — App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835400"
---
# <a name="cli-samples-for-azure-app-service"></a>Przykłady interfejsu wiersza polecenia dla usługi Azure App Service

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Tworzenie aplikacji**||
| [Tworzenie aplikacji i wdrażanie plików za pomocą protokołu FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i wdraża w niej plik przy użyciu protokołu FTP. |
| [Tworzenie aplikacji i wdrażanie kodu z usługi GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i wdraża kod z publicznego repozytorium GitHub. |
| [Tworzenie aplikacji z ciągłym wdrażaniem z usługi GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service z ciągłym publikowaniem z własnego repozytorium GitHub. |
| [Tworzenie aplikacji i wdrażanie kodu z lokalnego repozytorium Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i konfiguruje wypychanie kodu z lokalnego repozytorium Git. |
| [Tworzenie aplikacji i wdrażanie kodu w środowisku przejściowym](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service z miejscem wdrożenia dla przejściowych zmian kodu. |
| [Tworzenie aplikacji ASP.NET Core w kontenerze platformy Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service w systemie Linux, a następnie ładuje obraz platformy Docker z usługi Docker Hub. |
|**Konfigurowanie aplikacji**||
| [Mapowanie domeny niestandardowej na aplikację](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i mapuje na nią nazwę domeny niestandardowej. |
| [Tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service, a następnie tworzy powiązanie certyfikatu SSL nazwy domeny niestandardowej z tą aplikacją. |
|**Skalowanie aplikacji**||
| [Ręczne skalowanie aplikacji](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i skaluje ją na 2 wystąpienia. |
| [Skalowanie aplikacji na całym świecie przy użyciu architektury wysokiej dostępności](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy dwie aplikacje usługi App Service w dwóch różnych regionach geograficznych i udostępnia je za pośrednictwem pojedynczego punktu końcowego przy użyciu usługi Azure Traffic Manager. |
|**Łączenie aplikacji z zasobami**||
| [Łączenie aplikacji z programem SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i bazę danych SQL, a następnie dodaje parametry połączenia z bazą danych do ustawień aplikacji. |
| [Łączenie aplikacji z kontem magazynu](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Tworzy aplikację usługi App Service i konto magazynu, a następnie dodaje parametry połączenia z magazynem do ustawień aplikacji. |
| [Łączenie aplikacji z usługą Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i usługę Azure Cache for Redis, a następnie dodaje szczegóły połączenia z serwerem Redis do ustawień aplikacji. |
| [Łączenie aplikacji z usługą Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service i usługę Cosmos DB, a następnie dodaje szczegóły połączenia z usługą Cosmos DB do ustawień aplikacji. |
|**Tworzenie i przywracanie kopii zapasowej aplikacji**||
| [Tworzenie kopii zapasowej aplikacji](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej jednorazową kopię zapasową. |
| [Tworzenie zaplanowanej kopii zapasowej aplikacji](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service oraz jej zaplanowaną kopię zapasową. |
| [Przywracanie aplikacji z kopii zapasowej](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Przywraca aplikację usługi App Service z kopii zapasowej. |
|**Monitorowanie aplikacji**||
| [Monitorowanie aplikacji za pomocą dzienników serwera internetowego](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Tworzy aplikację usługi App Service, włącza dla niej rejestrowanie i pobiera dzienniki na komputer lokalny. |
| | |