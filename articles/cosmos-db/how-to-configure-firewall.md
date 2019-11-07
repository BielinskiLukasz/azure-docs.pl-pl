---
title: Skonfiguruj zaporę IP dla konta usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować zasady kontroli dostępu do adresów IP na potrzeby obsługi zapory na kontach usługi Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 8522a537301c1d35da2a2eb46b4374fa4daf6a27
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580683"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurowanie zapory IP w Azure Cosmos DB

Dane przechowywane na koncie usługi Azure Cosmos DB można zabezpieczyć za pomocą zapór protokołu IP. Azure Cosmos DB obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Zaporę IP można ustawić na koncie Azure Cosmos DB przy użyciu jednego z następujących sposobów:

* z Azure Portal
* Deklaratywnie przy użyciu szablonu Azure Resource Manager
* Programowo za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aktualizując Właściwość **ipRangeFilter**

## <a id="configure-ip-policy"></a>Skonfiguruj zaporę IP przy użyciu Azure Portal

Aby ustawić zasady kontroli dostępu IP w Azure Portal, przejdź do strony konto Azure Cosmos DB i wybierz opcję **Zapora i sieci wirtualne** w menu nawigacji. Zmień wartość **Zezwalaj na dostęp z** wartości na **wybrane sieci**, a następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób otwierania strony zapory w Azure Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Po włączeniu kontroli dostępu do adresów IP Azure Portal zapewnia możliwość określania adresów IP, zakresów adresów IP i przełączników. Przełączniki umożliwiają dostęp do innych usług platformy Azure i Azure Portal. Poniższe sekcje zawierają szczegółowe informacje dotyczące tych przełączników.

> [!NOTE]
> Po włączeniu zasad kontroli dostępu IP dla konta Azure Cosmos DB wszystkie żądania dotyczące konta Azure Cosmos DB z maszyn spoza listy dozwolonych zakresów adresów IP zostaną odrzucone. Przeglądanie zasobów Azure Cosmos DB z portalu jest również blokowane, aby zapewnić integralność kontroli dostępu.

### <a name="allow-requests-from-the-azure-portal"></a>Zezwalaj na żądania z Azure Portal

Po włączeniu programowo zasad kontroli dostępu IP należy dodać adres IP dla Azure Portal do właściwości **ipRangeFilter** , aby zachować dostęp. Adresy IP portalu to:

|Region|Adres IP|
|------|----------|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|Rząd USA|52.244.48.71|
|Wszystkie pozostałe regiony|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Dostęp do Azure Portal można włączyć, wybierając opcję **Zezwalaj na dostęp z Azure Portal** , jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający sposób włączania dostępu Azure Portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zezwalaj na żądania z globalnych centrów danych platformy Azure lub innych źródeł na platformie Azure

W przypadku uzyskania dostępu do konta Azure Cosmos DB z usług, które nie zapewniają statycznego adresu IP (na przykład Azure Stream Analytics i Azure Functions), można nadal korzystać z zapory IP w celu ograniczenia dostępu. Aby zezwolić na dostęp do konta Azure Cosmos DB z takich usług, należy dodać adres IP 0.0.0.0 do listy dozwolonych adresów IP. Adres 0.0.0.0 ogranicza żądania do konta Azure Cosmos DB z zakresu adresów IP centrum danych platformy Azure. To ustawienie nie zezwala na dostęp do innych zakresów adresów IP do konta Azure Cosmos DB.

> [!NOTE]
> Ta opcja umożliwia skonfigurowanie zapory do zezwalania na wszystkie żądania z platformy Azure, w tym żądania z subskrypcji innych klientów wdrożonych na platformie Azure. Lista adresów IP dozwolona przez tę opcję jest szeroka, dlatego ogranicza efektywność zasad zapory. Użyj tej opcji tylko wtedy, gdy Twoje żądania nie pochodzą ze statycznych adresów IP lub podsieci w sieciach wirtualnych. Wybranie tej opcji powoduje automatyczne Zezwalanie na dostęp z Azure Portal, ponieważ Azure Portal jest wdrażany na platformie Azure.

Dostęp do Azure Portal można włączyć, wybierając opcję **Akceptuj połączenia z poziomu centrów danych platformy Azure** , jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający sposób otwierania strony zapory w Azure Portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Żądania z bieżącego adresu IP

Aby uprościć programowanie, Azure Portal ułatwia identyfikowanie i Dodawanie adresu IP komputera klienckiego do listy dozwolonych. Aplikacje uruchamiające maszynę mogą uzyskiwać dostęp do konta Azure Cosmos DB.

Portal automatycznie wykrywa adres IP klienta. Może to być adres IP klienta komputera lub adres IP bramy sieci. Pamiętaj o usunięciu tego adresu IP przed przejęciem obciążeń do środowiska produkcyjnego.

Aby dodać bieżący adres IP do listy adresów IP, wybierz pozycję **Dodaj mój bieżący adres IP**. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Żądania z usług Cloud Services

