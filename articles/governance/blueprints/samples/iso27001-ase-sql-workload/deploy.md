---
title: Przykład-ISO 27001 ASE/plan obciążeń SQL — wdrażanie kroków
description: Wdróż kroki z przykładowego planu obciążeń ISO 27001 App Service Environment/SQL Database.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: fb68b2f78e48dabf5b1377b7c811840a85604f53
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802433"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Wdróż przykład planu obciążenia ISO 27001 App Service Environment/SQL Database

Aby wdrożyć przykładowy plan obciążeń ISO 27001 App Service Environment/SQL Database, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Wdróż przykład strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md)
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdróż przykład strategii usług udostępnionych ISO 27001

Przed wdrożeniem przykładu strategii należy wdrożyć przykład strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md) w ramach subskrypcji docelowej. Bez pomyślnego wdrożenia przykładu usługi udostępnionej ISO 27001, ten przykład strategii nie będzie miał zależności infrastruktury i zakończy się niepowodzeniem podczas wdrażania.

> [!IMPORTANT]
> Ten przykład strategii musi być przypisany w tej samej subskrypcji co przykład planu [usług udostępnionych ISO 27001](../iso27001-shared/index.md) .

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź plik **ISO 27001: Przykład planu obciążeń** środowiska ASE/SQL w _innych próbkach_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładowego planu obciążeń ISO 27001 ASE/SQL.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może ją przenieść z normy ISO 27001.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana w przykładowej strategii ISO 27001". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisz przykładową kopię

Po pomyślnym **opublikowaniu**kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. Ten krok polega na tym, że parametry są dostarczane, aby każde wdrożenie kopii przykładowej strategii było unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz plan** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Ustawienia podstawowe

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji zostanie utworzone przypisanie dla każdego z nich przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: Nazwa jest wstępnie wypełniana na podstawie nazwy planu.
       Zmień stosownie do potrzeb lub pozostaw jako is.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji**planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Zablokuj przypisanie

     Wybierz ustawienie blokowania planu dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry planu

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.

     - **Nazwa organizacji**: Wprowadź krótką nazwę organizacji. Ta właściwość jest używana głównie do określania nazw zasobów.
     - **Identyfikator subskrypcji usługi udostępnionej**: Identyfikator subskrypcji, w której jest przypisany przykład strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md) .
     - **Domyślny prefiks adresu podsieci**: Notacja CIDR dla domyślnej podsieci sieci wirtualnej.
       Wartość domyślna to _10.1.0.0/16_.
     - **Lokalizacja obciążenia**: Określa lokalizację, w której są wdrażane artefakty. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Artefakty wdrażające takie usługi zapewniają opcję parametru dla lokalizacji, w której ma zostać wdrożony artefakt.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w ramach którego jest zdefiniowany. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Utworzono przypisanie strategii i rozpocznie się wdrażanie artefaktów. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, Otwórz przypisanie planu.

