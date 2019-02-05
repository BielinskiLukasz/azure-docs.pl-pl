---
title: Wprowadzenie do usługi App Service w systemie Linux — Azure | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure App Service w systemie Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 2e75ff08acdda03c0080f49c6616274a4b031075
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903727"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Wprowadzenie do usługi Azure App Service w systemie Linux

[Azure App Service](../overview.md) to w pełni zarządzana platforma obliczeniowa zoptymalizowana pod kątem hostowania witryn i aplikacji internetowych. Klienci mogą używać usługi App Service w systemie Linux do natywnego hostowania aplikacji internetowych w systemie Linux dla obsługiwanych stosów aplikacji. W sekcji [Języki](#languages) wymieniono stosy aplikacji, które są obecnie obsługiwane.

## <a name="languages"></a>Języki

Usługa App Service w systemie Linux obsługuje szereg wbudowanych obrazów w celu zwiększenia produktywności deweloperów. Jeśli aplikacja wymaga środowiska uruchomieniowego, które nie jest obsługiwane we wbudowanych obrazach, skorzystaj z instrukcji dotyczących sposobu [tworzenia własnego obrazu platformy Docker](tutorial-custom-docker-image.md), aby wykonać wdrożenie w usłudze Web App for Containers.

| Język | Obsługiwane wersje |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1, 10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (z uruchomionym środowiskiem JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (wersja zapoznawcza) | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>Wdrożenia

* FTP
* Lokalna usługa Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Środowiska przejściowe
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) i ciągła integracja/ciągłe dostarczanie w usłudze DockerHub

## <a name="console-publishing-and-debugging"></a>Konsola, publikowanie i debugowanie

* Środowiska
* Wdrożenia
* Podstawowa konsola
* Protokół SSH

## <a name="scaling"></a>Skalowanie

* Klienci mogą skalować aplikacje internetowe w górę i w dół, zmieniając warstwę w swoim [planie usługi App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Lokalizacje

Sprawdź [Pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ograniczenia

Witryna Azure Portal wyświetla tylko te funkcje, które obecnie działają dla usługi Web App for Containers. Kiedy włączymy więcej funkcji, będą one widoczne w portalu.

Niektóre funkcje, takie jak integracja sieci wirtualnej, uwierzytelnianie w usłudze Azure Active Directory/innej firmy lub rozszerzenia lokacji usługi Kudu, nie są jeszcze dostępne. Po udostępnieniu tych funkcji zaktualizujemy naszą dokumentację i powiadomimy w blogu o zmianach.

Usługa App Service w systemie Linux jest obsługiwana tylko w planach usługi App Service [Podstawowa, Standardowa oraz Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) i nie ma warstwy [Bezpłatna lub Współdzielona](https://azure.microsoft.com/pricing/details/app-service/plans/). Nie można utworzyć usługi Web App for Containers w planie usługi App Service już hostującym usługę Web Apps w systemie innym niż Linux. 

Ponadto, ze względu na aktualne ograniczenia, nie należy umieszczać aplikacji dla systemów Windows i Linux w tej samej grupie zasobów.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie można uruchomić aplikacji lub chcesz sprawdzić rejestrowanie z aplikacji, sprawdź dzienniki platformy Docker w katalogu LogFiles. Dostęp do tego katalogu można uzyskać za pomocą witryny funkcji SCM lub za pośrednictwem protokołu FTP.
Aby rejestrować `stdout` i `stderr` z kontenera, musisz włączyć **Rejestrowanie kontenerów Docker** w obszarze **Dzienniki diagnostyczne**.

![Włączanie rejestrowania][2]

To ustawienie jest stosowane od razu. Usługa App Service wykrywa zmianę ustawienia i automatycznie ponownie uruchamia kontener.

Dostęp do witryny SCM można uzyskać za pomocą opcji **Narzędzia zaawansowane** w menu **Narzędzia programistyczne**.

![Wyświetlanie dzienników platformy Docker przy użyciu narzędzia Kudu][1]

## <a name="next-steps"></a>Następne kroki

Następujące artykuły ułatwią rozpoczęcie pracy z usługą App Service w systemie Linux w przypadku aplikacji internetowych napisanych w różnych językach:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplikacje z wieloma kontenerami](quickstart-multi-container.md)

Aby uzyskać więcej informacji na temat usługi App Service dla systemu Linux, zobacz:

* [Usługa App Service dla systemu Linux — często zadawane pytania](app-service-linux-faq.md)
* [Obsługa protokołu SSH dla usługi App Service w systemie Linux](app-service-linux-ssh-support.md)
* [Konfigurowanie środowisk przejściowych w usłudze App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie w usłudze Docker Hub](app-service-linux-ci-cd.md)

Pytania i uwagi można zamieszczać na [naszym forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
