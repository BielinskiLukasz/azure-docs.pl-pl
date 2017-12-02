---
title: "Zarządzane tożsamości usługi (MSI) dla usługi Azure Active Directory"
description: "Przegląd zarządzane tożsamości usługi dla zasobów platformy Azure."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 5444e9d54bd9a2f7250ce590c3b6ced6b8b7bc51
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Zarządzane tożsamości usługi (MSI) dla zasobów platformy Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Typowe wyzwanie podczas tworzenia aplikacji w chmurze jest sposób zarządzania poświadczeniami, które powinny znajdować się w kodzie do uwierzytelniania usługi w chmurze. Dzięki te poświadczenia bezpieczna jest ważnym zadaniem. W idealnym przypadku one nigdy nie są wyświetlane na stacjach roboczych deweloperów lub pobrać wyewidencjonowany do kontroli źródła. Usługa Azure Key Vault zapewnia bezpieczne przechowywanie poświadczeń i innych kluczy i kluczy tajnych, ale kodu musi zostać uwierzytelniona Key Vault w celu ich pobrania. Tożsamość usługi zarządzanej (MSI) powoduje, że rozwiązania tego problemu prostszy, zapewniając usług Azure automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, w tym usługi Key Vault bez żadnych poświadczeń w kodzie.

## <a name="how-does-it-work"></a>Jak to działa?

Po włączeniu zarządzane tożsamości usługi w usłudze Azure Azure automatycznie tworzy tożsamość wystąpienia usługi w dzierżawie usługi Azure AD używane przez subskrypcję platformy Azure.  W obszarze obejmuje Azure udostępnia poświadczenia tożsamości na wystąpienie usługi.  Kod można następnie wprowadzić żądanie lokalne uzyskanie tokenów dostępu do usług, które obsługują usługi Azure AD authentication.  Platforma Azure jest odpowiedzialna wycofania poświadczenia używane przez wystąpienie usługi.  Jeśli wystąpienie usługi zostanie usunięty, Azure automatycznie oczyszcza poświadczenia i tożsamość w usłudze Azure AD.

Oto przykład działania zarządzane tożsamość usługi z maszyn wirtualnych platformy Azure.

![Przykład MSI maszyny wirtualnej](./media/msi-vm-example.png)

1. Usługa Azure Resource Manager odbiera wiadomości, aby włączyć MSI w maszynie Wirtualnej.
2. Usługa Azure Resource Manager tworzy nazwy głównej usługi w usłudze Azure AD do reprezentowania tożsamości maszyny wirtualnej. Nazwy głównej usługi jest tworzony w dzierżawie usługi Azure AD, który jest zaufany dla tej subskrypcji.
3. Usługa Azure Resource Manager konfiguruje szczegóły nazwy głównej usługi w pliku MSI rozszerzenia maszyny Wirtualnej maszyny wirtualnej.  Ten krok obejmuje Konfigurowanie identyfikator klienta i certyfikat używany przez rozszerzenie do uzyskania dostępu do tokenów z usługi Azure AD.
4. Teraz, nosi nazwę główną usługi tożsamość maszyny Wirtualnej, może zostać przydzielony dostęp do zasobów platformy Azure.  Na przykład jeśli kod wymaga wywołań usługi Azure Resource Manager, następnie należy przypisywanej nazwy głównej usługi maszyny Wirtualnej odpowiednią rolę przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure AD.  Kod musi wywołać Key Vault, czy udzielić kodu dostępu do określonego hasła lub klucza w magazynie kluczy.
5. Kod uruchomiony na maszynie Wirtualnej żądania tokenu z lokalnego punktu końcowego, który jest obsługiwany przez rozszerzenie maszyny Wirtualnej MSI: http://localhost:50342/oauth2/token.  Parametr zasobu określa usługi, do którego wysyłane jest token. Na przykład, jeśli chcesz, aby kod do uwierzytelniania w usłudze Azure Resource Manager, można skorzystać zasobu = https://management.azure.com/.
6. MSI rozszerzenia maszyny Wirtualnej używa Identyfikatora klienta skonfigurowanego i certyfikatu poproś token dostępu z usługi Azure AD.  Usługi Azure AD zwraca token dostępu tokenu Web JSON (JWT).
7. Kod wysyła ten token dostępu na wywołanie do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.

Każdy usługa Azure, która obsługuje zarządzane tożsamości usługi ma własną metodę dla kodu można uzyskać tokenu dostępu. Zapoznaj się z samouczkami dla każdej usługi dowiedzieć się, określonej metody w celu pobrania tokenu.

## <a name="try-managed-service-identity"></a>Spróbuj tożsamość usługi zarządzanej

