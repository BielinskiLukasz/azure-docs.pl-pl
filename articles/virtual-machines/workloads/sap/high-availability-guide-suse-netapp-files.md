---
title: Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files | Microsoft Docs
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: c8fcf4afa5a363d355f627be95dd7fe8131203ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67797968"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności przy użyciu [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
W przykładowych konfiguracjach, poleceniach instalacji itp. wystąpienie ASCS ma numer 00, wystąpienie wykres WYWOŁUJĄCYCH o numerze 01, podstawowe wystąpienie aplikacji (PAS) to 02, a wystąpienie aplikacji (AAS) to 03. Identyfikator systemowy SAP QAS jest używany. 

W tym artykule wyjaśniono, jak uzyskać wysoką dostępność aplikacji SAP NetWeaver za pomocą Azure NetApp Files. Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule.

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], która ma:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [1944799] SAP HANA wytycznych dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* Przewodniki dla systemu [SUSE SAP ha Best Practices][suse-ha-guide] Przewodniki zawierają wszystkie informacje wymagane do skonfigurowania NetWeaver HA i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako ogólnego planu bazowego. Zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Wysoka dostępność (HA) dla usług SAP NetWeaver Central Services wymaga magazynu udostępnionego.
Aby osiągnąć ten dostęp w systemie SUSE Linux, konieczne było skompilowanie oddzielnego klastra NFS o wysokiej dostępności. 

