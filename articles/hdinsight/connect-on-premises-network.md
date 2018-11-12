---
title: HDInsight nawiązać połączenie z siecią lokalną — Azure HDInsight
description: Dowiedz się, jak utworzyć klaster usługi HDInsight w usłudze Azure Virtual Network, a następnie połącz go z sieci lokalnej. Dowiedz się, jak skonfigurować rozpoznawanie nazw między HDInsight i siecią lokalną za pomocą niestandardowego serwera DNS.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: hrasheed
ms.openlocfilehash: d8ab61d99b617f21557b6f1ef476418e25bcee55
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016934"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Łączenie usługi HDInsight z siecią lokalną

Dowiedz się, jak połączyć HDInsight z siecią lokalną za pomocą usługi Azure Virtual Networks i bramy sieci VPN. Ten dokument zawiera informacje na temat planowania na:

* Używanie HDInsight w sieci wirtualnej platformy Azure, który nawiązuje połączenie z siecią lokalną.

* Konfigurowanie rozpoznawania nazw DNS między sieć wirtualną i siecią lokalną.

* Konfigurowanie grup zabezpieczeń sieci, aby ograniczyć dostęp do Internetu do HDInsight.

* Porty udostępnianego przez usługę HDInsight na sieć wirtualną.

## <a name="create-the-virtual-network-configuration"></a>Utwórz konfigurację sieci wirtualnej

Aby dowiedzieć się, jak utworzyć sieci wirtualnej platformy Azure, która jest połączona z siecią lokalną, należy użyć następujących dokumentów:
    
