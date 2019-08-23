---
title: Funkcje zabezpieczeń używane z maszynami wirtualnymi platformy Azure — zabezpieczenia platformy Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z usługą Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 7b33484084b4ada5aeaf89eb90167658ade15ad8
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899781"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń usługi Azure Virtual Machines
Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z maszynami wirtualnymi.

Możesz użyć usługi Azure Virtual Machines do wdrożenia szerokiego zakresu rozwiązań obliczeniowych w sposób Agile. Usługa obsługuje systemy Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Azure BizTalk Services. Pozwala to na wdrożenie dowolnego obciążenia i dowolnego języka w prawie każdym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Możesz tworzyć i wdrażać aplikacje z gwarancją, że dane są chronione i bezpieczne w wysoce zabezpieczonych centrach danych.

Na platformie Azure można tworzyć rozwiązania zgodne z ulepszonymi zabezpieczeniami, które:

* Chroń maszyny wirtualne przed wirusami i złośliwym oprogramowaniem.
* Szyfruj poufne dane.
* Bezpieczny ruch sieciowy.
* Identyfikowanie i wykrywanie zagrożeń.
* Spełnianie wymagań dotyczących zgodności.  

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Na platformie Azure możesz używać oprogramowania chroniącego przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky pomogą. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami.

Firma Microsoft chroniąca przed złośliwym kodem Cloud Services i Virtual Machines to funkcja ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania.  Program Microsoft chroniący przed złośliwym kodem dla systemu Azure udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w swoich systemach platformy Azure.

Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla systemu Azure to rozwiązanie o pojedynczej agencie dla aplikacji i środowisk dzierżawców. Jest ona przeznaczona do uruchamiania w tle bez interwencji człowieka. Ochronę można wdrożyć na podstawie potrzeb obciążeń aplikacji z podstawową, bezpieczną lub zaawansowaną konfiguracją niestandardową, w tym z monitorowaniem złośliwego oprogramowania.

Po wdrożeniu i włączeniu ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure dostępne są następujące podstawowe funkcje:

* **Ochrona w czasie rzeczywistym**: Monitoruje aktywność w Cloud Services i na Virtual Machines w celu wykrywania i blokowania wykonywania złośliwego oprogramowania.
* **Zaplanowane skanowanie**: Program okresowo przeprowadza skanowanie w celu wykrywania złośliwego oprogramowania, w tym aktywnie działających programów.
* **Korygowanie złośliwego oprogramowania**: Program automatycznie podejmuje akcję dotyczącą wykrytego złośliwego oprogramowania, takiego jak usuwanie lub poddawania kwarantannie złośliwych plików oraz oczyszczanie złośliwych wpisów rejestru.
* **Aktualizacje sygnatur**: Program automatycznie instaluje najnowsze sygnatury ochrony (definicje wirusów), aby zapewnić aktualność ochrony przed ustaloną częstotliwością.
* **Aktualizacje aparatu ochrony przed złośliwym kodem**: Automatycznie aktualizuje aparat ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure.
* **Aktualizacje platformy chroniącej przed złośliwym kodem**: Automatycznie aktualizuje platformę Microsoft chroniącą przed złośliwym kodem dla platformy Azure.
* **Aktywna ochrona**: Raportuje metadane telemetrii na platformie Azure dotyczące wykrytych zagrożeń i podejrzanych zasobów, aby zapewnić szybką odpowiedź. Włącza przekazywanie synchronicznych podpisów w czasie rzeczywistym za pomocą systemu Microsoft Active Protection System (MAPS).
* **Przykłady raportowania**: Dostarcza i raportuje przykłady do usługi Microsoft chroniącej przed złośliwym kodem dla platformy Azure, aby ułatwić udoskonalanie usługi i rozwiązywanie problemów.
* **Wykluczenia**: Umożliwia administratorom aplikacji i usługi Konfigurowanie określonych plików, procesów i dysków w celu wykluczenia ich z ochrony i skanowania pod kątem wydajności i innych przyczyn.
* **Zbieranie zdarzeń ochrony przed złośliwym kodem**: Rejestruje kondycję usługi chroniącej przed złośliwym kodem, podejrzane działania i akcje korygowania wykonywane w dzienniku zdarzeń systemu operacyjnego i zbiera je na koncie usługi Azure Storage.

