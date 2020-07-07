---
title: Jak zapewnić sprawdzanie poprawności szyfrowania danych Azure Database for MySQL
description: Dowiedz się, jak zweryfikować szyfrowanie szyfrowania danych Azure Database for MySQL przy użyciu klucza zarządzanego przez klientów.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: ee80f50e925bf4545f885d701e70bc21208f1d1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82515406"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Weryfikowanie szyfrowania danych dla Azure Database for MySQL

Ten artykuł pomaga sprawdzić, czy szyfrowanie danych przy użyciu klucza zarządzanego przez klienta Azure Database for MySQL działa zgodnie z oczekiwaniami.

## <a name="check-the-encryption-status"></a>Sprawdź stan szyfrowania

### <a name="from-portal"></a>Z portalu

1. Jeśli chcesz sprawdzić, czy klucz klienta jest używany do szyfrowania, wykonaj następujące czynności:

    * W Azure Portal przejdź do **Azure Key Vault**  ->  **kluczy** Azure Key Vault
    * Wybierz klucz używany do szyfrowania serwera.
    * Ustaw stan klucza z **włączony** na wartość **nie**.
  
       Po pewnym czasie (**~ 15 min**) **stan** serwera Azure Database for MySQL powinien być **niedostępny**. Wszystkie operacje we/wy wykonywane względem serwera zakończą się niepowodzeniem, co oznacza, że serwer jest rzeczywiście szyfrowany za pomocą klucza Customers, a klucz jest obecnie nieprawidłowy.
    
       Aby zapewnić **dostęp** do serwera, możesz ponownie sprawdzić poprawność klucza. 
    
    * Ustaw stan klucza w Key Vault na **tak**.
    * Na stronie **szyfrowanie danych**serwera wybierz pozycję ponownie **Zweryfikuj klucz**.
    * Po pomyślnym zakończeniu ponownej weryfikacji klucza **stan** serwera zmieni się na **dostępne**.

2. Na Azure Portal, jeśli masz pewność, że klucz szyfrowania jest ustawiony, dane są szyfrowane przy użyciu klucza klienci używanego w Azure Portal.

  ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Z interfejsu wiersza polecenia

1. Możemy użyć polecenia *AZ CLI* polecenie, aby sprawdzić poprawność najważniejszych zasobów używanych dla serwera Azure Database for MySQL.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    W przypadku serwera bez zestawu szyfrowania danych to polecenie powoduje wypełnienie pustego zestawu [].

### <a name="azure-audit-reports"></a>Raporty inspekcji platformy Azure

Można również przejrzeć [raporty inspekcji](https://servicetrust.microsoft.com) , które zawierają informacje o zgodności z normami ochrony danych i wymaganiami prawnymi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta](concepts-data-encryption-mysql.md).