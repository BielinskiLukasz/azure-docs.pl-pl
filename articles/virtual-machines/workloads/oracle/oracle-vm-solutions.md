---
title: Rozwiązania firmy Oracle na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Informacje o obsługiwanych konfiguracjach i ograniczeniach obrazów maszyn wirtualnych Oracle na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 4480819a08ef9a7a4ad7257f75a94c5d10a3d312
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858563"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure

Ten artykuł zawiera informacje na temat rozwiązań firmy Oracle opartych na obrazach maszyn wirtualnych opublikowanych przez program Oracle w portalu Azure Marketplace. Jeśli interesujesz się rozwiązaniami aplikacji w chmurze za pomocą infrastruktury chmurowej Oracle, zobacz [rozwiązania aplikacji Oracle integrujące Microsoft Azure i infrastrukturę chmurową firmy Oracle](oracle-oci-overview.md).

Aby uzyskać listę aktualnie dostępnych obrazów, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Od maja 2019 dostępne są następujące obrazy:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Te obrazy są uważane za "dostarczenie własnej licencji", a w związku z tym opłaty są naliczane tylko za zasoby obliczeniowe, magazynowe i sieciowe związane z uruchamianiem maszyny wirtualnej.  Przyjęto założenie, że masz licencję na korzystanie z oprogramowania Oracle i że masz bieżącą umowę pomocy technicznej na platformie Oracle. Firma Oracle gwarantuje mobilność licencji ze środowiska lokalnego na platformę Azure. Aby uzyskać szczegółowe informacje na temat mobilności licencji, zobacz opublikowane [oprogramowanie Oracle i Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) uwagi. 

Użytkownicy mogą również zdecydować się na oparcie rozwiązań w obrazie niestandardowym utworzonym od podstaw na platformie Azure lub przekazaniem obrazu niestandardowego ze środowiska lokalnego.

