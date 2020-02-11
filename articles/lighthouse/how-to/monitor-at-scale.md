---
title: Monitorowanie delegowanych zasobów na dużą skalę
description: Dowiedz się, jak efektywnie korzystać z dzienników Azure Monitor w sposób skalowalny dla dzierżaw klientów, którymi zarządzasz.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122898"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorowanie delegowanych zasobów na dużą skalę

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do zarządzania zasobami delegowanymi przez platformę Azure. Usługa [Azure Lighthouse](../overview.md) umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawcach, co sprawia, że zadania zarządzania są bardziej wydajne.

W tym temacie przedstawiono sposób korzystania z [dzienników Azure monitor](../../azure-monitor/platform/data-platform-logs.md) w sposób skalowalny dla dzierżaw klientów, którymi zarządzasz.

## <a name="create-log-analytics-workspaces"></a>Tworzenie Log Analytics obszarów roboczych

Aby zbierać dane, musisz utworzyć Log Analytics obszary robocze. Te obszary robocze Log Analytics są unikatowymi środowiskami dla danych zbieranych przez Azure Monitor. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym.

Zalecamy tworzenie tych obszarów roboczych bezpośrednio w dzierżawach klientów. W ten sposób ich dane pozostają w swoich dzierżawcach, a nie eksportowane do użytkownika. Umożliwia to również Scentralizowane monitorowanie wszelkich zasobów i usług obsługiwanych przez Log Analytics, co zapewnia większą elastyczność w zakresie typów monitorowanych danych.

Obszar roboczy Log Analytics można utworzyć przy użyciu [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), przy użyciu [interfejsu wiersza polecenia platformy Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)lub przy użyciu [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Wdrażanie zasad, które rejestrują dane

Po utworzeniu obszarów roboczych Log Analytics można wdrożyć [Azure Policy](../../governance/policy/index.yml) między hierarchiami klientów, aby dane diagnostyczne były wysyłane do odpowiedniego obszaru roboczego w każdej dzierżawie. Dokładne wdrożone zasady mogą się różnić w zależności od typów zasobów, które mają być monitorowane.

Aby dowiedzieć się więcej o tworzeniu zasad, zobacz [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../../governance/policy/tutorials/create-and-manage.md). To [Narzędzie Community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator) zawiera skrypt ułatwiający Tworzenie zasad w celu monitorowania wybranych typów zasobów.

Po ustaleniu zasad, które mają zostać wdrożone, można [je wdrożyć w ramach delegowanych subskrypcji na dużą skalę](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analizowanie zebranych danych

Po wdrożeniu zasad dane będą rejestrowane w obszarach roboczych Log Analytics utworzonych w każdej dzierżawie klienta. Aby uzyskać szczegółowe informacje na temat wszystkich zarządzanych klientów, można użyć narzędzi, takich jak [Azure monitor skoroszyty](../../azure-monitor/platform/workbooks-overview.md) do zbierania i analizowania informacji z wielu źródeł danych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure monitor](../../azure-monitor/index.yml).
- Dowiedz się więcej na temat [dzienników Azure monitor](../../azure-monitor/platform/data-platform-logs.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
