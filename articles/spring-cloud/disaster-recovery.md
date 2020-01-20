---
title: Azure sprężynowe w chmurze — odzyskiwanie po awarii | Microsoft Docs
description: Dowiedz się, jak chronić aplikację w chmurze ze sprężyną przed awarią regionalną
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279150"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Odzyskiwanie po awarii w chmurze z platformy Azure

W tym artykule opisano niektóre strategie, których można użyć do ochrony Twoich aplikacji w chmurze platformy Azure przed wykorzystaniem przestojów.  W każdym regionie lub centrum danych może wystąpić przestój spowodowany przez regionalne awarie, ale staranne planowanie może ograniczyć wpływ na klientów.

## <a name="plan-your-application-deployment"></a>Planowanie wdrożenia aplikacji

Aplikacje chmurowe platformy Azure są uruchamiane w określonym regionie.  Platforma Azure działa w wielu lokalizacjach geograficznych na całym świecie. Lokalizacja geograficzna platformy Azure to zdefiniowany obszar świata, który zawiera co najmniej jeden region świadczenia usługi Azure. Region świadczenia usługi Azure jest obszarem geograficznym zawierającym co najmniej jedno centrum danych.  Każdy region platformy Azure jest sparowany z innym regionem w tej samej lokalizacji geograficznej, tworząc parę regionalną. Platforma Azure serializacji aktualizacje platformy (planowana konserwacja) między parami regionalnymi, co zapewnia, że tylko jeden region w każdej parze jest aktualizowany jednocześnie. W przypadku awarii mającej wpływ na wiele regionów, co najmniej jeden region w każdej parze będzie miał priorytet na potrzeby odzyskiwania.

Zapewnienie wysokiej dostępności i ochrony przed awariami wymaga wdrożenia aplikacji w chmurze ze sprężyną w wielu regionach.  Platforma Azure udostępnia listę [par regionów](../best-practices-availability-paired-regions.md) , aby można było zaplanować wdrożenia w chmurze ze sprężyną regionalną.  Zaleca się, aby wziąć pod uwagę trzy kluczowe czynniki podczas projektowania architektury mikrousług: dostępność regionów, sparowane regiony platformy Azure i dostępność usług.

*  Dostępność regionu: Wybierz obszar geograficzny blisko użytkowników, aby zminimalizować czas opóźnienia i transmisji w sieci.
*  Sparowane regiony platformy Azure: Wybierz sparowane regiony w wybranym obszarze geograficznym, aby zapewnić skoordynowane aktualizacje platformy i priorytetowe działania odzyskiwania w razie potrzeby.
*  Dostępność usługi: należy zdecydować, czy sparowane regiony powinny uruchamiać gorącą, gorącą, gorącą/zimną.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Kierowanie ruchu przy użyciu usługi Azure Traffic Manager

Usługa [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) zapewnia Równoważenie obciążenia ruchem opartym na systemie DNS i umożliwia dystrybucję ruchu sieciowego między wieloma regionami.  Użyj usługi Azure Traffic Manager, aby skierować klientów do najbliższego wystąpienia usługi w chmurze ze sprężyną na platformie Azure.  Aby uzyskać najlepszą wydajność i nadmiarowość, należy skierować cały ruch aplikacji za pośrednictwem platformy Azure Traffic Manager przed wysłaniem go do usługi w chmurze Azure wiosennej.

Jeśli masz aplikacje chmurowe platformy Azure w wielu regionach, Użyj usługi Azure Traffic Manager, aby kontrolować przepływ ruchu do aplikacji w poszczególnych regionach.  Zdefiniuj punkt końcowy Traffic Manager platformy Azure dla każdej usługi korzystającej z adresu IP usługi. Klienci powinni łączyć się z nazwą DNS usługi Azure Traffic Manager, wskazując na usługę Azure wiosennej w chmurze.  Usługa Azure Traffic Manager równoważy obciążenie ruchu między zdefiniowanymi punktami końcowymi.  Jeśli awaria zostanie wyświetlona w centrum danych, usługa Azure Traffic Manager przekieruje ruch z tego regionu do pary, zapewniając ciągłość usługi.