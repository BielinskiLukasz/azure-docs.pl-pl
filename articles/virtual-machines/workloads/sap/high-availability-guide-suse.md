---
title: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie SUSE Linux Enterprise Server dla aplikacji SAP | Dokumentacja firmy Microsoft
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie SUSE Linux Enterprise Server dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 935b501964435e80172ef3e147f777bf47119b48
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285126"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Wysoka dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i instalacji o wysokiej dostępności systemu SAP NetWeaver 7.50.
W przykładowych konfiguracji polecenia instalacji itp. Numer wystąpienia ASCS 00 numer 02 wystąpienia Wywołujących i służy NW1 identyfikator systemu SAP. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualnych) w przykładzie założono użycie [zbieżności szablonu] [ template-converged] z systemem SAP NW1 identyfikator, aby utworzyć zasoby.

Najpierw przeczytaj następujące uwagi SAP i dokumenty

* Uwaga SAP [1928533], która zawiera:
  * Listę rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o pojemności ważne w przypadku rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP i systemu operacyjnego (OS) i kombinacji bazy danych
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2205917] zawiera zalecane ustawienia systemu operacyjnego SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [1944799] zawiera wskazówki dotyczące programu SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [2178632] zawiera szczegółowe informacje o metrykach wszystkie funkcje monitorowania zgłoszone dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide]
* [SUSE SAP HA przewodniki z najlepszymi rozwiązaniami] [ suse-ha-guide] prowadnice zawiera wszystkie wymagane informacje, aby skonfigurować Netweaver wysokiej dostępności i replikacji systemu SAP HANA w środowisku lokalnym. Użyj, który prowadzi to jako głównej linii bazowej. Zapewniają one znacznie bardziej szczegółowe informacje.
* [Wysoce dostępny magazyn systemu plików NFS przy użyciu DRBD i program Pacemaker] [ suse-drbd-guide] przewodnik zawiera wszystkie wymagane informacje, aby skonfigurować serwer NFS o wysokiej dostępności. Użyj tego przewodnika jako punkt odniesienia.


## <a name="overview"></a>Przegląd

Aby osiągnąć wysoką dostępność, oprogramowanie SAP NetWeaver wymaga serwera systemu plików NFS. Serwer systemu plików NFS jest skonfigurowana w klastrze oddzielonym i mogą być używane przez wiele systemów SAP.

![Oprogramowanie SAP NetWeaver wysokiej dostępności Przegląd](./media/high-availability-guide-suse/img_001.png)

Serwer systemu plików NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Wywołujących i bazy danych SAP HANA, użyj nazwy hosta wirtualnego i wirtualnych adresów IP. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację (A) SCS i Wywołujących modułu równoważenia obciążenia.

### <a name="ascs"></a>(A)SCS

* Konfiguracja frontonu
  * Adres IP 10.0.0.7
* Konfiguracja zaplecza
  * Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 620**&lt;nr&gt;**
* Reguł równoważenia obciążenia
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Konfiguracja frontonu
  * Adres IP 10.0.0.8
* Konfiguracja zaplecza
  * Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 621**&lt;nr&gt;**
* Reguł równoważenia obciążenia
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera systemu plików NFS o wysokiej dostępności

Oprogramowanie SAP NetWeaver wymaga magazynu udostępnionego dla katalogu transportu i profilu. Odczyt [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server] [ nfs-ha] o tym, jak skonfigurować serwer NFS dla oprogramowania SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie () SCS

Możesz użyć szablonu platformy Azure z usługi github do wdrażania wszystkich wymaganych zasobów platformy Azure, łącznie z maszynami wirtualnymi, dostępność zestawu i modułu równoważenia obciążenia lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux przy użyciu szablonu platformy Azure

W portalu Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych. Obraz z witryny marketplace zawiera agenta zasobu środowiska SAP NetWeaver.

