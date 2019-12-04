---
title: Konfigurowanie dostępu dla wielu ISEs
description: W przypadku wielu środowisk usługi Integration Service (ISEs) można skonfigurować jeden publiczny adres IP wychodzący, aby uzyskać dostęp do systemów zewnętrznych z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f3b422a55b7e2abbc8b1538183fd57fb234900d4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792690"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Konfigurowanie dostępu dla wielu środowisk usługi integracji w Azure Logic Apps

Podczas pracy z Azure Logic Apps można skonfigurować [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) do hostowania aplikacji logiki, które wymagają dostępu do zasobów w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli masz wiele wystąpień ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, wdróż [zaporę platformy Azure](../firewall/overview.md) lub [sieciowe urządzenie wirtualne](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i Roześlij ruch wychodzący za pomocą tej zapory lub wirtualnego urządzenia sieciowego. Następnie wszystkie wystąpienia ISE w sieci wirtualnej używają jednego, przewidywalnego i publicznego adresu IP do komunikacji z systemami docelowymi. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w systemach docelowych dla każdego ISEu. W tym temacie pokazano, jak skierować ruch wychodzący za pośrednictwem zapory platformy Azure, ale możesz zastosować podobne koncepcje do wirtualnego urządzenia sieciowego, takiego jak zapora innej firmy z witryny Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

* Zapora platformy Azure działająca w tej samej sieci wirtualnej co ISE. Jeśli nie masz zapory, najpierw [Dodaj podsieć](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) o nazwie `AzureFirewallSubnet` do sieci wirtualnej. Następnie możesz [utworzyć i wdrożyć zaporę](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) w sieci wirtualnej.

* [Tabela tras](../virtual-network/manage-route-table.md)platformy Azure. Jeśli nie masz takiego, należy najpierw [utworzyć tabelę tras](../virtual-network/manage-route-table.md#create-a-route-table). Aby uzyskać więcej informacji na temat routingu, zobacz [routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Konfigurowanie tabeli tras

1. W [Azure Portal](https://portal.azure.com)wybierz tabelę tras, na przykład:

   ![Wybierz tabelę tras z regułą dla ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Aby [dodać nową trasę](../virtual-network/manage-route-table.md#create-a-route), w menu tabela tras wybierz pozycję **trasy** > **Dodaj**.

   ![Dodawanie trasy do kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. W okienku **Dodawanie trasy** [Skonfiguruj nową trasę](../virtual-network/manage-route-table.md#create-a-route) z regułą określającą, że cały ruch wychodzący do systemu docelowego jest zgodny z tym zachowaniem:

   * Używa [**urządzenia wirtualnego**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako typu następnego przeskoku.

   * Przechodzi do prywatnego adresu IP dla wystąpienia zapory jako adres następnego przeskoku.

     Aby znaleźć ten adres IP, w menu zapory wybierz pozycję **Przegląd**, Znajdź adres w obszarze **prywatny adres IP**, na przykład:

     ![Znajdowanie prywatnego adresu IP zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Oto przykład, który pokazuje, jak taka reguła może wyglądać:

   ![Konfigurowanie reguły kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa trasy** | <*unikatową nazwę trasy*> | Unikatowa nazwa trasy w tabeli tras |
   | **Prefiks adresu** | <*adres docelowy*> | Adres systemu docelowego, do którego ma być wykonywany ruch. Upewnij się, że dla tego adresu jest używana [notacja CIDR (Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . |
   | **Typ następnego skoku** | **Urządzenie wirtualne** | [Typ przeskoku](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) używany przez ruch wychodzący |
   | **Adres następnego skoku** | <*firewall-Private-IP-address*> | Prywatny adres IP dla zapory |
   |||

## <a name="set-up-network-rule"></a>Konfigurowanie reguły sieci

1. W Azure Portal Znajdź i wybierz zaporę. W menu Zapora w obszarze **Ustawienia**wybierz pozycję **reguły**. W okienku reguły wybierz pozycję **Kolekcja reguł sieciowych** > **Dodawanie kolekcji reguł sieci**.

   ![Dodawanie kolekcji reguł sieci do zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. W kolekcji Dodaj regułę zezwalającą na ruch do systemu docelowego.

   Załóżmy na przykład, że masz aplikację logiki, która działa w ISE i musi komunikować się z systemem SFTP. Tworzysz kolekcję reguł sieci o nazwie `LogicApp_ISE_SFTP_Outbound`, która zawiera regułę sieci o nazwie `ISE_SFTP_Outbound`. Ta zasada zezwala na ruch z adresu IP każdej podsieci, w której ISE działa w sieci wirtualnej, do docelowego systemu SFTP przy użyciu prywatnego adresu IP zapory.

   ![Konfigurowanie reguły sieci dla zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Właściwości kolekcji reguł sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwę> sieci-kolekcji* | Nazwa kolekcji reguł sieci |
   | **Priorytet** | > <*poziomu priorytetu* | Kolejność, w której ma zostać uruchomiona Kolekcja reguł. Aby uzyskać więcej informacji, zobacz [co to są niektóre zagadnienia dotyczące zapory platformy Azure](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Akcja** | **Zezwalaj** | Typ akcji do wykonania dla tej reguły |
   |||

   **Właściwości reguły sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwę reguły sieci*> | Nazwa reguły sieci |
   | **Protokół** | <*połączenia — protokoły*> | Protokoły połączeń do użycia. Na przykład jeśli korzystasz z reguł sieciowej grupy zabezpieczeń, wybierz zarówno **TCP** , jak i **UDP**, a nie tylko **TCP**. |
   | **Adresy źródłowe** | <*ISE — adresy podsieci*> | Adresy IP podsieci, w których są uruchamiane ISE oraz miejsce, w którym pochodzą ruch z aplikacji logiki |
   | **Adresy docelowe** | <*miejsce docelowe — adres IP*> | Adres IP systemu docelowego, do którego ma nastąpić ruch |
   | **Porty docelowe** | <*portów docelowych*> | Wszystkie porty używane przez system docelowy na potrzeby komunikacji przychodzącej |
   |||

   Aby uzyskać więcej informacji na temat reguł sieci, zobacz następujące artykuły:

   * [Konfigurowanie reguły sieci](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika przetwarzania reguł zapory platformy Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Często zadawane pytania dotyczące zapory platformy Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfejs wiersza polecenia platformy Azure: AZ Network firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Następne kroki

* [Łączenie z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)