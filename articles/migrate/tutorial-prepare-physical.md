---
title: Przygotowywanie serwerów fizycznych do oceny/migracji za pomocą usługi Azure Migrate
description: Dowiedz się, jak przygotować się do oceny/migracji serwerów fizycznych za pomocą usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5f9048b08b3e77a0c8d5ae9a9d10c614a4e0af61
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336683"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Przygotowanie do oceny i migracji serwerów fizycznych na platformę Azure

W tym artykule opisano sposób przygotowania do oceny lokalnych serwerów fizycznych za pomocą [usługi Azure Migrate](migrate-services-overview.md).

[Usługa Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV). 

Ten samouczek jest pierwszym z serii, który pokazuje, jak ocenić serwery fizyczne za pomocą usługi Azure Migrate. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj platformę Azure. Skonfiguruj uprawnienia do konta platformy Azure i zasoby do pracy z programem Azure Migrate.
> * Przygotuj lokalne serwery fizyczne do oceny serwera.


> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami oceny serwerów fizycznych.


Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

### <a name="azure-permissions"></a>Uprawnienia platformy Azure

Należy skonfigurować uprawnienia do wdrażania migracji platformy Azure.

**Zadanie** | **Szczegóły** 
--- | --- 
**Tworzenie projektu migracji platformy Azure** | Twoje konto platformy Azure wymaga uprawnień programu Contributer lub Owner do utworzenia projektu. | 
**Rejestrowanie dostawców zasobów** | Usługa Azure Migrate używa lekkiego urządzenia migracji platformy Azure do odnajdywania i oceny maszyn wirtualnych funkcji Hyper-V za pomocą oceny serwera migracji platformy Azure.<br/><br/> Podczas rejestracji urządzenia dostawcy zasobów są rejestrowani w subskrypcji wybranej w urządzeniu. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Aby zarejestrować dostawców zasobów, potrzebujesz roli współautora lub właściciela w ramach subskrypcji.
**Tworzenie aplikacji usługi Azure AD** | Podczas rejestrowania urządzenia usługa Azure Migrate tworzy aplikację usługi Azure Active Directory (Azure AD), która jest używana do komunikacji między agentami działającymi na urządzeniu z odpowiednimi usługami uruchomionymi na platformie Azure. [Dowiedz się więcej](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Potrzebne są uprawnienia do tworzenia aplikacji usługi Azure AD (dostępnych w roli dewelopera aplikacji).



### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

Sprawdź, czy masz uprawnienia do tworzenia projektu migracji platformy Azure.

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Powinieneś mieć uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.


### <a name="assign-permissions-to-register-the-appliance"></a>Przypisywanie uprawnień do rejestrowania urządzenia

Można przypisać uprawnienia do migracji platformy Azure, aby utworzyć aplikację usługi Azure AD podczas rejestracji urządzenia, przy użyciu jednej z następujących metod:

- Dzierżawca/administrator globalny może udzielić uprawnień użytkownikom w dzierżawie, aby utworzyć i zarejestrować aplikacje usługi Azure AD.
- Dzierżawca/administrator globalny może przypisać rolę dewelopera aplikacji (która ma uprawnienia) do konta.

> [!NOTE]
> - Aplikacja nie ma żadnych innych uprawnień dostępu do subskrypcji innych niż te opisane powyżej.
> - Te uprawnienia są potrzebne tylko podczas rejestracji nowego urządzenia. Uprawnienia można usunąć po skonfigurowaniu urządzenia.


#### <a name="grant-account-permissions"></a>Udziel uprawnień konta

Dzierżawca/administrator globalny może udzielić uprawnień w następujący sposób:

1. W usłudze Azure AD administrator dzierżawy/globalnej powinien przejść do**ustawień użytkownika****użytkowników** > usługi Azure **Active Directory** > .
2. Administrator powinien ustawić **rejestracje aplikacji** na **Tak**.

    ![Uprawnienia usługi Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Jest to ustawienie domyślne, które nie jest poufne. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Przypisywanie roli dewelopera aplikacji

Dzierżawca/administrator globalny można przypisać rolę dewelopera aplikacji do konta. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Przygotowanie do fizycznej oceny serwera

Aby przygotować się do oceny serwera fizycznego, należy zweryfikować ustawienia serwera fizycznego i zweryfikować ustawienia wdrożenia urządzenia:

### <a name="verify-physical-server-settings"></a>Weryfikowanie ustawień serwera fizycznego

1. Sprawdź [wymagania serwera fizycznego](migrate-support-matrix-physical.md#physical-server-requirements) do oceny serwera.
2. Upewnij się, że [wymagane porty](migrate-support-matrix-physical.md#port-access) są otwarte na serwerach fizycznych.


### <a name="verify-appliance-settings"></a>Weryfikowanie ustawień urządzenia

Przed skonfigurowaniem urządzenia migracji platformy Azure i rozpoczęciem oceny w następnym samouczku przygotuj się do wdrożenia urządzenia.

1. [Sprawdź](migrate-appliance.md#appliance---physical) wymagania dotyczące urządzeń dla serwerów fizycznych.
2. [Przejrzyj](migrate-appliance.md#url-access) adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać dostęp.
3. [Sprawdź,](migrate-appliance.md#collected-data---vmware) czy urządzenie będzie zbierać podczas wykrywania i oceny.
4. [Uwaga](migrate-support-matrix-physical.md#port-access) wymagania dotyczące dostępu do portu fizycznej oceny serwera.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Konfigurowanie konta do odnajdowania serwera fizycznego

Usługa Azure Migrate potrzebuje uprawnień do odnajdywanie serwerów lokalnych.

- **Windows:** Skonfiguruj konto użytkownika lokalnego na wszystkich serwerach systemu Windows, które chcesz uwzględnić w odnajdowaniu. Konto użytkownika musi zostać dodane do następujących grup: - Użytkownicy zdalnego zarządzania - Użytkownicy monitora wydajności - Użytkownicy dziennika wydajności
- **Linux:** Potrzebujesz konta głównego na serwerach Linuksa, które chcesz odkryć.

## <a name="prepare-for-physical-server-migration"></a>Przygotowanie do migracji serwera fizycznego

Przejrzyj wymagania dotyczące migracji serwerów fizycznych.

- [Przejrzyj](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania serwera fizycznego dotyczące migracji.
- Migracja platformy Azure: Migracja serwera używa serwera replikacji do migracji serwera fizycznego:
    - [Przejrzyj](migrate-replication-appliance.md#appliance-requirements) wymagania dotyczące wdrażania urządzenia replikacji i [opcje](migrate-replication-appliance.md#mysql-installation) instalacji MySQL na urządzeniu.
    - Przejrzyj wymagania dotyczące dostępu [do adresu URL](migrate-replication-appliance.md#url-access) i [port] (migrate-replication-appliance.md#port-access) dla urządzenia replikacji.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie uprawnień konta platformy Azure.
> * Przygotowane serwery fizyczne do oceny.

Przejdź do następnego samouczka, aby utworzyć projekt migracji platformy Azure i ocenić serwery fizyczne pod kątem migracji na platformę Azure

> [!div class="nextstepaction"]
> [Ocena serwerów fizycznych](./tutorial-assess-physical.md)
