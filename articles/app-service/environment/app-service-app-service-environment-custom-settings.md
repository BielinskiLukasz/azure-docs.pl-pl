---
title: Ustawienia niestandardowe dla środowisk App Service Environment — Azure
description: Niestandardowe ustawienia konfiguracji dla środowisk App Service Environment
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: de68c59987a7ec1198c344cc22978ebed09c75e8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271362"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Niestandardowe ustawienia konfiguracji dla środowisk App Service Environment
## <a name="overview"></a>Omówienie
Ponieważ środowiska App Service Environment są izolowane do jednego klienta, istnieją pewne ustawienia konfiguracji, które można stosować wyłącznie do środowisk App Service Environment. W tym artykule opisano różne dostosowania specjalne, które są dostępne w przypadku środowisk App Service Environment.

Jeśli nie masz środowiska App Service Environment, zobacz, [jak utworzyć środowisko App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Dostosowania środowiska App Service Environment można przechowywać przy użyciu tablicy w nowym atrybucie **clusterSettings**. Ten atrybut można znaleźć w słowniku „Właściwości” jednostki *hostingEnvironments* usługi Azure Resource Manager.

Poniższy skrócony fragment kodu szablonu usługi Resource Manager przedstawia atrybut **clusterSettings**:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

Atrybut **clusterSettings** można uwzględnić w szablonie usługi Resource Manager, aby zaktualizować usługę App Service Environment.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Aktualizowanie usługi App Service Environment za pomocą witryny Azure Resource Explorer
Usługę App Service Environment można również aktualizować przy użyciu witryny [Azure Resource Explorer](https://resources.azure.com).  

1. W witrynie Resource Explorer przejdź do węzła dla środowiska App Service Environment (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Następnie kliknij wybrane środowisko App Service Environment, które chcesz zaktualizować.
2. W okienku po prawej stronie kliknij pozycję **Do odczytu/zapisu** na górnym pasku narzędzi, aby zezwolić na interakcyjne edytowanie w usłudze Resource Explorer.  
3. Kliknij niebieski przycisk **Edytuj**, aby umożliwić edytowanie szablonu usługi Resource Manager.
4. Przewiń okienko po prawej stronie w dół. Atrybut **clusterSettings** znajduje się na samym dole strony, gdzie można wprowadzić lub zaktualizować jego wartość.
5. Wpisz (lub skopiuj i wklej) wybraną tablicę wartości konfiguracji w obszarze atrybutu **clusterSettings**.  
6. Kliknij zielony przycisk **PUT** w górnej części okienka po prawej stronie, aby zatwierdzić zmianę środowiska App Service Environment.

Bez względu na to, jak prześlesz zmianę, jej zastosowanie potrwa około 30 minut pomnożonych przez liczbę frontonów w środowisku App Service Environment.
Jeśli na przykład środowisko App Service Environment ma cztery frontony, kończenie aktualizacji konfiguracji potrwa około dwie godziny. W trakcie wdrażania żadne inne operacje skalowania ani zmian konfiguracji nie mogą być wykonywane w środowisku App Service Environment.

## <a name="disable-tls-10"></a>Wyłączanie protokołu TLS 1.0
Klienci, w szczególności ci, którzy przeprowadzają inspekcje zgodności ze specyfikacją PCI, często pytają, jak jawnie wyłączyć protokół TLS 1.0 w swoich aplikacjach.

Protokół TLS 1.0 można wyłączyć za pośrednictwem następującego wpisu atrybutu **clusterSettings**:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Zmienianie kolejności zestawów szyfrowania protokołu TLS
Kolejne pytanie klientów dotyczy tego, czy mogą oni modyfikować listę szyfrów wynegocjowaną przez serwer. Ten cel można osiągnąć, modyfikując atrybut **clusterSettings**, jak pokazano poniżej. Listę dostępnych zestawów szyfrowania można pobrać z [tego artykułu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Jeśli dla zestawów szyfrowania zostaną skonfigurowane nieprawidłowe wartości, których nie będzie mógł rozpoznać kanał SChannel, cała komunikacja protokołu TLS z serwerem może przestać działać. W takim przypadku trzeba będzie usunąć wpis *FrontEndSSLCipherSuiteOrder* z atrybutu **clusterSettings** i przesłać zaktualizowany szablon usługi Resource Manager, aby przywrócić domyślne ustawienia zestawu szyfrowania.  Tej funkcji należy używać ostrożnie.
> 
> 

## <a name="get-started"></a>Rozpoczęcie pracy
W witrynie szablonów szybkiego startu Azure Resource Manager można znaleźć szablon z podstawową definicją umożliwiającą [tworzenie środowiska App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
