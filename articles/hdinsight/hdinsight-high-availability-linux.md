---
title: Wysoka dostępność dla platformy Hadoop — Azure HDInsight
description: Dowiedz się, jak klastry HDInsight poprawy niezawodności i dostępności przy użyciu dodatkowego węzła głównego. Dowiedz się, jak ma to wpływ na usług Hadoop, takich jak Ambari i Hive, oraz jak połączyć osobno do każdego węzła głównego przy użyciu protokołu SSH.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
keywords: Wysoka dostępność usługi hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 89878b2774727d49d81ebec4c2a3c2cee355d8e8
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743667"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostępność i niezawodność klastrów Apache Hadoop w HDInsight

Klastry HDInsight zapewniają dwa węzły główne, aby zwiększyć dostępność i niezawodność usługi Apache Hadoop i uruchamianie zadań.

Hadoop osiąga wysoką dostępność i niezawodność przez replikowanie usług i danych w wielu węzłach w klastrze. Jednak standardowymi dystrybucjami platformy hadoop zwykle mają tylko jeden węzeł główny. Ewentualnej awarii jednego węzła głównego może spowodować nieoczekiwane zatrzymanie działania klastra. HDInsight dostarcza dwóch węzłów głównych, aby zwiększyć dostępność i niezawodność usługi Hadoop.

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="availability-and-reliability-of-nodes"></a>Dostępność i niezawodność węzłów

Węzły w klastrze usługi HDInsight są implementowane za pomocą usługi Azure Virtual Machines. W poniższych sekcjach omówiono typy poszczególnych węzłów, używane z HDInsight. 

