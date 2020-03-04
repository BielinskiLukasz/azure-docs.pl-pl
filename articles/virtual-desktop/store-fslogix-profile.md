---
title: Storage profile profil FSLogix Windows Virtual Desktop — Azure
description: Opcje przechowywania profilu FSLogix pulpitu wirtualnego systemu Windows w usłudze Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: 165e87b47deca2b7f175ec07b5c615ac25db0dd2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251747"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opcje magazynu dla kontenerów profilów FSLogix w programie Virtual Desktop systemu Windows

Platforma Azure oferuje wiele rozwiązań magazynu, których można użyć do przechowywania kontenera profilu FSLogix. W tym artykule porównano rozwiązania magazynu oferowane przez platformę Azure dla kontenerów profilów użytkowników FSLogix pulpitu wirtualnego systemu Windows.

Pulpit wirtualny systemu Windows oferuje kontenery profilu FSLogix jako zalecane rozwiązanie profilu użytkownika. FSLogix zaprojektowano pod kątem roamingu profilów w zdalnych środowiskach obliczeniowych, takich jak pulpit wirtualny systemu Windows. Podczas logowania ten kontener jest dynamicznie dołączany do środowiska obliczeniowego przy użyciu natywnie obsługiwanego wirtualnego dysku twardego (VHD) oraz wirtualnego dysku twardego funkcji Hyper-V (VHDX). Profil użytkownika jest natychmiast dostępny i pojawia się w systemie tak samo jak natywny profil użytkownika.

W poniższych tabelach porównano oferty magazynów usługi Azure Storage dla systemu Windows Virtual Desktop FSLogix profile użytkowników kontenera.

## <a name="azure-platform-details"></a>Szczegóły platformy Azure

|Funkcje|Azure Files|Azure NetApp Files|Bezpośrednie miejsca do magazynowania|
|--------|-----------|------------------|---------------------|
|Przypadek użycia|Zastosowania ogólne|Niezwykle wydajna lub migracja z NetApp lokalnego|Wiele platform|
|Usługa platformy|Tak, rozwiązanie platformy Azure — natywne|Tak, rozwiązanie platformy Azure — natywne|Nie, samozarządzane|
|Dostępność regionalna|Wszystkie regiony|[Wybierz regiony](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Wszystkie regiony|
|Nadmiarowość|Lokalnie nadmiarowy/strefowo nadmiarowy/Geograficznie nadmiarowy|Lokalnie nadmiarowy|Lokalnie nadmiarowy/strefowo nadmiarowy/Geograficznie nadmiarowy|
|Warstwy i wydajność|Standard<br>Premium<br>Maksymalnie Maksymalna liczba operacji we/wy 100 000 na udział dzięki 5 GB/s na udział na około 3 ms opóźnienia|Standard<br>Premium<br>Ultrafiolet<br>Do 320k (16 KB) operacji wejścia/wyjścia na sekundę z 4,5 GB/s|HDD w warstwie Standardowa: maksymalnie 500 limitów liczby operacji we/wy na dysku<br>SSD w warstwie Standardowa: maksymalnie 4 k limity liczby operacji we/wy na dysku<br>SSD w warstwie Premium: maksymalnie 20 000 limitów liczby operacji we/wy na dysku<br>Zalecamy używanie dysków w warstwie Premium dla Bezpośrednie miejsca do magazynowania|
|Pojemność|100 TiB na udział|100 TiB na wolumin, do 12,5 PiB na subskrypcję|Maksymalna 32 TiB na dysk|
|Wymagana infrastruktura|Minimalny rozmiar udziału 1 GiB|Minimalna Pula pojemności 4 TiB, minimalny rozmiar woluminu 100 GiB|Dwie maszyny wirtualne na platformie Azure IaaS (+ Monitor w chmurze) lub co najmniej trzy maszyny wirtualne bez opłat i kosztów dysków|
|Protokoły|SMB 2.1/3. i REST|NFSv3, NFSv 4.1 (wersja zapoznawcza), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Szczegóły zarządzania platformy Azure

|Funkcje|Azure Files|Azure NetApp Files|Bezpośrednie miejsca do magazynowania|
|--------|-----------|------------------|---------------------|
|Access|Chmura, lokalne i hybrydowe (usługa Azure File Sync)|Chmura, lokalna (za pośrednictwem ExpressRoute)|Chmura, lokalna|
|Backup|Integracja z migawką usługi Azure Backup|Migawki Azure NetApp Files|Integracja z migawką usługi Azure Backup|
|Zabezpieczenia i zgodność|[Wszystkie certyfikaty obsługiwane przez platformę Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Zakończono ISO|[Wszystkie certyfikaty obsługiwane przez platformę Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integracja z usługą Azure Active Directory|[Natywne Active Directory i Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services i natywne Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Tylko natywny Active Directory lub Azure Active Directory Domain Services|

Po wybraniu metody Storage zapoznaj się z [cennikiem pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/) , aby uzyskać informacje na temat naszych planów cenowych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat kontenerów profilów FSLogix, dysków profilu użytkownika i innych technologii profilu użytkownika, zobacz tabelę w [kontenerach profilów FSLogix i usługi Azure Files](fslogix-containers-azure-files.md).

Jeśli chcesz utworzyć własne kontenery profilu FSLogix, zacznij korzystać z jednego z następujących samouczków:

- [Wprowadzenie do kontenerów profilów FSLogix na Azure Files na pulpicie wirtualnym systemu Windows](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu plików NetApp platformy Azure](create-fslogix-profile-container.md)
- Instrukcje w temacie [Wdrażanie serwera plików skalowalnego w poziomie bezpośrednie miejsca do magazynowania na potrzeby magazynu UPD na platformie Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) mają zastosowanie również w przypadku używania kontenera profilu FSLogix zamiast dysku profilu użytkownika.

Możesz również zacząć od początku i skonfigurować własne rozwiązanie pulpitu wirtualnego systemu Windows w temacie [Tworzenie dzierżawy w systemie Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
