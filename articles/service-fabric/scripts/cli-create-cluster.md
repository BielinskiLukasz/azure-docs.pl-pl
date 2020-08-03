---
title: Przykład wdrażania skryptu interfejsu wiersza polecenia platformy Azure
description: Jak utworzyć bezpieczny klaster Service Fabric Linux na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a0a17e3f352c9e39f118baec7e045521289f5ed0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502413"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Tworzenie bezpiecznego klastra systemu Linux usługi Service Fabric na platformie Azure

To polecenie tworzy certyfikat z podpisem własnym, dodaje go do magazynu kluczy i pobiera certyfikat lokalnie.  Nowy certyfikat służy do zabezpieczania klastra podczas wdrażania.  Zamiast tworzenia nowego certyfikatu można też użyć istniejącego.  W każdym razie nazwa podmiotu certyfikatu musi być zgodna z domeną używaną w celu uzyskiwania dostępu do klastra usługi Service Fabric. Takie dopasowanie jest wymagane w celu udostępnienia protokołu TLS dla punktów końcowych zarządzania HTTPS klastra i Service Fabric Explorer. Nie można uzyskać certyfikatu TLS/SSL z urzędu certyfikacji dla `.cloudapp.azure.com` domeny. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

W razie potrzeby zainstaluj [interfejs wiersza polecenia platformy Azure](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, klaster i wszystkie powiązane zasoby.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) | Tworzy nowy klaster usługi Service Fabric.  |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
