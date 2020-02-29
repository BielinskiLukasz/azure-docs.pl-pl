---
title: Przewodnik przechodzenia do analizy kodu zabezpieczeń firmy Microsoft
description: W tym artykule opisano Instalowanie rozszerzenia Microsoft Security code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197009"
---
# <a name="onboarding-and-installing"></a>Dołączanie i Instalowanie

Wymagania wstępne dotyczące analizy kodu zabezpieczeń firmy Microsoft:

- Kwalifikująca się oferta Microsoft Unified Support, zgodnie z opisem w następnej sekcji.
- Organizacja usługi Azure DevOps.
- Uprawnienie do instalowania rozszerzeń w organizacji usługi Azure DevOps.
- Kod źródłowy, który można synchronizować z potokiem usługi Azure DevOps hostowanym w chmurze.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Dołączanie rozszerzenia Microsoft Security code Analysis

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Interesuje Cię zakup rozszerzenia Microsoft Security code Analysis?

Jeśli masz jedną z następujących ofert pomocy technicznej, skontaktuj się z kierownikiem ds. klientów, aby zakupić lub zamienić istniejące godziny w celu uzyskania dostępu do rozszerzenia:

- Ujednolicona warstwa zaawansowanej pomocy technicznej
- Ujednolicona warstwa wydajności wsparcia
- pomoc techniczna Premier dla deweloperów
- pomoc techniczna Premier dla partnerów
- pomoc techniczna Premier dla przedsiębiorstw

Jeśli nie masz jednego z wymienionych powyżej umów pomocy technicznej, możesz kupić rozszerzenie od jednego z naszych partnerów.

**Następne kroki:**

Skontaktuj się z partnerem z poniższej listy i poproś o zakupienie rozszerzenia Microsoft Security code Analysis.

>**Partnerów**

- Strefy — szczegóły kontaktu: cloudsupport@zones.com

### <a name="become-a-partner"></a>Zostań partnerem

Zespół ds. analizy kodu zabezpieczeń firmy Microsoft chce dołączać partnerów do pomoc techniczna Premier umowy partnerskiej. Partnerzy mogą pomóc klientom z DevOpsm się bardziej bezpiecznym programowaniem, oferując rozszerzenie klientom, którzy chcą go zakupić, ale nie mają umowy wsparcia dla przedsiębiorstw z firmą Microsoft. Zainteresowani partnerzy mogą zarejestrować się [tutaj](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalowanie rozszerzenia Microsoft Security code Analysis

1. Po udostępnieniu rozszerzenia organizacji usługi Azure DevOps przejdź do strony organizacji usługi Azure DevOps. Przykładowy adres URL dla takiej strony jest `https://dev.azure.com/contoso`.
1. Wybierz ikonę torba do zakupów w prawym górnym rogu obok nazwy, a następnie wybierz pozycję **Zarządzaj rozszerzeniami**.
1. Wybierz pozycję **udostępnione**.
1. Wybierz rozszerzenie Microsoft Security code Analysis, a następnie wybierz **Zainstaluj**.
1. Z listy rozwijanej wybierz organizację usługi Azure DevOps, w której ma zostać zainstalowane rozszerzenie.
1. Wybierz pozycję **Zainstaluj**. Po zakończeniu instalacji możesz zacząć korzystać z rozszerzenia.

>[!NOTE]
> Nawet jeśli nie masz dostępu do instalacji rozszerzenia, Kontynuuj kroki instalacji. Podczas procesu instalacji możesz zażądać dostępu z administratora organizacji usługi Azure DevOps.

Po zainstalowaniu rozszerzenia zadania tworzenia bezpiecznego opracowywania są widoczne i dostępne do dodania do Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Dodawanie określonych zadań kompilacji do potoku usługi Azure DevOps

1. W organizacji usługi Azure DevOps Otwórz projekt zespołowy.
1. Wybierz pozycję **potoki** > **kompilacje**.
1. Wybierz potok, do którego chcesz dodać zadania kompilacji rozszerzenia:
   - Nowy potok: wybierz pozycję **Nowy** i postępuj zgodnie z szczegółowymi krokami, aby utworzyć nowy potok.
   - Edytuj potok: wybierz istniejący potok, a następnie wybierz pozycję **Edytuj** , aby rozpocząć edytowanie potoku.
1. Wybierz pozycję **+** i przejdź do okienka **Dodawanie zadań** .
1. Z listy lub przy użyciu pola wyszukiwania Znajdź zadanie kompilacji, które chcesz dodać. Wybierz pozycję **Dodaj**.
1. Określ parametry, które są zbędne dla zadania.
1. Utwórz nową kompilację w kolejce.
   >[!NOTE]
   >Ścieżki plików i folderów są względne dla katalogu głównego repozytorium źródłowego. Jeśli określisz pliki wyjściowe i foldery jako parametry, zostaną one zastąpione wspólną lokalizacją zdefiniowaną w agencie kompilacji.

> [!TIP]
>
> - Aby uruchomić analizę po kompilacji, umieść zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft po kroku publikowanie artefaktów kompilacji. Dzięki temu kompilacja może zakończyć i publikować wyniki przed uruchomieniem narzędzi do analizy statycznej.
> - Zawsze wybieraj pozycję **Kontynuuj przy błędzie** dla zadań kompilacji w celu zapewnienia bezpieczeństwa. Nawet jeśli jedno z narzędzi nie powiedzie się, inne mogą działać. Nie ma współzależności między narzędziami.
> - Zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft kończą się niepowodzeniem tylko wtedy, gdy uruchomienie narzędzia nie powiodło się. Ale zakończyły się powodzeniem, nawet jeśli narzędzie zidentyfikuje problemy w kodzie. Korzystając z zadania kompilacji po analizie, można skonfigurować kompilację do niepowodzenia, gdy narzędzie zidentyfikuje problemy w kodzie.
> - Niektóre zadania kompilacji DevOps platformy Azure nie są obsługiwane, gdy są uruchamiane za pośrednictwem potoku wydania. Dokładniej mówiąc, usługa Azure DevOps nie obsługuje zadań, które publikują artefakty z poziomu potoku wydania.
> - Aby uzyskać listę wstępnie zdefiniowanych zmiennych w kompilacji zespołu usługi Azure DevOps, którą można określić jako parametry, zobacz [zmienne kompilacji DevOps platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania zadań kompilacji, zobacz nasze [Przewodnik po konfiguracji](security-code-analysis-customize.md) lub [Przewodnik po konfiguracji YAML](yaml-configuration.md).

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się ze [stroną często zadawanych pytań](security-code-analysis-faq.md).
