---
title: Planowanie sieci wirtualnej dla usługi Azure HDInsight
description: Dowiedz się, jak zaplanować wdrożenie Virtual Network platformy Azure, aby połączyć usługę HDInsight z innymi zasobami w chmurze lub zasobami w centrum danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 8d89031a3b27742149d450ab79c9febf0aaef1ff
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185626"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planowanie sieci wirtualnej dla usługi Azure HDInsight

Ten artykuł zawiera ogólne informacje dotyczące korzystania z [sieci wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md) za pomocą usługi Azure HDInsight. Omówiono w nim również decyzje dotyczące projektowania i wdrażania, które należy wykonać, aby można było zaimplementować sieć wirtualną dla klastra usługi HDInsight. Po zakończeniu fazy planowania można [utworzyć sieci wirtualne dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md). Aby uzyskać więcej informacji na temat adresów IP zarządzania usługą HDInsight, które są konieczne do poprawnego skonfigurowania sieciowych grup zabezpieczeń i tras zdefiniowanych przez użytkownika, zobacz [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).

Korzystanie z Virtual Network platformy Azure umożliwia wykonywanie następujących scenariuszy:

* Łączenie z usługą HDInsight bezpośrednio z sieci lokalnej.
* Łączenie usługi HDInsight z magazynami danych w sieci wirtualnej platformy Azure.
* Bezpośredni dostęp do usług [Apache Hadoop](https://hadoop.apache.org/) , które nie są publicznie dostępne za pośrednictwem Internetu. Na przykład [Apache Kafka](https://kafka.apache.org/) interfejsy API lub interfejs API platformy [Apache HBase](https://hbase.apache.org/) Java.

> [!IMPORTANT]
> Utworzenie klastra usługi HDInsight w sieci wirtualnej spowoduje utworzenie kilku zasobów sieciowych, takich jak karty sieciowe i moduły równoważenia obciążenia. **Nie** usuwaj tych zasobów sieciowych, ponieważ są one potrzebne do poprawnego działania klastra z siecią wirtualną.
>
> Po 28 lutego 2019, zasobów sieciowych (takich jak karty sieciowe, funty itp.) dla nowych klastrów usługi HDInsight utworzonych w sieci wirtualnej zostanie zainicjowany w tej samej grupie zasobów klastra usługi HDInsight. Wcześniej te zasoby zostały zainicjowane w grupie zasobów sieci wirtualnej. Nie wprowadzono zmian w aktualnie uruchomionych klastrach i tych klastrach utworzonych bez sieci wirtualnej.

## <a name="planning"></a>Planowanie

Poniżej przedstawiono pytania, na które należy odpowiedzieć podczas planowania instalacji usługi HDInsight w sieci wirtualnej:

* Czy musisz zainstalować HDInsight w istniejącej sieci wirtualnej? Lub tworzysz nową sieć?

    Jeśli używasz istniejącej sieci wirtualnej, przed zainstalowaniem usługi HDInsight może zajść potrzeba zmodyfikowania konfiguracji sieci. Aby uzyskać więcej informacji, zobacz sekcję [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](#existingvnet) .

* Czy chcesz połączyć sieć wirtualną zawierającą usługi HDInsight z inną siecią wirtualną lub siecią lokalną?

    Aby łatwo współpracować z zasobami między sieciami, może być konieczne utworzenie niestandardowego DNS i skonfigurowanie przekazywania DNS. Aby uzyskać więcej informacji, zobacz sekcję [łączenie wielu sieci](#multinet) .

* Czy chcesz ograniczyć/przekierować ruch przychodzący lub wychodzący do usługi HDInsight?

    Usługa HDInsight musi mieć nieograniczoną komunikację z określonymi adresami IP w centrum danych platformy Azure. Istnieje również kilka portów, które muszą być dozwolone przez zapory do komunikacji z klientem. Aby uzyskać więcej informacji, zobacz sekcję [sterowanie ruchem sieciowym](#networktraffic) .

## <a id="existingvnet"></a>Dodawanie usługi HDInsight do istniejącej sieci wirtualnej

Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, jak dodać nową usługę HDInsight do istniejącej Virtual Network platformy Azure.

> [!NOTE]  
> Nie można dodać istniejącego klastra usługi HDInsight do sieci wirtualnej.

1. Czy korzystasz z klasycznego modelu wdrażania lub Menedżer zasobów dla sieci wirtualnej?

    Usługa HDInsight 3,4 i nowsze wymagają Menedżer zasobów sieci wirtualnej. Wcześniejsze wersje usługi HDInsight wymagały klasycznej sieci wirtualnej.

    Jeśli istniejąca sieć jest klasyczną siecią wirtualną, należy utworzyć Menedżer zasobów sieci wirtualnej, a następnie połączyć te dwa. [Łączenie klasycznej sieci wirtualnych z nowym sieci wirtualnych](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po dołączeniu Usługa HDInsight zainstalowana w sieci Menedżer zasobów może korzystać z zasobów w sieci klasycznej.

2. Czy używasz sieciowych grup zabezpieczeń, tras zdefiniowanych przez użytkownika lub urządzeń Virtual Network do ograniczania ruchu do sieci wirtualnej czy z niej?

    Usługa HDInsight wymaga nieograniczonego dostępu do kilku adresów IP w centrum danych platformy Azure. Aby umożliwić komunikację z tymi adresami IP, należy zaktualizować wszystkie istniejące sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika.
    
    Usługa HDInsight obsługuje wiele usług, które korzystają z różnych portów. Nie blokuj ruchu do tych portów. Aby uzyskać listę portów dozwolonych przez zapory urządzeń wirtualnych, zobacz sekcję zabezpieczenia.
    
    Aby znaleźć istniejącą konfigurację zabezpieczeń, użyj następujących Azure PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure:

    * Grupy zabezpieczeń sieci

        Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz dokument [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) .

        > [!IMPORTANT]  
        > Reguły sieciowej grupy zabezpieczeń są stosowane w kolejności na podstawie priorytetu reguły. Stosowana jest pierwsza reguła zgodna ze wzorcem ruchu, a żadne inne nie są stosowane do tego ruchu. Kolejność reguł od najmniejszej do najmniej ograniczającej. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu grup zabezpieczeń sieci](../virtual-network/security-overview.md) .

    * Trasy definiowane przez użytkownika

        Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozwiązywania problemów z trasami](../virtual-network/diagnose-network-routing-problem.md) .

3. Utwórz klaster usługi HDInsight i wybierz Virtual Network platformy Azure podczas konfiguracji. Aby zrozumieć proces tworzenia klastra, wykonaj kroki opisane w następujących dokumentach:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Portal)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)
    * [Tworzenie usługi HDInsight przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Tworzenie usługi HDInsight przy użyciu szablonu Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Dodawanie usługi HDInsight do sieci wirtualnej to opcjonalny krok konfiguracji. Pamiętaj, aby podczas konfigurowania klastra wybrać sieć wirtualną.

## <a id="multinet"></a>Łączenie wielu sieci

Największym wyzwaniem z konfiguracją wielosieciową jest rozpoznawanie nazw między sieciami.

Platforma Azure udostępnia rozpoznawanie nazw dla usług platformy Azure, które są zainstalowane w sieci wirtualnej. To wbudowane rozwiązanie do rozpoznawania nazw umożliwia usłudze HDInsight łączenie się z następującymi zasobami przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN):

* Wszystkie zasoby, które są dostępne w Internecie. Na przykład microsoft.com, windowsupdate.com.

* Wszystkie zasoby znajdujące się w tym samym Virtual Network platformy Azure przy użyciu __wewnętrznej nazwy DNS__ zasobu. Na przykład podczas korzystania z domyślnego rozpoznawania nazw, poniżej przedstawiono przykłady wewnętrznych nazw DNS przypisanych do węzłów procesu roboczego usługi HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba węzły mogą komunikować się bezpośrednio ze sobą oraz z innymi węzłami w usłudze HDInsight przy użyciu wewnętrznych nazw DNS.

Domyślne rozpoznawanie nazw __nie zezwala usłudze HDInsight na rozpoznawanie__ nazw zasobów w sieciach, które są przyłączone do sieci wirtualnej. Na przykład często można przyłączyć sieć lokalną do sieci wirtualnej. Tylko w przypadku domyślnego rozpoznawania nazw Usługa HDInsight nie ma dostępu do zasobów w sieci lokalnej według nazwy. Przeciwieństwem jest również wartość true, zasoby w sieci lokalnej nie mogą uzyskać dostępu do zasobów w sieci wirtualnej według nazwy.

> [!WARNING]  
> Należy utworzyć niestandardowy serwer DNS i skonfigurować sieć wirtualną tak, aby korzystała z niej przed utworzeniem klastra usługi HDInsight.

Aby włączyć rozpoznawanie nazw między siecią wirtualną i zasobami w połączonych sieciach, należy wykonać następujące czynności:

1. Utwórz niestandardowy serwer DNS w Virtual Network platformy Azure, w którym planujesz zainstalować usługę HDInsight.

2. Skonfiguruj sieć wirtualną tak, aby korzystała z niestandardowego serwera DNS.

3. Znajdź sufiks DNS przypisany przez platformę Azure dla sieci wirtualnej. Ta wartość jest podobna do `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Informacje dotyczące znajdowania sufiksu DNS znajdują się w sekcji [przykład: Custom DNS](hdinsight-create-virtual-network.md#example-dns) .

4. Skonfiguruj przekazywanie między serwerami DNS. Konfiguracja zależy od typu sieci zdalnej.

   * Jeśli sieć zdalna jest siecią lokalną, skonfiguruj system DNS w następujący sposób:
        
     * __Niestandardowy serwer DNS__ (w sieci wirtualnej):

         * Przekazuj żądania dotyczące sufiksu DNS sieci wirtualnej do programu rozpoznawania cyklicznego Azure (168.63.129.16). Platforma Azure obsługuje żądania dotyczące zasobów w sieci wirtualnej

         * Przekazuj wszystkie inne żądania do lokalnego serwera DNS. Lokalna usługa DNS obsługuje wszystkie inne żądania rozpoznawania nazw, nawet żądania dotyczące zasobów internetowych, takich jak Microsoft.com.

     * __Lokalna usługa DNS__: przekazuj żądania dla sufiksu DNS sieci wirtualnej do NIESTANDARDOWEGO serwera DNS. Niestandardowy serwer DNS przekazuje następnie do programu rozpoznawania cyklicznego Azure.

       Ta konfiguracja kieruje żądania dla w pełni kwalifikowanych nazw domen, które zawierają sufiks DNS sieci wirtualnej do niestandardowego serwera DNS. Wszystkie inne żądania (nawet dla publicznych adresów internetowych) są obsługiwane przez lokalny serwer DNS.

   * Jeśli sieć zdalna jest kolejną Virtual Network platformy Azure, skonfiguruj system DNS w następujący sposób:

     * __Niestandardowy serwer DNS__ (w każdej sieci wirtualnej):

         * Żądania dotyczące sufiksu DNS sieci wirtualnych są przekazywane do niestandardowych serwerów DNS. System DNS w każdej sieci wirtualnej jest odpowiedzialny za rozwiązywanie zasobów w sieci.

         * Przekazuj wszystkie inne żądania do programu rozpoznawania cyklicznego Azure. Program rozpoznawania cyklicznego jest odpowiedzialny za rozwiązywanie zasobów lokalnych i internetowych.

       Serwer DNS dla każdej sieci przekazuje żądania do drugiego, na podstawie sufiksu DNS. Inne żądania są rozwiązywane przy użyciu programu rozpoznawania cyklicznego Azure.

     Przykład każdej konfiguracji można znaleźć w sekcji [przykład: Custom DNS](hdinsight-create-virtual-network.md#example-dns) .

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Połącz się bezpośrednio z usługami Apache Hadoop Services

Możesz połączyć się z klastrem w `https://CLUSTERNAME.azurehdinsight.net`. Ten adres używa publicznego adresu IP, który może nie być osiągalny, jeśli używasz sieciowych grup zabezpieczeń, aby ograniczyć ruch przychodzący z Internetu. Ponadto podczas wdrażania klastra w sieci wirtualnej można uzyskać do niego dostęp przy użyciu prywatnego punktu końcowego `https://CLUSTERNAME-int.azurehdinsight.net`. Ten punkt końcowy jest rozpoznawany jako prywatny adres IP w sieci wirtualnej na potrzeby dostępu do klastra.

Aby nawiązać połączenie z usługą Apache Ambari i innymi stronami sieci Web za pomocą sieci wirtualnej, wykonaj następujące czynności:

1. Aby odnaleźć wewnętrzne w pełni kwalifikowane nazwy domen (FQDN) węzłów klastra usługi HDInsight, użyj jednej z następujących metod:

    Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na liście zwracanych węzłów Znajdź nazwę FQDN węzłów głównych i Użyj nazw FQDN, aby nawiązać połączenie z usługą Ambari i innymi usługami sieci Web. Na przykład użyj `http://<headnode-fqdn>:8080`, aby uzyskać dostęp do Ambari.

    > [!IMPORTANT]  
    > Niektóre usługi hostowane w węzłach głównych są aktywne tylko na jednym węźle naraz. Jeśli spróbujesz uzyskać dostęp do usługi w jednym węźle głównym i zwróci błąd 404, przełącz się do drugiego węzła głównego.

2. Aby określić węzeł i port, na którym usługa jest dostępna, zobacz [porty używane przez usługi Hadoop w dokumencie HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

## <a id="networktraffic"></a>Kontrolowanie ruchu sieciowego

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniki kontrolowania ruchu przychodzącego i wychodzącego do klastrów usługi HDInsight

Ruch sieciowy w sieciach wirtualnych platformy Azure można kontrolować przy użyciu następujących metod:

* **Sieciowe grupy zabezpieczeń** (sieciowej grupy zabezpieczeń) umożliwiają filtrowanie ruchu przychodzącego i wychodzącego do sieci. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu grup zabezpieczeń sieci](../virtual-network/security-overview.md) .

* **Wirtualne urządzenia sieciowe** (urządzenie WUS) mogą być używane tylko z ruchem wychodzącym. Urządzeń WUS replikację funkcji urządzeń, takich jak zapory i routery. Aby uzyskać więcej informacji, zobacz dokument [urządzenia sieciowe](https://azure.microsoft.com/solutions/network-appliances) .

Usługa HDInsight wymaga nieograniczonego dostępu do kondycji usługi HDInsight i usług zarządzania zarówno dla ruchu przychodzącego, jak i wychodzącego z sieci wirtualnej. W przypadku korzystania z sieciowych grup zabezpieczeń należy upewnić się, że te usługi mogą nadal komunikować się z klastrem HDInsight.

![Diagram obiektów usługi HDInsight utworzonych w niestandardowej sieci wirtualnej platformy Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight z sieciowymi grupami zabezpieczeń

Jeśli planujesz używać **grup zabezpieczeń sieci** do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

1. Określ region platformy Azure, który ma być używany przez usługę HDInsight.

2. Zidentyfikuj Tagi usługi wymagane przez usługę HDInsight dla danego regionu. Aby uzyskać więcej informacji, zobacz [Tagi usług sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight](hdinsight-service-tags.md).

3. Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight.

    * __Sieciowe grupy zabezpieczeń__: Zezwalaj na ruch __przychodzący__ na porcie __443__ z adresów IP. Dzięki temu usługi HDInsight Management Services mogą dotrzeć do klastra spoza sieci wirtualnej.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Kontrolowanie ruchu wychodzącego z klastrów usługi HDInsight

Aby uzyskać więcej informacji na temat sterowania ruchem wychodzącym z klastrów usługi HDInsight, zobacz [Konfigurowanie ograniczenia ruchu sieciowego wychodzącego dla klastrów Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premise"></a>Wymuszone tunelowanie do środowiska lokalnego

Wymuszone tunelowanie jest konfiguracją routingu zdefiniowaną przez użytkownika, w której cały ruch z podsieci jest wymuszany w określonej sieci lub lokalizacji, na przykład w sieci lokalnej. Usługa HDInsight __nie obsługuje__ wymuszonego tunelowania ruchu do sieci lokalnych. 

## <a id="hdinsight-ip"></a>Wymagane adresy IP

Jeśli używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika do kontrolowania ruchu, zobacz [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a>Wymagane porty

Jeśli planujesz używanie **zapory** i dostęp do klastra z zewnątrz na określonych portach, może być konieczne zezwolenie na ruch na tych portach wymaganych przez ten scenariusz. Domyślnie nie jest wymagana żadna specjalna listy dozwolonych portów, tak długo, jak ruch związany z zarządzaniem platformy Azure opisany w poprzedniej sekcji może dotrzeć do klastra na porcie 443.

Aby uzyskać listę portów dla określonych usług, zobacz [porty używane przez usługi Apache Hadoop Services w usłudze HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

Aby uzyskać więcej informacji na temat reguł zapory dla urządzeń wirtualnych, zobacz dokument [scenariusza wirtualnego urządzenia](../virtual-network/virtual-network-scenario-udr-gw-nva.md) .

## <a name="load-balancing"></a>Równoważenie obciążenia

Podczas tworzenia klastra usługi HDInsight jest również tworzony moduł równoważenia obciążenia. Typ tego modułu równoważenia obciążenia jest na [poziomie podstawowej jednostki SKU](../load-balancer/load-balancer-overview.md#skus) , który ma pewne ograniczenia. Jedno z tych ograniczeń polega na tym, że w przypadku dwóch sieci wirtualnych w różnych regionach nie można nawiązać połączenia z podstawowymi usługami równoważenia obciążenia. Zobacz [często zadawane pytania dotyczące usługi Virtual Networks: ograniczenia dotyczące globalnej komunikacji równorzędnej sieci](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)wirtualnej, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

* Aby zapoznać się z przykładami kodu i przykłady tworzenia sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* Aby zapoznać się z kompleksowym przykładem konfigurowania usługi HDInsight w celu nawiązania połączenia z siecią lokalną, zobacz [łączenie usługi HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby skonfigurować klastry Apache HBase w sieciach wirtualnych platformy Azure, zobacz [Tworzenie klastrów Apache HBase w usłudze HDInsight na platformie azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Aby skonfigurować replikację geograficzną Apache HBase, zobacz [Konfigurowanie replikacji klastra Apache HBase w sieciach wirtualnych platformy Azure](hbase/apache-hbase-replication.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../virtual-network/security-overview.md).
* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).
