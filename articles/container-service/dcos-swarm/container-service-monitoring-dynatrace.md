---
title: PRZESTARZAŁE Monitorowanie klastra DC/OS platformy Azure — dynaTrace
description: Monitoruj klaster Azure Container Service DC/OS przy użyciu dynaTrace. Wdróż dynaTrace OneAgent przy użyciu pulpitu nawigacyjnego DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277748"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>PRZESTARZAŁE Monitoruj klaster Azure Container Service DC/OS przy użyciu dynaTrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule pokazano, jak wdrożyć [dynaTrace](https://www.dynatrace.com/) OneAgent w celu monitorowania wszystkich węzłów agenta w klastrze Azure Container Service. Do tej konfiguracji jest potrzebne konto z dynaTrace SaaS/zarządzane. 

## <a name="dynatrace-saasmanaged"></a>DynaTrace SaaS/zarządzany
DynaTrace to natywne rozwiązanie do monitorowania chmurowego dla wysoce dynamicznego środowiska kontenera i klastrów. Dzięki temu można lepiej zoptymalizować wdrożenia kontenerów i alokacje pamięci za pomocą danych użycia w czasie rzeczywistym. Jest ona w stanie automatycznie wykrywać problemy związane z aplikacjami i infrastrukturą, zapewniając automatyczne określania poziomu odniesienia, korelację problemu i wykrywanie przyczyn głównych.

Na poniższej ilustracji przedstawiono interfejs użytkownika dynaTrace:

![Interfejs użytkownika dynaTrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Wymagania wstępne 
[Wdróż](container-service-deployment.md) i [Połącz](./../container-service-connect.md) się z klastrem skonfigurowanym przez Azure Container Service. Przegląd [interfejsu użytkownika platformy Marathon](container-service-mesos-marathon-ui.md). Przejdź do [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) , aby skonfigurować konto dynaTrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurowanie wdrożenia dynaTrace za pomocą Marathon
W tych krokach pokazano, jak skonfigurować i wdrożyć aplikacje dynaTrace w klastrze przy użyciu Marathon.

1. Dostęp do interfejsu użytkownika DC/OS za pośrednictwem [http://localhost:80/](http://localhost:80/). Po uruchomieniu interfejsu użytkownika DC/OS przejdź do karty **Universe** , a następnie wyszukaj ciąg **dynaTrace**.

    ![DynaTrace na platformie DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Aby ukończyć konfigurację, musisz mieć konto dynaTrace SaaS lub bezpłatne konto próbne. Po zalogowaniu się do pulpitu nawigacyjnego dynaTrace wybierz pozycję **Wdróż dynaTrace**.

    ![DynaTrace konfiguracja integracji PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na stronie wybierz pozycję **Skonfiguruj integrację PaaS**. 

    ![Token interfejsu API dynaTrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Wprowadź token interfejsu API do konfiguracji OneAgent dynaTrace w ramach platformy DC/OS. 

    ![Konfiguracja dynaTrace OneAgent w systemie operacyjnym platformy DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ustaw wystąpienia na liczbę węzłów, które mają zostać uruchomione. Ustawienie większej liczby również działa, ale platforma DC/OS będzie nadal próbować znaleźć nowe wystąpienia, dopóki ten numer nie zostanie rzeczywiście osiągnięty. Jeśli wolisz, możesz również ustawić tę wartość na taką, jak 1000000. W takim przypadku za każdym razem, gdy nowy węzeł zostanie dodany do klastra, dynaTrace automatycznie wdraża agenta w nowym węźle, a cena DC/OS stale próbuje wdrożyć kolejne wystąpienia.

    ![DynaTrace konfiguracja w wystąpieniach platformy DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu pakietu przejdź z powrotem do pulpitu nawigacyjnego dynaTrace. Możesz poznać różne metryki użycia dla kontenerów w klastrze. 