Można użyć jednego z szablonów szybkiego startu w usłudze github do wdrażania wszystkich wymaganych zasobów. Szablon umożliwia wdrożenie maszyn wirtualnych, moduł równoważenia obciążenia, dostępności, ustaw itp. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablonu SID Multi ASCS/SCS] [ template-multisid-xscs] lub [zbieżności szablonu] [ template-converged] w witrynie Azure portal, tworzy tylko ASCS/SCS szablonu reguł równoważenia obciążenia SAP NetWeaver ASCS/SCS i Wywołujących wystąpień (tylko system Linux) należy konwergentnej szablon powoduje utworzenie reguły równoważenia obciążenia dla bazy danych (na przykład Microsoft SQL Server lub SAP HANA). Jeśli planujesz zainstalować system SAP NetWeaver na podstawie, a użytkownik chce również zainstalować bazę danych na tym samym maszynach, należy użyć [zbieżności szablonu][template-converged].
1. Wprowadź następujące parametry
   1. Prefiks zasobu (tylko szablony ASCS/SCS Multi SID)  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   3. Identyfikator systemu SAP (tylko szablony konwergentnej)  
      Wprowadź identyfikator systemu SAP systemu SAP, w którym chcesz zainstalować. Ten identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
   4. Typ stosu  
      Wybierz typ stos oprogramowania SAP NetWeaver
   5. Typ systemu operacyjnego  
      Wybierz jeden z dystrybucje systemu Linux. W tym przykładzie wybierz SLES 12 BYOS
   6. Typ bazy danych  
      Wybierz platformy HANA
   7. Rozmiar systemu SAP  
      Ilość protokoły SAP udostępnia nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, zapytaj partnerów technologicznych SAP lub Integrator systemu
   8. Dostępność systemu  
      Wybierz opcję wysokiej dostępności
   9. Nazwa użytkownika administratora i hasła administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   10. Identyfikator podsieci  
   Identyfikator podsieci, do którego powinny być połączone maszyny wirtualne.  Pozostaw puste, jeśli chcesz utworzyć nową sieć wirtualną lub wybierz tej samej podsieci, która użycia lub utworzenia jako część wdrożenia serwera systemu plików NFS. Identyfikator zwykle wygląda /subscriptions/**&lt;identyfikator subskrypcji&gt;**/resourceGroups/**&lt;nazwy grupy zasobów&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;nazwa sieci wirtualnej&gt;**/subnets/**&lt;Nazwa podsieci&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pomocą witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne, dla tego klastra systemu plików NFS. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Domena aktualizacji Maksymalny zestaw
1. Tworzenie maszyny wirtualnej 1   
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP1, w tym obrazie przykład SLES4SAP 12 z dodatkiem SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP aplikacji 12 z dodatkiem SP1 jest używany.  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Tworzenie maszyny wirtualnej 2   
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP1, w tym obrazie przykład SLES4SAP 12 z dodatkiem SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP aplikacji 12 z dodatkiem SP1 jest używany.  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Dodaj co najmniej jeden dysk danych do obu maszyn wirtualnych  
   Dyski danych są używanedla/usr/sap/`<SAPSID`> katalog
1. Utwórz moduł równoważenia obciążenia (wewnętrzny)  
   1. Tworzenie adresów IP frontonu
      1. Adres IP 10.0.0.7 ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **nw1-ascs-frontend**)
         1. Ustawić przypisania jako statyczny i podaj adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla Wywołujących (na przykład **10.0.0.8** i **nw1-aers-backend**)
   1. Utwórz pule zaplecza
      1. Tworzenie puli zaplecza dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz opcję pule zaplecza, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-ascs-backend**)
         1. Kliknij przycisk Dodaj maszynę wirtualną.
         1. Wybierz zestaw dostępności została utworzona wcześniej
         1. Wybierz maszyny wirtualne (a) SCS klastra
         1. Kliknij przycisk OK
      1. Tworzenie puli zaplecza dla Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć pulę zaplecza dla Wywołujących (na przykład **nw1-aers-backend**)
   1. Utworzenie sond kondycji.
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz opcję TCP jako protokół, port 620**00**, Zachowaj 5 interwał i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS Wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla Wywołujących (na przykład 621**02** i **nw1-aers-hp**)
   1. Reguł równoważenia obciążenia
      1. 32**00** protokołu TCP na potrzeby ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **nw1-lb-3200**)
         1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład **nw1-ascs-frontend**)
         1. Zachowaj protokołu **TCP**, wprowadź numer portu **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Pamiętaj włączyć pływającego adresu IP**
         1. Kliknij przycisk OK    
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i protokołu TCP na potrzeby ASCS
      1. Dodatkowe porty dla Wywołujących ASCS
         * Powtórz powyższe kroki dla portów 33**02**, 5**02**13, 5**02**14, 5**02**16 i protokołu TCP na potrzeby Wywołujących ASCS

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) do utworzenia podstawowego program Pacemaker klastra dla tego serwera () SCS.

### <a name="installation"></a>Instalacja

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Zainstalować łącznik SUSE
   
   <pre><code>
   sudo zypper install sap-suse-cluster-connector
   </code></pre>

   Upewnij się, że zainstalowano nową wersję łącznika klastra SAP SUSE. Stary certyfikat został wywołany sap_suse_cluster_connector i nosi nazwę nowego **klastra łącznika, sap suse w-**.

   <pre><code>
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

1. **[A]**  SAP aktualizacji zasobu agentów  
   
   Poprawka dla pakietu zasobów agentów jest wymagana do korzystania z nowej konfiguracji opisanej w tym artykule. Możesz sprawdzić, jeśli poprawka jest już zainstalowany za pomocą następującego polecenia

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Dane wyjściowe powinny być podobne do

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie może znaleźć parametr IS_ERS, należy zainstalować poprawkę na [stronę pobierania SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów   

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. **[A]**  Tworzenia udostępnionych katalogów

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  Skonfigurować autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz plik o

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
   </code></pre>

   Uruchom ponownie autofs, aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Skonfigurować wymiany plików

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

   Uruchom ponownie agenta aby aktywować zmiany

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie SAP NetWeaver ASCS/Wywołujących

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia ASCS

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]**  Zainstalować oprogramowanie SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako główny urząd certyfikacji na pierwszym węźle za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>nw1 ascs</b>, <b>10.0.0.7</b> i numer, który był używany przez sondy modułu równoważenia obciążenia, na przykład wystąpienia <b>00</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**NW1**/ASCS**00**, spróbuj ustawić właściciela i grupy ASCS**00** folder, a następnie spróbuj ponownie.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia Wywołujących

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]**  Zainstalować oprogramowanie SAP NetWeaver Wywołujących  

   Zainstaluj oprogramowanie SAP NetWeaver Wywołujących jako główny urząd certyfikacji na drugi węzeł, za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla Wywołujących, na przykład <b>nw1 aers</b>, <b>10.0.0.8</b> i numer, który był używany przez sondy modułu równoważenia obciążenia, na przykład wystąpienia <b>02</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub nowszej. Niższe wersje nie należy ustawiać uprawnienia poprawnie, a instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**NW1**/ERS**02**, spróbuj ustawić właściciela i grupy Wywołujących**02** folder, a następnie spróbuj ponownie.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASCS/SCS i Wywołujących wystąpienia profilów
 
   * Profil ASCS/SCS

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil Wywołujących

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Skonfigurować podtrzymywaniu

   Komunikacja między serwerem aplikacji SAP NetWeaver i ASCS/SCS odbywa się za pomocą programowego modułu równoważenia obciążenia. Moduł równoważenia obciążenia rozłączy nieaktywnego połączenia po upłynięciu limitu czasu można konfigurować. Aby zapobiec takiej sytuacji należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienianie ustawień systemu Linux. Odczyt [1410736 Uwaga SAP] [ 1410736] Aby uzyskać więcej informacji.
   
   ASCS/SCS profilu parametr umieścić/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Po instalacji, skonfigurować użytkowników oprogramowania SAP
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  Dodaj services ASCS i SAP Wywołujących do pliku sapservice

   Dodaj ASCS usługi wpisu do drugiego węzła i kopiowanie wpisu usługi Wywołujących w pierwszym węźle.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Utworzenie zasobów klastra SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera programu SAP NetWeaver aplikacji