Na platformie Azure usługi Cloud Services są typowym sposobem hostowania logiki usług warstwy środkowej przy użyciu Azure Cosmos DB. Aby włączyć dostęp do konta Azure Cosmos DB z usługi w chmurze, musisz dodać publiczny adres IP usługi w chmurze do listy dozwolonych adresów IP skojarzonych z Twoim kontem Azure Cosmos DB przez [skonfigurowanie zasad kontroli dostępu do adresów IP](#configure-ip-policy). Dzięki temu wszystkie wystąpienia roli usług w chmurze mają dostęp do konta Azure Cosmos DB.

Możesz pobrać adresy IP dla usług w chmurze w Azure Portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla usługi w chmurze, która jest wyświetlana w Azure Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

W przypadku skalowania w poziomie usługi w chmurze przez dodanie wystąpień roli nowe wystąpienia będą automatycznie miały dostęp do konta Azure Cosmos DB, ponieważ są one częścią tej samej usługi w chmurze.

### <a name="requests-from-virtual-machines"></a>Żądania z maszyn wirtualnych

Do hostowania usług warstwy środkowej przy użyciu Azure Cosmos DB można także użyć [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/) lub [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) . Aby skonfigurować konto Cosmos DB w taki sposób, aby umożliwiało dostęp z maszyn wirtualnych, należy skonfigurować publiczny adres IP maszyny wirtualnej i/lub zestawu skalowania maszyn wirtualnych jako jeden z dozwolonych adresów IP dla konta Azure Cosmos DB przez [skonfigurowanie zasady kontroli dostępu do adresów IP](#configure-ip-policy).

Adresy IP dla maszyn wirtualnych można pobrać w Azure Portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla maszyny wirtualnej wyświetlanej w Azure Portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Po dodaniu wystąpień maszyn wirtualnych do grupy automatycznie otrzymuje ona dostęp do konta Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Żądania z Internetu

W przypadku uzyskiwania dostępu do konta Azure Cosmos DB z komputera w Internecie należy dodać adres IP lub zakres adresów IP komputera do listy dozwolonych adresów IP dla Twojego konta.

## <a id="configure-ip-firewall-arm"></a>Konfigurowanie zapory IP przy użyciu szablonu Menedżer zasobów

Aby skonfigurować kontrolę dostępu do konta Azure Cosmos DB, upewnij się, że szablon Menedżer zasobów określa atrybut **ipRangeFilter** z listą dozwolonych zakresów adresów IP. W przypadku konfigurowania zapory IP dla już wdrożonego konta Cosmos upewnij się, że tablica `locations` jest zgodna z aktualnie wdrożonym. Nie można jednocześnie zmodyfikować tablicy `locations` i innych właściwości. Aby uzyskać więcej informacji i przykłady szablonów Azure Resource Manager dla Azure Cosmos DB Zobacz, [Azure Resource Manager szablony dla Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a id="configure-ip-firewall-cli"></a>Konfigurowanie zasad kontroli dostępu IP przy użyciu interfejsu wiersza polecenia platformy Azure

Następujące polecenie pokazuje, jak utworzyć konto Azure Cosmos DB przy użyciu kontroli dostępu do adresów IP:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a id="configure-ip-firewall-ps"></a>Konfigurowanie zasad kontroli dostępu IP przy użyciu programu PowerShell

Poniższy skrypt pokazuje, jak utworzyć konto Azure Cosmos DB przy użyciu kontroli dostępu do adresów IP:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>Rozwiązywanie problemów z zasadami kontroli dostępu IP

Problemy z zasadami kontroli dostępu IP można rozwiązać, korzystając z następujących opcji:

### <a name="azure-portal"></a>Azure Portal

Po włączeniu zasad kontroli dostępu IP dla konta Azure Cosmos DB należy zablokować wszystkie żądania do konta z maszyn spoza listy dozwolonych zakresów adresów IP. Aby włączyć operacje płaszczyzny danych portalu, takie jak kontenery przeglądania i wysyłanie zapytań do dokumentów, należy jawnie zezwolić na dostęp Azure Portal przy użyciu okienka **Zapora** w portalu.

### <a name="sdks"></a>Zestawy SDK

Gdy uzyskujesz dostęp do zasobów Azure Cosmos DB przy użyciu zestawów SDK z maszyn, które nie znajdują się na liście dozwolonych, zostanie zwrócona ogólna odpowiedź z **zabronionym 403** bez dodatkowych szczegółów. Sprawdź listę dozwolonych adresów IP dla swojego konta i upewnij się, że do konta Azure Cosmos DB jest stosowana poprawna konfiguracja zasad.

### <a name="source-ips-in-blocked-requests"></a>Źródłowe adresy IP w zablokowanych żądaniach

Włącz rejestrowanie diagnostyczne na koncie Azure Cosmos DB. Te dzienniki zawierają wszystkie żądania i odpowiedzi. Komunikaty dotyczące zapory są rejestrowane przy użyciu kodu powrotu 403. Filtrując te komunikaty, można zobaczyć źródłowe adresy IP dla zablokowanych żądań. Zobacz [rejestrowanie diagnostyczne Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Żądania z podsieci z punktem końcowym usługi dla Azure Cosmos DB włączone

Żądania z podsieci w sieci wirtualnej, która ma punkt końcowy usługi dla Azure Cosmos DB włączone, wysyła do kont Azure Cosmos DB sieć wirtualną i tożsamość podsieci. Te żądania nie mają publicznego adresu IP źródła, więc filtry adresów IP są odrzucane. Aby zezwolić na dostęp z określonych podsieci w sieciach wirtualnych, należy dodać listę kontroli dostępu, jak opisano w temacie [jak skonfigurować sieć wirtualną i dostęp oparty na podsieci dla konta Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Zastosowanie reguł zapory może potrwać do 15 minut.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować punkt końcowy usługi sieci wirtualnej dla konta Azure Cosmos DB, zobacz następujące artykuły:

* [Kontrola dostępu do sieci wirtualnej i podsieci dla konta usługi Azure Cosmos DB](vnet-service-endpoint.md)
* [Konfigurowanie sieci wirtualnej i dostępu opartego na podsieci dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
