---
title: Konfigurowanie grup dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure — Linux Virtual Machines | Microsoft Docs
description: Dowiedz się więcej o konfigurowaniu wysokiej dostępności w środowisku klastra RHEL i konfigurowaniu STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: e48f41f1e13346f551f1d83f462de697631d1b9a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775382"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Samouczek: Konfigurowanie grup dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure 

> [!NOTE]
> Przedstawiony samouczek jest w **publicznej wersji zapoznawczej**. 
>
> W tym samouczku używamy SQL Server 2017 z RHEL 7,6, ale można użyć SQL Server 2019 w RHEL 7 lub RHEL 8, aby skonfigurować HA. Polecenia służące do konfigurowania zasobów grupy dostępności zostały zmienione w RHEL 8. Aby zapoznać się z artykułem, [Utwórz zasób grupy dostępności](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) i zasoby RHEL 8, aby uzyskać więcej informacji na temat prawidłowych poleceń.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie nowej grupy zasobów, zestawu dostępności i usługi Azure Linux Virtual Machines (VM)
> - Włącz wysoką dostępność (HA)
> - Tworzenie klastra Pacemaker
> - Konfigurowanie Agenta ogrodzenia przez utworzenie urządzenia STONITH
> - Instalowanie SQL Server i narzędzi MSSQL w systemie RHEL
> - Skonfiguruj zawsze włączona Grupa dostępności SQL Server
> - Konfigurowanie zasobów grupy dostępności (AG) w klastrze Pacemaker
> - Testowanie trybu failover i Agenta ogrodzenia

Ten samouczek umożliwia wdrażanie zasobów na platformie Azure przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli masz więcej niż jedną subskrypcję, [Ustaw subskrypcję](/cli/azure/manage-azure-subscriptions-azure-cli) , do której chcesz wdrożyć te zasoby.

