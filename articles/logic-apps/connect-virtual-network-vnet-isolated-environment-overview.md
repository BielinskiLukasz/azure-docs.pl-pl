---
title: Dostęp do sieci wirtualnych platformy Azure
description: Omówienie sposobu, w jaki środowiska usług Integration Service (ISEs) ułatwiają aplikacjom logiki dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127250"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)

Czasami Aplikacje logiki muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy i usługi, które znajdują się w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, można [utworzyć *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE jest izolowanym wystąpieniem usługi Logic Apps, która używa dedykowanych zasobów i jest uruchamiana niezależnie od "globalnej" usługi Logic Apps z wieloma dzierżawami.

Uruchamianie aplikacji logiki we własnym osobnym wystąpieniu izolowanym pozwala zmniejszyć wpływ innych dzierżawców platformy Azure na wydajność aplikacji, nazywanych również [efektem "sąsiedzi](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)". ISE zapewnia również następujące korzyści:

* Własne statyczne adresy IP, które są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w usłudze wielu dzierżawców. W celu komunikowania się z systemami docelowymi można także skonfigurować jeden publiczny, statyczny i przewidywalny wychodzący adres IP. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w tych systemach docelowych dla każdego ISEu.

* Zwiększono limity czasu trwania przebiegu, przechowywanie magazynu, przepływność, żądania HTTP i limity czasu odpowiedzi, rozmiary komunikatów i żądania łączników niestandardowych. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych ([prywatnego linku platformy Azure](../private-link/private-link-overview.md)) do zapewniania dostępu do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB lub Azure SQL Database, usług partnerskich lub usług klienta hostowanych na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które używają prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki w ramach ISE.

Po utworzeniu ISE platforma Azure wprowadza lub wdraża te ISE w sieci wirtualnej *platformy Azure.* Następnie można użyć tego ISE jako lokalizacji dla aplikacji logiki i kont integracji, które wymagają dostępu.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikacje logiki mogą uzyskać dostęp do zasobów, które znajdują się w sieci wirtualnej lub połączone z nią, za pomocą tych elementów, które działają w tym samym ISE co Aplikacje logiki:

* Wbudowany wyzwalacz lub akcja z oznaczeniem **rdzenia**, na przykład wyzwalacz http lub Akcja
* Łącznik **ISE**z etykietą dla tego systemu lub usługi
* Łącznik niestandardowy

Nadal można także używać łączników, które nie mają etykiety **Core** ani **ISE** z aplikacjami logiki w ISE. Te łączniki działają w ramach usługi Logic Apps z wieloma dzierżawcami. Aby uzyskać więcej informacji, zobacz następujące sekcje:

* [Izolowane względem wielu dzierżawców](#difference)
* [Nawiązywanie połączenia ze środowiskiem usługi integracji](../connectors/apis-list.md#integration-service-environment)
* [Łączniki ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego, który różni się od planu cenowego opartego na zużyciu. Aby uzyskać więcej informacji, zobacz [Logic Apps model cen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Logic Apps](../logic-apps/logic-apps-pricing.md).

W tym omówieniu opisano więcej informacji o tym, jak usługa ISE umożliwia aplikacjom logiki bezpośredni dostęp do sieci wirtualnej platformy Azure, a następnie porównuje różnice między ISE i wielodostępną usługą Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Izolowane względem wielu dzierżawców

Podczas tworzenia i uruchamiania aplikacji logiki w ISE można korzystać z tych samych środowisk użytkownika i podobnych funkcji, co usługa Logic Apps wielodostępna. Można użyć wszystkich wbudowanych wyzwalaczy, akcji i łączników zarządzanych, które są dostępne w usłudze Logic Apps wielodostępnej. Niektóre zarządzane łączniki oferują dodatkowe wersje ISE. Różnica między łącznikami ISE i łącznikami innymi niż ISE istnieją w miejscu, w którym są uruchamiane, oraz etykietami, które znajdują się w Projektancie aplikacji logiki, gdy Pracujesz w ISE.

![Łączniki z etykietami i bez nich w ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Wbudowane wyzwalacze i akcje wyświetlają **podstawową** etykietę. Są one zawsze uruchamiane w tym samym ISE, w którym znajduje się aplikacja logiki. Łączniki zarządzane, które wyświetlają etykietę **ISE** , są również uruchamiane w tym samym ISE, jak w aplikacji logiki.

  Na przykład Oto kilka łączników oferujących wersje ISE:

  * Blob Storage, File Storage i Table Storage platformy Azure
  * Azure Queues, Azure Service Bus, Azure Event Hubs i IBM MQ
  * FTP i SFTP — SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 i EDIFACT

* Łączniki zarządzane, które nie wyświetlają żadnych dodatkowych etykiet, są zawsze uruchamiane w usłudze Logic Apps z wieloma dzierżawcami, ale nadal można używać tych łączników w aplikacji logiki hostowanej przez usługę ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Dostęp do systemów lokalnych

Aby uzyskać dostęp do systemów lokalnych lub źródeł danych, które są połączone z siecią wirtualną platformy Azure, usługa Logic Apps w ISE może używać następujących elementów:

* Akcja HTTP

* Łącznik ISE z etykietą dla tego systemu

  > [!NOTE]
  > Aby użyć uwierzytelniania systemu Windows za pomocą łącznika SQL Server w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), użyj wersji innej niż ISE łącznika z [lokalną bramą danych](../logic-apps/logic-apps-gateway-install.md). ISE wersja nie obsługuje uwierzytelniania systemu Windows.

* Łącznik niestandardowy

  * Jeśli masz łączniki niestandardowe wymagające lokalnej bramy danych i utworzono te łączniki poza ISE, Aplikacje logiki w ISE mogą również używać tych łączników.

  * Łączniki niestandardowe utworzone w ISE nie współpracują z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskać dostęp do lokalnych źródeł danych, które są połączone z siecią wirtualną hostującym ISE. W związku z tym aplikacje logiki w ISE najprawdopodobniej nie potrzebują bramy danych podczas komunikowania się z tymi zasobami.

W przypadku systemów lokalnych, które nie są połączone z siecią wirtualną ani nie mają łączników z ISEmi, musisz najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) , aby umożliwić aplikacjom logiki łączenie się z tymi systemami.

<a name="ise-level"></a>

## <a name="ise-skus"></a>Jednostki SKU ISE

Po utworzeniu ISE można wybrać jednostkę SKU dla deweloperów lub jednostkę SKU Premium. Poniżej przedstawiono różnice między tymi jednostkami SKU:

* **Developer**

  Oferuje tańsze ISE, których można użyć do eksperymentowania, programowania i testowania, ale nie do testowania produkcji ani wydajności. Jednostka SKU dla deweloperów obejmuje wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa oraz zintegrowane konto integracji z jedną [warstwą](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) dla ustalonej ceny miesięcznej. Jednak ta jednostka SKU nie obejmuje żadnej umowy dotyczącej poziomu usług (SLA), opcji skalowania pojemności lub nadmiarowości podczas odtwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje.

* **Premium**

  Oferuje ISE, którego można użyć do produkcji i obejmuje obsługę umów SLA, wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa, pojedyncze konto integracji [warstwy standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opcje skalowania pojemności i nadmiarowości podczas odtwarzania przez ustaloną cenę miesięczną.

> [!IMPORTANT]
> Opcja SKU jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić.

Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Dostęp do punktu końcowego ISE

Po utworzeniu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej.

Te punkty końcowe wpływają również na sposób, w jaki można uzyskać dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki.

* **Wewnętrzne**: prywatne punkty końcowe zezwalające na wywołania aplikacji LOGIKI w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w ramach historii uruchamiania *tylko z poziomu sieci wirtualnej*

* **Zewnętrzne**: publiczne punkty końcowe, które umożliwiają wywoływanie usługi Logic Apps w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w historii uruchamiania *spoza sieci wirtualnej*. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), upewnij się, że są one skonfigurowane przy użyciu reguł ruchu przychodzącego, aby zezwolić na dostęp do danych wejściowych i wyjściowych historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Opcja punktu końcowego dostępu jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji z usługą ISE

Konta integracji z aplikacjami logiki można używać w środowisku usługi integracji (ISE). Jednak te konta integracji muszą używać tego *samego ISE* , co połączone Aplikacje logiki. Aplikacje logiki w ISE mogą odwoływać się tylko do tych kont integracji, które znajdują się w tym samym ISE. Podczas tworzenia konta integracji możesz wybrać swój ISE jako lokalizację konta integracji. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla kont integracji za pomocą ISE, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Następne kroki

* [Łączenie z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
