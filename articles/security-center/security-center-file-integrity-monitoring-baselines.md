---
title: Porównaj linie bazowe z monitorowaniem integralności plików w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak porównać linie bazowe z monitorowaniem integralności plików w Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: afc03baa71f17deb0b923f483fde214a86c5e9b4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296463"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porównaj linie bazowe przy użyciu funkcji monitorowania integralności plików (FIM)

Program do monitorowania integralności plików (FIM) informuje o zmianach w obszarach poufnych zasobów, dzięki czemu można zbadać i rozwiązać nieautoryzowane działanie. Program FIM monitoruje pliki systemu Windows, rejestry systemu Windows i pliki Linux.

W tym temacie wyjaśniono, jak włączyć program FIM dla plików i rejestrów. Aby uzyskać więcej informacji na temat programu FIM, zobacz [monitorowanie integralności plików w Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Dlaczego warto korzystać z programu FIM?

System operacyjny, aplikacje i skojarzone konfiguracje sterują zachowaniem i stanem zabezpieczeń zasobów. W związku z tym, osoby atakujące są przeznaczone dla plików kontrolujących Twoje zasoby, aby overtake system operacyjny zasobu i/lub wykonywać działania bez wykrycia.

W rzeczywistości wiele standardów zgodności z przepisami, takich jak PCI-DSS & ISO 17799, wymaga zaimplementowania kontrolek FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Włącz wbudowane testy cykliczne rejestru

Domyślne ustawienia gałęzi rejestru programu FIM zapewniają wygodny sposób monitorowania zmian cyklicznych w ramach wspólnych obszarów zabezpieczeń.  Na przykład atakującej może skonfigurować skrypt do wykonania w kontekście LOCAL_SYSTEM przez skonfigurowanie wykonania przy uruchamianiu lub zamknięciu.  Aby monitorować zmiany tego typu, Włącz wbudowane sprawdzanie.  

![Rejestr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Testy cykliczne mają zastosowanie tylko do zalecanych gałęzi zabezpieczeń, a nie do niestandardowych ścieżek rejestru.  

## <a name="adding-a-custom-registry-check"></a>Dodawanie niestandardowego sprawdzania rejestru

Linie bazowe programu FIM zaczynają się od określenia cech znanego dobrego stanu dla systemu operacyjnego i aplikacji pomocniczej.  Na potrzeby tego przykładu będziemy skupić się na konfiguracjach zasad haseł dla systemu Windows Server 2008 i nowszych.


|Policy Name (Nazwa zasad)                 | Ustawienie rejestru|
|---------------------------------------|-------------|
|Kontroler domeny: Odrzuć zmiany hasła konta komputera| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Członek domeny: Cyfrowe szyfrowanie lub podpisywanie bezpiecznych danych kanału (zawsze)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Członek domeny: Szyfruj cyfrowo dane bezpiecznego kanału (jeśli to możliwe)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Członek domeny: Cyfrowe podpisywanie danych bezpiecznego kanału (jeśli to możliwe)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Członek domeny: Wyłącz zmiany hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Członek domeny: Maksymalny wiek hasła konta komputera|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Członek domeny: Wymagaj silnego klucza sesji (Windows 2000 lub nowszego)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpieczenia sieci: Ogranicz uwierzytelnianie NTLM:  Uwierzytelnianie NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpieczenia sieci: Ogranicz uwierzytelnianie NTLM: Dodaj wyjątki serwera w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpieczenia sieci: Ogranicz uwierzytelnianie NTLM: Inspekcja uwierzytelniania NTLM w tej domenie|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Aby dowiedzieć się więcej na temat ustawień rejestru obsługiwanych przez różne wersje systemu operacyjnego, zapoznaj się z [arkuszem kalkulacyjnym informacje o ustawieniach zasady grupy](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Aby skonfigurować FIM do monitorowania planów bazowych rejestru:*

1. W oknie dialogowym **Dodawanie rejestru systemu Windows dla Change Tracking** w polu tekstowym **klucz rejestru systemu Windows** wprowadź klucz rejestru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Włączanie programu FIM w rejestrze](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Śledzenie zmian w plikach systemu Windows

1. W oknie **Dodawanie pliku systemu Windows dla Change Tracking** w polu tekstowym **wprowadź ścieżkę** wprowadź folder zawierający pliki, które chcesz śledzić. W przykładzie na poniższej ilustracji znajduje się **aplikacja sieci Web firmy Contoso** w D:\ dysk w strukturze folderów **ContosWebApp** .  
1. Utwórz niestandardowy wpis w pliku systemu Windows, podając nazwę klasy ustawień, włączając rekursję i określając folder Top z sufiksem symboli wieloznacznych (*).

    ![Włącz program FIM na pliku](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Pobieranie zmian danych

Dane monitorowania integralności plików znajdują się w zestawie tabel Log Analytics/Zmianakonfiguracji platformy Azure.  

 1. Ustaw zakres czasu, aby pobrać podsumowanie zmian według zasobu.
W poniższym przykładzie pobieramy wszystkie zmiany w ciągu ostatnich czternastu dni w kategorii rejestru i plików:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Aby wyświetlić szczegóły zmian w rejestrze:

    1. Usuń **pliki** z klauzuli **WHERE** , 
    1. Usuń wiersz podsumowania i zastąp go klauzulą porządkowania:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Raporty można eksportować do pliku CSV w celu archiwizacji i/lub kanału do raportu Power BIowego.  

![Dane programu FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)