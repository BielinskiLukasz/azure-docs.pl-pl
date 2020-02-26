---
title: Tworzenie udziału plików platformy Azure
titleSuffix: Azure Files
description: Jak utworzyć udział plików platformy Azure przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596920"
---
# <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Aby utworzyć udział plików platformy Azure, musisz odpowiedzieć na trzy pytania dotyczące sposobu ich używania:

- **Jakie są wymagania dotyczące wydajności udziału plików platformy Azure?**  
    Azure Files oferuje standardowe udziały plików, które są hostowane na dyskach twardych na podstawie sprzętu komputerowego i Premium udziałów plików, które są hostowane na dysku SSD (opartym na dyskach półprzewodnikowych).

- **Jakiego rozmiaru udziału plików potrzebujesz?**  
    Standardowe udziały plików mogą obejmować do 100 TiB, jednak ta funkcja nie jest domyślnie włączona. Jeśli potrzebujesz udziału plików, który jest większy niż 5 TiB, musisz włączyć funkcję dużego udziału plików dla konta magazynu. Udziały plików w warstwie Premium mogą obejmować do 100 TiB bez żadnych specjalnych ustawień, jednak są udostępniane udziały plików w warstwie Premium, a nie płatność zgodnie z rzeczywistym użyciem w przypadku standardowych udziałów plików. Oznacza to, że udostępnienie udziału plików znacznie większe niż to, co jest potrzebne, spowoduje zwiększenie łącznego kosztu magazynowania.