## <a name="oracle-database-vm-images"></a>Obrazy maszyn wirtualnych bazy danych Oracle
Oprogramowanie Oracle obsługuje uruchamianie Oracle DB 12,1 i nowszych wersji Standard i Enterprise na platformie Azure w obrazach maszyn wirtualnych na podstawie Oracle Linux.  Aby uzyskać najlepszą wydajność dla obciążeń produkcyjnych Oracle DB na platformie Azure, należy pamiętać o prawidłowym rozmiarze obrazu maszyny wirtualnej i użyć SSD w warstwie Premium lub SSD w warstwie Ultra Managed Disks. Aby uzyskać instrukcje na temat szybkiego uzyskiwania Oracle DB na platformie Azure przy użyciu opublikowanego obrazu maszyny wirtualnej Oracle, [wypróbuj Oracle DB przewodnika Szybki Start](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opcje konfiguracji dołączonego dysku

Dołączone dyski korzystają z usługi Azure Blob Storage. Każdy dysk w warstwie Standardowa może teoretycznie maksymalnie wynosić około 500 operacji wejścia/wyjścia na sekundę (IOPS). Nasza oferta dysku Premium jest preferowana w przypadku obciążeń baz danych o wysokiej wydajności i może osiągać 5000 operacji we/wy na dysku. Można użyć jednego dysku, jeśli spełnia wymagania dotyczące wydajności. Można jednak zwiększyć wydajność wydajności operacji we/wy, jeśli używasz wielu dołączonych dysków, rozłożyć dane bazy danych między nimi, a następnie użyj funkcji automatycznego zarządzania magazynem (ASM) firmy Oracle. Zobacz [Omówienie automatycznego magazynu Oracle](https://www.oracle.com/technetwork/database/index-100339.html) , aby uzyskać więcej informacji dotyczących programu Oracle ASM. Przykład instalacji i konfiguracji programu Oracle ASM na maszynie wirtualnej platformy Azure z systemem Linux można znaleźć w temacie [Instalowanie i Konfigurowanie zautomatyzowanego zarządzania magazynem programu Oracle](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Opcje konfiguracji magazynu udostępnionego

Azure NetApp Files został zaprojektowany tak, aby spełniał podstawowe wymagania dotyczące uruchamiania obciążeń o wysokiej wydajności, takich jak bazy danych w chmurze, i zapewnia;
- Usługa Azure Native Shared Storage NFS do uruchamiania obciążeń Oracle przy użyciu natywnego klienta systemu plików NFS lub Oracle dNFS
- Skalowalne warstwy wydajności odzwierciedlające rzeczywisty zakres żądań IOPS
- Małe opóźnienia
- Wysoka dostępność, wysoka trwałość i łatwość zarządzania na dużą skalę, zazwyczaj zapotrzebowanie na zadania o znaczeniu krytycznym dla przedsiębiorstw (na przykład SAP i Oracle)
- Szybka i wydajna kopia zapasowa i odzyskiwanie, aby osiągnąć najbardziej agresywne umowy SLA RTO i RPO

Te możliwości są możliwe, ponieważ Azure NetApp Files jest oparty na NetApp® ONTAP® wszystkie systemy Flash działające w środowisku centrum danych platformy Azure — jako usługa natywna platformy Azure. Wynikiem jest idealna technologia magazynu bazy danych, która może zostać zainicjowana i zużyta podobnie jak w przypadku innych opcji usługi Azure Storage. Więcej informacji na temat sposobu wdrażania i uzyskiwania dostępu do Azure NetApp Files woluminów NFS znajduje się w [dokumentacji Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) . Zapoznaj się z tematem zalecenia dotyczące [najlepszych rozwiązań w programie Oracle na platformie Azure, korzystając z Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) z najlepszymi rozwiązaniami dotyczącymi obsługi bazy Azure NetApp Files danych Oracle.


## <a name="licensing-oracle-database--software-on-azure"></a>Licencjonowanie Oracle Database & oprogramowania na platformie Azure
Microsoft Azure to autoryzowane środowisko chmury do uruchamiania Oracle Database. Tabela czynników podstawowych firmy Oracle nie ma zastosowania w przypadku licencjonowania baz danych Oracle w chmurze. Zamiast tego podczas korzystania z maszyn wirtualnych z technologią Hyper-Threading dla baz danych w wersji Enterprise należy liczyć dwie procesorów wirtualnych vCPU jako równoważne jednej licencji procesora Oracle, jeśli jest włączona funkcja wielowątkowości (zgodnie z opisem w dokumencie zasad). Szczegóły zasad można znaleźć [tutaj](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Bazy danych Oracle zazwyczaj wymagają wyższych ilości pamięci i operacji we/wy. Z tego powodu zaleca się używanie [maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-memory) dla tych obciążeń. Aby jeszcze bardziej zoptymalizować obciążenia, Oracle DB zaleca się użycie [procesorów wirtualnych vcpuych rdzeni](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/constrained-vcpu) , które wymagają dużej ilości pamięci, magazynu I przepustowości we/wy, ale nie dużej liczby rdzeni.

W przypadku migrowania oprogramowania i obciążeń programu Oracle ze środowiska lokalnego do Microsoft Azure firma Oracle zapewnia mobilność licencji zgodnie z opisem w artykule [Oracle na platformie Azure — często zadawane pytania](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Klaster aplikacji w rzeczywistości Oracle (Oracle RAC)
Certyfikat Oracle RAC został zaprojektowany z myślą o ograniczeniu awarii pojedynczego węzła w lokalnej konfiguracji klastra wielowęzłowego. Opiera się on na dwóch lokalnych technologiach, które nie są natywne dla środowisk chmury publicznej z funkcją Hyper-skalowania: sieć z obsługą wielu multiemisji i dysk udostępniony. Jeśli rozwiązanie bazy danych wymaga programu Oracle RAC na platformie Azure, do włączenia tych technologii wymagane jest oprogramowanie trzeciej firmy. Więcej informacji na temat programu Oracle RAC można znaleźć na [stronie FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia dotyczące wysokiej dostępności i odzyskiwania po awarii
W przypadku korzystania z baz danych Oracle na platformie Azure użytkownik jest odpowiedzialny za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii w celu uniknięcia wszelkich przestojów. 

Wysoką dostępność i odzyskiwanie po awarii dla Oracle Database Enterprise Edition (bez polegania na programie Oracle RAC) można uzyskać na platformie Azure przy użyciu funkcji [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)lub [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)z dwiema bazami danych na dwóch oddzielnych wirtualnych Win64. Obie maszyny wirtualne powinny znajdować się w tej samej [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) , aby zapewnić, że będą mogły uzyskać do nich dostęp za pośrednictwem prywatnego trwałego adresu IP.  Ponadto Zalecamy umieszczenie maszyn wirtualnych w tym samym zestawie dostępności, aby umożliwić platformie Azure umieszczenie ich w osobnych domenach błędów i domenach uaktualnienia. Jeśli chcesz mieć nadmiarowość geograficzną, skonfiguruj dwie bazy danych do replikowania między dwoma różnymi regionami i Połącz te dwa wystąpienia z VPN Gateway.

Samouczek [implementujący funkcję Oracle Data Guard na platformie Azure](configure-oracle-dataguard.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.  

Przy użyciu funkcji Oracle Data Guard można uzyskać wysoką dostępność przy użyciu podstawowej bazy danych na jednej maszynie wirtualnej, dodatkowej (w stanie gotowości) w innej maszynie wirtualnej i replikacji jednokierunkowej. Wynikiem jest dostęp do odczytu do kopii bazy danych programu. Za pomocą programu Oracle GoldenGate można skonfigurować dwukierunkową replikację między dwiema bazami danych. Aby dowiedzieć się, jak skonfigurować rozwiązanie wysokiej dostępności dla baz danych przy użyciu tych narzędzi, zobacz artykuł [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) i dokumentacja [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) w witrynie Oracle. Jeśli potrzebujesz dostępu do odczytu i zapisu do kopii bazy danych programu, możesz użyć usługi [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Samouczek [implementacji programu Oracle GoldenGate na platformie Azure](configure-oracle-golden-gate.md) przeprowadzi Cię przez podstawową procedurę konfiguracji na platformie Azure.

Oprócz posiadania rozwiązań o wysokiej dostępności i rozwiązaniu DR na platformie Azure należy utworzyć strategię tworzenia kopii zapasowych w celu przywrócenia bazy danych. W samouczku [Tworzenie kopii zapasowej i odzyskiwanie Oracle Database](oracle-backup-recovery.md) przeprowadzi Cię przez podstawową procedurę tworzenia spójnej kopii zapasowej.


## <a name="support-for-jd-edwards"></a>Obsługa JD Edwards
Zgodnie z pomocą techniczną firmy Oracle [Identyfikator doc 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne wersje 9,2 i nowsze są obsługiwane we **wszystkich ofertach chmury publicznej** , które `Minimum Technical Requirements` spełniają ich specyficzne (MTR).  Należy utworzyć niestandardowe obrazy, które spełniają specyfikacje MTR dla zgodności aplikacji systemu operacyjnego i oprogramowania. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Obrazy maszyn wirtualnych z programem Oracle WebLogic Server

* **Klastrowanie jest obsługiwane tylko w wersji Enterprise.** Masz licencję na korzystanie z klastrowania WebLogic tylko w przypadku korzystania z serwera WebLogic w wersji Enterprise. Nie należy używać klastrowania z programem WebLogic Server Standard Edition.
* **Multiemisja UDP nie jest obsługiwana.** Platforma Azure obsługuje emisję pojedynczą UDP, ale nie multiemisję ani rozgłaszanie. Serwer WebLogic może polegać na możliwościach emisji pojedynczej protokołu UDP platformy Azure. W celu uzyskania najlepszych wyników polegających na emisji pojedynczej UDP zaleca się, aby rozmiar klastra WebLogic był przechowywany statyczny lub przechowywany bez więcej niż 10 serwerów zarządzanych.
* **Serwer WebLogic oczekuje, że porty publiczne i prywatne mają być takie same dla dostępu T3 (na przykład w przypadku korzystania z usługi Enterprise JavaBeans).** Rozważmy scenariusz wielowarstwowy, w którym aplikacja warstwy usług (EJB) jest uruchomiona w klastrze serwera WebLogic składającym się z co najmniej dwóch maszyn wirtualnych w sieci wirtualnej o nazwie *SLWLS*. Warstwa klienta znajduje się w innej podsieci w tej samej sieci wirtualnej, uruchamiając prosty program języka Java próbujący wywołać EJB w warstwie usług. Ponieważ konieczne jest równoważenie obciążenia warstwy usług, należy utworzyć publiczny punkt końcowy o zrównoważonym obciążeniu dla maszyn wirtualnych w klastrze serwerów WebLogic. Jeśli określony port prywatny różni się od portu publicznego (na przykład 7006:7008), wystąpi błąd, taki jak następujące:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Jest to spowodowane tym, że w przypadku każdego zdalnego dostępu T3 serwer WebLogic oczekuje, że port usługi równoważenia obciążenia i port serwera zarządzanego WebLogic są takie same. W poprzednim przypadku klient uzyskuje dostęp do portu 7006 (Port usługi równoważenia obciążenia), a serwer zarządzany nasłuchuje na 7008 (port prywatny). To ograniczenie dotyczy tylko dostępu T3, a nie protokołu HTTP.

   Aby uniknąć tego problemu, użyj jednego z następujących obejść:

  * Użyj tych samych numerów portów prywatnych i publicznych dla punktów końcowych z równoważeniem obciążenia, które są przeznaczone dla dostępu T3.
  * Podczas uruchamiania serwera WebLogic należy uwzględnić następujący parametr JVM:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Aby uzyskać powiązane informacje, zobacz artykuł **860340,1** w <https://support.oracle.com>bazie wiedzy o.

* **Ograniczenia dynamicznego klastrowania i równoważenia obciążenia.** Załóżmy, że chcesz użyć klastra dynamicznego na serwerze WebLogic i uwidocznić go za pomocą jednego publicznego punktu końcowego ze zrównoważonym obciążeniem na platformie Azure. Można to zrobić tak długo, jak używasz stałego numeru portu dla każdego z serwerów zarządzanych (nie jest to przypisywany dynamicznie z zakresu) i nie uruchamiaj więcej zarządzanych serwerów niż maszyny, które są śledzone przez administratora. Oznacza to, że nie ma więcej niż jednego serwera zarządzanego na maszynę wirtualną. Jeśli konfiguracja powoduje, że serwery WebLogic są uruchamiane dłużej niż maszyny wirtualne (w przypadku wielu wystąpień serwera WebLogic, które współużytkują tę samą maszynę wirtualną), wówczas nie jest możliwe w przypadku więcej niż jednego wystąpienia serwerów WebLogic w celu utworzenia powiązania z danym numerem portu. Inne osoby na tej maszynie wirtualnej zakończą się niepowodzeniem.

   W przypadku skonfigurowania serwera administracyjnego do automatycznego przypisywania unikatowych numerów portów do serwerów zarządzanych, równoważenie obciążenia nie jest możliwe, ponieważ platforma Azure nie obsługuje mapowania z jednego portu publicznego na wiele portów prywatnych, zgodnie z wymaganiami tego skonfigurować.
* **Wiele wystąpień serwera WebLogic na maszynie wirtualnej.** W zależności od wymagań wdrożenia można rozważyć uruchomienie wielu wystąpień serwera WebLogic na tej samej maszynie wirtualnej, jeśli maszyna wirtualna jest wystarczająco duża. Na przykład na maszynie wirtualnej o średnim rozmiarze, która zawiera dwa rdzenie, można uruchomić dwa wystąpienia serwera WebLogic. Jednak nadal zalecamy uniknięcie wprowadzenia pojedynczych punktów awarii do architektury, w przypadku gdy używana jest tylko jedna maszyna wirtualna z wieloma wystąpieniami serwera WebLogic. Korzystanie z co najmniej dwóch maszyn wirtualnych może być lepszym rozwiązaniem, a każda maszyna wirtualna może uruchamiać wiele wystąpień serwera WebLogic. Każde wystąpienie serwera WebLogic może być częścią tego samego klastra. Obecnie nie jest możliwe używanie platformy Azure do równoważenia obciążenia punktów końcowych, które są udostępniane przez takie wdrożenia serwera WebLogic w ramach tej samej maszyny wirtualnej, ponieważ moduł równoważenia obciążenia platformy Azure wymaga dystrybuowania serwerów z równoważeniem obciążenia między unikatowymi Maszyny wirtualne.

## <a name="oracle-jdk-virtual-machine-images"></a>Obrazy maszyn wirtualnych Oracle JDK
* **JDK 6 i 7 najnowszych aktualizacji.** Chociaż zalecamy korzystanie z najnowszej publicznej, obsługiwanej wersji języka Java (obecnie Java 8), platforma Azure udostępnia również obrazy z JDK 6 i 7. Jest to przeznaczone dla starszych aplikacji, które nie są jeszcze gotowe do uaktualnienia do JDK 8. Mimo że aktualizacje poprzednich JDK obrazów mogą nie być już ogólnie dostępne publicznie, biorąc pod partnerstwo firmy Microsoft z firmą Oracle, obrazy JDK 6 i 7 udostępniane przez platformę Azure mają zawierać nowszą, niepubliczną aktualizację, która zwykle jest oferowana przez firmę Oracle do tylko wybrana grupa klientów firmy Oracle. Nowe wersje obrazów JDK będą udostępniane w czasie z zaktualizowanymi wersjami JDK 6 i 7.

   JDK dostępne w obrazach JDK 6 i 7, a maszyny wirtualne i obrazy pochodzące od nich mogą być używane tylko na platformie Azure.
* **64-bitowy JDK.** Obrazy maszyn wirtualnych programu Oracle WebLogic Server i obrazy maszyn wirtualnych Oracle JDK udostępniane przez platformę Azure zawierają 64-bitowe wersje systemów Windows Server i JDK.

## <a name="next-steps"></a>Następne kroki
Teraz masz przegląd bieżących rozwiązań firmy Oracle opartych na obrazach maszyn wirtualnych w Microsoft Azure. Następnym krokiem jest wdrożenie pierwszej bazy danych Oracle Database na platformie Azure.

> [!div class="nextstepaction"]
> [Tworzenie bazy danych Oracle na platformie Azure](oracle-database-quick-create.md)