Niektóre bazy danych wymagają, że instalacja wystąpienia bazy danych jest wykonywana na serwerze aplikacji. Przygotowywanie maszyn wirtualnych serwera aplikacji, aby móc używać ich w tych przypadkach.

W poniższych krokach przyjęto założenie, zainstalować serwer aplikacji na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki przedstawione poniżej (np. Konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.

1. Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach
   ```bash
   sudo vi /etc/hosts
   ```
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Utwórz katalog sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurowanie autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik za pomocą

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Uruchom ponownie autofs, aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurowanie pliku wymiany
 
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

   Uruchom ponownie agenta aby aktywować zmiany

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie oprogramowanie SAP NetWeaver jest zainstalowany na platformie SAP HANA. Wszystkie obsługiwane bazy danych można użyć dla tej instalacji. Aby uzyskać więcej informacji na temat sposobu instalowania oprogramowania SAP HANA na platformie Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]. Aby uzyskać listę obsługiwanych baz danych, zobacz [1928533 Uwaga SAP][1928533].

1. Uruchom program instalacyjny wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalogu głównego przy użyciu wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych, na przykład <b>nw1 db</b> i <b>10.0.0.13</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja programu SAP NetWeaver aplikacji serwera

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP. 

1. Przygotowanie serwera aplikacji

Wykonaj kroki opisane w rozdziale [przygotowania serwera aplikacji SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie serwera aplikacji SAP NetWeaver

   Instalacja serwera aplikacji SAP NetWeaver podstawowej lub dodatkowej.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja oprogramowania SAP HANA bezpiecznego magazynu

   Aktualizacja oprogramowania SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualnego ustawień replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>
   hdbuserstore List
   </code></pre>

   To powinno wyświetlić listę wszystkich wpisów i powinien wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   Dane wyjściowe pokazują, wskazuje wpis domyślny adres IP maszyny wirtualnej, a nie adres IP modułu równoważenia obciążenia. Ten wpis, trzeba je zmienić i wskazać do wirtualnej nazwy hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w powyższych danych wyjściowych)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]
