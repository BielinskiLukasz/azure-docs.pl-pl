---
title: Dowiedz się więcej o opcjach pomocy technicznej platformy Azure Service Fabric | Microsoft Docs
description: Obsługiwane wersje klastra platformy Azure Service Fabric i linki do biletów pomocy technicznej dotyczącej plików
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ac36b1a538d5899613e19f157695d0bc333d9ece
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679058"
---
# <a name="azure-service-fabric-support-options"></a>Opcje pomocy technicznej platformy Azure Service Fabric

Aby zapewnić odpowiednie wsparcie dla klastrów Service Fabric, na których jest uruchomiona aplikacja, załadujemy różne opcje. W zależności od wymaganego poziomu pomocy technicznej i ważności problemu można wybrać odpowiednie opcje. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Zgłoś problemy produkcyjne lub zażądaj płatnej pomocy technicznej dla platformy Azure

W przypadku problemów z raportowaniem w klastrze Service Fabric wdrożonym na platformie Azure Otwórz bilet pomocy technicznej [w witrynie Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) lub [portalu pomocy technicznej firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Dowiedz się więcej o usługach:
 
- [Pomoc techniczna firmy Microsoft dla platformy Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Klastry działające w ramach warstwy niezawodności Bronów lub klastra z jednym węzłem będą umożliwiały uruchamianie obciążeń testowych. Jeśli występują problemy z klastrem uruchomionym w usłudze Bronze lub klastrze z jednym węzłem, zespół pomocy technicznej firmy Microsoft pomoże Ci uniknąć problemu, ale nie będzie wykonywał analizy głównej przyczyny. Aby uzyskać więcej informacji, zapoznaj się z [charakterystyką niezawodności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .
>
> Aby uzyskać więcej informacji o tym, co jest wymagane w przypadku klastra gotowego do produkcji, zapoznaj się z [listą kontrolną gotowości produkcyjnej](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Zgłoś problemy produkcyjne lub zażądaj płatnej pomocy technicznej dla autonomicznych klastrów Service Fabric

W przypadku problemów z raportowaniem w klastrze Service Fabric wdrożonym lokalnie lub w innych chmurach Otwórz bilet dla profesjonalnej pomocy technicznej w [portalu pomocy technicznej firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Dowiedz się więcej o usługach:

- [Profesjonalne wsparcie firmy Microsoft dla firm lokalnych](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Zgłoś problemy dotyczące Service Fabric platformy Azure

Skonfigurowano repozytorium GitHub na potrzeby raportowania Service Fabric problemów.  Aktywnie monitorujemy również następujące fora.

### <a name="github-repo"></a>Repozytorium GitHub 

Zgłoś problemy dotyczące Service Fabric platformy Azure w [usłudze Service-Fabric — problemy z repozytorium git](https://github.com/Azure/service-fabric-issues). To repozytorium jest przeznaczone do raportowania i śledzenia problemów z usługą Azure Service Fabric oraz do tworzenia małych żądań funkcji. **Nie należy używać tej usługi do zgłaszania problemów z witryną**.

### <a name="stackoverflow-and-msdn-forums"></a>Fora StackOverflow i MSDN

[Tag Service Fabric na StackOverflow][stackoverflow] i [forum Service Fabric w witrynie MSDN][msdn-forum] są najlepiej używane do zadawania pytań dotyczących działania platformy i sposobu wykonywania określonych zadań.

### <a name="azure-feedback-forum"></a>Forum opinii na platformie Azure

[Forum opinii na platformie Azure dla Service Fabric][uservoice-forum] jest najlepszym miejscem do przesyłania pomysłów dotyczących dużych cech dla produktu, podczas gdy przeglądamy najpopularniejsze żądania stanowią część naszego średniego planowania. Zachęcamy do Rally pomocy technicznej dla swoich sugestii w społeczności.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Wersje zapoznawcze Service Fabric — nieobsługiwane do użycia w środowisku produkcyjnym

Od czasu do czasu firma Microsoft udostępnia wersje, które mają znaczące funkcje, dla których chcemy uzyskać opinię, które są udostępniane jako wersje zapoznawcze. Te wersje zapoznawcze powinny być używane tylko do celów testowych. Klaster produkcyjny powinien mieć zawsze uruchomioną obsługiwaną, stabilną wersję Service Fabric. Wersja zapoznawcza zawsze rozpoczyna się od wersji głównej i pomocniczej o numerze 255. Na przykład jeśli zostanie wyświetlona Service Fabric wersja 255.255.5703.949, ta wersja jest używana tylko w przypadku klastrów testowych i jest w wersji zapoznawczej. Te wersje zapoznawcze są również ogłoszone na [blogu zespołu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) i zawiera szczegółowe informacje dotyczące funkcji uwzględnionych w tym obszarze.
Brak płatnych opcji pomocy technicznej dla tych wersji zapoznawczych. Skorzystaj z jednej z opcji wymienionych w sekcji [Zgłoś problemy Service Fabric platformy Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) , aby zadać pytania lub przekazać opinię.

## <a name="next-steps"></a>Następne kroki

[Obsługiwane Service Fabric wersje](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
