---
title: Rozwiązywanie problemów z powrotu po awarii do środowiska lokalnego podczas odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozwiązywać problemy podczas powrotu po awarii i ponownego włączania ochrony podczas odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: a0707ad356e51a9e4d4f73c54085fe9736bb0752
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885298"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Rozwiązywanie problemów z błędami odnajdywania vCenter

Ten artykuł pomaga rozwiązywać problemy, które występują ze względu na błędy wykrywania programu VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Wartości nieliczbowe we właściwości maxSnapShots

W wersjach przed 9,20 vCenter odłącza podczas pobierania wartości nieliczbowe właściwość `snapshot.maxSnapShots` właściwości na maszynie Wirtualnej.

Ten problem jest identyfikowana przez identyfikator błędu 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Aby rozwiązać ten problem:

- Zidentyfikuj maszynę Wirtualną, a następnie ustaw wartość na wartość numeryczną (maszyny Wirtualnej, Edytuj ustawienia w programie vCenter).

Lub

- Uaktualnij serwer konfiguracji do wersji 9.20 lub nowszej.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemy z konfiguracją serwera proxy dla połączenia vCenter

vCenter odnajdywania honoruje systemowe ustawienia proxy domyślne skonfigurowany przez użytkownika systemu. Usługa agenta DRA honoruje ustawienia serwera proxy, dostarczone przez użytkownika podczas instalacji serwera konfiguracji za pomocą ujednoliconej konfiguracji Instalatora lub szablonu OVA. 

Ogólnie rzecz biorąc serwer proxy jest używany do komunikacji z sieci publicznych. takich jak komunikacja z platformą Azure. Jeśli skonfigurowano serwer proxy, a vCenter znajduje się w środowisku lokalnym, nie będzie mogła nawiązać połączenia z agenta DRA.

Po ten problem występuje, wykonywane są następujące sytuacje:

- Serwer vCenter <vCenter> jest nieosiągalny z powodu błędu: Serwer zdalny zwrócił błąd: Serwer (503) jest niedostępny
- Serwer vCenter <vCenter> jest nieosiągalny z powodu błędu: Serwer zdalny zwrócił błąd: Nie można nawiązać połączenia z serwerem zdalnym.
- Nie można połączyć się z serwerem vCenter/ESXi.

Aby rozwiązać ten problem:

Pobierz [narzędzia PsExec](https://aka.ms/PsExec). 

Narzędzie PsExec dostęp do kontekstu użytkownika systemu i sprawdzenie, czy adres serwera proxy jest skonfigurowany. Następnie można dodać vCenter do listy pomijania, korzystając z poniższych procedur.

Konfigurowanie serwera proxy odnajdywania:

1. Otwieranie programu Internet Explorer w kontekście użytkownika systemu za pomocą narzędzia PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć vCenter adresu IP.
3. Uruchom ponownie usługę tmanssvc.

Konfigurowanie serwera proxy DRA:

1. Otwórz wiersz polecenia, a następnie otwórz folder dostawcy Microsoft Azure Site Recovery.
 
    **Dostawca CD C:\Program Files\Microsoft Azure Site Recovery**

3. W wierszu polecenia Uruchom następujące polecenie.
   
   **DRCONFIGURATOR. Plik EXE / configure /AddBypassUrls [adres IP/nazwę FQDN programu vCenter Server podane w czasie, Dodawanie serwera vCenter]**

4. Uruchom ponownie usługę agenta DRA dostawcy.

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
