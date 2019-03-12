---
title: Połączyć się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps za pośrednictwem środowiska usług integracji (ISE)
description: Tworzenie środowiska usługi integracji (ISE) aby logic apps i kont integracji mogą uzyskiwać dostęp do sieci wirtualnych platformy Azure (Vnet), pozostając prywatnych i odizolowane od publicznych lub "global" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: c31d260c99707f4231a6833479517b9b69575d55
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778913"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Łączenie z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps, za pomocą środowiska usługi integracji (ISE)

> [!NOTE]
> Ta funkcja jest w [ *publicznej wersji zapoznawczej*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W scenariuszach, w której muszą mieć dostęp do usługi logic apps i kont integracji [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE jest prywatne i izolowanym środowisku, używającej dedykowanego magazynu i inne zasoby oddzielone z usługi Logic Apps publicznej lub "global". Ta separacja zmniejsza żadnego wpływu, której innych dzierżaw usługi Azure może występować na wydajność Twojej aplikacji. Twoje ISE jest *wprowadzony* w sieci wirtualnej platformy Azure, który następnie wdraża usługę Logic Apps do sieci wirtualnej. Podczas tworzenia konta aplikacji lub Integracja z usługą Logic Apps, wybierz ten ISE ich lokalizacji. Twoje konto aplikacji lub Integracja z usługą Logic Apps mogą uzyskiwać bezpośrednio dostęp do zasobów, takich jak maszyny wirtualne (VM), serwery, systemów i usług w Twojej sieci wirtualnej.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

W tym artykule przedstawiono sposób wykonania następujących zadań:

* Konfigurowanie portów w sieci wirtualnej platformy Azure umożliwiające ruchu mogą być przesyłane za pośrednictwem środowiska integration service environment (ISE) między podsieciami w sieci wirtualnej.

* Tworzenie środowiska integration service environment (ISE).

* Tworzenie aplikacji logiki, które mogą być uruchamiane w swoje środowiska ISE.

* Tworzenie konta integracji aplikacji logiki w swoje środowiska ISE.

Aby uzyskać więcej informacji na temat środowisk usługi integracji, zobacz [dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

  > [!IMPORTANT]
  > Logic apps, wbudowanych akcji i łączników, działających w Twojej platformy ISE przy użyciu innego planu cen nie na podstawie użycia planu cenowego zawiera. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](../logic-apps/logic-apps-pricing.md).

* [Sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli nie masz sieci wirtualnej, Dowiedz się, jak [tworzenie sieci wirtualnej platformy Azure](../virtual-network/quick-create-portal.md). 

  * Twoja sieć wirtualna musi mieć cztery *pusty* podsieci w celu wdrażania i tworzenia zasobów w Twojej ISE. Te podsieci można utworzyć wcześniej lub możesz poczekać do czasu utworzenia Twojego środowiska ISE, gdzie można utworzyć podsieci, w tym samym czasie. Dowiedz się więcej o [wymagania podsieci](#create-subnet).

  * Upewnij się, że Twoja sieć wirtualna [udostępnia te porty](#ports) swoje ISE działa poprawnie i pozostaje dostępna.

* Na potrzeby wdrażania w sieci wirtualnej platformy Azure co najmniej jeden niestandardowe serwery DNS [Konfigurowanie tych serwerów następujące wskazówki te](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) przed wdrożeniem usługi ISE z siecią wirtualną. W przeciwnym razie po każdej zmianie serwera DNS, musisz też ponowne uruchomienie usługi ISE, co jest to funkcja, która jest dostępna w publicznej wersji zapoznawczej środowiska ISE.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Konfigurowanie portów sieciowych

Aby działać poprawnie i pozostają dostępne, środowiska integration service environment (ISE) musi mieć określone porty dostępne w sieci wirtualnej. W przeciwnym razie jeśli którekolwiek z tych portów są niedostępne, mogą utracić dostęp do ISE może spowodować nieoczekiwane zatrzymanie. Gdy używasz środowiska ISE w sieci wirtualnej powszechny problem konfiguracji ma co najmniej jeden port blokowania. W przypadku połączeń między swoje ISE i systemu docelowego łącznik, którego używasz, że własne wymagania dotyczące portów. Na przykład jeśli komunikować się z systemem FTP za pomocą łącznika usługi FTP, upewnij się, port, z którego korzystasz na dostępnej systemu FTP, takich jak port 21 do wysyłania poleceń.

Aby kontrolować ruch przychodzący i wychodzący między podsieciami sieci wirtualnej, w którym wdrożyć swoje ISE, można skonfigurować [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) dla tych podsieci ucząc [jak filtrowanie ruchu sieciowego między podsieci](../virtual-network/tutorial-filter-network-traffic.md). Te tabele zawierają opis portów w sieci wirtualnej korzystającej z ISE i Pobierz użycia tych portów. [Tag usługi](../virtual-network/security-overview.md#service-tags) reprezentuje grupę prefiksów adresów IP, które zminimalizować złożoność podczas tworzenia reguł zabezpieczeń. 

> [!IMPORTANT]
> Do wewnętrznej komunikacji wewnątrz podsieci ISE wymaga otwarcia wszystkich portów w ramach tych podsieci. 

| Przeznaczenie | Kierunek | Porty | Tag usługi źródłowej | Docelowy tag usługi | Uwagi |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Komunikacja z usługi Azure Logic Apps | Wychodzący | 80 & 443 | VIRTUAL_NETWORK | INTERNET | Numer portu jest zależna od usługi zewnętrznej, z którym komunikuje się usługi Logic Apps |
| Usługa Azure Active Directory | Wychodzący | 80 & 443 | VIRTUAL_NETWORK | AzureActiveDirectory | |
| Zależności usługi Azure Storage | Wychodzący | 80 & 443 | VIRTUAL_NETWORK | Magazyn | |
| Intersubnet komunikacji | Dla ruchu przychodzącego i wychodzącego | 80 & 443 | VIRTUAL_NETWORK | VIRTUAL_NETWORK | Do komunikacji między podsieciami |
| Komunikacja z usługi Azure Logic Apps | Przychodzący | 443 | INTERNET  | VIRTUAL_NETWORK | Adres IP komputera lub usługi, która wywołuje ani wyzwalacza żądania elementu webhook, który znajduje się w aplikacji logiki. Zamykanie lub blokuje ten port zapobiega połączeń HTTP z aplikacji logiki z wyzwalaniem na żądanie.  |
| Historia przebiegu aplikacji logiki | Przychodzący | 443 | INTERNET  | VIRTUAL_NETWORK | Adres IP komputera, na której możesz wyświetlić aplikację logiki przebiegu historii. Mimo że zamknięcie lub blokuje ten port nie uniemożliwia wyświetlanie historii uruchamiania, nie można wyświetlić dane wejściowe i wyjściowe dla każdego kroku w tym historii uruchamiania. |
| Zarządzanie połączeniami | Wychodzący | 443 | VIRTUAL_NETWORK  | INTERNET | |
| Publikowanie dzienniki diagnostyczne i metryki | Wychodzący | 443 | VIRTUAL_NETWORK  | AzureMonitor | |
| Projektant aplikacji logiki — właściwości dynamiczne | Przychodzący | 454 | INTERNET  | VIRTUAL_NETWORK | Żądania pochodzą z aplikacji logiki [dostęp do punktu końcowego dla ruchu przychodzącego adresów IP w danym regionie](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| App Service Management zależności | Przychodzący | 454 & 455 | AppServiceManagement | VIRTUAL_NETWORK | |
| Wdrażanie łącznika | Przychodzący | 454 & 3443 | INTERNET  | VIRTUAL_NETWORK | Niezbędne do wdrażania i aktualizowania łączników. Zamykanie lub blokuje ten port powoduje wdrożeń ISE, aby zakończyć się niepowodzeniem i zapobiega łącznika aktualizacji lub poprawki. |
| Zależność SQL platformy Azure | Wychodzący | 1433 | VIRTUAL_NETWORK | SQL |
| Azure Resource Health | Wychodzący | 1886 | VIRTUAL_NETWORK | INTERNET | Do publikowania stan kondycji Resource Health |
| API Management — punkt końcowy zarządzania | Przychodzący | 3443 | APIManagement  | VIRTUAL_NETWORK | |
| Zależność od dziennika do zasad Centrum zdarzeń i agenta monitorowania | Wychodzący | 5672 | VIRTUAL_NETWORK  | EventHub | |
| Uzyskiwanie dostępu do usługi Azure Cache dla wystąpienia usługi Redis między wystąpieniami roli | Przychodzący <br>Wychodzący | 6379-6383 | VIRTUAL_NETWORK  | VIRTUAL_NETWORK | Ponadto dla środowiska ISE do pracy z usługą Azure Cache dla pamięci podręcznej Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w pamięci podręcznej Azure dla usługi Redis — często zadawane pytania](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Przychodzący | * | AZURE_LOAD_BALANCER | VIRTUAL_NETWORK |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Utwórz swoje środowiska ISE

Aby utworzyć środowiska integration service environment (ISE), wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **Utwórz zasób**.

   ![Tworzenie nowego zasobu](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W polu wyszukiwania wprowadź "środowisko usługi integracji" jako filtr.
Wybierz z listy wyników **środowisko usługi integracji (wersja zapoznawcza)**, a następnie wybierz **Utwórz**.

   ![Wybierz pozycję "Środowisko integracji usługi"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Wybierz pozycję "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Podaj te szczegóły dla danego środowiska, a następnie wybierz **przeglądanie + tworzenie**, na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Yes | <*Azure-subscription-name*> | Subskrypcji platformy Azure do użycia w danym środowisku |
   | **Grupa zasobów** | Yes | <*Azure-resource-group-name*> | Grupa zasobów platformy Azure, w którym chcesz utworzyć swoje środowisko |
   | **Nazwa środowiska usługi integracji** | Yes | <*Nazwa środowiska*> | Nazwa do nadania środowiska |
   | **Lokalizacja** | Yes | <*Azure-datacenter-region*> | Region centrum danych platformy Azure miejsca wdrożenia środowiska |
   | **Dodatkowe możliwości obliczeniowe** | Yes | 0, 1, 2, 3 | Liczba jednostek przetwarzania do użycia dla tego zasobu platformy ISE. Aby dodać pojemności po jej utworzeniu, zobacz [dodanie pojemności](#add-capacity). |
   | **Sieć wirtualna** | Yes | <*Azure-virtual-network-name*> | Azure sieci wirtualnej, której chcesz wstawić środowiska, dzięki czemu aplikacje logiki w tym środowisku mają dostęp do sieci wirtualnej. Jeśli nie masz sieci, można utworzyć jeden tutaj. <p>**Ważne**: Możesz *tylko* wykonywać takie działanie, podczas tworzenia usługi ISE. Jednakże przed utworzeniem tej relacji, upewnij się, że już [Konfigurowanie kontroli dostępu opartej na rolach w Twojej sieci wirtualnej dla usługi Azure Logic Apps](#vnet-access). |
   | **Podsieci** | Yes | <*subnet-resource-list*> | ISE wymaga czterech *pusty* podsieci w celu tworzenia zasobów w danym środowisku. Do tworzenia każdej podsieci [wykonaj czynności opisane w tej tabeli](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   Twoje ISE wymaga czterech *pusty* podsieci, *nie są delegowane* do dowolnej usługi do tworzenia zasobów w danym środowisku. 
   Możesz *nie można zmienić* tych adresów podsieci po utworzeniu środowiska. Każda podsieć musi spełniać następujące kryteria:

   * Używa nazwy nie zaczyna się od numeru lub łącznik.

   * Używa [formatu Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) i przestrzeń adresów klasy B.

   * Używa co najmniej jeden `/27` adres miejsca, ponieważ każda podsieć musi mieć 32 adresów jako *minimalne*. Na przykład:

     * `10.0.0.0/27` zawiera 32 adresów, ponieważ 2<sup>(32-27)</sup> 2<sup>5</sup> lub 32.

     * `10.0.0.0/24` ma 256 adresów, ponieważ 2<sup>(32-24)</sup> 2<sup>8</sup> lub 256.

     * `10.0.0.0/28` ma tylko 16 adresów i jest za mały, ponieważ 2<sup>(32-28)</sup> 2<sup>4</sup> lub 16.

     Aby dowiedzieć się więcej na temat obliczania adresów, zobacz [bloków IPv4 CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   1. W obszarze **podsieci** wybierz **konfigurację podsieci Zarządzaj**.

      ![Zarządzaj konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Na **podsieci** okienku wybierz **podsieci**.

      ![Dodawanie podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Na **Dodaj podsieć** okienku, podaj te informacje.

      * **Nazwa**: Nazwa podsieci
      * **Zakres adresów (blok CIDR)**: Zakres swoje podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodaj szczegóły podsieci](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   1. Powtórz te czynności dla trzech więcej podsieci.

1. Po Azure weryfikuje pomyślnie informacji ISE, wybierz **Utwórz**, na przykład:

   ![Po pomyślnej weryfikacji wybierz polecenie "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure rozpoczyna się w środowisku, ale ten proces wdrażania *może* dwóch godzin przed zakończeniem. 
   Aby sprawdzić stan wdrożenia, na pasku narzędzi usługi Azure wybierz ikonę powiadomienia, który zostanie otwarty w okienku powiadomienia.

   ![Sprawdź stan wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Jeśli wdrożenie zakończy się pomyślnie, Azure pokazuje to powiadomienie:

   ![Wdrażanie zakończyło się pomyślnie](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Jeśli wdrożenie zakończy się niepowodzeniem lub usunąć platformy ISE, Azure *może* potrwać do godziny przed zwolnieniem podsieci. Dlatego trzeba odczekać przez ponowne użycie tych podsieci w innym środowisku ISE.

1. Aby wyświetlić swoje środowisko, wybierz opcję **przejdź do zasobu** Jeśli Azure nie automatyczne przejście do środowiska, po zakończeniu wdrożenia.  

<a name="add-capacity"></a>

### <a name="add-capacity"></a>Dodawaj możliwości obliczeniowe

Jednostkę podstawową ISE ma ustaloną pojemność, więc jeśli potrzebujesz więcej przepływności, można dodać kolejne jednostki skalowania. Możesz wybrać opcję skalowania automatycznego w oparciu o metryki wydajności lub na podstawie określonej liczby jednostek. Jeśli wybierzesz opcję skalowania automatycznego na podstawie metryk, należy wybrać różnych kryteriów i określić warunki progowe spełniających kryteria tego.

1. W witrynie Azure portal Znajdź swoje ISE.

1. Aby wyświetlić metryki wydajności dla Twojego środowiska ISE w menu głównym usługi ISE, wybierz **Przegląd**.

1. Aby skonfigurować automatyczne skalowanie, w obszarze **ustawienia**, wybierz opcję **skalowanie w poziomie**. Na **Konfiguruj** kartę, wybrać **włączyć Skalowanie automatyczne**.

1. W **domyślne** sekcji, wybierają **skalowania na podstawie metryki** lub **Skaluj do określonej liczby wystąpień**.

1. Jeśli zdecydujesz się na podstawie wystąpienia, wprowadź liczbę jednostek przetwarzania zakresu od 0 do 3 (włącznie). W przeciwnym razie dla opartą na metryce, wykonaj następujące kroki:

   1. W **domyślne** wybierz pozycję **Dodaj regułę**.

   1. Na **reguły skalowania** okienku skonfigurować Twoje kryteria i akcję do wykonania po wyzwoleniu reguły.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj**.

1. Gdy skończysz, pamiętaj, aby zapisać zmiany.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Tworzenie aplikacji logiki — środowiska ISE

Aby tworzyć aplikacje logiki, które używają środowiska integration service environment (ISE), wykonaj kroki opisane w [jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) jednak z następującymi ograniczeniami: 

* Podczas tworzenia aplikacji logiki, w obszarze **lokalizacji** właściwości, wybierz swoje ISE z **środowiska usług integracji** sekcji, na przykład:

  ![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Można użyć tych samych wbudowanych wyzwalacze i akcje, takie jak HTTP, które działają w tej samej platformy ISE jako aplikację logiki. Łączniki z usługą **ISE** również etykietę uruchomienia w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Po należy wstrzyknąć Twojego środowiska ISE w sieci wirtualnej platformy Azure, logic apps w swoje środowiska ISE można uzyskać dostęp do zasobów w tej sieci wirtualnej. Dla systemów lokalnych, które są podłączone do sieci wirtualnej należy wstrzyknąć środowiska ISE do tej sieci, dzięki czemu aplikacje logiki można uzyskać dostęp do tych systemów przy użyciu dowolnego z tych elementów: 

  * Łącznik platformy ISE dla tego systemu, na przykład SQL Server
  
  * Akcja HTTP 
  
  * Łącznik niestandardowy

  Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz łączniki ISE, najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Utwórz konto integracji — środowiska ISE

Aby użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE), należy użyć tego konta integracji *tym samym środowisku* co usługa logic apps. Logic apps w środowisku ISE można odwoływać się tylko konta integracji w tym samym środowisku ISE. 

Aby utworzyć konto integracji, która używa środowiska ISE, wykonaj kroki opisane w [sposób tworzenia kont integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z wyjątkiem **lokalizacji** właściwość gdzie **środowiska usług integracji**  pojawi się w sekcji. Zamiast tego wybierz swoje ISE, a nie region, na przykład:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="https://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