Teraz można osiągnąć rozwiązanie SAP NetWeaver HA przy użyciu magazynu udostępnionego, wdrożonego na Azure NetApp Files. Używanie Azure NetApp Files dla magazynu udostępnionego eliminuje konieczność dodatkowego [klastra NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker jest nadal wymagana w przypadku wysokiej dostępności usług SAP NetWeaver Central Services (ASCS/SCS).


![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure [moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH.

> [!IMPORTANT]
> Klastrowanie z obsługą protokołu ASCS/wykres WYWOŁUJĄCYCH w systemie SUSE Linux jako system operacyjny gościa na maszynach wirtualnych platformy Azure **nie jest obsługiwane**. Klastrowanie wielu identyfikatorów SID opisuje instalację wielu wystąpień SAP ASCS/wykres WYWOŁUJĄCYCH z różnymi identyfikatorami SID w jednym klastrze Pacemaker


### <a name="ascs"></a>(A)SCS

* Konfiguracja frontonu
  * Adres IP 10.1.1.20
* Konfiguracja zaplecza
  * Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra programu (A) SCS/wykres WYWOŁUJĄCYCH
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * <strong>32&lt;Nr&gt;</strong>  TCP
  * <strong>36&lt;Nr&gt;</strong>  TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * <strong>81&lt;Nr&gt;</strong>  TCP
  * <strong>5&lt;Nr&gt;</strong>13 TCP
  * <strong>5&lt;Nr&gt;</strong>14 TCP
  * <strong>5&lt;Nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguracja frontonu
  * Adres IP 10.1.1.21
* Konfiguracja zaplecza
  * Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra programu (A) SCS/wykres WYWOŁUJĄCYCH
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * <strong>32&lt;Nr&gt;</strong>  TCP
  * <strong>33&lt;Nr&gt;</strong>  TCP
  * <strong>5&lt;Nr&gt;</strong>13 TCP
  * <strong>5&lt;Nr&gt;</strong>14 TCP
  * <strong>5&lt;Nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury Azure NetApp Files 

Rozwiązanie SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportowego i profilu.  Przed przystąpieniem do konfigurowania infrastruktury usługi Azure NetApp Files zapoznaj się z [dokumentacją Azure NetApp Files][anf-azure-doc]. Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files. Poniższy link przedstawia dostępność Azure NetApp Files według regionu platformy Azure: [Azure NetApp Files dostępność według regionów świadczenia usługi Azure][anf-avail-matrix].

Pliki usługi Azure NetApp są dostępne w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Przed wdrożeniem Azure NetApp Files należy zażądać dołączenia do Azure NetApp Files, postępując zgodnie z instrukcjami dotyczącymi [usługi Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files  

W tych krokach przyjęto założenie, że wdrożono już [Virtual Network platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Zasoby Azure NetApp Files i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli jeszcze tego nie zrobiono, zażądaj dołączenia [do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z [instrukcjami dotyczącymi tworzenia konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z [instrukcjami dotyczącymi konfigurowania Azure NetApp Files puli pojemności](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura SAP NetWeaver w tym artykule używa puli pojemności Azure NetApp Files w wersji Premium. Zalecamy Azure NetApp Files jednostki SKU Premium dla obciążenia aplikacji SAP NetWeaver na platformie Azure.  

4. Delegowanie podsieci do plików NetApp platformy Azure zgodnie z opisem w [instrukcji delegowania podsieci do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z [instrukcjami, aby utworzyć wolumin dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Wdróż woluminy w wyznaczeniej [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files. Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tym samym Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład sapmnt<b>QAS</b>, usrsap<b>QAS</b>itp. są nazwami woluminów<b>i sapmnt QAS</b>, usrsap<b>QAS</b>, itp. są ścieżkami do woluminów Azure NetApp Files.  

   1. Volume sapmnt<b>QAS</b> (NFS://10.1.0.4/sapmnt<b>QAS</b>)
   2. Volume usrsap<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>)
   3. Volume usrsap<b>QAS</b>sys (NFS://10.1.0.5/usrsap<b>QAS</b>sys)
   4. Volume usrsap<b>QAS</b>wykres wywołujących (NFS://10.1.0.4/usrsap<b>QAS</b>wykres wywołujących)
   5. transakcja zbiorcza (nfs://10.1.0.4/trans)
   6. Volume usrsap<b>QAS</b>pas (NFS://10.1.0.5/usrsap<b>QAS</b>pas)
   7. Volume usrsap<b>QAS</b>aas (NFS://10.1.0.4/usrsap<b>QAS</b>AAS)
   
W tym przykładzie użyto Azure NetApp Files dla wszystkich systemów plików SAP NetWeaver, aby zademonstrować, jak Azure NetApp Files może być używany. Systemy plików SAP, które nie muszą być instalowane za pośrednictwem systemu plików NFS, można również wdrożyć jako [usługę Azure Disk Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . W tym przykładzie <b>a-e</b> musi znajdować się w Azure NetApp Files i <b>f-g</b> (czyli/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) można wdrożyć jako usługę Azure Disk Storage. 

### <a name="important-considerations"></a>Ważne zagadnienia

Rozważając Azure NetApp Files dla architektury SAP NetWeaver w systemie SUSE wysokiej dostępności, weź pod uwagę następujące ważne zagadnienia:

- Minimalna Pula pojemności to 4 TiB. Rozmiar puli pojemności musi być wielokrotnością 4 TiB.
- Minimalny wolumin to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie. Azure NetApp Files dostęp za pośrednictwem komunikacji równorzędnej sieci wirtualnej w tym samym regionie jest obecnie obsługiwany. Dostęp do usługi Azure NetApp za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Azure NetApp Files obecnie obsługuje tylko NFSv3 
- Azure NetApp Files oferuje [zasady eksportowania](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): można kontrolować dozwolonych klientów, typ dostępu (odczyt & zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Ręczne wdrażanie maszyn wirtualnych z systemem Linux za pośrednictwem Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Wdróż maszyny wirtualne. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w pulach zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie Virtual Network
1. Utwórz zestaw dostępności dla ASCS  
   Ustaw maksymalną domenę aktualizacji
1. Utwórz maszynę wirtualną 1  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla ASCS  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla ASCS  
1. Utwórz zestaw dostępności dla wystąpień aplikacji SAP (PAS, AAS)    
   Ustaw maksymalną domenę aktualizacji
1. Tworzenie maszyny wirtualnej 3  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla platformy PAS/AAS   
1. Tworzenie maszyny wirtualnej 4  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla platformy PAS/AAS  

## <a name="setting-up-ascs"></a>Konfigurowanie (A) SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [Azure Portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Wdróż Azure Load Balancer ręcznie za pośrednictwem Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Wdróż maszyny wirtualne. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w pulach zaplecza.

1. Tworzenie Load Balancer (wewnętrzny)  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.1.1.20 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład frontonu) **. QAS. ASCS**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.1.1.20**)
         1. Kliknij przycisk OK.
      1. 10.1.1.21 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.1.1.21** i **fronton. QAS. Wykres WYWOŁUJĄCYCH**)
   1. Tworzenie pul zaplecza
      1. Tworzenie puli zaplecza dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli zaplecza (na przykład zaplecze **. QAS**)
         1. Kliknij pozycję Dodaj maszynę wirtualną.
         1. Wybierz zestaw dostępności utworzony wcześniej dla ASCS 
         1. Wybierz Maszyny wirtualne klastra SCS
         1. Kliknij przycisk OK.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **kondycja. QAS. ASCS**)
         1. Wybierz pozycję TCP jako protokół, port 620**00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621**01** dla ASCS wykres wywołujących
            * Powtórz powyższe kroki w sekcji "c", aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621**01** i kondycja) **. QAS. Wykres WYWOŁUJĄCYCH**)
   1. Reguły równoważenia obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **LB. QAS. ASCS. 3200**)
         1. Wybierz adres IP frontonu dla ASCS, puli zaplecza i sondy kondycji utworzonej wcześniej (na przykład frontonu) **. QAS. ASCS**)
         1. Utrzymywanie protokołu **TCP**, wprowadź port **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 36**00**, 39**00**, 81**00**, 5**00**, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "d" dla portów 33**01**, 5**01**13, 5**01**14, 5**01**16 i TCP dla ASCS wykres wywołujących

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net. IPv4. TCP _timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego (a) serwera SCS.

### <a name="installation"></a>Instalacja

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]** Zainstaluj łącznik SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Nie używaj kresek w nazwach hostów węzłów klastra. W przeciwnym razie klaster nie będzie działał. Jest to znane ograniczenie, a SUSE działa z poprawkami. Poprawka zostanie wydana jako poprawka pakietu SAP-SUSE-Cloud-Connector.

   Upewnij się, że zainstalowano nową wersję łącznika klastra SAP SUSE. Stary został o nazwie sap_suse_cluster_connector, a nowy o nazwie **SAP-SUSE-Cluster-Connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** zaktualizuj agentów zasobów SAP  
   
   Poprawka do pakietu zasobów-agentów jest wymagana do korzystania z nowej konfiguracji, która została opisana w tym artykule. Możesz sprawdzić, czy poprawka jest już zainstalowana przy użyciu następującego polecenia

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Dane wyjściowe powinny być podobne do

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie znajdzie parametru IS_ERS, należy zainstalować poprawkę wymienioną na [stronie pobierania systemu SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. **[A]** Tworzenie katalogów udostępnionych

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** Skonfiguruj AutoFS

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz plik z

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Obecnie Azure NetApp Files obsługuje tylko NFSv3. Nie pomijaj przełącznika nfsvers = 3.
   
   Uruchom `autofs` ponownie, aby zainstalować nowe udziały
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** Skonfiguruj plik wymiany

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie oprogramowania SAP NetWeaver ASCS/wykres WYWOŁUJĄCYCH

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia ASCS

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>anftstsapvh</b>, <b>10.1.1.20</b> i numer wystąpienia użyty dla Sonda modułu równoważenia obciążenia, na przykład <b>00</b>.

   Można użyć sapinst parametru SAPINST_REMOTE_ACCESS_USER, aby zezwolić niegłównemu użytkownikowi na łączenie się z usługą sapinst. Do zainstalowania SAP przy użyciu nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**QAS**/ASCS**00**, spróbuj ustawić właściciela i grupę folderu ASCS**00** , a następnie ponów próbę. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia wykres wywołujących

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących

   Zainstaluj oprogramowanie SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w drugim węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH, na przykład <b>anftstsapers</b>, <b>10.1.1.21</b> i numer wystąpienia użyty dla Sonda modułu równoważenia obciążenia, na przykład <b>01</b>.

   Można użyć sapinst parametru SAPINST_REMOTE_ACCESS_USER, aby zezwolić niegłównemu użytkownikowi na łączenie się z usługą sapinst. Do zainstalowania SAP przy użyciu nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub wyższej. Niższa wersja nie ustawia prawidłowo uprawnień i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się, utwórz podfolder w/usr/SAP/**QAS**/ERS**01**, spróbuj ustawić właściciela i grupę folderu wykres wywołujących**01** i ponów próbę.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** dostosowuje profile wystąpienia ASCS/SCS i wykres wywołujących
 
   * Profil ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil wykres WYWOŁUJĄCYCH

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** Skonfiguruj utrzymywanie aktywności

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest kierowana przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie konfigurowalnego limitu czasu. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienić ustawienia systemu Linux. Aby uzyskać więcej informacji, przeczytaj temat [SAP Note 1410736][1410736] .

   Parametr ASCS/SCS profilu umieścić/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** Skonfiguruj użytkowników SAP po instalacji

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Dodaj usługi ASCS i wykres wywołujących SAP do pliku sapservice

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi wykres WYWOŁUJĄCYCH do pierwszego węzła.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** tworzenie zasobów klastra SAP

W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
Jeśli używana jest architektura Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowywanie serwera aplikacji SAP NetWeaver 

Niektóre bazy danych wymagają wykonania instalacji wystąpienia bazy danych na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby mogły z nich korzystać w tych przypadkach.

W poniższych krokach założono, że serwer aplikacji jest instalowany na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki (takie jak Konfigurowanie rozpoznawania nazw hostów) nie są wymagane.

Następujące elementy są poprzedzone znakiem **[A]** — dotyczy zarówno platformy pas, jak i AAS, **[P]** — dotyczy tylko platformy pas lub **[S]** — dotyczy tylko AAS.


1. **[A]**  Konfiguruj system operacyjny

   Zmniejsz rozmiar zmieniony pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu w systemie SLES 11/12 serwery z dużą ilość pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   ```bash
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** Utwórz katalog sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** Utwórz katalog pas

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** Utwórz katalog AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** Skonfiguruj AUTOFS na pas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik z

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Uruchom `autofs` ponownie, aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** Skonfiguruj AUTOFS w AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik z

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Uruchom `autofs` ponownie, aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Skonfiguruj plik wymiany

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Zainstaluj bazę danych

W tym przykładzie system SAP NetWeaver jest instalowany na SAP HANA. Dla tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

* Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako element główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych.

   Można użyć sapinst parametru SAPINST_REMOTE_ACCESS_USER, aby zezwolić niegłównemu użytkownikowi na łączenie się z usługą sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. **[A]** Przygotuj serwer aplikacji, postępując zgodnie z instrukcjami opisanymi w rozdziale [serwer aplikacji SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

2. **[A]** Zainstaluj serwer aplikacji SAP NetWeaver Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   Można użyć sapinst parametru SAPINST_REMOTE_ACCESS_USER, aby zezwolić niegłównemu użytkownikowi na łączenie się z usługą sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** aktualizowanie SAP HANA bezpiecznego magazynu

   Zaktualizuj SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualną konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>
   hdbuserstore List
   </code></pre>

   Powinno to być lista wszystkich wpisów i powinna wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Dane wyjściowe wskazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis należy zmienić, aby wskazywał wirtualną nazwę hosta usługi równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w danych wyjściowych powyżej) i nazwy bazy danych (**QAS** w danych wyjściowych powyżej)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

Poniższe testy to kopia przypadków testowych w [przewodnikach z najlepszymi rozwiązaniami dla systemu SUSE][suse-ha-guide]. Są one kopiowane dla wygody użytkownika. Zawsze należy przeczytać przewodniki dotyczące najlepszych rozwiązań i wykonać wszystkie dodatkowe testy, które mogły zostać dodane.

1. Testowanie HAGetFailoverConfig, HACheckConfig i HACheckFailoverConfig

   Uruchom następujące polecenia jako \<sapsid > adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenia zakończą się niepowodzeniem, wystąpi błąd: Za mało pamięci, może to być spowodowane przez myślniki w nazwie hosta. Jest to znany problem, który zostanie rozwiązany przez SUSE w pakiecie SAP-SUSE-Cluster-Connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Ręczne Migrowanie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Test HAFailoverToNode

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako \<sapsid > adm, aby zmigrować wystąpienie ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Symulowanie awarii węzła 

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione wystąpienie ASCS

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Jeśli używasz SBD, Pacemaker nie powinien automatycznie rozpoczynać się w zamkniętym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Użyj następujących poleceń, aby uruchomić Pacemaker w zamkniętym węźle, wyczyść komunikaty SBD i wyczyść zasoby zakończone niepowodzeniem.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testowanie ręcznego ponownego uruchomienia wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Utwórz blokadę do kolejki, na przykład Edytuj użytkownika w usłudze Transaction su01. Uruchom następujące polecenia jako < sapsid\>adm w węźle, w którym działa wystąpienie ASCS. Polecenia zatrzymają wystąpienie ASCS i uruchomią je ponownie. W przypadku korzystania z architektury Server 1 w kolejce w tym teście oczekuje się, że blokada kolejki zostanie utracona. W przypadku korzystania z architektury serwera 2 w kolejce zostanie zachowany. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Wystąpienie ASCS powinno być teraz wyłączone w Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Uruchom ponownie wystąpienie ASCS w tym samym węźle.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Blokada kolejki transakcji su01 powinna zostać utracona, jeśli jest używana architektura replikacji serwera w kolejce 1, a zaplecze powinno zostać zresetowane. Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Przerwij proces serwera komunikatów

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zidentyfikować proces serwera komunikatów i Kasuj go.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Jeśli serwer wiadomości zostanie skasowany tylko raz, zostanie on uruchomiony ponownie przez sapstart. Jeśli zakończysz go często, Pacemaker będzie ostatecznie przenieść wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Kasuj proces serwera z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym uruchomiono wystąpienie ASCS, aby skasować serwer z kolejki.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinno natychmiast przejść w tryb failover do innego węzła. Wystąpienie wykres WYWOŁUJĄCYCH powinno również przechodzić w tryb failover po rozpoczęciu wystąpienia ASCS. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Kasuj proces serwera replikacji z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym uruchomiono wystąpienie wykres WYWOŁUJĄCYCH, aby skasować proces serwera replikacji z kolejki.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Jeśli uruchamiasz polecenie tylko raz, `sapstart` program uruchomi ponownie ten proces. Jeśli uruchomisz go często, `sapstart` program nie uruchomi ponownie procesu, a zasób stanie się zatrzymany. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia wykres WYWOŁUJĄCYCH po teście.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Kasuj proces sapstartsrvnia w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym działa ASCS.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Proces sapstartsrv powinien być zawsze ponownie uruchamiany przez agenta zasobów Pacemaker. Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak ustanowić wysoką dostępność i plan odzyskiwania po awarii dla oprogramowania SAP 
* HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
