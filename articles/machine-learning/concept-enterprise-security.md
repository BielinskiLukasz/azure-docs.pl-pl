---
title: Zabezpieczenia przedsiębiorstwa
titleSuffix: Azure Machine Learning
description: 'Bezpiecznie używaj Azure Machine Learning: uwierzytelnianie, autoryzacja, zabezpieczenia sieci, szyfrowanie danych i monitorowanie.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/09/2020
ms.openlocfilehash: 32b3135f805cc6c68d8cd9d6fa2b6f957cd140ad
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444149"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Zabezpieczenia przedsiębiorstwa dla Azure Machine Learning

W tym artykule przedstawiono informacje o funkcjach zabezpieczeń dostępnych dla Azure Machine Learning.

W przypadku korzystania z usługi w chmurze najlepszym rozwiązaniem jest ograniczenie dostępu tylko do użytkowników, którzy ich potrzebują. Zacznij od ustalenia modelu uwierzytelniania i autoryzacji używanego przez usługę. Możesz również ograniczyć dostęp do sieci lub bezpiecznie dołączać zasoby w sieci lokalnej do chmury. Szyfrowanie danych jest również niezbędne, zarówno w czasie spoczynku, jak i podczas przenoszenia danych między usługami. Na koniec należy mieć możliwość monitorowania usługi i tworzenia dziennika inspekcji dla wszystkich działań.

> [!NOTE]
> Informacje przedstawione w tym artykule współdziałają z Azure Machine Learning Python SDK w wersji 1.0.83.1 lub nowszej.

## <a name="authentication"></a>Uwierzytelnianie

Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli Azure Active Directory (Azure AD) jest skonfigurowany do korzystania z niego. Oto proces uwierzytelniania:

1. Klient loguje się do usługi Azure AD i pobiera token Azure Resource Manager.  Nazwy główne użytkowników i usług są w pełni obsługiwane.
1. Klient przedstawia token do Azure Resource Manager i wszystkich Azure Machine Learning.
1. Usługa Machine Learning udostępnia token usługi Machine Learning dla elementu docelowego obliczeń użytkownika (na przykład środowisko obliczeniowe usługi Machine Learning). Ten token jest używany przez element docelowy obliczeń użytkownika do wywołania zwrotnego do usługi Machine Learning po zakończeniu przebiegu. Zakres jest ograniczony do obszaru roboczego.

