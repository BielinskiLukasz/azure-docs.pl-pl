---
title: Nawiązywanie połączenia z kontem usługi Azure Cosmos za pomocą prywatnego linku platformy Azure
description: Dowiedz się, jak bezpiecznie uzyskać dostęp do konta usługi Azure Cosmos z maszyny wirtualnej, tworząc prywatny punkt końcowy.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252600"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Połącz się prywatnie z kontem usługi Azure Cosmos za pomocą prywatnego linku platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie z zasobami łączy prywatnych.

W tym artykule dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure i konto usługi Azure Cosmos z prywatnym punktem końcowym przy użyciu Azure Portal. Następnie możesz bezpiecznie uzyskać dostęp do konta usługi Azure Cosmos z poziomu maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Azure Portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (konto usługi Azure Cosmos w tym przykładzie).

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<Nazwa grupy zasobów>**  | myResourceGroup|
| **\<Nazwa sieci wirtualnej>** | myVirtualNetwork         |
| **\<Nazwa regionu>**          | Zachodnio-środkowe stany USA     |
| **\<Adresy IPv4>miejsca**   | 10.1.0.0 \ 16          |
| **\<>nazwy podsieci**          | mySubnet        |
| **\<>zakresu adresów podsieci** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Wprowadź ponownie hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="create-an-azure-cosmos-account"></a>tworzenie konta usługi Azure Cosmos

Utwórz [konto interfejsu API SQL usługi Azure Cosmos](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Dla uproszczenia można utworzyć konto usługi Azure Cosmos w tym samym regionie, w którym są inne zasoby (czyli "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos

Utwórz prywatny link do konta usługi Azure Cosmos, zgodnie z opisem w sekcji [Tworzenie prywatnego linku przy użyciu Azure Portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) w połączonym artykule.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik *rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji** > **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Dostęp do konta usługi Azure Cosmos do prywatnego z poziomu maszyny wirtualnej

W tej sekcji nastąpi połączenie prywatne z kontem usługi Azure Cosmos za pomocą prywatnego punktu końcowego. 

1. Aby uwzględnić adresy IP i mapowania DNS, zaloguj się do maszyny wirtualnej *myVM*, Otwórz `c:\Windows\System32\Drivers\etc\hosts` plik i Dołącz informacje DNS z poprzedniego kroku w następującym formacie:

   [Prywatny adres IP] [Punkt końcowy konta]. Documents. Azure. com

   **Przykład:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. W Pulpit zdalny *myVM*zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Wybierz pozycję **konta Cosmos dB (wersja zapoznawcza)** , klikając prawym przyciskiem myszy.

1. Wybierz pozycję **Połącz z Cosmos DB**.

1. Wybierz pozycję **Interfejs API**.

1. Wprowadź parametry połączenia, wklejając wcześniej skopiowane informacje.

1. Wybierz pozycję **Dalej**.

1. Wybierz przycisk **Połącz**.

1. Przeglądaj bazy danych i kontenery usługi Azure Cosmos z *mycosmosaccount*.

1. (Opcjonalnie) Dodaj nowe elementy do *mycosmosaccount*.

1. Zamknij połączenie pulpitu zdalnego z *myVM*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z prywatnego punktu końcowego, konta usługi Azure Cosmos i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 

1. Wprowadź w polu **wyszukiwania** w górnej części portalu *i wybierz pozycję* *moja zasobów z* wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono MASZYNę wirtualną w sieci wirtualnej, konto usługi Azure Cosmos i prywatny punkt końcowy. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z kontem usługi Azure Cosmos za pomocą linku prywatnego.

* Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).

* Aby dowiedzieć się więcej na temat ograniczenia prywatnego punktu końcowego w przypadku korzystania z programu z usługą Azure Cosmos DB, zobacz [link prywatny platformy Azure z Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artykułem.
