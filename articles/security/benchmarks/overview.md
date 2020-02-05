---
title: Omówienie testu porównawczego zabezpieczeń platformy Azure
description: Omówienie testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: de6bbb81b04b93cce8ab7ddaf1d983cb7e4a700d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990593"
---
# <a name="overview-of-azure-security-controls"></a>Omówienie kontroli zabezpieczeń platformy Azure

Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia, które pomagają ulepszyć zabezpieczenia aplikacji i danych na platformie Azure.

Ten test porównawczy koncentruje się na obszarach formantów skoncentrowanych na chmurze. Te kontrolki są spójne z dobrze znanymi testami zabezpieczeń, takimi jak opisane przez formanty Centrum zabezpieczeń internetowych (CIS) w wersji 7,1.

W teście zabezpieczeń Azure są używane następujące kontrolki: 

- [Zabezpieczenia sieci](security-control-network-security.md)
- [Rejestrowanie i monitorowanie](security-control-logging-monitoring.md)
- [Tożsamość i Access Control](security-control-identity-access-control.md)
- [Ochrona danych](security-control-data-protection.md)
- [Zarządzanie lukami w zabezpieczeniach](security-control-vulnerability-management.md)
- [Zarządzanie zapasami i zasobami](security-control-inventory-asset-management.md)
- [Bezpieczna konfiguracja](security-control-secure-configuration.md)
- [Ochrona przed złośliwym oprogramowaniem](security-control-malware-defense.md)
- [Odzyskiwanie danych](security-control-data-recovery.md)
- [Odpowiedź na zdarzenie](security-control-incident-response.md)
- [Testy penetracji i czerwone ćwiczenia zespołu](security-control-penetration-tests-red-team-exercises.md)

Możesz również pobrać [Arkusz kalkulacyjny programu Excel z usługi Azure Security Tests w wersji 1](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Zalecenia dotyczące usługi Azure Security test 

Każde zalecenie zawiera następujące informacje: 

- **Identyfikator platformy Azure**: Identyfikator testu porównawczego zabezpieczeń platformy Azure, który odpowiada zalecenia. 
- **Identyfikatory CIS**: zalecenia dotyczące testów porównawczych CIS, które odpowiadają temu rekomendacji.  
- **Odpowiedzialność**: niezależnie od tego, czy klient lub dostawca usług (lub obie) są odpowiedzialni za wdrożenie tego zalecenia. Obowiązki związane z zabezpieczeniami są udostępniane w chmurze publicznej. Niektóre funkcje kontroli zabezpieczeń są dostępne tylko dla dostawcy usług w chmurze, dlatego dostawca jest odpowiedzialny za adresowanie. Są to ogólne obserwacje — w przypadku niektórych poszczególnych usług odpowiedzialność będzie różna od tego, co jest wymienione w teście zabezpieczeń platformy Azure. Te różnice opisano w podstawowych zaleceniach dotyczących poszczególnych usług. 
- **Szczegóły**: uzasadnienie zalecenia i linki do wskazówek dotyczących sposobu jego wdrożenia. Jeśli zalecenie jest obsługiwane przez Azure Security Center, te informacje będą również wyświetlane.

Poznamy Twoją szczegółową opinię i aktywne uczestnictwo w pracy z usługą Azure Security test. Jeśli chcesz podać dane wejściowe bezpośredniego zespołu testowego, Wypełnij formularz w [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark).

## <a name="next-steps"></a>Następne kroki

- Zobacz pierwszą kontrolę zabezpieczeń: [zabezpieczenia sieci](security-control-network-security.md)
- Przeczytaj [wprowadzenie do usługi Azure Security test](introduction.md)
- Pobieranie [arkusza programu Excel dotyczącego usługi Azure Security test w wersji 1](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
