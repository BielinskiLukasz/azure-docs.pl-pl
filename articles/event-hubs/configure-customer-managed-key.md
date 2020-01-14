---
title: Skonfiguruj własny klucz do szyfrowania danych usługi Azure Event Hubs magazynowanych
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania własnego klucza do szyfrowania danych Event Hubs platformy Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 50d12a0aba9018b1ecb30c018249e8f94ebe6d95
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903286"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta do szyfrowania danych Event Hubs platformy Azure przechowywanych przy użyciu Azure Portal
Usługa Azure Event Hubs zapewnia szyfrowanie danych przechowywanych przy użyciu usługi Azure szyfrowanie usługi Storage (SSE platformy Azure). Event Hubs opiera się na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="overview"></a>Przegląd
Usługa Azure Event Hubs obsługuje teraz opcję szyfrowania danych przechowywanych przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta (Bring Your Own Key – BYOK). Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania danych Event Hubs platformy Azure.

Włączenie funkcji BYOK to jednorazowy proces konfiguracji w przestrzeni nazw.

> [!NOTE]
> Funkcja BYOK jest obsługiwana przez [Event Hubs dedykowanych klastrów z jedną dzierżawą](event-hubs-dedicated-overview.md) . Nie można jej włączyć dla przestrzeni nazw w warstwie Standardowa Event Hubs.