Użyj następującego polecenia, aby utworzyć grupę zasobów `<resourceGroupName>` w regionie. Zastąp `<resourceGroupName>` wybraną nazwą. Używamy `East US 2` w tym samouczku. Aby uzyskać więcej informacji, zobacz poniższy [Przewodnik Szybki Start](../quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Następnym krokiem jest utworzenie zestawu dostępności. Uruchom następujące polecenie w Azure Cloud Shell i Zastąp `<resourceGroupName>` nazwą grupy zasobów. Wybierz nazwę `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Po zakończeniu wykonywania polecenia należy uzyskać następujące wyniki:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Tworzenie maszyn wirtualnych RHEL wewnątrz zestawu dostępności

> [!WARNING]
> Jeśli wybierzesz obraz RHEL z opcją płatność zgodnie z rzeczywistym użyciem i skonfigurujesz wysoką dostępność (HA), może być konieczne zarejestrowanie subskrypcji. Może to spowodować dwukrotne zapłacenie subskrypcji, ponieważ zostanie naliczona opłata za subskrypcję usługi Microsoft Azure RHEL dla maszyny wirtualnej i subskrypcję usługi Red Hat. Aby uzyskać więcej informacji, zobacz https://access.redhat.com/solutions/2458541.
>
> Aby uniknąć "podwójnego rozliczania", Użyj obrazu RHEL HA podczas tworzenia maszyny wirtualnej platformy Azure. Obrazy oferowane jako obrazy RHEL-HA to również obrazy PAYG z wstępnie włączonym repozytorium HA.

1. Pobierz listę obrazów maszyn wirtualnych, które oferują RHEL o wysokiej dostępności:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    Na potrzeby tego samouczka wybieramy obraz `RedHat:RHEL-HA:7.6:7.6.2019062019`.

    > [!IMPORTANT]
    > Aby można było skonfigurować grupę dostępności, nazwy maszyn muszą mieć mniej niż 15 znaków. Nazwa użytkownika nie może zawierać wielkich liter, a hasła muszą składać się z więcej niż 12 znaków.

1. Chcemy utworzyć 3 maszyny wirtualne w zestawie dostępności. W poniższym poleceniu Zastąp następujące polecenie:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` — przykładem może być "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Powyższe polecenie tworzy maszyny wirtualne i tworzy domyślną sieć wirtualną dla tych maszyn wirtualnych. Aby uzyskać więcej informacji o różnych konfiguracjach, zobacz [AZ VM Create](https://docs.microsoft.com/cli/azure/vm) article.

Po zakończeniu wykonywania polecenia dla każdej maszyny wirtualnej powinny zostać wyświetlone wyniki podobne do następujących:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Domyślny obraz tworzony za pomocą powyższego polecenia domyślnie tworzy dysk o 32 GB systemu operacyjnego. W przypadku tej instalacji domyślnej można było za mało miejsca. Do powyższego polecenia `az vm create` można użyć następującego parametru, aby utworzyć dysk systemu operacyjnego z 128 GB na przykład: `--os-disk-size-gb 128`.
>
> Następnie można [skonfigurować Menedżera woluminów logicznych (LVM)](../../../virtual-machines/linux/configure-lvm.md) , jeśli konieczne jest rozwinięcie odpowiednich woluminów folderu w celu zaspokojenia instalacji.

### <a name="test-connection-to-the-created-vms"></a>Test connection utworzonych maszyn wirtualnych

Połącz się z usługą VM1 lub innymi maszynami wirtualnymi przy użyciu następującego polecenia w Azure Cloud Shell. Jeśli nie możesz znaleźć adresów IP maszyn wirtualnych, postępuj zgodnie z tym [przewodnikiem Szybki Start na Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

W przypadku pomyślnego nawiązania połączenia powinny zostać wyświetlone następujące dane wyjściowe reprezentujące terminal systemu Linux:

```output
[<username>@<VM1> ~]$
```

Wpisz `exit`, aby opuścić sesję SSH.

## <a name="enable-high-availability"></a>Włącz tryb wysokiej dostępności

> [!IMPORTANT]
> Aby ukończyć tę część samouczka, musisz mieć subskrypcję usługi RHEL i dodatek o wysokiej dostępności. Jeśli używasz obrazu zalecanego w poprzedniej sekcji, nie musisz rejestrować kolejnej subskrypcji.
 
Połącz się z każdym węzłem maszyny wirtualnej i postępuj zgodnie z poniższym przewodnikiem, aby włączyć HA. Aby uzyskać więcej informacji, zobacz [Włączanie subskrypcji wysokiej dostępności dla RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Będzie to łatwiejsze, jeśli otworzysz sesję SSH do każdej z maszyn wirtualnych jednocześnie, ponieważ te same polecenia będą musiały zostać uruchomione na poszczególnych maszynach wirtualnych w całym artykule.
>
> Jeśli kopiujesz i wklejasz wiele poleceń `sudo` i zostanie wyświetlony monit o podanie hasła, dodatkowe polecenia nie zostaną uruchomione. Uruchom każde polecenie osobno.


1. Uruchom następujące polecenia na każdej maszynie wirtualnej, aby otworzyć porty zapory Pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Zaktualizuj i zainstaluj pakiety Pacemaker na wszystkich węzłach przy użyciu następujących poleceń:

    > [!NOTE]
    > **Nmap** jest instalowany w ramach tego bloku poleceń jako narzędzie do znajdowania dostępnych adresów IP w sieci. Nie musisz instalować **Nmap**, ale będzie to przydatne w dalszej części tego samouczka.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Ustaw hasło dla domyślnego użytkownika tworzonego podczas instalowania pakietów Pacemaker. Użyj tego samego hasła we wszystkich węzłach.

    ```bash
    sudo passwd hacluster
    ```

1. Użyj poniższego polecenia, aby otworzyć plik Hosts i skonfigurować rozpoznawanie nazw hostów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) na konfigurowaniu pliku Hosts.

    ```
    sudo vi /etc/hosts
    ```

    W edytorze **VI** wprowadź `i`, aby wstawić tekst, a w pustym wierszu Dodaj **prywatny adres IP** odpowiedniej maszyny wirtualnej. Następnie Dodaj nazwę maszyny wirtualnej po miejscu obok adresu IP. Każdy wiersz powinien mieć oddzielny wpis.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Zalecamy korzystanie z powyższego **prywatnego adresu IP** . Użycie publicznego adresu IP w tej konfiguracji spowoduje niepowodzenie instalacji i nie zalecamy uwidaczniania maszyny wirtualnej w sieciach zewnętrznych.

    Aby wyjść z edytora **VI** , najpierw naciśnij klawisz **ESC** , a następnie wprowadź `:wq` polecenie, aby zapisać plik i zakończyć pracę.

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

W tej sekcji zostanie włączona i uruchomiona usługa pcsd, a następnie zostanie skonfigurowany klaster. W przypadku SQL Server on Linux zasoby klastra nie są tworzone automatycznie. Należy ręcznie włączyć i utworzyć zasoby Pacemaker. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [konfigurowania wystąpienia klastra trybu failover dla usługi RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Włączanie i uruchamianie usługi pcsd oraz Pacemaker

1. Uruchom polecenia we wszystkich węzłach. Te polecenia umożliwiają węzłom ponowne dołączenie do klastra po ponownym uruchomieniu.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Usuń istniejącą konfigurację klastra ze wszystkich węzłów. Uruchom następujące polecenie:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. W węźle podstawowym Uruchom następujące polecenia, aby skonfigurować klaster.

    - Po uruchomieniu polecenia `pcs cluster auth` w celu uwierzytelnienia węzłów klastra zostanie wyświetlony monit o podanie hasła. Wprowadź hasło utworzone wcześniej przez użytkownika **hacluster** .

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Uruchom następujące polecenie, aby sprawdzić, czy wszystkie węzły są w trybie online.

    ```bash
    sudo pcs status
    ```

    Jeśli wszystkie węzły są w trybie online, zostaną wyświetlone dane wyjściowe podobne do następujących:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Ustaw oczekiwane głosy w klastrze na żywo na 3. To polecenie ma wpływ tylko na aktywny klaster i nie zmienia plików konfiguracji.

    Na wszystkich węzłach Ustaw oczekiwane głosy przy użyciu następującego polecenia:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Skonfiguruj Agenta ogrodzenia

Urządzenie STONITH udostępnia Agenta ogrodzenia. Poniższe instrukcje są modyfikowane w tym samouczku. Aby uzyskać więcej informacji, zobacz [Tworzenie urządzenia STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Sprawdź wersję agenta usługi Azure ogrodzenia, aby upewnić się, że został zaktualizowany](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Użyj następującego polecenia:

```bash
sudo yum info fence-agents-azure-arm
```

Powinny być widoczne podobne dane wyjściowe do poniższego przykładu.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Zarejestruj nową aplikację w Azure Active Directory
 
 1. Przejdź do usługi https://portal.azure.com
 2. Otwórz [blok Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Przejdź do właściwości i zanotuj nazwę katalogu. Jest to `tenant ID`
 3. Kliknij [ **rejestracje aplikacji**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Kliknij pozycję **Nowa rejestracja**
 5. Wprowadź **nazwę** , taką jak `<resourceGroupName>-app`, zaznacz opcję **konta tylko w tym katalogu organizacji**
 6. Wybierz pozycję typ aplikacji **Sieć Web**, wprowadź adres URL logowania (na przykład http://localhost) a następnie kliknij przycisk Dodaj. Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
 7. Wybierz pozycję **Certyfikaty i wpisy tajne**, a następnie kliknij pozycję **Nowy wpis tajny klienta** .
 8. Wprowadź opis nowego klucza (klucz tajny klienta), wybierz pozycję **nigdy nie wygasa** , a następnie kliknij przycisk **Dodaj** .
 9. Zapisz wartość klucza tajnego. Służy jako hasło dla nazwy głównej usługi
10. Wybierz pozycję **Przegląd**. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa użytkownika (identyfikator logowania w poniższych krokach) nazwy głównej usługi
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Utwórz rolę niestandardową dla agenta ogranicznika

Postępuj zgodnie z samouczkiem, aby [utworzyć rolę niestandardową dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia Azure](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Plik JSON powinien wyglądać podobnie do poniższego:

- Zastąp ciąg `<username>` wybraną nazwą. Ma to na celu uniknięcie duplikowania podczas tworzenia tej definicji roli.
- Zastąp `<subscriptionId>` IDENTYFIKATORem subskrypcji platformy Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Aby dodać rolę, uruchom następujące polecenie:

- Zastąp `<filename>` nazwą pliku.
- Jeśli wykonujesz polecenie z ścieżki innej niż folder, w którym zapisano plik, dołącz ścieżkę do pliku w poleceniu.

```bash
az role definition create --role-definition "<filename>.json"
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Przypisywanie roli niestandardowej do nazwy głównej usługi

Przypisz rolę niestandardową `Linux Fence Agent Role-<username>` utworzoną w ostatnim kroku do nazwy głównej usługi. Nie używaj roli właściciel już!
 
1. Przejdź do usługi https://portal.azure.com
2. Otwórz [blok wszystkie zasoby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Wybierz maszynę wirtualną, w pierwszym węźle klastra
4. Kliknij pozycję **Kontrola dostępu (IAM)**
5. Kliknij pozycję **Dodaj przypisanie roli**
6. Wybierz `Linux Fence Agent Role-<username>` roli z listy **ról**
7. Na liście **Wybierz** wprowadź nazwę aplikacji utworzonej powyżej, `<resourceGroupName>-app`
8. Kliknij pozycję **Zapisz**
9. Powtórz powyższe kroki dla węzła wszystkie klastry.

### <a name="create-the-stonith-devices"></a>Tworzenie urządzeń STONITH

Uruchom następujące polecenia w węźle 1:

- Zastąp `<ApplicationID>` wartością identyfikatora z rejestracji aplikacji.
- Zastąp `<servicePrincipalPassword>` wartością z klucza tajnego klienta.
- Zastąp `<resourceGroupName>` z grupą zasobów w ramach subskrypcji używanej w tym samouczku.
- Zastąp `<tenantID>` i `<subscriptionId>` z subskrypcji platformy Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Ponieważ została już dodana reguła do zapory, aby zezwolić usłudze HA (`--add-service=high-availability`), nie ma potrzeby otwierania następujących portów zapory na wszystkich węzłach: 2224, 3121, 21064, 5405. Jeśli jednak występują problemy z połączeniem z HA, użyj następującego polecenia, aby otworzyć te porty, które są skojarzone z HA.

> [!TIP]
> Opcjonalnie możesz dodać wszystkie porty w tym samouczku, aby zaoszczędzić trochę czasu. Porty, które należy otworzyć, zostały omówione w sekcji względnej poniżej. Jeśli chcesz teraz dodać wszystkie porty, Dodaj dodatkowe porty: 1433 i 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalowanie SQL Server i narzędzi MSSQL
 
Poniższa sekcja służy do instalowania SQL Server i narzędzi MSSQL na maszynach wirtualnych. Wykonaj każdą z tych akcji na wszystkich węzłach. Aby uzyskać więcej informacji, zobacz [install SQL Server a Red Hat VM](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Instalowanie SQL Server na maszynach wirtualnych

Następujące polecenia służą do instalowania SQL Server:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Otwórz port zapory 1433 dla połączeń zdalnych

Aby można było połączyć się zdalnie, należy otworzyć port 1433 na maszynie wirtualnej. Użyj następujących poleceń, aby otworzyć port 1433 w zaporze każdej maszyny wirtualnej:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalowanie SQL Server narzędzi wiersza polecenia

Następujące polecenia służą do instalowania SQL Server narzędzia wiersza polecenia. Aby uzyskać więcej informacji, zobacz [instalowanie SQL Server narzędzia wiersza polecenia](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Dla wygody Dodaj/opt/MSSQL-Tools/bin/do zmiennej środowiskowej PATH. Dzięki temu można uruchamiać narzędzia bez określania pełnej ścieżki. Uruchom następujące polecenia, aby zmodyfikować ścieżkę zarówno dla sesji logowania, jak i sesji interakcyjnych/niezwiązanych z logowaniem:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Sprawdź stan SQL Server

Po zakończeniu konfiguracji można sprawdzić stan SQL Server i sprawdzić, czy jest on uruchomiony:

```bash
systemctl status mssql-server --no-pager
```

Powinny zostać wyświetlone następujące dane wyjściowe:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>Skonfiguruj zawsze włączona Grupa dostępności SQL Server

Wykonaj poniższe kroki, aby SQL Server skonfigurować grupę dostępności zawsze włączone dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [SQL Server Konfigurowanie grupy dostępności zawsze włączone w systemie Linux w celu zapewnienia wysokiej dostępności](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Włączanie zawsze włączonych grup dostępności i ponowne uruchamianie programu MSSQL-Server

Włącz zawsze włączone grupy dostępności na każdym węźle, który hostuje wystąpienie SQL Server. Następnie uruchom ponownie program MSSQL-Server. Uruchom następujący skrypt:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Tworzenie certyfikatu

Obecnie nie obsługujemy uwierzytelniania usługi AD w punkcie końcowym AG. W związku z tym musimy używać certyfikatu do szyfrowania za pomocą usługi AG.

1. Połącz się ze **wszystkimi węzłami** przy użyciu narzędzia SQL Server Management Studio (SSMS) lub polecenia SQL. Uruchom następujące polecenia, aby włączyć sesję AlwaysOn_health i utworzyć klucz główny:

    > [!IMPORTANT]
    > Jeśli łączysz się zdalnie z wystąpieniem SQL Server, musisz mieć otwarty port 1433 w zaporze. Należy również zezwolić na połączenia przychodzące do portu 1433 w sieciowej grupy zabezpieczeń dla każdej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie reguły zabezpieczeń](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) na potrzeby tworzenia reguły zabezpieczeń dla ruchu przychodzącego.

    - Zastąp `<Master_Key_Password>` własnym hasłem.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Połącz się z repliką podstawową przy użyciu programu SSMS lub CMD. Poniższe polecenia spowodują utworzenie certyfikatu w `/var/opt/mssql/data/dbm_certificate.cer` i klucza prywatnego w `var/opt/mssql/data/dbm_certificate.pvk` w podstawowej replice SQL Server:

    - Zastąp `<Private_Key_Password>` własnym hasłem.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

Zakończ sesję programu SQL CMD, uruchamiając polecenie `exit` i wróć do sesji SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Kopiuj certyfikat do replik pomocniczych i Utwórz certyfikaty na serwerze

1. Skopiuj dwa pliki, które zostały utworzone w tej samej lokalizacji na wszystkich serwerach, które będą hostować repliki dostępności.
 
    Na serwerze podstawowym Uruchom następujące polecenie `scp`, aby skopiować certyfikat na serwery docelowe:

    - Zastąp `<username>` i `<VM2>` nazwą użytkownika i docelową nazwą maszyny wirtualnej, której używasz.
    - Uruchom to polecenie dla wszystkich replik pomocniczych.

    > [!NOTE]
    > Nie musisz uruchamiać `sudo -i`, co zapewnia środowisko główne. Po prostu można uruchomić polecenie `sudo` przed każdym poleceniem, tak jak wcześniej w tym samouczku.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Na serwerze docelowym Uruchom następujące polecenie:

    - Zastąp `<username>` nazwą użytkownika.
    - Polecenie `mv` przenosi pliki lub katalogi z jednego miejsca do drugiego.
    - `chown` polecenie służy do zmiany właściciela i grupy plików, katalogów lub linków.
    - Uruchom te polecenia dla wszystkich replik pomocniczych.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Poniższy skrypt języka Transact-SQL tworzy certyfikat z kopii zapasowej utworzonej w replice SQL Server głównej. Zaktualizuj skrypt przy użyciu silnych haseł. Hasło odszyfrowywania to to samo hasło, które zostało użyte do utworzenia pliku. PVK w poprzednim kroku. Aby utworzyć certyfikat, uruchom następujący skrypt przy użyciu programu SQL CMD lub SSMS na wszystkich serwerach pomocniczych:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Utwórz punkty końcowe dublowania bazy danych dla wszystkich replik

Uruchom następujący skrypt na wszystkich wystąpieniach SQL za pomocą programu SQL CMD lub SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>Utwórz grupę dostępności

Połącz się z wystąpieniem SQL Server, które obsługuje replikę podstawową przy użyciu programu SQL CMD lub narzędzia SSMS. Uruchom następujące polecenie, aby utworzyć grupę dostępności:

- Zastąp `ag1` nazwą żądanej grupy dostępności.
- Zastąp wartości `<VM1>`, `<VM2>`i `<VM3>` nazwami wystąpień SQL Server, które obsługują repliki.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Utwórz SQL Server Login dla Pacemaker

Na wszystkich serwerach SQL Utwórz nazwę logowania SQL dla Pacemaker. Poniższy kod Transact-SQL tworzy nazwę logowania.

- Zastąp `<password>` własnym złożonym hasłem.

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

Na wszystkich serwerach SQL Zapisz poświadczenia używane do logowania SQL Server. 

1. Utwórz plik:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Dodaj następujące 2 wiersze do pliku:

    ```bash
    pacemakerLogin
    <password>
    ```

    Aby wyjść z edytora **VI** , najpierw naciśnij klawisz **ESC** , a następnie wprowadź `:wq` polecenie, aby zapisać plik i zakończyć pracę.

1. Uczyń plik tylko do odczytu w katalogu głównym:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Sprzęganie replik pomocniczych z grupą dostępności

1. Aby można było przyłączyć repliki pomocnicze do usługi AG, należy otworzyć port 5022 na zaporze dla wszystkich serwerów. Uruchom następujące polecenie w sesji SSH:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. W replikach pomocniczych Uruchom następujące polecenia, aby przyłączyć je do sieci AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. Uruchom następujący skrypt języka Transact-SQL w replice podstawowej i każdej z nich:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. Po przyłączeniu replik pomocniczych można je zobaczyć w programie SSMS Eksplorator obiektów, rozszerzając węzeł **Always On High Availability** :

    ![Availability-Group-JOINED. png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Dodawanie bazy danych do grupy dostępności

[Po dodaniu bazy danych będziemy przestrzegać artykułu Konfigurowanie grupy dostępności](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

W tym kroku są używane następujące polecenia języka Transact-SQL. Uruchom następujące polecenia w replice podstawowej:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Sprawdź, czy baza danych została utworzona na serwerach pomocniczych

W każdej pomocniczej replice SQL Server uruchom następujące zapytanie, aby sprawdzić, czy baza danych DB1 została utworzona i czy jest w stanie ZSYNCHRONIZOWANym:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Jeśli lista `synchronization_state_desc` SYNCHRONIZOWANA dla `db1`, oznacza to, że repliki są synchronizowane. Serwery pomocnicze pokazują `db1` w replice podstawowej.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Tworzenie zasobów grupy dostępności w klastrze Pacemaker

[W celu utworzenia zasobów grupy dostępności w klastrze Pacemaker](/sql/linux/sql-server-linux-create-availability-group]#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)będzie dostępny przewodnik.

### <a name="create-the-ag-cluster-resource"></a>Tworzenie zasobu w klastrze AG

1. Użyj poniższego polecenia, aby utworzyć zasób `ag_cluster` w `ag1`dostępnej grupie.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Sprawdź zasób i upewnij się, że są w trybie online przed kontynuowaniem pracy przy użyciu następującego polecenia:

    ```bash
    sudo pcs resource
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>Tworzenie zasobu wirtualnego adresu IP

1. Użyj dostępnego statycznego adresu IP z sieci, aby utworzyć zasób wirtualnego adresu IP. Można go znaleźć za pomocą narzędzia poleceń `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Ustaw właściwość **stonith** na wartość false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Utwórz zasób wirtualnego adresu IP za pomocą następującego polecenia:

    - Zastąp wartość `<availableIP>` poniżej nieużywanym adresem IP.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Dodaj ograniczenia

1. Aby upewnić się, że adres IP i Grupa dostępności są uruchomione w tym samym węźle, należy skonfigurować ograniczenie między lokalizacjami. Uruchom następujące polecenie:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Utwórz ograniczenie porządkowania, aby upewnić się, że w ramach tego zasobu jest uruchomiona wartość "AG" przed adresem IP. Chociaż ograniczenie wspólnej lokalizacji implikuje ograniczenie kolejności, wymusza je.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Aby sprawdzić ograniczenia, uruchom następujące polecenie:

    ```bash
    sudo pcs constraint list --full
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Włącz ponownie stonith

Jesteśmy gotowi do testowania. Ponownie włącz stonith w klastrze, uruchamiając następujące polecenie w węźle 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Sprawdzanie stanu klastra

Stan zasobów klastra można sprawdzić za pomocą następującego polecenia:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Aby upewnić się, że konfiguracja zakończyła się pomyślnie, przetestujemy tryb failover. Aby uzyskać więcej informacji, zobacz [zawsze włączone przełączanie do trybu failover grupy dostępności w systemie Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Uruchom następujące polecenie, aby ręcznie przełączyć replikę podstawową do `<VM2>`. Zastąp `<VM2>` wartością nazwy serwera.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Jeśli ponownie sprawdzisz swoje ograniczenia, zobaczysz, że zostało dodane inne ograniczenie ze względu na ręczną pracę awaryjną:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Usuń ograniczenie o IDENTYFIKATORze `cli-prefer-ag_cluster-master` przy użyciu następującego polecenia:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Sprawdź zasoby klastra, korzystając z polecenia `sudo pcs resource`, i sprawdź, czy wystąpienie podstawowe jest teraz `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>Ogrodzenie testowe

Możesz testować STONITH, uruchamiając następujące polecenie. Spróbuj uruchomić poniższe polecenie w `<VM1>`, aby `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Domyślnie akcja ogrodzenia powoduje, że węzeł jest wyłączony, a następnie włączony. Jeśli chcesz tylko przełączyć węzeł do trybu offline, użyj opcji `--off` w poleceniu.

Należy uzyskać następujące dane wyjściowe:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Więcej informacji na temat testowania urządzenia ogrodzenia można znaleźć w następującym artykule [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) .

## <a name="next-steps"></a>Następne kroki

Aby można było korzystać z odbiornika grupy dostępności dla serwerów SQL utworzonych na platformie Azure, należy najpierw utworzyć i skonfigurować moduł równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Utwórz i skonfiguruj moduł równoważenia obciążenia w Azure Portal](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)