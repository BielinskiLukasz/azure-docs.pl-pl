---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — CloudSimple konserwacja i aktualizacje
description: Opisuje proces usługi CloudSimple na potrzeby zaplanowanej konserwacji i aktualizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6eeecbecc89995c25e687cc6808ed3b0c5dc5c
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816221"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple konserwacja i aktualizacje

Środowisko chmury prywatnej zostało zaprojektowane tak, aby nie zawierało single point of failure:

* Klastry ESXi są skonfigurowane z wysoką dostępnością vSphere. Rozmiary klastrów mają co najmniej jeden węzeł zapasowy na potrzeby odporności.
* Nadmiarowy magazyn podstawowy jest dostarczany przez sieci vSAN, który wymaga co najmniej trzech węzłów w celu zapewnienia ochrony przed pojedynczym awarią. Sieci vSAN można skonfigurować w celu zapewnienia większej odporności dla większych klastrów.
* Maszyny wirtualne vCenter, PSC i NSX Manager są skonfigurowane za pomocą zasad magazynu RAID-10 w celu ochrony przed awariami magazynu. Maszyny wirtualne są chronione przed awariami węzłów/sieci przez vSphere HA.
* Hosty ESXi mają nadmiarowe wentylatory i karty sieciowe.
* Przełączniki TOR i spin są skonfigurowane w parach HA w celu zapewnienia odporności.

CloudSimple stale monitoruje następujące maszyny wirtualne pod kątem czasu i dostępności, a także zapewnia dostępność umowy SLA:

* Hosty ESXi
* vCenter
* PSC
* NSX Manager

CloudSimple również stale monitoruje następujące kwestie dotyczące niepowodzeń:

* Dyski twarde
* Fizyczne porty kart sieciowych
* Serwery
* Wachlarz
* Awansowan
* Przełączniki
* Przełącz porty

Jeśli dysk lub węzeł ulegnie awarii, nowy węzeł zostanie automatycznie dodany do klastra programu VMware, którego dotyczy usterka, w celu natychmiastowego przywrócenia kondycji.

CloudSimple wykonuje kopię zapasową, utrzymuje i aktualizuje te elementy VMware w chmurach prywatnych:

* ESXi
* Usługi platformy vCenter
* Kontroler
* Sieci vSAN
* NSX

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

CloudSimple Backup obejmuje:

* Nocne przyrostowe kopie zapasowe reguł vCenter, PSC i DVS.
* Używanie natywnych interfejsów API programu vCenter do tworzenia kopii zapasowych składników w warstwie aplikacji.
* Automatyczna kopia zapasowa przed aktualizacją lub uaktualnieniem oprogramowania do zarządzania VMware.
* Szyfrowanie danych w źródle przez program vCenter przed transferem danych przez szyfrowany kanał TLS 1.2 do platformy Azure. Dane są przechowywane w obiekcie blob platformy Azure, w którym są replikowane między regionami.

Możesz zażądać przywrócenia, otwierając [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Konserwacja

CloudSimple wykonuje kilka typów planowanej konserwacji.

### <a name="backendinternal-maintenance"></a>Zaplecze/konserwacja wewnętrzna

Ta konserwacja zazwyczaj polega na ponownym konfigurowaniu zasobów fizycznych lub instalowaniu poprawek oprogramowania. Nie ma to wpływu na normalne użycie zasobów, które są w trakcie obsługi. W przypadku nadmiarowych kart sieciowych w każdym stojaku fizycznym nie ma to wpływu na normalny ruch sieciowy i operacje chmury prywatnej. Możesz zauważyć, że wpływ na wydajność jest możliwy tylko wtedy, gdy organizacja oczekuje, że w czasie konserwacji ma być używana pełna nadmiarowa przepustowość.

### <a name="cloudsimple-portal-maintenance"></a>Obsługa portalu CloudSimple

Niektóre ograniczone przestojy usługi są wymagane, gdy jest aktualizowana płaszczyzna lub infrastruktura kontroli CloudSimple. Obecnie interwały konserwacji mogą być tak częste jak raz miesięcznie. Częstotliwość powinna odrzucać się w czasie. CloudSimple zapewnia powiadomienia o konserwacji portalu i utrzymuje interwał tak krótki jak to możliwe. W czasie konserwacji portalu następujące usługi kontynuują działanie bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

### <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Czasami konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware.  Obecnie te interwały mogą wystąpić co 1-2 miesięcy, ale częstotliwość powinna odrzucać się w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego użycia usług CloudSimple. W czasie konserwacji programu VMware następujące usługi nadal działają bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez oprogramowanie VMware.
* **Aktualizacje**. Zmiana wersji pomocniczej składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

CloudSimple testuje krytyczną poprawkę zabezpieczeń, gdy tylko staną się dostępne z programu VMware. Zgodnie z umową SLA CloudSimple wprowadza poprawki zabezpieczeń do środowisk chmury prywatnej w ciągu tygodnia.

CloudSimple dostarcza kwartalne aktualizacje dla składników oprogramowania VMware. Po udostępnieniu nowej wersji głównej oprogramowania VMware CloudSimple współpracuje z klientami w celu koordynowania odpowiedniego okna obsługi w celu uaktualnienia.

## <a name="next-steps"></a>Następne kroki

[Tworzenie kopii zapasowych maszyn wirtualnych obciążeń przy użyciu Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).