Za pomocą Azure Key Vault można zarządzać kluczami i przeprowadzać inspekcję użycia klucza. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/key-vault-overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Użycie kluczy zarządzanych przez klienta w usłudze Azure Event Hubs wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to: **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do klastra Event Hubs — warstwa Dedykowana.
1. Wybierz przestrzeń nazw, w której chcesz włączyć BYOK.
1. Na stronie **Ustawienia** obszaru nazw Event Hubs wybierz pozycję **szyfrowanie**. 
1. Wybierz **szyfrowanie klucza zarządzanego przez klienta** , jak pokazano na poniższej ilustracji. 

    ![Włącz klucz zarządzany przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami
Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany przez klienta z przestrzenią nazw platformy Azure Event Hubs. Event Hubs obsługuje tylko Azure Key Vault. Po włączeniu opcji **szyfrowania z kluczem zarządzanym przez klienta** w poprzedniej sekcji należy zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć **nietrwałe usuwanie** i nie można ich **czyścić** skonfigurować dla klucza. Te ustawienia można skonfigurować przy użyciu [programu PowerShell](../key-vault/key-vault-soft-delete-powershell.md) lub [interfejsu wiersza polecenia](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [przewodnika Szybki Start](../key-vault/key-vault-overview.md). Aby uzyskać więcej informacji o importowaniu istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/about-keys-secrets-and-certificates.md).
1. Aby włączyć zarówno nietrwałe usuwanie, jak i przeczyszczanie, podczas tworzenia magazynu należy użyć polecenia [AZ Create webmagazynion](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczeniem do istniejącego magazynu (ma już włączony nietrwałe usuwanie), użyj polecenia [AZ webmagazyn Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, wykonując następujące czynności:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.
        
        ![Przycisk Wybierz opcję Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 
    1. Teraz możesz wybrać ten klucz do skojarzenia z przestrzenią nazw Event Hubs do szyfrowania z listy rozwijanej. 

        ![Wybierz klucz z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Wprowadź szczegóły klucza i kliknij przycisk **Wybierz**. Umożliwi to szyfrowanie danych przechowywanych w przestrzeni nazw za pomocą klucza zarządzanego przez klienta. 


## <a name="rotate-your-encryption-keys"></a>Obróć klucze szyfrowania
Możesz obrócić klucz w magazynie kluczy przy użyciu mechanizmu rotacji magazynów kluczy platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rotacji kluczy i inspekcji](../key-vault/key-vault-key-rotation-log-monitoring.md). Daty aktywacji i wygaśnięcia można także skonfigurować w celu zautomatyzowania rotacji kluczy. Usługa Event Hubs wykryje nowe wersje klucza i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Odwołaj dostęp do kluczy
Cofnięcie dostępu do kluczy szyfrowania nie spowoduje przeczyszczenia danych z Event Hubs. Nie można jednak uzyskać dostępu do danych z przestrzeni nazw Event Hubs. Możesz odwołać klucz szyfrowania za pomocą zasad dostępu lub usunąć klucz. Dowiedz się więcej na temat zasad dostępu i zabezpieczania magazynu kluczy, [Aby uzyskać bezpieczny dostęp do magazynu kluczy](../key-vault/key-vault-secure-your-key-vault.md).

Po odwołaniu klucza szyfrowania usługa Event Hubs w zaszyfrowanej przestrzeni nazw stanie się niezależna. Jeśli dostęp do klucza jest włączony lub przywrócono klawisz Delete, usługa Event Hubs wybierze klucz, aby można było uzyskać dostęp do danych z przestrzeni nazw zaszyfrowanej Event Hubs.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych 
Ustawienie dzienników diagnostycznych dla przestrzeni nazw z włączoną obsługą BYOK umożliwia uzyskanie wymaganych informacji o operacjach, gdy przestrzeń nazw jest zaszyfrowana przy użyciu kluczy zarządzanych przez klienta. Te dzienniki mogą być włączone i później przesyłane strumieniowo do centrum zdarzeń lub analizowane przy użyciu usługi log Analytics lub przesyłane strumieniowo do magazynu w celu przeprowadzenia dostosowanej analizy. Aby dowiedzieć się więcej na temat dzienników diagnostycznych, zobacz [Omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Włącz dzienniki użytkowników
Wykonaj następujące kroki, aby włączyć dzienniki dla kluczy zarządzanych przez klienta.

1. W Azure Portal przejdź do przestrzeni nazw, dla której włączono BYOK.
1. Wybierz pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie**.

    ![Wybieranie ustawień diagnostycznych](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**. 

    ![Wybierz pozycję Dodaj ustawienie diagnostyczne](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Podaj **nazwę** i wybierz miejsce, do którego chcesz przesyłać strumieniowo dzienniki.
1. Wybierz pozycję **CustomerManagedKeyUserLogs** i **Zapisz**. Ta akcja włącza dzienniki dla BYOK w przestrzeni nazw.

    ![Wybierz opcję dzienniki użytkownika klucza zarządzanego przez klienta](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schemat dziennika 
Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższej tabeli. 

| Nazwa | Opis |
| ---- | ----------- | 
| TaskName | Opis zadania, które nie powiodło się. |
| Identyfikator działania | Wewnętrzny identyfikator używany do śledzenia. |
| category | Definiuje klasyfikację zadania. Na przykład jeśli klucz z magazynu kluczy jest wyłączany, będzie to kategoria informacji lub jeśli klucz nie może zostać rozpakowany, może to oznaczać, że wystąpił błąd. |
| resourceId | Identyfikator zasobu Azure Resource Manager |
| keyVault | Pełna nazwa magazynu kluczy. |
| key | Nazwa klucza służąca do szyfrowania przestrzeni nazw Event Hubs. |
| version | Używana wersja klucza. |
| operation | Operacja wykonywana na kluczu w magazynie kluczy. Na przykład Wyłącz/Włącz klucz, Zawijaj lub Odpakuj |
| kod | Kod, który jest skojarzony z operacją. Przykład: kod błędu, 404 oznacza, że nie znaleziono klucza. |
| message | Dowolny komunikat o błędzie skojarzony z operacją |

Oto przykład dziennika dla klucza zarządzanego przez klienta:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Najlepszym rozwiązaniem jest zawsze włączenie dzienników, jak pokazano w poprzedniej sekcji. Ułatwia śledzenie działań po włączeniu szyfrowania BYOK. Ułatwia ona również określanie zakresu problemów.

Poniżej przedstawiono kody typowych błędów, które należy wyszukać, gdy włączone jest szyfrowanie BYOK.

| Działanie | Kod błędu | Wynikający ze stanu danych |
| ------ | ---------- | ----------------------- | 
| Usuń uprawnienie zawijania/odwinięcia z magazynu kluczy | 403 |    Niedostępny |
| Usuń członkostwo roli usługi AAD z podmiotu usługi AAD, który udzielił uprawnienia do zawijania/depakowania | 403 |  Niedostępny |
| Usuwanie klucza szyfrowania z magazynu kluczy | 404 | Niedostępny |
| Usuń Magazyn kluczy | 404 | Niedostępne (przyjęto, że jest włączone polecenie Soft-Delete, które jest wymagane.) |
| Zmiana okresu ważności klucza szyfrowania w taki sposób, że już wygasł | 403 |   Niedostępny  |
| Zmiana protokołu NBF (nie wcześniej) w taki sposób, że klucz szyfrowania klucza nie jest aktywny | 403 | Niedostępny  |
| Wybieranie opcji **Zezwalaj na usługi MSFT** dla zapory magazynu kluczy lub w inny sposób blokowanie dostępu do sieci do magazynu kluczy, który ma klucz szyfrowania | 403 | Niedostępny |
| Przeniesienie magazynu kluczy do innej dzierżawy | 404 | Niedostępny |  
| Sporadyczny problem z siecią lub awaria usługi DNS/AAD/MSI |  | Dostępne przy użyciu klucza szyfrowania danych w pamięci podręcznej |

> [!IMPORTANT]
> Aby włączyć funkcję Geo-DR w przestrzeni nazw używającej szyfrowania BYOK, pomocnicza przestrzeń nazw dla parowania musi znajdować się w dedykowanym klastrze i musi mieć włączoną dla niej tożsamość zarządzaną przez system. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd usługi Event Hubs](event-hubs-about.md)
- [Przegląd Key Vault](../key-vault/key-vault-overview.md)




