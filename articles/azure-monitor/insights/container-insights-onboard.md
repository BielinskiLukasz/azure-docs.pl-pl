---
title: Jak włączyć Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób włączania i konfigurowania Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jak działa kontener i jakie problemy związane z wydajnością zostały zidentyfikowane.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/11/2019
ms.openlocfilehash: f2a33f96f77678e02c5b72c36563781e7d7ac334
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928279"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak włączyć Azure Monitor dla kontenerów

Ten artykuł zawiera omówienie opcji dostępnych w celu skonfigurowania Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes i hostowanych w [usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/), aparacie AKS na [Azure Stack ](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)lub Kubernetes wdrożone lokalnie.

Azure Monitor dla kontenerów można włączyć dla nowych lub jednego lub kilku istniejących wdrożeń AKS przy użyciu następujących obsługiwanych metod:

* Z Azure Portal, Azure PowerShell lub z interfejsem wiersza polecenia platformy Azure
* Korzystanie z [Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* **Obszar roboczy Log Analytics.**

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Można utworzyć obszar roboczy po włączeniu monitorowania nowego klastra usługi AKS lub umożliwieniu funkcji dołączania tworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS. Jeśli wybrano opcję utworzenia jej samodzielnie, można ją utworzyć za pomocą [Azure Resource Manager](../platform/template-workspace-configuration.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md). Aby uzyskać listę obsługiwanych par mapowania używanych dla domyślnego obszaru roboczego, zobacz [Mapowanie regionów dla Azure monitor kontenerów](container-insights-region-mapping.md).

* Musisz być członkiem **roli współautor log Analytics** , aby umożliwić monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../platform/manage-access.md).

* Jesteś członkiem roli **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** w zasobie klastra AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metryki Prometheus nie są domyślnie zbierane. Przed [skonfigurowaniem agenta](container-insights-prometheus-integration.md) do zbierania danych należy zapoznać się z [dokumentacją](https://prometheus.io/) Prometheus, aby zrozumieć, co można zdefiniować.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Poniższe elementy są oficjalnie obsługiwane w przypadku kontenerów Azure Monitor.

- Środowiska: Kubernetes lokalnego, aparatu AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Wersje programu Kubernetes i zasady pomocy technicznej są takie same, jak wersje programu [AKS obsługiwane](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

Informacje w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory wymagane dla agenta kontenera do komunikowania się z Azure Monitor dla kontenerów. Cały ruch sieciowy z agenta jest wychodzący do Azure Monitor.

|Zasób agenta|Porty |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

W poniższej tabeli przedstawiono informacje o konfiguracji serwera proxy i zapory dla Chin platformy Azure.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Wprowadzanie danych |
| *. oms.opinsights.azure.cn | 443 | Przechodzenie do pakietu OMS |
| *.blob.core.windows.net | 443 | Służy do monitorowania łączności wychodzącej. |
| microsoft.com | 80 | Używany do łączności sieciowej. Jest to wymagane tylko wtedy, gdy wersja obrazu agenta to ciprod09262019 lub wcześniejsza. |
| dc.services.visualstudio.com | 443 | Dla programu na potrzeby telemetrii agenta przy użyciu publicznej chmury Application Insights platformy Azure. |

Informacje w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory dla instytucji rządowych USA platformy Azure.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.us | 443 | Wprowadzanie danych |
| *. oms.opinsights.azure.us | 443 | Przechodzenie do pakietu OMS |
| *.blob.core.windows.net | 443 | Służy do monitorowania łączności wychodzącej. |
| microsoft.com | 80 | Używany do łączności sieciowej. Jest to wymagane tylko wtedy, gdy wersja obrazu agenta to ciprod09262019 lub wcześniejsza. |
| dc.services.visualstudio.com | 443 | W przypadku telemetrii agenta przy użyciu Application Insights publicznej chmury platformy Azure. |

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności opiera się na Log Analytics agencie dla systemu Linux opracowaną dla Azure Monitor dla kontenerów. Ten wyspecjalizowany Agent zbiera dane dotyczące wydajności i zdarzeń ze wszystkich węzłów w klastrze, a agent jest automatycznie wdrażany i rejestrowany przy użyciu określonego obszaru roboczego Log Analytics podczas wdrażania. Wersja agenta to Microsoft/OMS: ciprod04202018 lub nowsza. jest reprezentowana przez datę w następującym formacie: *mmddyyyy*.

>[!NOTE]
>W wersji zapoznawczej obsługi systemu Windows Server dla programu AKS klaster AKS z węzłami systemu Windows Server nie ma zainstalowanego agenta do zbierania danych i przesyłania dalej do Azure Monitor. Zamiast tego węzeł systemu Linux jest automatycznie wdrażany w klastrze w ramach standardowego wdrożenia zbiera dane i przekazuje je do Azure Monitor w imieniu wszystkich węzłów Windows w klastrze.  
>

Po wydaniu nowej wersji agenta zostanie ona automatycznie uaktualniona do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Aby postępować zgodnie z wydaną wersją, zobacz [anonse dotyczące wersji agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Jeśli klaster AKS został już wdrożony, należy włączyć monitorowanie za pomocą interfejsu wiersza polecenia platformy Azure lub dostarczonego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl`, aby uaktualnić, usunąć, ponownie wdrożyć lub wdrożyć agenta.
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Azure Monitor dla kontenerów można włączyć za pomocą jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster AKS | [Tworzenie klastra przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra przy użyciu Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą narzędzia typu open source Terraform. |
| Istniejący klaster AKS | [Włączanie przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Możesz włączyć monitorowanie klastra AKS już wdrożonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| |[Włącz korzystanie z Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Można włączyć monitorowanie klastra AKS już wdrożonego za pomocą narzędzia typu "open source" Terraform. |
| | [Włącz z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Można włączyć monitorowanie jednego lub więcej klastrów AKS już wdrożonych ze strony AKS wielu klastrów w Azure Monitor. |
| | [Włącz z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra AKS w Azure Portal. |
| | [Włączanie przy użyciu szablonu Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Można włączyć monitorowanie klastra AKS przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |
| | [Włącz dla hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) | Można włączyć monitorowanie aparatu AKS hostowanego w Azure Stack lub dla Kubernetes hostowanego lokalnie. |

## <a name="next-steps"></a>Następne kroki

* Po włączeniu monitorowania pod kątem przechwytywania metryk kondycji zarówno dla węzłów klastra AKS, jak i dla każdego zasobnika te metryki kondycji są dostępne w Azure Portal. Aby dowiedzieć się, jak używać Azure Monitor kontenerów, zobacz temat [Wyświetlanie kondycji usługi Azure Kubernetes](container-insights-analyze.md).