- **Jakie są wymagania związane z nadmiarowością udziału plików platformy Azure?**  
    Standardowe udziały plików oferują Magazyn lokalnie nadmiarowy (LRS), strefowo nadmiarowy (ZRS), Geograficznie nadmiarowy (GRS) lub strefy geograficznej (GZRS), jednak funkcja dużego udziału plików jest obsługiwana tylko lokalnie nadmiarowe i nadmiarowe udziały plików. Udziały plików w warstwie Premium nie obsługują żadnej formy nadmiarowości geograficznej.

    Udziały plików w warstwie Premium są dostępne z lokalną nadmiarowością w większości regionów, które oferują konta magazynu i nadmiarowość stref w mniejszym podzbiorze regionów. Aby dowiedzieć się, czy w Twoim regionie są obecnie dostępne udziały plików w warstwie Premium, zobacz stronę [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure. Aby uzyskać informacje na temat regionów, które obsługują ZRS, zobacz [nadmiarowość usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby uzyskać więcej informacji na temat tych trzech opcji, zobacz [Planowanie wdrożenia Azure Files](storage-files-planning.md).

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Udziały plików platformy Azure są wdrażane na *kontach magazynu*, które są obiektami najwyższego poziomu reprezentującymi udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. 

Platforma Azure obsługuje wiele typów kont magazynu dla różnych klientów scenariuszy magazynu, które mogą mieć, ale istnieją dwa główne typy kont magazynu dla Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w warstwie Premium: 

- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** : GPv2 konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych. Oprócz przechowywania udziałów plików platformy Azure GPv2 konta magazynu mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 

- **FileStorage kont magazynu**: FileStorage konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach Premium/SSD (opartym na dysku półprzewodnikowym). Kont FileStorage można używać tylko do przechowywania udziałów plików platformy Azure. nie można wdrażać innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) w ramach konta FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu za pośrednictwem Azure Portal, wybierz pozycję **+ Utwórz zasób** z poziomu pulpitu nawigacyjnego. W wynikowym oknie wyszukiwania portalu Azure Marketplace Wyszukaj pozycję **konto magazynu** i wybierz wynikowe wyniki wyszukiwania. Spowoduje to wyświetlenie strony Przegląd kont magazynu; Wybierz pozycję **Utwórz** , aby kontynuować pracę z kreatorem tworzenia konta magazynu.

![Zrzut ekranu przedstawiający opcję szybkie tworzenie konta magazynu w przeglądarce](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Sekcja podstawy
Pierwsza sekcja, która ma zostać ukończona, aby utworzyć konto magazynu, jest oznaczona jako **podstawy**. Zawiera wszystkie wymagane pola, aby utworzyć konto magazynu. Aby utworzyć konto magazynu GPv2, upewnij się, że przycisk radiowy **wydajność** jest ustawiony na wartość *standardowa* , a w polu listy rozwijanej **rodzaj konta** jest wybrana wartość *StorageV2 (ogólnego przeznaczenia w wersji 2)* .

![Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybranym standardem i rodzajem konta z wybranym StorageV2](media/storage-how-to-create-file-share/create-storage-account-1.png)

Aby utworzyć konto magazynu FileStorage, upewnij się, że przycisk radiowy **wydajność** jest ustawiony na wartość *Premium* , a na liście rozwijanej **rodzaj konta** jest wybrana wartość *FileStorage*.

![Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybranym typem Premium i rodzajem konta z wybraną pozycją FileStorage](media/storage-how-to-create-file-share/create-storage-account-2.png)

Inne podstawowe pola są niezależne od wyboru konta magazynu:
- **Subskrypcja**: subskrypcja konta magazynu do wdrożenia. 
- **Grupa zasobów**: Grupa zasobów dla konta magazynu, do której ma zostać wdrożone. Możesz utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów.
- **Nazwa konta magazynu**: nazwa zasobu konta magazynu, który ma zostać utworzony. Ta nazwa musi być globalnie unikatowa, ale w przeciwnym razie można mieć dowolną nazwę. Nazwa konta magazynu zostanie użyta jako nazwa serwera podczas instalowania udziału plików platformy Azure za pośrednictwem protokołu SMB.
- **Lokalizacja**: region konta magazynu, w którym ma zostać wdrożone. Może to być region skojarzony z grupą zasobów lub dowolnym innym dostępnym regionem.
- **Replikacja**: Chociaż jest to nazwa replikacji, to pole faktycznie oznacza **nadmiarowość**; jest to żądany poziom nadmiarowości: lokalna nadmiarowość (LRS), nadmiarowość stref (ZRS), nadmiarowość geograficzna (GRS) i strefa geograficzna — nadmiarowość. Ta lista rozwijana zawiera również informacje o nadmiarowości geograficznej (RA-GRS) dostępu do odczytu i dostęp do odczytu (RA-GZRS), które nie dotyczą udziałów plików platformy Azure; wszystkie udziały plików utworzone na koncie magazynu z tymi wybranymi będzie odpowiednio Geograficznie nadmiarowy lub geograficznie nadmiarowy. Niektóre opcje nadmiarowości mogą nie być dozwolone w zależności od regionu lub wybranego typu konta magazynu.
- **Warstwa dostępu**: to pole nie ma zastosowania do Azure Files, więc można wybrać jeden z przycisków radiowych.

#### <a name="the-networking-blade"></a>Blok sieć
Sekcja sieci umożliwia skonfigurowanie opcji sieciowych. Te ustawienia są opcjonalne do tworzenia konta magazynu i można je później skonfigurować w razie potrzeby. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Zaawansowany blok
Sekcja zaawansowane zawiera kilka ważnych ustawień udziałów plików platformy Azure:

- **Wymagany bezpieczny transfer**: to pole wskazuje, czy konto magazynu wymaga szyfrowania podczas przesyłania komunikacji z kontem magazynu. Zaleca się pozostawienie tej funkcji, jeśli jest wymagana obsługa protokołu SMB 2,1, należy ją wyłączyć. Zalecamy wyłączenie szyfrowania, aby ograniczyć dostęp konta magazynu do sieci wirtualnej za pomocą punktów końcowych usługi i/lub prywatnych punktów końcowych.
- **Duże udziały plików**: to pole umożliwia konto magazynu dla udziałów plików o rozmiarze do 100 TIB. Włączenie tej funkcji ograniczy konto magazynu tylko do lokalnego nadmiarowego i nadmiarowego magazynu strefy. Po włączeniu konta magazynu GPv2 dla dużych udziałów plików nie można wyłączyć funkcji dużego udziału plików. W przypadku kont magazynu FileStorage (konta magazynu dla udziałów plików w warstwie Premium) nie ma tej opcji, ponieważ wszystkie udziały plików w warstwie Premium mogą być skalowane do 100 TiB. 

![Zrzut ekranu przedstawiający ważne zaawansowane ustawienia, które mają zastosowanie do Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

Inne ustawienia, które są dostępne na karcie Zaawansowane (obiekty blob unsoft-Delete, hierarchiczna przestrzeń nazw dla Azure Data Lake Storage Gen 2 i NFSv3 for BLOB Storage) nie mają zastosowania do Azure Files.

#### <a name="tags"></a>Tagi
Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu dla wielu zasobów i grup zasobów. Są one opcjonalne i mogą być stosowane po utworzeniu konta magazynu.

#### <a name="review--create"></a>Przegląd + tworzenie
Ostatnim krokiem tworzenia konta magazynu jest wybranie przycisku **Utwórz** na karcie **Recenzja + tworzenie** . Ten przycisk nie będzie dostępny, jeśli nie wypełniono wszystkich pól wymaganych dla konta magazynu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu przy użyciu programu PowerShell, użyjemy polecenia cmdlet `New-AzStorageAccount`. To polecenie cmdlet ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej na temat opcji zaawansowanych, zobacz [dokumentację poleceń cmdlet`New-AzStorageAccount`](/powershell/module/az.storage/new-azstorageaccount).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, firma Microsoft będzie przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Aby utworzyć konto magazynu z możliwością przechowywania standardowych udziałów plików platformy Azure, użyjemy poniższego polecenia. `-SkuName` parametr odnosi się do żądanego typu nadmiarowości; Jeśli potrzebujesz geograficznie nadmiarowej lub geograficznie nadmiarowego konta magazynu, należy również usunąć parametr `-EnableLargeFileShare`.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Aby utworzyć konto magazynu z możliwością przechowywania udziałów plików platformy Azure w warstwie Premium, użyjemy poniższego polecenia. Należy zauważyć, że parametr `-SkuName` został zmieniony w taki sposób, aby obejmował zarówno `Premium`, jak i żądany poziom nadmiarowości lokalnie nadmiarowy (`LRS`). Parametr `-Kind` jest `FileStorage` zamiast `StorageV2` ponieważ udziały plików w warstwie Premium muszą zostać utworzone na koncie magazynu FileStorage, a nie na koncie magazynu GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, zostanie użyte polecenie AZ Storage account Create. To polecenie ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej o opcjach zaawansowanych, zobacz [dokumentację poleceń`az storage account create`](/cli/azure/storage/account).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, firma Microsoft będzie przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Aby utworzyć konto magazynu z możliwością przechowywania standardowych udziałów plików platformy Azure, użyjemy poniższego polecenia. `--sku` parametr odnosi się do żądanego typu nadmiarowości; Jeśli potrzebujesz geograficznie nadmiarowej lub geograficznie nadmiarowego konta magazynu, należy również usunąć parametr `--enable-large-file-share`.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Aby utworzyć konto magazynu z możliwością przechowywania udziałów plików platformy Azure w warstwie Premium, użyjemy poniższego polecenia. Należy zauważyć, że parametr `--sku` został zmieniony w taki sposób, aby obejmował zarówno `Premium`, jak i żądany poziom nadmiarowości lokalnie nadmiarowy (`LRS`). Parametr `--kind` jest `FileStorage` zamiast `StorageV2` ponieważ udziały plików w warstwie Premium muszą zostać utworzone na koncie magazynu FileStorage, a nie na koncie magazynu GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Tworzenie udziału plików
Po utworzeniu konta magazynu pozostało do utworzenia udziału plików. Ten proces jest w większości taka sama niezależnie od tego, czy używany jest udział plików w warstwie Premium czy standardowy udział plików. Podstawowa różnica polega na **przydziale** i tym, co reprezentuje.

W przypadku standardowych udziałów plików jest to górne ograniczenie udziału plików platformy Azure, poza tym, którzy użytkownicy końcowi nie mogą go używać. Głównym celem przydziału dla standardowego udziału plików jest budżet: "nie chcę, aby ten udział plików został powiększony poza ten punkt". Jeśli nie określono limitu przydziału, standardowy udział plików może obejmować do 100 TiB (lub 5 TiB, jeśli nie ustawiono właściwości dużych udziałów plików dla konta magazynu).

W przypadku udziałów plików w warstwie Premium limit przydziału jest przeciążony do średniego **rozmiaru**. Zarezerwowany rozmiar to kwota, za którą zostanie naliczona opłata, niezależnie od rzeczywistego użycia. Gdy udostępniasz udział plików w warstwie Premium, chcesz wziąć pod uwagę dwa czynniki: 1) przyszły wzrost udziału z perspektywy użycia miejsca i 2) liczba IOPS wymagana dla obciążenia. Każdy zainicjowany GiB uprawnia do dodatkowych operacji wejścia/wyjścia na sekundę. Aby uzyskać więcej informacji na temat planowania udziału plików w warstwie Premium, zobacz Udostępnianie [udziałów plików w warstwie Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Jeśli konto magazynu zostało właśnie utworzone, możesz przejść do niego z poziomu ekranu wdrożenia, wybierając pozycję **Przejdź do zasobu**. Jeśli wcześniej utworzono konto magazynu, możesz przejść do niego za pośrednictwem grupy zasobów zawierającej ją. Na koncie magazynu wybierz kafelek z etykietą **udziały plików** (można również przejść do **udziałów plików** za pośrednictwem spisu treści dla konta magazynu).

![Zrzut ekranu przedstawiający kafelek udziały plików](media/storage-how-to-create-file-share/create-file-share-1.png)

Na liście udział plików powinny być widoczne wszystkie udziały plików utworzone wcześniej na tym koncie magazynu. pusta tabela, jeśli nie utworzono jeszcze żadnych udziałów plików. Wybierz pozycję **+ udział plików** , aby utworzyć nowy udział plików.

Nowy blok udział plików powinien pojawić się na ekranie. Wypełnij pola w bloku nowy udział plików, aby utworzyć udział plików:

- **Name**: nazwa udziału plików, który ma zostać utworzony.
- **Przydział**: przydział udziału plików dla standardowych udziałów plików; udostępniony rozmiar udziału plików dla udziałów plików w warstwie Premium.

Wybierz pozycję **Utwórz** , aby ukończyć tworzenie nowego udziału. Należy pamiętać, że jeśli konto magazynu znajduje się w sieci wirtualnej, nie będzie można pomyślnie utworzyć udziału plików platformy Azure, chyba że klient jest również w sieci wirtualnej. Możesz również obejść to ograniczenie do czasu w czasie za pomocą polecenia cmdlet Azure PowerShell `New-AzRmStorageShare`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Udział plików platformy Azure można utworzyć za pomocą polecenia cmdlet [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) . W poniższych poleceniach programu PowerShell przyjęto założenie, że zmienne `$resourceGroupName` i `$storageAccountName`, jak zdefiniowano powyżej, znajdują się w sekcji Tworzenie konta magazynu przy użyciu Azure PowerShell. 

> [!Important]  
> Dla udziałów plików w warstwie Premium parametr `-QuotaGiB` odnosi się do udostępnionego rozmiaru udziału plików. Udostępniony rozmiar udziału plików to kwota, za którą zostanie naliczona opłata, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie od rozmiaru aprowizacji.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby można było utworzyć udział plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, należy uzyskać klucz konta magazynu w celu autoryzacji operacji tworzenia udziału plików w programie. Można to zrobić za pomocą polecenia [`az storage account keys list`](/cli/azure/storage/account/keys) :

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Gdy masz klucz konta magazynu, możesz utworzyć udział plików platformy Azure za pomocą polecenia [`az storage share create`](/cli/azure/storage/share) . 

> [!Important]  
> Dla udziałów plików w warstwie Premium parametr `--quota` odnosi się do udostępnionego rozmiaru udziału plików. Udostępniony rozmiar udziału plików to kwota, za którą zostanie naliczona opłata, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie od rozmiaru aprowizacji.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

To polecenie zakończy się niepowodzeniem, jeśli konto magazynu jest zawarte w sieci wirtualnej, a komputer, z którego jest wywoływana polecenie, nie należy do sieci wirtualnej. Można obejść to ograniczenie do określonego momentu przy użyciu polecenia cmdlet Azure PowerShell `New-AzRmStorageShare`, jak opisano powyżej, lub przez wykonanie interfejsu wiersza polecenia platformy Azure z komputera, który jest częścią sieci wirtualnej, w tym za pośrednictwem połączenia sieci VPN.

---

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Następne kroki
- [Zaplanuj wdrożenie Azure Files](storage-files-planning.md) lub [Zaplanuj wdrożenie Azure File Sync](storage-sync-files-planning.md). 
- [Omówienie sieci](storage-files-networking-overview.md).
- Łączenie i Instalowanie udziału plików w [systemach Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md).