Dowiedz się więcej o oprogramowaniu chroniącym przed złośliwym kodem, aby pomóc w ochronie maszyn wirtualnych:

* [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](antimalware.md)
* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować Trend Micro głębokiego zabezpieczenia jako usługi na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak zainstalować i skonfigurować Endpoint Protection firmy Symantec na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Rozwiązania zabezpieczeń w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

W celu zapewnienia jeszcze bardziej zaawansowanej ochrony należy rozważyć użycie funkcji [zaawansowanej ochrony przed zagrożeniami w usłudze Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Usługa Windows Defender ATP zapewnia następujące korzyści:

* [Zmniejszenie obszaru ataków](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrona nowej generacji](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection i odpowiedź](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Zautomatyzowane badanie i korygowanie](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Wynik zabezpieczony](/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Zaawansowany łowiectwo](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Zarządzanie i interfejsy API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Ochrona przed zagrożeniami firmy Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Więcej informacji:

* [Wprowadzenie do WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Przegląd możliwości WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Zwiększenie bezpieczeństwa klucza może zwiększyć ochronę szyfrowania i uwierzytelniania. Można uprościć zarządzanie i zabezpieczanie kluczowych kluczy tajnych i kluczy, przechowując je w Azure Key Vault.

Usługa Key Vault oferuje możliwość przechowywania kluczy w sprzętowych modułach zabezpieczeń z certyfikatami poświadczającymi zgodność ze standardami FIPS 140-2 (poziom 2). Klucze szyfrowania SQL Server na potrzeby tworzenia kopii zapasowej lub [przezroczystego szyfrowania danych](https://msdn.microsoft.com/library/bb934049.aspx) mogą być przechowywane w Key Vault z użyciem dowolnych kluczy lub wpisów tajnych aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za poorednictwem [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blog Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysku maszyny wirtualnej

Azure Disk Encryption to nowa funkcja szyfrowania dysków maszyn wirtualnych z systemami Windows i Linux. Azure Disk Encryption używa standardowej funkcji [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych.

Rozwiązanie jest zintegrowane z Azure Key Vault, które ułatwiają sterowanie kluczami szyfrowania dysków i wpisami tajnymi w ramach subskrypcji magazynu kluczy oraz zarządzanie nimi. Gwarantuje to, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage.

Więcej informacji:

* [Azure Disk Encryption maszyn wirtualnych IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Szybki start: Szyfrowanie maszyny wirtualnej z systemem Windows IaaS przy użyciu Azure PowerShell](../azure-disk-encryption-linux-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej

Azure Backup to skalowalne rozwiązanie, które pomaga chronić dane aplikacji bez konieczności inwestowania kapitału i minimalnych kosztów operacyjnych. Błędy aplikacji mogą powodować uszkodzenia danych, a błędy użytkowników — usterki aplikacji. W przypadku Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

Więcej informacji:

* [Co to jest Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Często zadawane pytania dotyczące usługi Azure Backup](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Istotną częścią strategii BCDR organizacji jest ustalenie, jak utrzymywać obciążenia i aplikacje firmowe w przypadku wystąpienia planowanych i nieplanowanych przestojów. Azure Site Recovery pomaga organizować replikację, pracę w trybie failover oraz odzyskiwanie obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja główna ulegnie awarii.

Site Recovery:

* **Upraszcza strategię BCDR**: Site Recovery ułatwia obsługę replikacji, trybu failover i odzyskiwania wielu obciążeń i aplikacji firmowych z jednej lokalizacji. Site Recovery organizuje replikację i pracę w trybie failover, ale nie przechwytuje danych aplikacji ani nie zawiera żadnych informacji o nim.
* **Zapewnia elastyczną replikację**: Za pomocą Site Recovery można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszynach wirtualnych VMware oraz serwerach fizycznych z systemem Windows/Linux.
* **Obsługuje tryb failover i odzyskiwanie**: Site Recovery zapewnia test pracy w trybie failover w celu obsługi testów odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po przejściu w tryb failover można wrócić do lokacji głównych. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Eliminuje dodatkowe centra**danych: Można przeprowadzić replikację do dodatkowej lokacji lokalnej lub do platformy Azure. Korzystanie z platformy Azure jako miejsca docelowego do odzyskiwania po awarii eliminuje koszt i złożoność utrzymywania lokacji dodatkowej. Zreplikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR**: Partnerzy Site Recovery z funkcjami BCDR innych aplikacji. Na przykład można użyć Site Recovery, aby pomóc w ochronie SQL Server zaplecza obciążeń firmowych. Obejmuje to natywną obsługę usługi SQL Server, która jest zawsze włączona, aby zarządzać trybem failover grup dostępności.

Więcej informacji:

* [Co to jest Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Jak działa Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Jakie obciążenia są chronione przez Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Sieć wirtualna

Maszyny wirtualne muszą mieć łączność sieciową. Aby zapewnić obsługę tego wymagania, platforma Azure wymaga, aby maszyny wirtualne były połączone z siecią wirtualną platformy Azure.

Usługa Azure Virtual Network to konstrukcja logiczna oparta na fizycznej sieci szkieletowej platformy Azure. Każda logiczna Sieć wirtualna platformy Azure jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępny dla innych klientów Microsoft Azure.

Więcej informacji:

* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
* [Omówienie usługi Virtual Network](/azure/virtual-network/virtual-networks-overview)
* [Funkcje sieciowe i partnerstwo dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie

Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie. Security Center zapewnia większą widoczność i kontrolę nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i działać z szerokim ekosystemem rozwiązań zabezpieczających.

Security Center pomaga zoptymalizować i monitorować zabezpieczenia maszyn wirtualnych, wykonując następujące działania:

* Zapewnianie zaleceń dotyczących [zabezpieczeń](/azure/security-center/security-center-recommendations) dla maszyn wirtualnych. Przykładowe zalecenia: Zastosuj aktualizacje systemu, skonfiguruj punkty końcowe list kontroli dostępu, Włącz ochronę przed złośliwym kodem, Włącz grupy zabezpieczeń sieci i Zastosuj szyfrowanie dysków.
* Monitorowanie stanu maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](/azure/security-center/security-center-intro)
* [Azure Security Center często zadawane pytania](/azure/security-center/security-center-faq)
* [Azure Security Center planowanie i operacje](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Zgodność

Usługa Azure Virtual Machines ma certyfikat dla FISMA, FedRAMP, HIPAA, PCI DSS poziomu 1 i innych programów do oceny zgodności. Ten certyfikat ułatwia korzystanie z własnych aplikacji platformy Azure w celu spełnienia wymagań dotyczących zgodności oraz dla Twojej firmy.

Więcej informacji:

* [Centrum zaufania firmy Microsoft: Przepisów](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufana chmura: Microsoft Azure zabezpieczenia, prywatność i zgodność](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Przetwarzanie poufne

Chociaż dane poufne nie są technicznie częścią zabezpieczeń maszyn wirtualnych, temat zabezpieczeń maszyn wirtualnych należy do podmiotu zabezpieczeń "COMPUTE" wyższego poziomu. Przetwarzanie poufne należy do kategorii zabezpieczeń "obliczeniowe".

Ochrona danych poufnych gwarantuje, że w przypadku, gdy dane są "jasne", co jest wymagane do wydajnego przetwarzania, dane są chronione wewnątrz zaufanego środowiska https://en.wikipedia.org/wiki/Trusted_execution_environment wykonawczego (tee-znanego również jako enklawy), przykładem, który pokazano na poniższej ilustracji. .  

TEEs upewnij się, że nie ma sposobu wyświetlania danych lub wykonywania operacji wewnątrz poza programem, nawet z debugerem. Nawet zapewniają, że dostęp do danych jest dozwolony tylko w autoryzowanym kodzie. Jeśli kod zostanie zmieniony lub naruszony, operacje są odrzucane i środowisko wyłączone. TEE wymusza te zabezpieczenia w czasie wykonywania kodu.

Więcej informacji:

* [Wprowadzenie do rozwiązania do przetwarzania poufnego platformy Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Dane poufne platformy Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  
