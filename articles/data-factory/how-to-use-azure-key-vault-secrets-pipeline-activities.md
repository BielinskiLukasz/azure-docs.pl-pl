---
title: Używanie wpisów tajnych usługi Azure Key Vault w działaniach potoku
description: Dowiedz się, jak pobrać przechowywane poświadczenia z magazynu kluczy Azure i korzystać z nich podczas uruchamiania potoków usługi Data Factory.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 9ca2ea6a45bdf37f15f2ab4fd9c685f11f6d7f64
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031496"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Używanie wpisów tajnych usługi Azure Key Vault w działaniach potoku

Poświadczenia lub wartości tajne można przechowywać w Azure Key Vault i używać ich podczas wykonywania potoku w celu przekazania do działań.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanej przez fabrykę danych.  Dowiedz się, jak działa z [tożsamości zarządzanej dla Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) i upewnij się, że Fabryka danych ma już skojarzoną.

## <a name="steps"></a>Kroki

1. Otwórz właściwości fabryki danych i skopiuj wartość identyfikatora aplikacji zarządzanej tożsamości.

    ![Wartość tożsamości zarządzanej](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Otwórz zasady dostępu do magazynu kluczy i Dodaj uprawnienia zarządzanej tożsamości, aby uzyskać i wyświetlić wpisy tajne.

    ![Zasady dostępu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Zasady dostępu Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kliknij przycisk **Dodaj**, a następnie kliknij przycisk **Zapisz**.

3. Przejdź do wpisu tajnego Key Vault i skopiuj identyfikator wpisu tajnego.

    ![Identyfikator wpisu tajnego](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Zanotuj swój tajny identyfikator URI, który chcesz uzyskać podczas przebiegu potoku fabryki danych.

4. W potoku Data Factory Dodaj nowe działanie sieci Web i skonfiguruj je w następujący sposób.  

    |Właściwość  |Wartość  |
    |---------|---------|
    |Bezpieczne wyjście     |True         |
    |Adres URL     |[Tajna wartość identyfikatora URI]? API-version = 7.0         |
    |Metoda     |GET         |
    |Uwierzytelnianie     |Tożsamość usługi zarządzanej         |
    |Zasób        |https://vault.azure.net       |

    ![Aktywność sieci Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Musisz dodać **? API-version = 7.0** na końcu TAJNEGO identyfikatora URI.  

    > [!CAUTION]
    > Ustaw opcję bezpieczne wyjście na wartość true, aby uniemożliwić Logowanie wartości klucza tajnego w postaci zwykłego tekstu.  Wszelkie dalsze działania, które zużywają tę wartość, powinny mieć ustawioną opcję bezpiecznego wprowadzania wartość true.

5. Aby użyć wartości w innym działaniu, użyj następującego wyrażenia kodu **@activity("Web"). Output. Value**.

    ![Wyrażenie kodu](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać Azure Key Vault do przechowywania poświadczeń dla magazynów danych i obliczeń, zobacz temat [przechowywanie poświadczeń w programie Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
