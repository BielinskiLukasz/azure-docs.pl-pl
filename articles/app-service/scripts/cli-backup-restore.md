---
title: 'Interfejs wiersza polecenia: Przywracanie aplikacji z kopii zapasowej'
description: Dowiedz się, jak zautomatyzować wdrażanie aplikacji App Service i zarządzanie nią za pomocą interfejsu wiersza polecenia platformy Azure. Ten przykład pokazuje, jak przywrócić aplikację z kopii zapasowej.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: be6a0a5b700c4110acd4481b12130185c9878883
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962781"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Przywracanie aplikacji internetowej z kopii zapasowej przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z jej powiązanymi zasobami, a następnie tworzy jednorazową kopię zapasową na potrzeby tej aplikacji. 

Aby uruchomić ten skrypt, należy posiadać istniejącą kopię zapasową dla aplikacji internetowej. Aby ją utworzyć, zobacz [Tworzenie kopii zapasowej aplikacji internetowej](cli-backup-onetime.md) lub [Tworzenie zaplanowanej kopii zapasowej dla aplikacji internetowej](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | Przywraca aplikację internetową z kopii zapasowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).