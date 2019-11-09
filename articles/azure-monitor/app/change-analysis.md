---
title: Użyj analizy zmian aplikacji w Azure Monitor, aby znaleźć problemy z aplikacją sieci Web | Microsoft Docs
description: Korzystając z analizy zmian aplikacji w Azure Monitor, można rozwiązywać problemy z aplikacjami w witrynach na żywo w programie Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: ed297a1005f67a14db1da15aba2c47c98e83df9c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884969"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Korzystanie z analizy zmian aplikacji (wersja zapoznawcza) w Azure Monitor

Gdy wystąpi problem lub awaria witryny na żywo, szybkie określenie głównej przyczyny ma krytyczne znaczenie. Standardowe rozwiązania do monitorowania mogą powiadamiać o problemie. Mogą nawet wskazywać, który składnik kończy się niepowodzeniem. Jednak ten alert nie zawsze natychmiast wyjaśni przyczynę niepowodzenia. Wiadomo, że witryna działała pięć minut temu, a teraz jest ona uszkodzona. Co zmieniło się w ciągu ostatnich pięciu minut? Jest to pytanie, że analiza zmian aplikacji została zaprojektowana pod kątem odpowiedzi w Azure Monitor.

W oparciu o możliwości [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zapewnia wgląd w zmiany aplikacji platformy Azure w celu zwiększenia zauważalności i zmniejszenia MTTR (średni czas naprawy).

> [!IMPORTANT]
> Analiza zmian jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Ta wersja nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="overview"></a>Omówienie

Analiza zmian wykrywa różne typy zmian z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji. Jest to dostawca zasobów platformy Azure na poziomie subskrypcji, który sprawdza zmiany zasobów w subskrypcji. Analiza zmian udostępnia dane dla różnych narzędzi diagnostycznych, które pomagają użytkownikom zrozumieć, jakie zmiany mogą powodować problemy.

Na poniższym diagramie przedstawiono architekturę analizy zmian:

![Diagram architektury sposobu, w jaki Analiza zmian pobiera zmiany danych i udostępnia je narzędziom klienckim](./media/change-analysis/overview.png)

Obecnie Analiza zmian jest zintegrowana z interfejsem **diagnozowanie i rozwiązywanie problemów** w aplikacji internetowej App Service, a także dostępne jako blok autonomiczny w Azure Portal.
Zapoznaj się z sekcją *Wyświetlanie zmian dla wszystkich zasobów w systemie Azure* , aby uzyskać dostęp do bloku zmiana analizy oraz *analizę zmian dla Web Apps funkcji* w celu użycia jej w portalu aplikacji sieci Web w dalszej części tego artykułu.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager zmian właściwości śledzonych

Przy użyciu [grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)Analiza zmian zawiera historyczny rekord, w jaki sposób zasoby platformy Azure obsługujące aplikację zostały zmienione w miarę upływu czasu. Można wykryć śledzone ustawienia, takie jak tożsamości zarządzane, uaktualnienie systemu operacyjnego platformy i nazwy hostów.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager zmiany ustawień serwera proxy
Ustawienia, takie jak reguła konfiguracji protokołu IP, ustawienia protokołu SSL i wersje rozszerzeń, nie są jeszcze dostępne w ARG, dlatego należy zmienić zapytania analizy i ponownie obliczyć te zmiany, aby zapewnić więcej szczegółów dotyczących zmian w aplikacji. Te informacje nie są jeszcze dostępne w usłudze Azure Resource Graph, ale będą dostępne wkrótce.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Zmiany w wdrażaniu i konfigurowaniu aplikacji sieci Web (zmiany w gościu)

Analiza zmian przechwytuje stan wdrożenia i konfiguracji aplikacji co 4 godziny. Może wykrywać na przykład zmiany w zmiennych środowiskowych aplikacji. Narzędzie oblicza różnice i prezentuje, co się zmieniło. W przeciwieństwie do Menedżer zasobów zmian, informacje o zmianie wdrożenia kodu mogą nie być dostępne natychmiast w narzędziu. Aby wyświetlić najnowsze zmiany w analizie zmiany, wybierz pozycję **Skanuj zmiany teraz**.

![Zrzut ekranu przedstawiający przycisk "Skanuj zmiany teraz"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Zmiany zależności

Zmiany zależności zasobów mogą również powodować problemy w aplikacji sieci Web. Na przykład jeśli aplikacja sieci Web wywołuje w pamięci podręcznej Redis, jednostka SKU pamięci podręcznej Redis może mieć wpływ na wydajność aplikacji sieci Web. Aby wykryć zmiany w zależnościach, Analiza zmian sprawdza rekord DNS aplikacji sieci Web. W ten sposób identyfikuje zmiany we wszystkich składnikach aplikacji, które mogą powodować problemy.
Obecnie obsługiwane są następujące zależności:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Zapewniania
Dostawca zasobów "Microsoft. ChangeAnalysis" musi być zarejestrowany w ramach subskrypcji dla Azure Resource Manager śledzonych właściwości i ustawień serwera proxy Zmień dane na dostępne. Po wprowadzeniu aplikacji sieci Web diagnozowanie i rozwiązywanie problemów lub wywołaj blok niezależny analizy zmian ten dostawca zasobów jest automatycznie rejestrowany. Nie ma żadnych implementacji wydajności i kosztów dla Twojej subskrypcji.
W przypadku zmian w aplikacji sieci Web w gościu do skanowania plików kodu w ramach aplikacji sieci Web jest wymagane oddzielne Włączanie. Aby uzyskać więcej informacji, zobacz sekcję *Włączanie analizy zmian w sekcji diagnozowanie i rozwiązywanie problemów* w dalszej części tego artykułu.

## <a name="viewing-changes-for-all-resources-in-azure"></a>Wyświetlanie zmian dla wszystkich zasobów na platformie Azure
W Azure Monitor istnieje autonomiczny blok służący do analizy zmian, który umożliwia wyświetlanie wszystkich zmian za pomocą szczegółowych informacji i zasobów zależności aplikacji.

Wyszukaj wartość Analiza zmian na pasku wyszukiwania na Azure Portal, aby uruchomić blok.

![Zrzut ekranu przeszukiwania analizy zmian w Azure Portal](./media/change-analysis/search-change-analysis.png)

Wybierz pozycję Grupa zasobów i zasoby, aby rozpocząć wyświetlanie zmian.

![Zrzut ekranu przedstawiający blok analizy zmian w Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Możesz wyświetlić informacje o zasobach i powiązanych zależnościach, które obsługują aplikację. Ten widok został zaprojektowany z założeniami, aby deweloperzy mogli rozwiązywać problemy.

Obecnie obsługiwane zasoby obejmują:
- Maszyny wirtualne
- Zestaw skalowania maszyn wirtualnych
- Zasoby sieci platformy Azure
- Aplikacja internetowa z funkcją śledzenia plików gościa i zmienne środowiskowe

Aby uzyskać opinię, użyj przycisku Wyślij opinię w bloku lub changeanalysisteam@microsoft.come-mail.

![Zrzut ekranu przycisku opinii w bloku Analiza zmian](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Zmień analizę dla funkcji Web Apps

W Azure Monitor analizy zmian są również wbudowane w funkcję **diagnozowania i rozwiązywania problemów** . Uzyskaj dostęp do tego środowiska na stronie **Przegląd** aplikacji App Service.

![Zrzut ekranu przedstawiający przycisk "przegląd" i przycisk "diagnozowanie i rozwiązywanie problemów"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Włącz analizę zmian w narzędziu diagnozowanie i rozwiązywanie problemów

1. Wybierz pozycję **dostępność i wydajność**.

    ![Zrzut ekranu przedstawiający opcje rozwiązywania problemów z "dostępnością i wydajnością"](./media/change-analysis/availability-and-performance.png)

1. Wybierz pozycję **zmiany aplikacji**. Nie jest również dostępna w przypadku **awarii aplikacji**.

   ![Zrzut ekranu przedstawiający przycisk "awarie aplikacji"](./media/change-analysis/application-changes.png)

1. Aby włączyć analizę zmian, wybierz pozycję **Włącz teraz**.

   ![Zrzut ekranu opcji "awarie aplikacji"](./media/change-analysis/enable-changeanalysis.png)

1. Włącz **analizę zmian** i wybierz pozycję **Zapisz**. Narzędzie wyświetla wszystkie aplikacje sieci Web w ramach planu App Services. Możesz użyć przełącznika poziomu planu, aby włączyć analizę zmian dla wszystkich aplikacji sieci Web w ramach planu.

    ![Zrzut ekranu przedstawiający interfejs użytkownika "Włącz analizę zmian"](./media/change-analysis/change-analysis-on.png)


1. Aby uzyskać dostęp do analizy zmian, wybierz opcję **Diagnozuj i rozwiąż problemy** > **dostępności i wydajności** > **awarie aplikacji**. Zobaczysz Wykres podsumowujący typ zmian w czasie wraz ze szczegółami dotyczącymi tych zmian. Domyślnie zmiany w ciągu ostatnich 24 godzin są wyświetlane, aby ułatwić natychmiastowe Rozwiązywanie problemów.

     ![Zrzut ekranu przedstawiający widok różnic między zmianami](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Włącz analizę zmian na dużą skalę

Jeśli Twoja subskrypcja obejmuje wiele aplikacji sieci Web, włączenie usługi na poziomie aplikacji sieci Web byłoby niewydajne. Uruchom następujący skrypt, aby włączyć wszystkie aplikacje sieci Web w ramach subskrypcji.

Wymagania wstępne:
* PowerShell AZ module. Postępuj zgodnie z instrukcjami w [instalacji modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Uruchom następujący skrypt:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Następne kroki

- Włącz Application Insights dla [aplikacji App Services platformy Azure](azure-web-apps.md).
- Włącz Application Insights dla [maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych platformy Azure dla aplikacji hostowanych przez usługi IIS](azure-vm-vmss-apps.md).
- Dowiedz się więcej o [usłudze Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), która ułatwia analizę zmian.
