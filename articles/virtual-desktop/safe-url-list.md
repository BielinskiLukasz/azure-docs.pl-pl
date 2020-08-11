---
title: Lista bezpiecznych adresów URL pulpitu wirtualnego systemu Windows — Azure
description: Lista adresów URL, które należy odblokować, aby upewnić się, że wdrożenie pulpitu wirtualnego systemu Windows działa zgodnie z oczekiwaniami.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37fb5ccf121fed6e772dc1cd3dcba2345d62c66f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067209"
---
# <a name="safe-url-list"></a>Lista bezpiecznych adresów URL

Należy odblokować niektóre adresy URL, aby wdrożenie pulpitu wirtualnego systemu Windows działało prawidłowo. W tym artykule wymieniono te adresy URL, dzięki czemu wiadomo, które z nich są bezpieczne.

## <a name="virtual-machines"></a>Maszyny wirtualne

Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*. wvd.microsoft.com|443|Ruch usługi|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aktualizacje stosu agenta i SXS|AzureCloud|
|*.core.windows.net|443|Ruch agenta|AzureCloud|
|*.servicebus.windows.net|443|Ruch agenta|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Ruch agenta|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktywacja systemu Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Obsługa Azure Portal|AzureCloud|
| 169.254.169.254 | 80 | [Punkt końcowy usługi metadanych wystąpienia platformy Azure](../virtual-machines/windows/instance-metadata-service.md) | Nie dotyczy |

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows obsługuje teraz tag FQDN. Aby uzyskać więcej informacji, zobacz [Korzystanie z zapory platformy Azure do ochrony wdrożeń pulpitów wirtualnych systemu Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Zalecamy używanie tagów FQDN lub tagów usług zamiast adresów URL, aby zapobiec problemom z usługą. Wymienione adresy URL i Tagi odnoszą się tylko do witryn i zasobów pulpitu wirtualnego systemu Windows. Nie obejmują one adresów URL dla innych usług, takich jak Azure Active Directory.

W poniższej tabeli przedstawiono opcjonalne adresy URL, do których maszyny wirtualne platformy Azure mogą mieć dostęp:

|Adres|Wychodzący port TCP|Przeznaczenie|Tag usługi|
|---|---|---|---|
|*.microsoftonline.com|443|Uwierzytelnianie w usługach online firmy Microsoft|Brak|
|*. events.data.microsoft.com|443|Usługa telemetrii|Brak|
|www.msftconnecttest.com|443|Wykrywa, czy system operacyjny jest połączony z Internetem|Brak|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Brak|
|login.windows.net|443|Zaloguj się do usług Microsoft Online Services, Microsoft 365|Brak|
|*. sfx.ms|443|Aktualizacje oprogramowania klienckiego usługi OneDrive|Brak|
|*. digicert.com|443|Sprawdzanie odwołania certyfikatu|Brak|

>[!NOTE]
>Pulpit wirtualny systemu Windows nie ma obecnie listy zakresów adresów IP, które można odblokować, aby zezwolić na ruch sieciowy. Obecnie obsługujemy odblokowywanie określonych adresów URL.
>
>Aby uzyskać listę bezpiecznych adresów URL związanych z pakietem Office, w tym wymaganych adresów URL związanych z Azure Active Directory, zobacz [adresy URL i zakresy adresów IP usługi office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Należy użyć symbolu wieloznacznego (*) dla adresów URL związanych z ruchem usługi. Jeśli wolisz używać * w przypadku ruchu związanego z agentem, Oto jak znaleźć adresy URL bez symboli wieloznacznych:
>
>1. Zarejestruj maszyny wirtualne w puli hostów systemu Windows Virtual Desktop.
>2. Otwórz **Podgląd zdarzeń**, a następnie przejdź do pozycji **Dzienniki systemu Windows**  >  **Application**  >  **WVD-Agent** i Wyszukaj zdarzenie o identyfikatorze 3701.
>3. Dozwolonych adresy URL Znalezione w obszarze zdarzenia o IDENTYFIKATORze 3701. Adresy URL pod IDENTYFIKATORem zdarzenia 3701 są specyficzne dla regionu. Należy powtórzyć proces odblokowywania przy użyciu odpowiednich adresów URL dla każdego regionu, w którym mają zostać wdrożone maszyny wirtualne.

## <a name="remote-desktop-clients"></a>Klienci usług pulpitu zdalnego

Wszyscy klienci korzystający z Pulpit zdalny muszą mieć dostęp do następujących adresów URL:

|Adres|Wychodzący port TCP|Przeznaczenie|Klienci|
|---|---|---|---|
|*. wvd.microsoft.com|443|Ruch usługi|Wszystko|
|*.servicebus.windows.net|443|Rozwiązywanie problemów z danymi|Wszystko|
|go.microsoft.com|443|Linki fwlink firmy Microsoft|Wszystko|
|aka.ms|443|Shortener URL firmy Microsoft|Wszystko|
|docs.microsoft.com|443|Dokumentacja|Wszystko|
|privacy.microsoft.com|443|Oświadczenie o ochronie prywatności|Wszystko|
|query.prod.cms.rt.microsoft.com|443|Aktualizacje klienta|Pulpit systemu Windows|

>[!IMPORTANT]
>Otwieranie tych adresów URL jest niezbędne dla niezawodnego środowiska klienta. Blokowanie dostępu do tych adresów URL nie jest obsługiwane i wpłynie na funkcjonalność usługi.
>
>Te adresy URL odnoszą się tylko do lokacji i zasobów klienta. Ta lista nie zawiera adresów URL dla innych usług, takich jak Azure Active Directory. Adresy URL Azure Active Directory można znaleźć pod IDENTYFIKATORem 56 dla [adresów URL i zakresów adresów IP pakietu Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