Spróbuj samouczek zarządzane tożsamość usługi, aby dowiedzieć się więcej na trasie scenariusze dostępu do różnych zasobów platformy Azure:
<br><br>
| Z zasobów włączone MSI | Dowiedz się, jak |
| ------- | -------- |
| Maszyna wirtualna platformy Azure (system Windows) | [Tożsamość usługi zarządzanej dostępu do usługi Azure Data Lake Store z maszyny Wirtualnej systemu Windows](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Tożsamość usługi zarządzanej dostępu usługi Azure Resource Manager z maszyny Wirtualnej systemu Windows](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Dostęp do usługi Azure SQL z maszyny Wirtualnej systemu Windows tożsamość usługi zarządzanej](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Dostęp do magazynu Azure za pomocą klucza dostępu z maszyny Wirtualnej z systemem Windows zarządzanych tożsamości usługi](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Dostęp do usługi Azure tożsamość usługi zarządzanej magazynu za pomocą sygnatury dostępu Współdzielonego z maszyny Wirtualnej systemu Windows](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Dostęp do zasobu AD innych niż Azure przy użyciu tożsamości usługi zarządzania maszyny Wirtualnej systemu Windows i usługi Azure Key Vault](msi-tutorial-windows-vm-access-nonaad.md) |
| Maszyna wirtualna platformy Azure (Linux)   | [Tożsamość usługi zarządzanej dostępu do usługi Azure Data Lake Store z maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Tożsamość usługi zarządzanej dostępu usługi Azure Resource Manager z maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Dostęp do magazynu Azure za pomocą klucza dostępu z tożsamością usługi zarządzania maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Dostęp do usługi Azure tożsamość usługi zarządzanej magazynu za pomocą sygnatury dostępu Współdzielonego z maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Dostęp do zasobu AD innych niż Azure z maszyny Wirtualnej systemu Linux zarządzanych tożsamość usługi i usługi Azure Key Vault](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Użyj tożsamości usług zarządzanych przez z usługi aplikacji Azure lub funkcji platformy Azure](/azure/app-service/app-service-managed-service-identity) |
| Azure — funkcja     | [Użyj tożsamości usług zarządzanych przez z usługi aplikacji Azure lub funkcji platformy Azure](/azure/app-service/app-service-managed-service-identity) |

## <a name="which-azure-services-support-managed-service-identity"></a>Usługi platformy Azure obsługuje zarządzane tożsamość usługi?

Usług Azure, które obsługują zarządzane tożsamość usługi umożliwia uwierzytelniania w usłudze, które obsługują usługi Azure AD authentication MSI.  Trwa integrowanie uwierzytelniania MSI i Azure AD na platformie Azure.  Sprawdź wstecz często dla aktualizacji.

### <a name="azure-services-that-support-managed-service-identity"></a>Usług Azure, które obsługują zarządzane tożsamości usługi

Następujących usług platformy Azure obsługuje zarządzane tożsamości usługi.

| Usługa | Stan | Date | Konfigurowanie | Uzyskaj token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | 2017 września | [Witryna Azure Portal](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Interfejs wiersza polecenia platformy Azure](msi-qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Przejdź](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Wersja zapoznawcza | 2017 września | [Witryna Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Stan usługi Funkcje Azure | Wersja zapoznawcza | 2017 września | [Witryna Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Szablon usługi Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure usługi uwierzytelniania pomocy technicznej usługi Azure AD

Następujące usługi obsługi uwierzytelniania usługi Azure AD i zostały przetestowane z klienta usług, które korzystają zarządzane tożsamości usługi.

| Usługa | Identyfikator zasobu | Stan | Date | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://Management.Azure.com/ | Dostępna | 2017 września | [Witryna Azure Portal](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Interfejs wiersza polecenia platformy Azure](msi-howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://Vault.Azure.NET/ | Dostępna | 2017 września | |
| Azure Data Lake | https://datalake.Azure.NET/ | Dostępna | 2017 września | |
| Azure SQL | https://Database.Windows.NET/ | Dostępna | 2017 października | |

## <a name="how-much-does-managed-service-identity-cost"></a>Ile kosztuje zarządzane tożsamości usługi

Tożsamość usługi zarządzanej pochodzi z usługi Azure Active Directory bez, co jest ustawieniem domyślnym dla subskrypcji platformy Azure.  Nie ma żadnych dodatkowych kosztów dla zarządzanych tożsamości usługi.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Chcielibyśmy usłyszeć Twoja!

* Zadaj pytania instrukcje na stronie Stack Overflow znacznikiem [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Funkcja żądania lub Prześlij opinię na temat [forum opinii usługi Azure AD dla deweloperów](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






