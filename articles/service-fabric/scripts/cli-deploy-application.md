---
title: Interfejs wiersza polecenia usługi Azure Service Fabric (sfctl) — przykładowy skrypt wdrażania
description: Wdrażanie aplikacji w klastrze Azure Service Fabric przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 7e2bd212e88b3efcf7f3aad3ef1555b3fc48ef1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641734"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Ten przykładowy skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji w klastrze i tworzy wystąpienie aplikacji na podstawie typu aplikacji. W tym czasie są również tworzone wszystkie usługi domyślne.

Jeśli wystąpi taka potrzeba, zainstaluj [interfejs wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po zakończeniu do usunięcia aplikacji można użyć skryptu [remove](cli-remove-application.md). Skrypt remove usuwa wystąpienie aplikacji, wyrejestrowuje typ aplikacji i usuwa pakiet aplikacji z magazynu obrazów.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia usługi Service Fabric](../service-fabric-cli.md).

Więcej przykładów dla interfejsu wiersza polecenia usługi Azure Service Fabric można znaleźć na stronie z [przykładami interfejsu wiersza polecenia usługi Service Fabric](../samples-cli.md).
