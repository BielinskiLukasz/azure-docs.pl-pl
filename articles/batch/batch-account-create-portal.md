---
title: Tworzenie konta w witrynie Azure Portal — Azure Batch | Microsoft Docs
description: Dowiedz się, jak utworzyć konto usługi Azure Batch w portalu Azure w celu równoległego uruchamiania dużych obciążeń w chmurze
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 04631431c03f6fdd378bfa99edb9b67f8d6a0cad
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193921"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Tworzenie konta usługi Batch w witrynie Azure Portal

Dowiedz się, jak utworzyć konto usługi Azure Batch w witrynie [Azure Portal][azure_portal] i wybrać właściwości konta pasujące do scenariusza obliczeniowego. Dowiedz się, gdzie znaleźć ważne właściwości konta, takie jak klucze dostępu i adresy URL konta.

Ogólne informacje o kontach usługi Batch i scenariuszach można znaleźć w [omówieniu funkcji](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Zaloguj się w witrynie [Azure Portal][azure_portal].

1. Wybierz pozycję **Utwórz zasób** > **Compute** > **Usługa Batch**.

    ![Usługa Batch w witrynie Marketplace][marketplace_portal]

1. Wprowadź ustawienia **Nowe konto usługi Batch**. Zobacz następujące szczegóły.

    ![Tworzenie konta usługi Batch][account_portal]

    a. **Nazwa konta**: wybrana nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym konto zostanie utworzone (zobacz **Lokalizacja** poniżej). Nazwa konta może zawierać tylko małe litery lub cyfry i musi mieć od 3 do 24 znaków.

    b. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.

    d. **Grupa zasobów**: istniejąca grupa zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.

    d. **Lokalizacja**: region świadczenia usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.

    e. **Konto magazynu** (opcjonalnie): konto usługi Azure Storage skojarzone z kontem usługi Batch. Jest to zalecane w przypadku większości kont usługi Batch. Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md#azure-storage-account). W portalu wybierz istniejące konto magazynu lub opcjonalnie utwórz nowe.

      ![Tworzenie konta magazynu][storage_account]

    f. **Tryb alokacji puli**: w przypadku większości scenariuszy zaakceptuj wartość domyślną **Usługa Batch**.

1. Wybierz przycisk **Utwórz**, aby utworzyć konto.

## <a name="view-batch-account-properties"></a>Wyświetlanie właściwości konta usługi Batch

Po utworzeniu konta wybierz je, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie zapewnia dostęp do wszystkich ustawień i właściwości konta.

![Strona konta usługi Batch w witrynie Azure Portal][account_blade]

* **Nazwa, adres URL i klucze konta usługi Batch**: aby po utworzeniu aplikacji za pomocą [interfejsów API usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development) uzyskać dostęp do zasobów usługi Batch, potrzebujesz klucza i adresu URL konta. (Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory).

    Aby wyświetlić informacje dostępu do konta usługi Batch, wybierz pozycję **Klucze**.

    ![Klucze konta usługi Batch w witrynie Azure Portal][account_keys]

* Aby wyświetlić nazwę i klucze konta magazynu skojarzone z kontem usługi Batch, wybierz pozycję **Konto magazynu**.

* Aby wyświetlić limity przydziału zasobów dotyczące konta usługi Batch, wybierz pozycję **Limity przydziału**. Aby uzyskać szczegółowe informacje, zobacz [Limity przydziału i limity usługi Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Dodatkowa konfiguracja dla trybu subskrypcji użytkownika

Jeśli wybrano tworzenie konta usługi Batch w trybie subskrypcji użytkownika, należy wykonać następujące dodatkowe kroki przed utworzeniem konta.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Umożliwia usłudze Azure Batch dostęp do subskrypcji (jednorazowa operacja)

Podczas tworzenia pierwszego konta usługi Batch w trybie subskrypcji użytkownika należy zarejestrować subskrypcję w usłudze Batch. (Jeśli wcześniej zostało to już zrobione, przejdź do następnej sekcji).

1. Zaloguj się w witrynie [Azure Portal][azure_portal].

1. Wybierz pozycję **Wszystkie usługi** > **Subskrypcje**, a następnie wybierz subskrypcję, której chcesz użyć dla konta usługi Batch.

1. Na stronie **Subskrypcja** wybierz pozycję **Dostawcy zasobów** i wyszukaj dostawcę **Microsoft.Batch**. Sprawdź, czy dostawca zasobów **Microsoft.Batch** został zarejestrowany w subskrypcji. Jeśli nie, wybierz link **Zarejestruj**.

    ![Rejestrowanie dostawcy Microsoft.Batch][register_provider]

1. Na stronie **Subskrypcja** wybierz pozycję **Kontrola dostępu (IAM)** > **Przypisania ról** > **Dodaj przypisanie roli**.

    ![Kontrola dostępu do subskrypcji][subscription_access]

1. Na stronie **Dodawanie przypisania roli** wybierz rolę **Współautor** i wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. W przypadku nowszych dzierżaw usługi Azure AD może być używana ta nazwa.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch.

1. Po odnalezieniu interfejsu API usługi Batch zaznacz jego pozycję i wybierz przycisk **Zapisz**.

    ![Dodawanie uprawnień usługi Batch][add_permission]

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

W trybie subskrypcji użytkownika wymagana jest usługa Azure Key Vault należąca do tej samej grupy zasobów, co konto usługi Batch, które ma zostać utworzone. Upewnij się, że grupa zasobów znajduje się w regionie, gdzie usługa Batch jest [dostępna](https://azure.microsoft.com/regions/services/) i który obsługuje Twoja subskrypcja.

1. W witrynie [Azure Portal][azure_portal] wybierz pozycję **Nowy** > **Bezpieczeństwo** > **Key Vault**.

1. Na stronie **Tworzenie magazynu Key Vault** wprowadź nazwę magazynu Key Vault i utwórz grupę zasobów w wymaganym regionie konta usługi Batch. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **Utwórz**.

Podczas tworzenia konta usługi Batch w trybie subskrypcji użytkownika użyj grupy zasobów dla magazynu kluczy, określ **subskrypcję użytkownika** jako tryb alokacji puli, a następnie wybierz magazyn kluczy.

### <a name="configure-subscription-quotas"></a>Konfigurowanie limitów przydziału subskrypcji

Limity przydziału rdzeni nie są ustawiane domyślnie na kontach usługi Batch subskrypcji użytkownika. Limity przydziału rdzeni trzeba ustawić ręcznie, ponieważ typowe limity przydziału rdzeni usługi Batch nie dotyczą kont w trybie subskrypcji użytkownika.

1. W [witrynie Azure Portal][azure_portal] wybierz konto usługi Batch trybu subskrypcji użytkownika, aby wyświetlić jego ustawienia i właściwości.

1. Z menu po lewej stronie wybierz pozycję **Limity przydziału**, aby wyświetlić i skonfigurować limity przydziału rdzeni skojarzone z kontem usługi Batch.

Zapoznaj się z [limitami przydziału i limitami usługi Batch](batch-quota-limit.md), aby uzyskać więcej informacji na temat limitów przydziałów rdzeni dla trybu subskrypcji użytkownika.

## <a name="other-batch-account-management-options"></a>Inne opcje zarządzania kontem usługi Batch

Poza korzystaniem z witryny Azure Portal można tworzyć konta usługi Batch i zarządzać nimi za pomocą narzędzi, takich jak:

* [Polecenia cmdlet programu PowerShell usługi Batch](batch-powershell-cmdlets-get-started.md)
* [Interfejs wiersza polecenia platformy Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). W artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i podzadania, oraz opisano funkcje dla obciążeń zasobów obliczeniowych na dużą skalę.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](quick-run-dotnet.md) lub języka [Python](quick-run-python.md). Te przewodniki Szybki start zawierają omówienie przykładowej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png