> [!NOTE]  
> Nie wszystkie typy węzłów są używane dla typu klastra. Na przykład typ klastra usługi Hadoop nie ma żadnych węzłów Nimbus. Aby uzyskać więcej informacji na węzłach używane przez typy klastrów HDInsight, zobacz sekcję typy klastrów [opartych na systemie Linux z Tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentu.

### <a name="head-nodes"></a>Węzły główne

Aby zapewnić wysoką dostępność usług Hadoop, HDInsight udostępnia dwa węzły główne. Zarówno węzły główne są jednocześnie aktywności i działania w ramach klastra HDInsight. Niektórych usług, takich jak Apache system plików HDFS lub Apache Hadoop YARN, są aktywne, w jednym węźle głównym tylko w danym momencie. Innych usług, takich jak usługi HiveServer2 lub Hive magazynu metadanych są aktywne na obu węzłów głównych w tym samym czasie.

Węzły główne (i innych węzłów w HDInsight) ma wartość numeryczną jako część nazwy hosta węzła. Na przykład `hn0-CLUSTERNAME` lub `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> Nie należy kojarzyć wartość liczbową z tego, czy węzeł jest podstawowy lub pomocniczy. Wartość liczbowa tylko jest obecna, aby podać unikatową nazwę dla każdego węzła.

### <a name="nimbus-nodes"></a>Węzły Nimbus

Węzły nimbus są dostępne przy użyciu klastrów Apache Storm. Węzły Nimbus zapewniają funkcje podobne do Hadoop JobTracker, dystrybucja i monitorując przetwarzania w węzłach procesu roboczego. HDInsight obejmuje dwa węzły Nimbus klastrów Storm

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper węzłów

[Dozorcy](https://zookeeper.apache.org/) węzły są używane na potrzeby lidera głównego usług na węzły główne. Są one także używane do upewnij się, że usługi, węzły danych (proces roboczy) i bram wiedzą, w których węzłem głównym usługi jest aktywna na. Domyślnie HDInsight udostępnia trzy węzły dozorcy.

### <a name="worker-nodes"></a>Węzły procesu roboczego

Po przesłaniu zadania do klastra, węzłów procesu roboczego wykonaj analiz danych rzeczywistych. W przypadku awarii węzła procesu roboczego zadania, którą on wykonywał jest przesyłany do innego węzła procesu roboczego. Domyślnie HDInsight tworzy cztery węzły procesu roboczego. Możesz zmienić tę wartość do własnych potrzeb, podczas i po utworzeniu klastra.

### <a name="edge-node"></a>Węzeł brzegowy

Węzeł krawędzi aktywnie uczestniczy w analizy danych w klastrze. Jest on używany przez deweloperów i analityków danych, podczas pracy z usługą Hadoop. W węźle brzegowym znajduje się w tej samej sieci wirtualnej platformy Azure, co pozostałe węzły w klastrze i można uzyskać dostęp do innych węzłów. W węźle brzegowym może służyć bez przełączania zasoby krytycznych usług Hadoop lub zadania analizy.

Obecnie usługi ML w HDInsight jest jedynym typem klastra, który zawiera węzeł krawędzi domyślnie. Usługi ML w HDInsight węzła krawędzi jest używany kod testu R lokalnie w węźle przed przesłaniem jej do klastra na potrzeby przetwarzania rozproszonego.

Aby uzyskać informacji na temat korzystania z węzła krawędzi przy użyciu innych typów klastrów, zobacz [używanie węzłów krawędzi w HDInsight](hdinsight-apps-use-edge-node.md) dokumentu.

## <a name="accessing-the-nodes"></a>Uzyskiwanie dostępu do węzłów

Dostęp do klastra za pośrednictwem Internetu znajduje się za pośrednictwem bramy sieci publicznej. Dostęp jest ograniczony do nawiązywania połączenia z węzłami głównymi i (jeśli istnieje) w węźle brzegowym. Dostęp do usług działających na węzły główne nie odbywa się przez wiele węzłów głównych. Publicznej bramy kieruje żądania do węzła głównego, który jest hostem żądanej usługi. Na przykład jeśli Apache Ambari aktualnie znajduje się w pomocniczym węzłem głównym, brama kieruje żądania przychodzące dla narzędzia Ambari do tego węzła.

Dostęp za pośrednictwem publicznej bramy jest ograniczony do portu 443 (HTTPS), 22 i 23.

* Port __443__ umożliwia dostęp do systemu Ambari i innymi aplikacjami internetowymi interfejsu użytkownika lub interfejsów API REST w serwisie węzłów głównych.

* Port __22__ umożliwia dostęp z podstawowym węzłem głównym lub węzłem krawędzi za pomocą protokołu SSH.

* Port __23__ umożliwia dostęp z pomocniczym węzłem głównym przy użyciu protokołu SSH. Na przykład `ssh username@mycluster-ssh.azurehdinsight.net` nawiązanie połączenia z podstawowym węzłem głównym klastra o nazwie **mycluster**.

Aby uzyskać więcej informacji na temat korzystania z protokołu SSH, zobacz [użycia protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Wewnętrzne w pełni kwalifikowanych nazw domen (FQDN)

Węzły w klastrze usługi HDInsight mają, wewnętrzny adres IP i nazwy FQDN, która może zostać oceniony jedynie z klastra. Podczas uzyskiwania dostępu do usług w klastrze przy użyciu wewnętrznego adresu nazwy FQDN lub adres IP, należy użyć narzędzia Ambari Weryfikacja adresu IP lub FQDN, do użycia podczas uzyskiwania dostępu do usługi.

Na przykład usługa Apache Oozie można uruchomić tylko na jednym węzłem głównym i przy użyciu `oozie` polecenie w sesji SSH wymaga adresu URL do usługi. Ten adres URL można pobrać z systemu Ambari za pomocą następującego polecenia:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

To polecenie zwróci wartość podobne do następujące polecenie, które zawiera wewnętrzny adres URL za pomocą `oozie` polecenia:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Aby uzyskać więcej informacji na temat pracy z interfejsem API REST Ambari, zobacz [monitorowanie i zarządzanie HDInsight przy użyciu interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Uzyskiwanie dostępu do innych typów węzła

Możesz połączyć węzły, które nie są dostępne bezpośrednio przez internet przy użyciu następujących metod:

* **SSH**: Po nawiązaniu połączenia z węzłem głównym przy użyciu protokołu SSH, możesz następnie używanie protokołu SSH z węzłem głównym, połączyć się z innych węzłów w klastrze. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH tunelu**: Jeśli potrzebujesz dostępu do usługi sieci web hostowanych na jednym z węzłów, które nie są połączone z Internetem, należy użyć tunelu SSH. Aby uzyskać więcej informacji, zobacz [tunelu SSH za pomocą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

* **Usługa Azure Virtual Network**: Jeśli klastra usługi HDInsight jest częścią usługi Azure Virtual Network, dowolnego zasobu na tej samej sieci wirtualnej można uzyskać dostęp do wszystkich węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="how-to-check-on-a-service-status"></a>Jak sprawdzić stan usługi

Aby sprawdzić stan usług korzystających z węzłami głównymi, użyj Interfejsu sieci Web Ambari lub interfejs API REST Ambari.

### <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web systemu Ambari

Interfejs użytkownika sieci Web Ambari będzie widoczny w https://CLUSTERNAME.azurehdinsight.net. Zastąp ciąg **CLUSTERNAME** nazwą klastra. Po wyświetleniu monitu wprowadź poświadczenia użytkownika protokołu HTTP dla klastra. Domyślna nazwa użytkownika protokołu HTTP jest **administratora** , a hasło to hasło wprowadzone podczas tworzenia klastra.

Gdy zostanie wyświetlony na stronie narzędzia Ambari, zainstalowanych usług będą wyświetlane w lewej części strony.

![Zainstalowanych usług](./media/hdinsight-high-availability-linux/services.png)

Istnieje szereg ikony, które mogą być wyświetlane obok usługi w celu wskazania stanu. Wszystkie alerty związane z usługą mogą być wyświetlane za pomocą **alerty** widocznego u góry strony. Można wybrać poszczególnych usług, aby wyświetlić więcej informacji na nim.

Gdy strona usługi zawiera informacje dotyczące stanu i konfiguracji poszczególnych usług, nie zapewnia informacje, na które węzłem usługa jest uruchomiona na. Aby wyświetlić te informacje, należy użyć **hosty** widocznego u góry strony. Ta strona wyświetla hosty w klastrze, w tym dla węzłów głównych.

![listy hostów](./media/hdinsight-high-availability-linux/hosts.png)

Wybierając link dla jednego z węzłów głównych Wyświetla usług i składników działających w tym węźle.

![Stan składnika](./media/hdinsight-high-availability-linux/nodeservices.png)

Aby uzyskać więcej informacji na temat korzystania z narzędzia Ambari, zobacz [monitorowanie i zarządzanie nimi przy użyciu interfejsu użytkownika sieci Web Ambari Apache HDInsight](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Interfejs API REST systemu Ambari

Interfejs API REST Ambari, jest dostępna za pośrednictwem Internetu. Publicznej bramy HDInsight obsługuje kierowania żądań do węzła głównego, które obecnie obsługuje interfejs API REST.

Aby sprawdzić stan usługi za pomocą interfejsu API REST Ambari, można użyć następującego polecenia:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Zastąp **hasło** przy użyciu hasła konta użytkownika (administratora) protokołu HTTP.
* Zamień ciąg **CLUSTERNAME** na nazwę klastra.
* Zastąp **SERVICENAME** nazwą usługi, aby sprawdzić stan.

Na przykład, aby sprawdzić stan **HDFS** usługi w klastrze o nazwie **mycluster**, używając hasła **hasła**, należy użyć następującego polecenia:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Odpowiedź jest podobna do następujące dane JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Adres URL informuje NAS, że obecnie usługa działa w węźle głównym o nazwie **hn0 CLUSTERNAME**.

Stan informuje NAS, że usługa jest obecnie uruchomiona, lub **uruchomiono**.

Jeśli nie wiesz, jakie usługi są zainstalowane w klastrze, służy następujące polecenie do pobierania listy:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Aby uzyskać więcej informacji na temat pracy z interfejsem API REST Ambari, zobacz [monitorowanie i zarządzanie HDInsight przy użyciu interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Składniki usługi

Usługi mogą zawierać składniki, które chcesz sprawdzić stan indywidualnie. Na przykład system plików HDFS zawiera składnik NameNode. Aby wyświetlić informacje dotyczące składnika, polecenie będzie:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Jeśli nie wiesz, jakie składniki są dostarczane przez usługę, służy następujące polecenie do pobierania listy:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak uzyskać dostęp do plików dziennika na węzły główne

### <a name="ssh"></a>Protokół SSH

Po nawiązaniu połączenia z węzłem głównym za pośrednictwem protokołu SSH, pliki dziennika znajdują się w obszarze **/var/log**. Na przykład **/var/log/hadoop-yarn/yarn** zawierać dzienniki usługi YARN.

Każdy węzeł główny może mieć wpisy dziennika unikatowy, więc należy sprawdzić dzienniki na obu.

### <a name="sftp"></a>SFTP

Możesz również nawiązać połączenie z węzłem za pomocą protokołu SSH File Transfer Protocol lub Secure File Transfer Protocol (SFTP) i bezpośredniego pobrania plików dziennika.

Podobnie jak przy użyciu klienta SSH, gdy łączenie z klastrem należy podać nazwę konta użytkownika SSH i adres SSH klastra. Na przykład `sftp username@mycluster-ssh.azurehdinsight.net`. Podaj hasło dla konta, po wyświetleniu monitu lub podaj klucza publicznego przy użyciu `-i` parametru.

Po nawiązaniu połączenia zostanie wyświetlony `sftp>` wiersza. Z wiersza polecenia, można zmienić katalogi, przekazywanie i pobieranie plików. Na przykład poniższe polecenia Zmień katalog na **/var/log/hadoop/hdfs** katalogu, a następnie Pobierz wszystkie pliki w katalogu.

    cd /var/log/hadoop/hdfs
    get *

Aby uzyskać listę dostępnych poleceń, wpisz `help` na `sftp>` wiersza.

> [!NOTE]  
> Dostępne są także graficznych interfejsów, które umożliwiają wizualizację systemu plików w przypadku połączenia przy użyciu protokołu SFTP. Na przykład [MobaXTerm](https://mobaxterm.mobatek.net/) pozwala przeglądać systemu plików, przy użyciu interfejsem podobnym do Eksploratora Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Aby uzyskać dostęp do plików dziennika przy użyciu narzędzia Ambari, należy użyć tunelu SSH. Interfejsy sieci web dla poszczególnych usług nie są widoczne publicznie w Internecie. Aby uzyskać informacje na temat używania tunelu SSH, zobacz [użycie tunelowania SSH](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Z poziomu interfejsu użytkownika sieci Web Ambari wybierz usługę, którą chcesz wyświetlić dzienniki (na przykład, YARN). Następnie użyj **szybkich łączy** które węzła głównego, aby wyświetlić dzienniki, aby wybrać.

![Aby wyświetlić dzienniki przy użyciu szybkich łączy](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Jak skonfigurować rozmiar węzła

Rozmiar węzła można wybrać tylko podczas tworzenia klastra. Można znaleźć listę dostępnych rozmiarów maszyn wirtualnych różnych HDInsight na [HDInsight stronę z cennikiem](https://azure.microsoft.com/pricing/details/hdinsight/).

Podczas tworzenia klastra, można określić rozmiar węzłów. Poniższe informacje znajdują się wskazówki dotyczące sposobu określania rozmiaru, za pomocą [witryny Azure portal][preview-portal], [programu Azure PowerShell][azure-powershell]i [Klasycznego wiersza polecenia platformy azure][azure-cli]:

* **Witryna Azure portal**: Podczas tworzenia klastra, można ustawić rozmiar węzłów, używane przez klaster:

    ![Obraz przedstawiający Kreatora tworzenia klastra przy użyciu wybór rozmiaru węzła](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Klasyczny interfejs wiersza polecenia Azure**: Korzystając z `azure hdinsight cluster create` polecenia, należy określić rozmiar head, procesu roboczego i węzły dozorcy przy użyciu `--headNodeSize`, `--workerNodeSize`, i `--zookeeperNodeSize` parametrów.

* **Program Azure PowerShell**: Korzystając z `New-AzureRmHDInsightCluster` polecenia cmdlet, należy określić rozmiar head, procesu roboczego i węzły dozorcy przy użyciu `-HeadNodeVMSize`, `-WorkerNodeSize`, i `-ZookeeperNodeSize` parametrów.

## <a name="next-steps"></a>Kolejne kroki

Użyj następujących łączy, aby dowiedzieć się więcej na temat czynności opisane w tym dokumencie.

* [Dokumentacja programu Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure Classic](../cli-install-nodejs.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Zarządzanie HDInsight przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Obsługa administracyjna klastrów HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
