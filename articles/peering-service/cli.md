---
title: Rejestrowanie połączenia usługi Komunikacja równorzędna w wersji zapoznawczej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zarejestrować połączenie z usługą komunikacji równorzędnej za pomocą interfejsu wiersza polecenia platformy Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872132"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Rejestrowanie połączenia usługi komunikacji równorzędnej za pomocą interfejsu wiersza polecenia platformy Azure

Usługa komunikacji równorzędnej Azure to usługa sieciowa, która zwiększa łączność klienta z usługami w chmurze firmy Microsoft, takimi jak Office 365, Dynamics 365, SaaS (Software as a Service), Azure lub dowolnych usług firmy Microsoft dostępnych za pośrednictwem publicznej sieci Internet. W tym artykule dowiesz się, jak zarejestrować połączenie z usługą komunikacji równorzędnej za pomocą interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, Utwórz [konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) teraz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne 

Wymagane są następujące elementy:

### <a name="azure-account"></a>Konto platformy Azure

Musisz mieć prawidłowe i aktywne konto Microsoft Azure. To konto jest wymagane do skonfigurowania połączenia z usługą komunikacji równorzędnej. Usługa komunikacji równorzędnej jest zasobem w ramach subskrypcji platformy Azure.

### <a name="connectivity-provider"></a>Dostawca połączenia

Aby uzyskać usługę komunikacji równorzędnej w celu połączenia sieci z siecią firmy Microsoft, możesz współpracować z usługodawcą internetowym lub z internetowym partnerem programu Exchange.

Upewnij się, że dostawcy łączności są partnerem z firmą Microsoft.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję

Aby rozpocząć konfigurację, zaloguj się do konta platformy Azure. Jeśli używasz Cloud Shell **Wypróbuj** tę opcję, nastąpi automatyczne logowanie. Poniższe przykłady ułatwiają nawiązanie połączenia.

```azurecli-interactive
az login
```

Sprawdź subskrypcje dostępne na koncie.

```azurecli-interactive
az account list
```

Wybierz subskrypcję, dla której chcesz zarejestrować połączenie usługi komunikacji równorzędnej.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed zarejestrowaniem połączenia z usługą komunikacji równorzędnej. Grupę zasobów można utworzyć, uruchamiając następujące polecenie:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Zarejestruj swoją subskrypcję przy użyciu dostawcy zasobów i flagi funkcji

Przed przejściem do procedury rejestrowania połączenia usługi komunikacji równorzędnej za pomocą interfejsu wiersza polecenia platformy Azure Zarejestruj swoją subskrypcję z flagą dostawca zasobów i funkcję przy użyciu interfejsu wiersza polecenia platformy Azure. Poleceń interfejsu wiersza polecenia platformy Azure opisano tutaj:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Zarejestruj połączenie usługi Komunikacja równorzędna

Zarejestruj połączenie usługi komunikacji równorzędnej za pomocą następującego zestawu poleceń za pośrednictwem interfejsu wiersza polecenia platformy Azure. Ten przykład rejestruje połączenie usługi komunikacji równorzędnej o nazwie myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. Zarejestruj prefiks

Zarejestruj prefiks dostarczony przez dostawcę połączenia, wykonując następujące polecenia za pośrednictwem interfejsu wiersza polecenia platformy Azure. Ten przykład rejestruje prefiks o nazwie mój prefiks.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
- Aby zarejestrować połączenie przy użyciu Azure PowerShell, zobacz [Rejestrowanie połączenia usługi Komunikacja równorzędna — Azure PowerShell](powershell.md).
- Aby zarejestrować połączenie przy użyciu Azure Portal, zobacz [Rejestrowanie połączenia usługi Komunikacja równorzędna — Azure Portal](azure-portal.md).