> [!WARNING]
> Usługa plany platformy Azure i wbudowane przykłady planów są **bezpłatne**. Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt uruchamiania zasobów wdrożonych przez ten przykład strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Grupa zasobów Log Analytics|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-workload-log-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Log Analytics|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia warstwę obszaru roboczego Log Analytics. Wartość domyślna to _PerNode_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Log Analytics|Szablon usługi Resource Manager|Location|Region używany do tworzenia obszaru roboczego Log Analytics. Wartość domyślna to _zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-workload-net-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów sieciowych|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Konfiguruje prywatny adres IP [zapory platformy Azure](../../../../firewall/overview.md). Powinna być częścią notacji CIDR zdefiniowanej _w ISO 27001: Parametry artefaktu usług_ udostępnionych **prefiks adresu podsieci zapory platformy Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Identyfikator subskrypcji usług udostępnionych|Wartość używana do włączania komunikacji równorzędnej sieci wirtualnej między obciążeniem i usługami udostępnionymi.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Domyślny prefiks adresu podsieci|Notacja CIDR dla domyślnej podsieci sieci wirtualnej. Wartość domyślna to _10.1.0.0/16_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Dodaje adres IP|Adres IP pierwszej dodania maszyny wirtualnej. Ta wartość jest używana jako niestandardowa usługa DNS sieci wirtualnej.|
|Grupa zasobów Key Vault|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-workload-kv-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Key Vault|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Identyfikator obiektu usługi AAD|Identyfikator obiektu usługi AAD konta, które wymaga dostępu do wystąpienia Key Vault. Brak wartości domyślnej i nie można jej pozostawić pustej. Aby zlokalizować tę wartość z Azure Portal, Wyszukaj i wybierz pozycję "Użytkownicy" w obszarze _usługi_. Użyj pola _Nazwa_ , aby odfiltrować nazwę konta, a następnie wybierz to konto. Na stronie _profil użytkownika_ wybierz ikonę "kliknij, aby skopiować" obok _identyfikatora obiektu_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Jednostka SKU usługi Key Vault|Określa jednostkę SKU utworzonego Key Vault. Wartość domyślna to _Premium_.|
|Szablon usługi Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera Azure SQL|Nazwa użytkownika używana do uzyskiwania dostępu do usługi Azure SQL Server. Musi być zgodna z tą samą wartością właściwości w **szablonie Azure SQL Database**. Wartość domyślna to _SQL-admin-User_.|
|Grupa zasobów Azure SQL Database|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-workload-azsql-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Azure SQL Database|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera Azure SQL|Nazwa użytkownika dla SQL Server platformy Azure. Musi być zgodna z tą samą wartością właściwości w **szablonie Key Vault**. Wartość domyślna to _SQL-admin-User_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Hasło administratora usługi Azure SQL Server (identyfikator zasobu Key Vault)|Identyfikator zasobu Key Vault. Użyj "/Subscription/{subscriptionId}/resourceGroups/{orgName}-Workload-KV/Providers/Microsoft.KeyVault/Vaults/{orgName}-Workload-KV" i Zastąp `{subscriptionId}` ciąg identyfikatorem subskrypcji i `{orgName}` nazwą organizacji parametr strategii.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Hasło administratora usługi Azure SQL Server (nazwa klucza tajnego Key Vault)|Nazwa użytkownika administratora SQL Server. Musi być zgodna wartość właściwości **szablonu Key Vault** **SQL Server Nazwa użytkownika administratora platformy Azure**.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Czas przechowywania dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Identyfikator obiektu administratora usługi AAD|Identyfikator obiektu usługi AAD, który zostanie przypisany jako administrator Active Directory. Brak wartości domyślnej i nie można jej pozostawić pustej. Aby zlokalizować tę wartość z Azure Portal, Wyszukaj i wybierz pozycję "Użytkownicy" w obszarze _usługi_. Użyj pola _Nazwa_ , aby odfiltrować nazwę konta, a następnie wybierz to konto. Na stronie _profil użytkownika_ wybierz ikonę "kliknij, aby skopiować" obok _identyfikatora obiektu_.|
|Szablon usługi Azure SQL Database|Szablon usługi Resource Manager|Logowanie administratora usługi AAD|Obecnie nie można ustawić kont Microsoft (takich jak live.com lub outlook.com) jako administrator. Jako administrator można ustawić tylko użytkowników i grupy zabezpieczeń w organizacji. Brak wartości domyślnej i nie można jej pozostawić pustej. Aby zlokalizować tę wartość z Azure Portal, Wyszukaj i wybierz pozycję "Użytkownicy" w obszarze _usługi_. Użyj pola _Nazwa_ , aby odfiltrować nazwę konta, a następnie wybierz to konto. Na stronie _profil użytkownika_ Skopiuj _nazwę użytkownika_.|
|Grupa zasobów App Service Environment|Resource group|Name|**Zablokowany** — łączy **nazwę organizacji** z `-workload-ase-rg` , aby uczynić grupę zasobów unikatową.|
|Grupa zasobów App Service Environment|Resource group|Location|**Zablokowany** — używa parametru strategii.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Nazwa domeny|Nazwa Active Directory utworzonego przez przykład. Wartość domyślna to _contoso.com_.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Lokalizacja środowiska ASE|App Service Environment lokalizacji. Wartość domyślna to _zachodnie stany USA 2_.|
|Szablon środowiska App Service Environment|Szablon usługi Resource Manager|Application Gateway przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładowego planu obciążeń ISO 27001 App Service Environment/SQL Database zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i mapowaniu formantów:

> [!div class="nextstepaction"]
> [Plan obciążeń ISO 27001 App Service Environment/SQL Database — Omówienie](./index.md)
> planu[obciążeń ISO 27001 App Service Environment/SQL Database](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).