* [Korzystanie z witryny Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Korzystanie z programu Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Konfigurowanie rozpoznawania nazw

Aby umożliwić HDInsight i zasobów w dołączonym do sieci do komunikowania się według nazwy, należy wykonać następujące czynności:

* Tworzenie niestandardowego serwera DNS w usłudze Azure Virtual Network.

* Konfigurowanie sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast domyślnej Azure cyklicznego programu rozpoznawania nazw.

* Konfigurowanie składnika przesyłanie dalej między niestandardowego serwera DNS i serwerem DNS w środowisku lokalnym.

Ta konfiguracja umożliwia następujące zachowanie:

* Żądania dla w pełni kwalifikowanych nazw domen, które mają sufiks DNS __dla sieci wirtualnej__ są przekazywane do niestandardowego serwera DNS. Niestandardowy serwer DNS przekazuje następnie te żądania na platformie Azure cyklicznego programu rozpoznawania nazw, która zwraca adres IP.

* Wszystkie inne żądania są przekazywane do lokalnego serwera DNS. Nawet żądania publicznych zasobów internetowych, np. microsoft.com są przekazywane do lokalnego serwera DNS do rozpoznawania nazw.

Na poniższym diagramie zielony wiersze są żądania dotyczące zasobów, które kończą się na sufiks DNS w sieci wirtualnej. Niebieskie linie są żądania dotyczące zasobów w sieci lokalnej lub w publicznym Internecie.

![Diagram przedstawiający sposób żądania DNS są rozpoznawane w konfiguracji używane w tym dokumencie](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Tworzenie niestandardowego serwera DNS

> [!IMPORTANT]
> Należy utworzyć i skonfigurować serwer DNS, przed zainstalowaniem HDInsight w sieci wirtualnej.

Do utworzenia maszyny Wirtualnej systemu Linux, który używa [powiązać](https://www.isc.org/downloads/bind/) oprogramowania DNS, wykonaj następujące czynności:

> [!NOTE]
> Następujące kroki użycia [witryny Azure portal](https://portal.azure.com) utworzyć maszynę wirtualną platformy Azure. Inne sposoby tworzenia maszyny wirtualnej na ten temat można znaleźć w następujących dokumentach:
>
> * [Tworzenie maszyny wirtualnej — interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Tworzenie maszyny Wirtualnej — program Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Z [witryny Azure portal](https://portal.azure.com), wybierz opcję __+__, __obliczenia__, i __Ubuntu Server 16.04 LTS__.

    ![Tworzenie maszyny wirtualnej systemu Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. W sekcji __Podstawowe__ wprowadź następujące informacje:

    * __Nazwa__: Przyjazna nazwa, która identyfikuje tę maszynę wirtualną. Na przykład __DNSProxy__.
    * __Nazwa użytkownika__: Nazwa konta SSH.
    * __Klucz publiczny SSH__ lub __hasło__: metodę uwierzytelniania dla konta SSH. Zalecamy używanie kluczy publicznych, ponieważ są one bardziej bezpieczne. Aby uzyskać więcej informacji, zobacz [tworzenia i używania kluczy SSH dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.
    * __Grupa zasobów__: Wybierz __Użyj istniejącej__, a następnie wybierz grupę zasobów, która zawiera utworzonej wcześniej sieci wirtualnej.
    * __Lokalizacja__: Wybierz z tej samej lokalizacji co sieć wirtualna.

    ![Podstawowa konfiguracja maszyny wirtualnej](./media/connect-on-premises-network/vm-basics.png)

    Pozostaw inne wpisy wartości domyślne, a następnie wybierz pozycję __OK__.

3. Z __wybierz rozmiar__ wybierz rozmiar maszyny Wirtualnej. Na potrzeby tego samouczka wybierz opcję najmniejszą i najniższy koszt. Aby kontynuować, należy użyć __wybierz__ przycisku.

4. Z __ustawienia__ sekcji, wprowadź następujące informacje:

    * __Sieć wirtualna__: Wybierz sieć wirtualną, która została utworzona wcześniej.

    * __Podsieci__: Wybierz podsieć domyślna dla sieci wirtualnej. Czy __nie__ Wybierz podsieć, używany przez bramę sieci VPN.

    * __Konto magazynu diagnostyki__: Wybierz istniejące konto magazynu lub Utwórz nową.

    ![Ustawienia sieci wirtualnej](./media/connect-on-premises-network/virtual-network-settings.png)

    Pozostaw inne wpisy wartość domyślną, a następnie wybierz __OK__ aby kontynuować.

5. Z __zakupu__ zaznacz __zakupu__ przycisk, aby utworzyć maszynę wirtualną.

6. Po utworzeniu maszyny wirtualnej jej __Przegląd__ sekcja jest wyświetlana. Wybierz z listy po lewej stronie __właściwości__. Zapisz __publiczny adres IP__ i __prywatny adres IP__ wartości. Będzie używany w następnej sekcji.

    ![Publiczne i prywatne adresy IP](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalowanie i konfigurowanie powiązania (oprogramowanie DNS)

1. Używanie protokołu SSH, aby nawiązać połączenie __publiczny adres IP__ maszyny wirtualnej. Poniższy przykład łączy się z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zastąp `sshuser` przy użyciu konta użytkownika SSH można określić podczas tworzenia klastra.

    > [!NOTE]
    > Istnieją różne sposoby uzyskania `ssh` narzędzia. W systemie Linux, Unix i z systemem macOS jest ona udostępniana jako część systemu operacyjnego. Jeśli używasz Windows należy rozważyć użycie jednej z następujących opcji:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować Bind, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować powiązania do przekazywania żądań rozpoznawania nazw na serwerze DNS w sieci lokalnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.options` pliku:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > Zastąp wartości w `goodclients` części z zakresem adresów IP sieci wirtualnej i siecią lokalną. Ta sekcja definiuje adresy, które ten serwer DNS odbiera z.
    >
    > Zastąp `192.168.0.1` wpis `forwarders` sekcji przy użyciu adresu IP serwera DNS w środowisku lokalnym. Ten wpis kieruje żądania DNS do serwera DNS w środowisku lokalnym do rozpoznawania.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

4. W sesji SSH Użyj następującego polecenia:

    ```bash
    hostname -f
    ```

    To polecenie zwraca wartość podobny do następującego tekstu:

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Tekst jest __sufiks DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

5. Aby skonfigurować powiązania do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Należy zastąpić `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufiksu DNS, pobranym wcześniej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

6. Aby rozpocząć Bind, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby zweryfikować tego powiązania stanie rozpoznawać nazwy zasobów w sieci lokalnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Zastąp `dns.mynetwork.net` z w pełni kwalifikowana nazwa domeny (FQDN) zasobów w sieci lokalnej.
    >
    > Zastąp `10.0.0.4` z __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź jest wyświetlana podobny do następującego tekstu:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do użycia niestandardowego serwera DNS

Aby skonfigurować sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast mechanizmu rozpoznawania cyklicznego platformy Azure, użyj następujących czynności:

1. W [witryny Azure portal](https://portal.azure.com), wybierz sieć wirtualną, a następnie wybierz __serwerów DNS__.

2. Wybierz __niestandardowe__, a następnie wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz pozycję __Zapisz__.

    ![Ustawianie niestandardowego serwera DNS dla sieci](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Konfigurowanie lokalnego serwera DNS

W poprzedniej sekcji należy skonfigurować niestandardowego serwera DNS do przekazywania żądań do serwera DNS w środowisku lokalnym. Następnie należy skonfigurować serwer DNS w środowisku lokalnym, aby przekazywać żądania do niestandardowego serwera DNS.

Aby poznać konkretne kroki dotyczące sposobu konfigurowania serwera DNS zajrzyj do dokumentacji oprogramowania serwera DNS. Sprawdź, aby uzyskać instrukcje dotyczące sposobu konfigurowania __warunkowego przesyłania dalej__.

Warunkowe do przodu tylko przekazuje żądania dotyczące określonego sufiksu DNS. W takim przypadku należy skonfigurować usługę przesyłania dalej dla sufiksu DNS w sieci wirtualnej. Żądania dotyczące ten sufiks powinien być przekazywany do adresu IP, niestandardowego serwera DNS. 

Poniższy tekst znajduje się przykład konfiguracji warunkowego przesyłania dalej dla **powiązać** oprogramowania DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Instrukcje dotyczące korzystania z systemu DNS **systemu Windows Server 2016**, zobacz [DnsServerConditionalForwarderZone Dodaj](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentacji...

Po skonfigurowaniu serwera DNS w środowisku lokalnym, możesz użyć `nslookup` z siecią lokalną, aby zweryfikować, że może rozpoznać nazwy w sieci wirtualnej. Poniższy przykład 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

W tym przykładzie używa lokalnego serwera DNS w 196.168.0.4 rozpoznać nazwę niestandardowego serwera DNS. Zastąp adres IP dla serwera DNS w środowisku lokalnym. Zastąp `dnsproxy` adresu za pomocą w pełni kwalifikowana nazwa domeny niestandardowego serwera DNS.

## <a name="optional-control-network-traffic"></a>Opcjonalne: Ruch sieciowy kontroli

Sieciowe grupy zabezpieczeń (NSG) lub trasy zdefiniowane przez użytkownika (UDR) umożliwia sterowanie ruchem sieciowym. Sieciowe grupy zabezpieczeń umożliwiają filtrowanie ruchu przychodzącego i wychodzącego ruchu sieciowego i blokują lub zezwalają na ruch. Tras zdefiniowanych przez użytkownika umożliwiają kontrolowanie, przepływ ruchu między zasobami w sieci wirtualnej, Internetu i siecią lokalną.

> [!WARNING]
> HDInsight wymaga dostępu przychodzącego z określonych adresów IP w chmurze platformy Azure i nieograniczony dostęp ruchu wychodzącego. Za pomocą sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika kontroli ruchu, należy wykonać następujące czynności:

1. Znajdowanie adresów IP dla lokalizacji, która zawiera sieci wirtualnej. Aby uzyskać listę wymaganych adresów IP według lokalizacji, zobacz [adresy IP wymagane](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Adresy IP w kroku 1, zezwalać na ruch przychodzący z tego adresu IP adresów.

   * Jeśli używasz __sieciowej grupy zabezpieczeń__: Zezwalaj na __dla ruchu przychodzącego__ ruch na porcie __443__ adresów IP.
   * Jeśli używasz __trasy zdefiniowanej przez użytkownika__: Ustaw __następnego skoku__ typ trasy do __Internet__ adresów IP.

Aby uzyskać przykład tworzenia sieciowych grup zabezpieczeń za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz [rozszerzyć HDInsight z usługą Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Tworzenie klastra HDInsight

> [!WARNING]
> Niestandardowego serwera DNS należy skonfigurować przed zainstalowaniem HDInsight w sieci wirtualnej.

Użyj kroków w [Tworzenie klastra usługi HDInsight przy użyciu witryny Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu, aby utworzyć klaster usługi HDInsight.

> [!WARNING]
> * Podczas tworzenia klastra musisz wybrać lokalizację zawierającą sieci wirtualnej.
>
> * W __Zaawansowane ustawienia__ część konfiguracji, należy wybrać sieć wirtualną i podsieć, która została utworzona wcześniej.

## <a name="connecting-to-hdinsight"></a>Nawiązywanie połączenia z HDInsight

Większość dokumentacji w HDInsight przyjęto założenie, że masz dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem https://CLUSTERNAME.azurehdinsight.net. Ten adres używa publicznej bramy, która nie jest dostępna, jeśli używano sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika do ograniczania dostępu z Internetu.

Odwołujący się dokumentacji `headnodehost` podczas nawiązywania połączenia z klastrem w sesji SSH. Ten adres jest dostępny tylko w węzłach w klastrze, a nie jest używany na komputerach klienckich połączonych za pośrednictwem sieci wirtualnej.

Na bezpośrednie łączenie się z HDInsight za pośrednictwem sieci wirtualnej, wykonaj następujące kroki:

1. Aby odnaleźć w wewnętrznej w pełni kwalifikowanych nazw domen węzłów klastra HDInsight, użyj jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Aby określić numer portu, którego usługa jest dostępna w, zobacz [porty używane przez usługi Hadoop w HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]
    > Niektóre usługi do węzłów głównych w serwisie tylko są aktywne na jednym węźle naraz. Jeśli zostanie podjęta, uzyskiwanie dostępu do usługi w jednym węźle głównym i zakończy się niepowodzeniem, przełącz się do innego węzła głównego.
    >
    > Na przykład Ambari jest tylko aktywne na jednego węzła głównego w danym momencie. Jeśli zostanie podjęta, uzyskiwanie dostępu do systemu Ambari na jednego węzła głównego i zwraca błąd 404, następnie działa on w innym węźle głównym.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat używania HDInsight w sieci wirtualnej, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat trasy zdefiniowane przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP](../virtual-network/virtual-networks-udr-overview.md).
