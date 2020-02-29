---
title: Uruchamianie systemu Linux na węzłach obliczeniowych maszyny wirtualnej — Azure Batch | Microsoft Docs
description: Dowiedz się, jak przetwarzać obciążenia równoległe obliczeniowe dla pul maszyn wirtualnych z systemem Linux w Azure Batch.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917502"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Inicjowanie obsługi węzłów obliczeniowych systemu Linux w pulach wsadowym

Za pomocą Azure Batch można uruchamiać obciążenia obliczeń równoległych na maszynach wirtualnych z systemem Linux i Windows. W tym artykule szczegółowo opisano sposób tworzenia pul węzłów obliczeniowych systemu Linux w usłudze Batch przy użyciu bibliotek klienckich [Python][py_batch_package] i [Batch platformy .NET][api_net] .

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji. Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej
Podczas tworzenia puli węzłów obliczeniowych w usłudze Batch dostępne są dwie opcje, z których można wybrać rozmiar węzła i system operacyjny: konfigurację Cloud Services i konfigurację maszyny wirtualnej.

**Konfiguracja usług Cloud Services** oferuje *tylko* węzły obliczeniowe systemu Windows. Dostępne rozmiary węzłów obliczeniowych są wymienione w obszarze [rozmiary dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md), a dostępne systemy operacyjne są wymienione w temacie [wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawu SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Podczas tworzenia puli, która zawiera węzły Cloud Services platformy Azure, należy określić rozmiar węzła i rodzinę systemów operacyjnych, które opisano w wymienionych wcześniej artykułach. W przypadku pul węzłów obliczeniowych systemu Windows najczęściej używane są Cloud Services.

**Konfiguracja maszyny wirtualnej** zapewnia zarówno obrazy systemu Linux, jak i Windows dla węzłów obliczeniowych. Dostępne rozmiary węzłów obliczeniowych są wymienione w obszarze [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) i [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Podczas tworzenia puli, która zawiera węzły konfiguracji maszyny wirtualnej, należy określić rozmiar węzłów, odwołanie do obrazu maszyny wirtualnej i jednostkę SKU agenta węzła wsadowego do zainstalowania w węzłach.

### <a name="virtual-machine-image-reference"></a>Odwołanie do obrazu maszyny wirtualnej

Usługa Batch używa [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) w celu zapewnienia węzłów obliczeniowych w konfiguracji maszyny wirtualnej. Możesz określić obraz z [witryny Azure Marketplace][vm_marketplace]lub udostępnić niestandardowy obraz, który został przygotowany. Aby uzyskać więcej informacji o obrazach niestandardowych, zobacz [Tworzenie puli za pomocą galerii obrazów udostępnionych](batch-sig-images.md).

Konfigurując odwołanie do obrazu maszyny wirtualnej, należy określić właściwości obrazu maszyny wirtualnej. Następujące właściwości są wymagane podczas tworzenia odwołania do obrazu maszyny wirtualnej:

| **Właściwości odwołania do obrazu** | **Przykład** |
| --- | --- |
| Wydawca |Canonical |
| Oferta |UbuntuServer |
| SKU |18,04 – LTS |
| Wersja |najnowsza |

> [!TIP]
> Możesz dowiedzieć się więcej o tych właściwościach oraz jak wyświetlać obrazy z witryny Marketplace w obszarze [Nawigacja i wybierać obrazy maszyn wirtualnych z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia lub programu PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy pamiętać, że nie wszystkie obrazy z portalu Marketplace są obecnie zgodne z usługą Batch. Aby uzyskać więcej informacji, zobacz [jednostki SKU agenta węzła](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Jednostka SKU agenta węzła
Agent węzła usługi Batch jest programem uruchamianym w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem a usługą Batch. Istnieją różne implementacje agenta węzła, nazywane jednostkami SKU dla różnych systemów operacyjnych. Zasadniczo podczas tworzenia konfiguracji maszyny wirtualnej należy najpierw określić odwołanie do obrazu maszyny wirtualnej, a następnie określić agenta węzła, który ma zostać zainstalowany na obrazie. Zazwyczaj każda jednostka SKU agenta węzła jest zgodna z wieloma obrazami maszyn wirtualnych. Poniżej przedstawiono kilka przykładów jednostek SKU agenta węzła:

* Batch. Node. Ubuntu 18,04
* Batch. Node. CentOS 7
* Batch. Node. Windows amd64

> [!IMPORTANT]
> Nie wszystkie obrazy maszyn wirtualnych, które są dostępne w portalu Marketplace, są zgodne z aktualnie dostępnymi agentami węzłów partii. Użyj zestawów SDK w usłudze Batch, aby wyświetlić listę dostępnych jednostek SKU agenta węzła i obrazów maszyn wirtualnych, z którymi są one zgodne. Zapoznaj się z [listą obrazów maszyn wirtualnych](#list-of-virtual-machine-images) w dalszej części tego artykułu, aby uzyskać więcej informacji i Przykłady sposobu pobierania listy prawidłowych obrazów w czasie wykonywania.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Tworzenie puli systemu Linux: Batch Python
Poniższy fragment kodu przedstawia przykład korzystania z [biblioteki klienta Microsoft Azure Batch dla języka Python][py_batch_package] w celu utworzenia puli węzłów obliczeniowych serwera Ubuntu. Dokumentacja referencyjna dla modułu usługi Batch w języku Python znajduje się w [pakiecie Azure. Batch][py_batch_docs] na stronie odczytywanie dokumentów.

Ten fragment kodu tworzy [elementu imagereference][py_imagereference] jawnie i określa każdą z jej właściwości (Wydawca, oferta, jednostka SKU, wersja). Jednak w kodzie produkcyjnym zalecamy użycie metody [list_supported_images][py_list_supported_images] do określenia i wybrania spośród dostępnych kombinacji jednostki SKU agenta węzła w czasie wykonywania.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak wspomniano wcześniej, zaleca się, aby zamiast tworzenia [elementu imagereference][py_imagereference] jawnie użyć metody [list_supported_images][py_list_supported_images] , aby dynamicznie wybierać z aktualnie obsługiwanych kombinacji węzłów/z obrazu portalu Marketplace. Poniższy fragment kodu w języku Python pokazuje, jak używać tej metody.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Tworzenie puli systemu Linux: Batch .NET
Poniższy fragment kodu przedstawia przykład użycia biblioteki klienta [programu Batch .NET][nuget_batch_net] do tworzenia puli węzłów obliczeniowych serwera Ubuntu. [Dokumentację referencyjną platformy .NET][api_net] w usłudze Batch można znaleźć w witrynie docs.Microsoft.com.

Poniższy fragment kodu używa [PoolOperations][net_pool_ops]. Metoda [ListSupportedImages][net_list_supported_images] do wybrania z listy aktualnie obsługiwanych kombinacji jednostek SKU obrazu i agenta węzła w portalu Marketplace. Ta technika jest pożądana, ponieważ lista obsługiwanych kombinacji może ulec zmianie od czasu do czasu. Najczęściej są dodawane obsługiwane kombinacje.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Mimo że poprzedni fragment kodu używa [PoolOperations][net_pool_ops]. Metoda [ListSupportedImages][net_list_supported_images] do dynamicznego wyświetlania i wybierania obsługiwanych kombinacji jednostek SKU agenta i węzła (zalecane) można również skonfigurować jawnie [elementu imagereference][net_imagereference] :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista obrazów maszyn wirtualnych
Aby uzyskać listę wszystkich obsługiwanych obrazów maszyn wirtualnych w portalu Marketplace dla usługi Batch i odpowiednich agentów węzłów, Skorzystaj z [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) lub odpowiedniego interfejsu API w odpowiednim zestawie SDK danego języka.

## <a name="connect-to-linux-nodes-using-ssh"></a>Łączenie z węzłami systemu Linux przy użyciu protokołu SSH
Podczas programowania lub rozwiązywania problemów może być konieczne zalogowanie się do węzłów w puli. W przeciwieństwie do węzłów obliczeniowych systemu Windows nie można używać Remote Desktop Protocol (RDP) do nawiązywania połączenia z węzłami z systemem Linux. Zamiast tego usługa Batch umożliwia dostęp do protokołu SSH w każdym węźle dla połączenia zdalnego.

Poniższy fragment kodu w języku Python tworzy użytkownika na każdym węźle w puli, który jest wymagany do połączenia zdalnego. Następnie drukuje informacje o połączeniu Secure Shell (SSH) dla każdego węzła.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Poniżej przedstawiono przykładowe dane wyjściowe dla poprzedniego kodu puli zawierającej cztery węzły systemu Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Zamiast hasła można określić klucz publiczny SSH podczas tworzenia użytkownika w węźle. W zestawie SDK języka Python Użyj parametru **ssh_public_key** w [ComputeNodeUser][py_computenodeuser]. W programie .NET Użyj [ComputeNodeUser][net_computenodeuser]. Właściwość [SshPublicKey][net_ssh_key] .

## <a name="pricing"></a>Ceny
Azure Batch jest oparta na platformie Azure Cloud Services i technologii Azure Virtual Machines. Sama usługa Batch jest oferowana bezpłatnie, co oznacza, że opłaty są naliczone wyłącznie za zasoby obliczeniowe (i powiązane z nimi koszty, które wiążą się z tym, że korzystasz z rozwiązań wsadowych). Po wybraniu **konfiguracji Cloud Services**zostanie naliczona opłata oparta na [Cloud Services strukturze cenowej][cloud_services_pricing] . Po wybraniu opcji **Konfiguracja maszyny wirtualnej**opłata jest naliczana na podstawie [Virtual Machinesj struktury cenowej][vm_pricing] .

W przypadku wdrażania aplikacji w węzłach usługi Batch przy użyciu [pakietów aplikacji](batch-application-packages.md)opłaty są naliczone również za zasoby magazynu platformy Azure zużywane przez pakiety aplikacji.

## <a name="next-steps"></a>Następne kroki

[Przykłady kodu][github_samples_py] w języku Python w repozytorium [Azure-Batch-Samples][github_samples] w witrynie GitHub zawierają skrypty pokazujące, jak wykonywać typowe operacje wsadowe, takie jak pule, zadania i tworzenie zadań. [Plik Readme][github_py_readme] zawierający przykłady języka Python zawiera szczegółowe informacje o sposobie instalowania wymaganych pakietów.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
