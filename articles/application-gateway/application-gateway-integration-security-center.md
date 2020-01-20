---
title: Integracja Application Gateway z Azure Security Center | Microsoft Docs
description: Ta strona zawiera informacje na temat sposobu integrowania Application Gateway z Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277188"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Omówienie integracji między Application Gateway i Azure Security Center

Dowiedz się, jak Application Gateway i Security Center ułatwiają ochronę zasobów aplikacji sieci Web. Zapora aplikacji sieci Web w usłudze Application Gateway (WAF) integruje się z usługą [Security Center](../security-center/security-center-intro.md) , aby zapewnić bezproblemowy widok, który zapobiega zagrożeniom, wykrywanie ich i reagowanie na nie w przypadku niechronionych aplikacji sieci Web w środowisku.

## <a name="overview"></a>Przegląd

Application Gateway WAF to zalecenie Security Center do ochrony aplikacji sieci Web przed atakami i lukami w zabezpieczeniach. Zasoby z obsługą sieci Web, które nie są chronione przez WAF, są wyświetlane w usłudze Security Center jako zalecenia o wysokiej ważności. Zalecenia dotyczące zapór aplikacji sieci Web są wyświetlane na stronie **Przegląd** w obszarze **aplikacje**.

![Integracja z usługą Security Center][1]

Kliknięcie wszelkich zaleceń dotyczących zapory aplikacji sieci Web otwiera nową stronę zawierającą szczegóły zalecenia.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Dodawanie zapory aplikacji sieci Web do istniejącego zasobu

Przejdź do **wszystkich usług** > **zabezpieczenia + tożsamość** > **Security Center** i na **Security Center — przegląd**kliknij pozycję **aplikacje**. W przypadku **aplikacji Security Center**w tabeli znajduje się lista aplikacji, które Security Center wykryte w ramach subskrypcji.

![aplikacje sieci Web][3]

Klikając aplikację sieci Web o krytycznym problemie, uzyskasz dostęp do strony **kondycja zabezpieczeń aplikacji** . Na poniższej ilustracji aplikacja sieci Web, która nie jest chroniona przez zaporę aplikacji sieci Web. 

![zasoby sieci Web niechronione][2]

Kliknij pozycję **Dodaj zaporę aplikacji sieci Web** w obszarze **zalecenia** , aby otworzyć stronę **Dodawanie zapory aplikacji sieci Web** .

Jeśli nie masz istniejącego Application Gateway lub chcesz utworzyć nowy, kliknij pozycję **Utwórz nowy** i w obszarze **Utwórz nową zaporę aplikacji sieci Web**, a następnie kliknij pozycję **Microsoft-Application Gateway**. Przeprowadzimy Cię przez kroki tworzenia bramy aplikacji. W tym momencie aplikacja sieci Web zostanie dodana jako zasób chroniony, Security Center teraz śledzi, że ten zasób jest chroniony przez zaporę aplikacji sieci Web. Nie dodaje go jako członka puli zaplecza.

Jeśli masz istniejącą bramę aplikacji, możesz wybrać ją w obszarze **Użyj istniejącego rozwiązania**

![Strona umożliwiająca dodanie zapory aplikacji sieci Web][4]

Dodawanie aplikacji sieci Web do bramy aplikacji za pomocą Security Center nie powoduje dodania zasobu jako członka puli zaplecza. Należy to zrobić bezpośrednio w zasobie bramy aplikacji.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Dodawanie zasobu do istniejącej zapory aplikacji sieci Web

Przejdź do **wszystkich usług** > **zabezpieczenia + tożsamość** > **Security Center** i na **Security Center — przegląd**kliknij pozycję **rozwiązania partnerskie**. Istniejące Security Center bramy aplikacji obsługujące aplikacje są wyświetlane na stronie **rozwiązania partnerskie** .

![rozwiązania partnerskie][7]

Kliknij pozycję **Połącz aplikację** , aby otworzyć **łącza aplikacje**. w tym miejscu można wybrać istniejące aplikacje. Wybierz aplikacje, które mają być chronione, a następnie kliknij przycisk **OK**. Nie powoduje to dodania aplikacji sieci Web do puli zaplecza bramy Application Gateway. Spowoduje to ustawienie zasobów jako zasobu chronionego, aby Security Center mógł ją śledzić. Aby dodać zasób jako element członkowski puli zaplecza, należy to zrobić na bramie aplikacji, na bieżącej stronie można kliknąć **konsolę rozwiązania** , która ma zostać przeprowadzona do zasobu bramy aplikacji, w którym można dodać aplikację sieci Web do puli zaplecza.

![aplikacje rozwiązań partnerskich][6]

## <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Security Center śledzi aplikacje dodane do bramy aplikacji jako zasób chroniony.  Monitoruje kondycję tego zasobu i gwarantuje, że jest on chroniony przez bramę aplikacji. Następnym krokiem jest dodanie prywatnego adresu IP, publicznego adresu IP lub karty sieciowej maszyny wirtualnej do puli zaplecza bramy aplikacji. Dopóki nie zostanie to zrobione, do momentu dodania zasobu zostanie wyświetlone dodatkowe zalecenie **finalizowania ochrony aplikacji** .

![Strona umożliwiająca dodanie zapory aplikacji sieci Web][5]

## <a name="security-alerts"></a>Alerty zabezpieczeń

W obszarze Security Center przejdź do **wykrywania** > **alerty zabezpieczeń**.  Tutaj znajdziesz alerty WAF dla bram aplikacji. Alerty są podzielone według reguły WAF.

![alerty zabezpieczeń][8]

Wybranie reguły spowoduje udostępnienie listy alertów dla tej konkretnej reguły WAF. Każdy alert zawiera dodatkowe szczegóły dotyczące wyszukiwania. Szczegóły zawierają link do bramy aplikacji.
 
![Szczegóły alertu][9]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć zaporę aplikacji sieci Web w istniejącej bramie aplikacji, odwiedź stronę [Tworzenie lub aktualizowanie Application Gateway platformy Azure za pomocą zapory aplikacji sieci Web](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png