[Uwierzytelnianie ![w Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla Azure Machine Learning zasobów i przepływów pracy](how-to-setup-authentication.md). Ten artykuł zawiera informacje i przykłady dotyczące uwierzytelniania, w tym użycie jednostek usługi i zautomatyzowanych przepływów pracy.


### <a name="authentication-for-web-service-deployment"></a>Uwierzytelnianie dla wdrożenia usługi sieci Web

Azure Machine Learning obsługuje dwie formy uwierzytelniania dla usług sieci Web: klucz i token. Każda usługa sieci Web może jednocześnie włączyć tylko jedną formę uwierzytelniania.

|Metoda uwierzytelniania|Opis|Azure Container Instances|AKS|
|---|---|---|---|
|Klucz|Klucze są statyczne i nie muszą być odświeżane. Klucze można generować ponownie ręcznie.|Domyślnie wyłączone| Domyślnie włączona|
|Token|Tokeny wygasają po upływie określonego czasu i wymagają odświeżenia.| Niedostępne| Domyślnie wyłączone |

Aby zapoznać się z przykładami kodu, zobacz [sekcję uwierzytelnianie usługi sieci Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autoryzacja

Możesz utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Po udostępnieniu obszaru roboczego można kontrolować do niego dostęp, przypisując te role do użytkowników:

* Właściciel
* Współautor
* Czytelnik

W poniższej tabeli wymieniono niektóre główne operacje Azure Machine Learning i role, które mogą je wykonać:

| Operacja Azure Machine Learning | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnianie obszaru roboczego | ✓ | |  |
| Uaktualnij obszar roboczy do wersji Enterprise | ✓ | |
| Utwórz element docelowy obliczeń | ✓ | ✓ | |
| Dołącz cel obliczeń | ✓ | ✓ | |
| Dołącz magazyny danych | ✓ | ✓ | |
| Uruchamianie eksperymentu | ✓ | ✓ | |
| Wyświetl przebiegi/metryki | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Utwórz obraz | ✓ | ✓ | |
| Wdróż usługę sieci Web | ✓ | ✓ | |
| Wyświetlanie modeli/obrazów | ✓ | ✓ | ✓ |
| Wywoływanie usługi sieci Web | ✓ | ✓ | ✓ |

Jeśli wbudowane role nie spełniają Twoich potrzeb, można utworzyć role niestandardowe. Role niestandardowe są obsługiwane tylko w przypadku operacji w obszarze roboczym i środowisko obliczeniowe usługi Machine Learning. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu lub usuwania w obszarze roboczym i w zasobie obliczeniowym w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na określonym poziomie grupy zasobów lub na określonym poziomie subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami w obszarze roboczym Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Zabezpieczanie obiektów docelowych obliczeń i danych

Właściciele i Współautorzy mogą używać wszystkich obiektów docelowych obliczeń i magazynów danych, które są dołączone do obszaru roboczego.  

Każdy obszar roboczy ma także skojarzoną tożsamość zarządzaną przypisaną przez system, która ma taką samą nazwę jak obszar roboczy. Zarządzana tożsamość ma następujące uprawnienia dotyczące dołączonych zasobów używanych w obszarze roboczym.

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Zasób | Uprawnienia |
| ----- | ----- |
| Obszar roboczy | Współautor |
| Konto magazynu | Współautor danych obiektu blob magazynu |
| Magazyn kluczy | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Azure Container Registry | Współautor |
| Grupa zasobów, która zawiera obszar roboczy | Współautor |
| Grupa zasobów zawierająca Magazyn kluczy (jeśli jest inna niż ta, która zawiera obszar roboczy) | Współautor |

Nie zaleca się, aby administratorzy mogli odwołać dostęp do tożsamości zarządzanej do zasobów wymienionych w powyższej tabeli. Dostęp można przywrócić przy użyciu operacji ponowna synchronizacja kluczy.

Azure Machine Learning tworzy dodatkową aplikację (nazwa rozpoczyna się od `aml-` lub `Microsoft-AzureML-Support-App-`) z dostępem na poziomie współautora w ramach subskrypcji dla każdego regionu obszaru roboczego. Jeśli na przykład masz jeden obszar roboczy w regionie Wschodnie stany USA i jeden w Europie Północnej w tej samej subskrypcji, zobaczysz dwie z tych aplikacji. Te aplikacje umożliwiają Azure Machine Learning ułatwiające zarządzanie zasobami obliczeniowymi.

## <a name="network-security"></a>Bezpieczeństwo sieci

Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (cele obliczeniowe) są używane do uczenia i wdrażania modeli. Te obiekty docelowe obliczeń można utworzyć w sieci wirtualnej. Na przykład możesz użyć usługi Azure Data Science Virtual Machine, aby szkolić model, a następnie wdrożyć model w AKS.  

Aby uzyskać więcej informacji, zobacz [jak uruchamiać eksperymenty i wnioskowania w sieci wirtualnej](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

> [!IMPORTANT]
> Jeśli obszar roboczy zawiera dane poufne, zalecamy ustawienie [flagi hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) podczas tworzenia obszaru roboczego. Pozwala to kontrolować ilość danych zbieranych przez firmę Microsoft do celów diagnostycznych i umożliwia dodatkowe szyfrowanie w środowiskach zarządzanych przez firmę Microsoft.


#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie usługi Azure Blob Storage, które jest powiązane z obszarem roboczym Azure Machine Learning i subskrypcją. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby uzyskać informacje na temat korzystania z własnych kluczy dla danych przechowywanych w usłudze Azure Blob Storage, zobacz [szyfrowanie usługi Azure Storage z kluczami zarządzanymi przez klienta w Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Dane szkoleniowe są zazwyczaj przechowywane w usłudze Azure Blob Storage, dzięki czemu są dostępne do uczenia celów obliczeniowych. Ten magazyn nie jest zarządzany przez Azure Machine Learning ale jest instalowany do celów obliczeniowych jako zdalny system plików.

Aby uzyskać informacje na temat ponownego generowania kluczy dostępu, zobacz Ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning przechowuje metryki i metadane w wystąpieniu Azure Cosmos DB. To wystąpienie jest skojarzone z subskrypcją firmy Microsoft zarządzaną przez Azure Machine Learning. Wszystkie dane przechowywane w Azure Cosmos DB są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby użyć własnych kluczy (zarządzanych przez klienta) do zaszyfrowania wystąpienia Azure Cosmos DB, można utworzyć dedykowane wystąpienie Cosmos DB do użycia z Twoim obszarem roboczym. Zalecamy to podejście, jeśli chcesz przechowywać dane, takie jak informacje o historii uruchamiania, poza wystąpieniem Cosmos DB wielodostępnego hostowanym w naszej subskrypcji firmy Microsoft. 

> [!NOTE]
> Ta funkcja jest obecnie dostępna tylko w regionach Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA.

Aby włączyć obsługę administracyjną wystąpienia Cosmos DB w ramach subskrypcji z kluczami zarządzanymi przez klienta, wykonaj następujące czynności:

* Włącz funkcje klucza zarządzanego przez klienta dla Cosmos DB. W tej chwili należy zażądać dostępu do korzystania z tej funkcji. Aby to zrobić, skontaktuj się z [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

* Zarejestruj Azure Machine Learning i Azure Cosmos DB dostawców zasobów w subskrypcji, jeśli jeszcze nie zostało to zrobione.

* Autoryzuj aplikację Machine Learning (w temacie Zarządzanie tożsamościami i dostępem) z uprawnieniami współautora w ramach subskrypcji.

    ![Autoryzuj aplikację "Azure Machine Learning App" w temacie Zarządzanie tożsamościami i dostępem w portalu](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Podczas tworzenia obszaru roboczego Azure Machine Learning Użyj następujących parametrów. Oba parametry są obowiązkowe i obsługiwane w zestawach SDK, interfejsu wiersza polecenia, interfejsach API REST i szablonach Menedżer zasobów.

    * `resource_cmk_uri`: ten parametr to pełny identyfikator URI zasobu klucza zarządzanego przez klienta w magazynie kluczy, w tym [Informacje o wersji klucza](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: ten parametr jest IDENTYFIKATORem zasobu magazynu kluczy w subskrypcji. Ten magazyn kluczy musi znajdować się w tym samym regionie i subskrypcji, który będzie używany dla obszaru roboczego Azure Machine Learning. 
    
        > [!NOTE]
        > To wystąpienie magazynu kluczy może być inne niż magazyn kluczy tworzony przez Azure Machine Learning podczas aprowizacji obszaru roboczego. Jeśli chcesz użyć tego samego wystąpienia magazynu kluczy dla obszaru roboczego, Przekaż ten sam magazyn kluczy podczas aprowizacji obszaru roboczego za pomocą [parametru key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

To wystąpienie Cosmos DB jest tworzone w grupie zasobów zarządzanej przez firmę Microsoft w ramach Twojej subskrypcji. 

> [!IMPORTANT]
> * Jeśli musisz usunąć to wystąpienie Cosmos DB, musisz usunąć obszar roboczy Azure Machine Learning, który go używa. 
> * Domyślne [__jednostki żądań__](../cosmos-db/request-units.md) dla tego konta Cosmos DB są ustawione na __8000__. Zmiana tej wartości nie jest obsługiwana. 

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta Cosmos DB, zobacz [Konfigurowanie kluczy zarządzanych przez klienta dla konta usługi Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Wszystkie obrazy kontenerów w rejestrze (Azure Container Registry) są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go, gdy Azure Machine Learning pobiera obraz.

Aby użyć własnych kluczy (zarządzanych przez klienta) do zaszyfrowania Azure Container Registry, należy utworzyć własne ACR i dołączyć je podczas aprowizacji obszaru roboczego lub zaszyfrować domyślne wystąpienie, które jest tworzone w momencie aprowizacji obszaru roboczego.

Przykład tworzenia obszaru roboczego przy użyciu istniejącego Azure Container Registry można znaleźć w następujących artykułach:

* [Utwórz obszar roboczy dla Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md).
* [Użyj szablonu Azure Resource Manager, aby utworzyć obszar roboczy dla Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Wystąpienie kontenera platformy Azure

Wystąpienie kontenera platformy Azure nie obsługuje szyfrowania dysków. Jeśli potrzebujesz szyfrowania dysku, zalecamy [wdrożenie do wystąpienia usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) . W takim przypadku może być również konieczne użycie Azure Machine Learning obsługi kontroli dostępu opartej na rolach, aby zapobiec wdrożeniom w ramach subskrypcji wystąpienia kontenera platformy Azure.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Wdrożony zasób usługi Azure Kubernetes można zaszyfrować w dowolnym momencie przy użyciu kluczy zarządzanych przez klienta. Aby uzyskać więcej informacji, zobacz temat [przenoszenie własnych kluczy za pomocą usługi Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Ten proces umożliwia zaszyfrowanie zarówno danych, jak i dysku systemu operacyjnego wdrożonych maszyn wirtualnych w klastrze Kubernetes.

> [!IMPORTANT]
> Ten proces działa tylko z AKS K8s w wersji 1,17 lub nowszej. Azure Machine Learning dodano obsługę AKS 1,17 w dniu 13 stycznia 2020.

#### <a name="machine-learning-compute"></a>środowisko obliczeniowe usługi Machine Learning

Dysk systemu operacyjnego dla każdego węzła obliczeniowego przechowywanego w usłudze Azure Storage jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft w ramach kont magazynu Azure Machine Learning. Ten obiekt docelowy obliczeń jest nieulotny, a klastry są zwykle skalowane w dół, gdy żadne przebiegi nie są umieszczane w kolejce. Podstawowa maszyna wirtualna jest nieobsługiwana i dysk systemu operacyjnego zostanie usunięty. Azure Disk Encryption nie jest obsługiwana w przypadku dysku systemu operacyjnego.

Każda maszyna wirtualna ma także lokalny dysk tymczasowy dla operacji systemu operacyjnego. Jeśli chcesz, możesz użyć dysku do przygotowania danych szkoleniowych. Dysk jest domyślnie szyfrowany dla obszarów roboczych z parametrem `hbi_workspace` ustawionym na `TRUE`. To środowisko jest krótko w czasie trwania uruchomienia, a obsługa szyfrowania jest ograniczona tylko do kluczy zarządzanych przez system.

Aby uzyskać więcej informacji na temat sposobu, w jaki szyfrowanie w spoczynku działa na platformie Azure, zobacz [szyfrowanie danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Za pomocą protokołu SSL można zabezpieczyć wewnętrzną komunikację między Azure Machine Learning mikrousługami i zabezpieczyć wywołania zewnętrzne do punktu końcowego oceniania. Cały dostęp do usługi Azure Storage odbywa się również za pośrednictwem bezpiecznego kanału.

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSL w celu zabezpieczenia usługi sieci Web za pośrednictwem Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Używanie Azure Key Vault

Azure Machine Learning używa wystąpienia Azure Key Vault skojarzonego z obszarem roboczym do przechowywania poświadczeń różnych rodzajów:

* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Hasła i klucze SSH do obliczeń docelowych, takich jak usługa Azure HDInsight i maszyny wirtualne, są przechowywane w osobnym magazynie kluczy skojarzonym z subskrypcją firmy Microsoft. W Azure Machine Learning nie są przechowywane żadne hasła ani klucze udostępniane przez użytkowników. Zamiast tego generuje, autoryzuje i przechowuje własne klucze SSH do łączenia się z maszynami wirtualnymi i HDInsight w celu uruchamiania eksperymentów.

Każdy obszar roboczy ma skojarzoną w systemie tożsamość zarządzaną, która ma taką samą nazwę jak obszar roboczy. Ta tożsamość zarządzana ma dostęp do wszystkich kluczy, wpisów tajnych i certyfikatów w magazynie kluczy.

## <a name="data-collection-and-handling"></a>Zbieranie i obsługa danych

### <a name="microsoft-collected-data"></a>Zebrane dane firmy Microsoft

Firma Microsoft może zbierać informacje o tożsamościach nienależących do użytkownika, takie jak nazwy zasobów (np. Nazwa zestawu danych lub nazwa eksperymentu usługi Machine Learning) lub zmienne środowiskowe zadań dla celów diagnostycznych. Wszystkie takie dane są przechowywane przy użyciu kluczy zarządzanych przez firmę Microsoft w magazynie hostowanym w subskrypcjach firmy Microsoft i zgodne ze [standardowymi zasadami ochrony prywatności firmy Microsoft i standardami obsługi danych](https://privacy.microsoft.com/privacystatement).

Firma Microsoft zaleca również, aby nie przechowywać poufnych informacji (takich jak wpisy tajne klucza konta) w zmiennych środowiskowych. Zmienne środowiskowe są rejestrowane, szyfrowane i przechowywane przez nas. Podobnie podczas nadawania nazwy [RunId](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)należy unikać uwzględniania poufnych informacji, takich jak nazwy użytkowników lub nazwy projektów tajnych. Te informacje mogą pojawić się w dziennikach telemetrii dostępnych dla pomoc techniczna firmy Microsoft inżynierów.

Możesz zrezygnować z zbierania danych diagnostycznych, ustawiając parametr `hbi_workspace`, aby `TRUE` podczas aprowizacji obszaru roboczego. Ta funkcja jest obsługiwana w przypadku korzystania z zestawu SDK środowiska Azure Python, interfejsu wiersza polecenia, interfejsów API REST lub Azure Resource Manager szablonów.

### <a name="microsoft-generated-data"></a>Dane generowane przez firmę Microsoft

W przypadku korzystania z usług, takich jak automatyczne Machine Learning, firma Microsoft może generować przejściowe, wstępnie przetworzone dane do szkolenia wielu modeli. Te dane są przechowywane w magazynie danych w obszarze roboczym, co umożliwia odpowiednie wymuszanie kontroli dostępu i szyfrowania.

Możesz również zaszyfrować [informacje diagnostyczne zarejestrowane ze wdrożonego punktu końcowego](how-to-enable-app-insights.md) w wystąpieniu usługi Azure Application Insights.

## <a name="monitoring"></a>Monitorowanie

### <a name="metrics"></a>Metryki

Za pomocą metryk Azure Monitor można wyświetlać i monitorować metryki dla Azure Machine Learningego obszaru roboczego. W [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy, a następnie wybierz pozycję **metryki**:

[Zrzut ekranu ![przedstawiający przykładowe metryki dla obszaru roboczego](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metryki obejmują informacje dotyczące uruchamiania, wdrożeń i rejestracji.

Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Dziennik aktywności

Możesz wyświetlić dziennik aktywności obszaru roboczego, aby zobaczyć różne operacje wykonywane w obszarze roboczym. Dziennik zawiera podstawowe informacje, takie jak nazwa operacji, inicjator zdarzenia i sygnatura czasowa.

Ten zrzut ekranu przedstawia dziennik aktywności obszaru roboczego:

[Zrzut ekranu ![przedstawiający dziennik aktywności obszaru roboczego](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Szczegóły żądania oceniania są przechowywane w Application Insights. Application Insights jest tworzony w ramach subskrypcji podczas tworzenia obszaru roboczego. Zarejestrowane informacje zawierają pola, takie jak:

* HTTPMethod
* UserAgent
* Computetype
* RequestUrl
* StatusCode
* IdentyfikatorŻądania
* Czas trwania

> [!IMPORTANT]
> Niektóre akcje w obszarze roboczym Azure Machine Learning nie rejestrują informacji w dzienniku aktywności. Na przykład rozpoczęcie przebiegu szkoleniowego i rejestracja modelu nie są rejestrowane.
>
> Niektóre z tych akcji są wyświetlane w obszarze **działania** obszaru roboczego, ale te powiadomienia nie wskazują, kto zainicjował działanie.

## <a name="data-flow-diagrams"></a>Diagramy przepływu danych

### <a name="create-workspace"></a>Tworzenie obszaru roboczego

Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.

* Zaloguj się do usługi Azure AD z jednego z obsługiwanych klientów Azure Machine Learning (interfejs wiersza polecenia platformy Azure, zestaw SDK języka Python, Azure Portal) i zażądaj odpowiedniego tokenu Azure Resource Manager.
* Wywołaj Azure Resource Manager, aby utworzyć obszar roboczy. 
* Azure Resource Manager skontaktować się z dostawcą zasobów Azure Machine Learning w celu udostępnienia obszaru roboczego.

Dodatkowe zasoby są tworzone w ramach subskrypcji użytkownika podczas tworzenia obszaru roboczego:

* Key Vault (do przechowywania wpisów tajnych)
* Konto usługi Azure Storage (w tym obiekty blob i udziały plików)
* Azure Container Registry (do przechowywania obrazów platformy Docker na potrzeby wnioskowania/oceny i eksperymentowania)
* Application Insights (do przechowywania danych telemetrycznych)

Użytkownik może również udostępnić innym obiektom docelowym obliczeń, które są dołączone do obszaru roboczego (np. usługi Azure Kubernetes lub maszyn wirtualnych).

[przepływ pracy ![tworzenia obszaru roboczego](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Zapisz kod źródłowy (skrypty szkoleniowe)

Na poniższym diagramie przedstawiono przepływ pracy migawek kodu.

Skojarzona z obszarem roboczym Azure Machine Learning to katalogi (eksperymenty), które zawierają kod źródłowy (skrypty szkoleniowe). Te skrypty są przechowywane na komputerze lokalnym i w chmurze (w usłudze Azure Blob Storage w ramach subskrypcji). Migawki kodu są używane do wykonywania lub inspekcji inspekcji historycznej.

[przepływ pracy migawek kodu ![](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Szkolenia

Na poniższym diagramie przedstawiono przepływ pracy szkoleniowej.

* Azure Machine Learning jest wywoływana z IDENTYFIKATORem migawki dla migawki kodu zapisanej w poprzedniej sekcji.
* Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) i token usługi Machine Learning, który jest później używany przez cele obliczeniowe, takie jak środowisko obliczeniowe usługi Machine Learning/VM, do komunikowania się z usługą Machine Learning.
* Aby uruchamiać zadania szkoleniowe, można wybrać zarządzany obiekt docelowy obliczeń (taki jak środowisko obliczeniowe usługi Machine Learning) lub niezarządzany obiekt docelowy obliczeń (na przykład maszyny wirtualne). Poniżej przedstawiono przepływy danych dla obu scenariuszy:
   * Maszyny wirtualne/HDInsight, do których dostęp odbywa się przy użyciu poświadczeń SSH w magazynie kluczy w ramach subskrypcji firmy Microsoft. Azure Machine Learning uruchamia kod zarządzania w obiekcie docelowym obliczeń, który:

   1. Przygotowuje środowisko. (Docker to opcja dla maszyn wirtualnych i komputerów lokalnych. Aby dowiedzieć się, jak działają eksperymenty w kontenerach platformy Docker, zobacz następujące środowisko obliczeniowe usługi Machine Learning kroki.
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypty użytkownika (migawka kodu wymieniona w poprzedniej sekcji).

   * Środowisko obliczeniowe usługi Machine Learning dostęp do programu za pomocą tożsamości zarządzanej przez obszar roboczy.
Ponieważ środowisko obliczeniowe usługi Machine Learning jest zarządzanym elementem docelowym obliczeń (czyli jest zarządzany przez firmę Microsoft), jest uruchamiany w ramach Twojej subskrypcji firmy Microsoft.

   1. Zdalna konstrukcja platformy Docker jest wyłączona, w razie konieczności.
   1. Kod zarządzania jest zapisywana w udziale Azure Files użytkownika.
   1. Kontener jest uruchamiany przy użyciu polecenia początkowego. Oznacza to, że kod zarządzania zgodnie z opisem w poprzednim kroku.

#### <a name="querying-runs-and-metrics"></a>Wykonywanie zapytań dotyczących przebiegów i metryk

Na poniższym diagramie przepływu ten krok występuje, gdy obiekt docelowy obliczeń szkolenia zapisuje metryki uruchamiania z powrotem do Azure Machine Learning z magazynu w bazie danych Cosmos DB. Klienci mogą wywoływać Azure Machine Learning. Machine Learning spowoduje włączenie metryk ściągania z bazy danych Cosmos DB i zwrócenie ich z powrotem do klienta.

[przepływ pracy szkolenia ![](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Tworzenie usług sieci Web

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej na danych produkcyjnych.

Oto szczegółowe informacje:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak zestaw Azure Machine Learning SDK.
* Użytkownik tworzy obraz przy użyciu modelu, pliku wynikowego i innych zależności modelu.
* Obraz platformy Docker jest tworzony i przechowywany w Azure Container Registry.
* Usługa sieci Web jest wdrażana w obiekcie docelowym obliczeń (Container Instances/AKS) przy użyciu obrazu utworzonego w poprzednim kroku.
* Szczegóły żądania oceniania są przechowywane w Application Insights, które znajdują się w subskrypcji użytkownika.
* Dane telemetryczne są również wypychane do subskrypcji Microsoft/Azure.

[przepływ pracy wnioskowania ![](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie Azure Machine Learning usług sieci Web przy użyciu protokołu SSL](how-to-secure-web-service.md)
* [Korzystanie z modelu Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Jak uruchamiać przewidywania wsadowe](how-to-use-parallel-run-step.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Korzystanie z Azure Machine Learning z platformą Azure Virtual Network](how-to-enable-virtual-network.md)
* [Najlepsze rozwiązania dotyczące kompilowania systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Kompilowanie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
