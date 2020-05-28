---
title: Szyfrowanie danych w usłudze translator
titleSuffix: Azure Cognitive Services
description: Szyfrowanie w usłudze translator danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: egeaney
ms.openlocfilehash: 0a4a1f49735a30d7ad764e7f031610eabd09763e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995772"
---
# <a name="translator-encryption-of-data-at-rest"></a>Szyfrowanie danych w usłudze translator

Usługa translator automatycznie szyfruje dane, które są przekazywane do tworzenia niestandardowych modeli tłumaczenia, gdy są utrwalane w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

## <a name="about-cognitive-services-encryption"></a>Informacje o szyfrowaniu Cognitive Services

Dane są szyfrowane i odszyfrowywane przy użyciu zgodności ze standardem [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitowego szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) . Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane przez Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Domyślnie Twoja subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. W przypadku korzystania z warstwy cenowej obsługującej klucze zarządzane przez klienta można wyświetlić ustawienia szyfrowania dla zasobu w sekcji **szyfrowanie** [Azure Portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetl ustawienia szyfrowania](../media/cognitive-services-encryption/encryptionblade.png)

W przypadku subskrypcji, które obsługują tylko zarządzane przez firmę Microsoft klucze szyfrowania, nie będziesz mieć sekcji **szyfrowanie** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Istnieje również możliwość zarządzania subskrypcją przy użyciu własnych kluczy. Klucze zarządzane przez klienta (CMK), znane także jako dające własny klucz (BYOK), zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.

> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne dla wszystkich warstw cenowych usługi Translator. Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza zarządzanego przez klienta](https://aka.ms/cogsvc-cmk) w ciągu około 3-5 dni roboczych, aby wypróbować stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK z usługą translator należy utworzyć nowy zasób usługi Translator. Po utworzeniu zasobu usługi Translator możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

Wykonaj następujące kroki, aby włączyć obsługę kluczy zarządzanych przez klienta w usłudze Translator:

1. Utwórz nowy, regionalny translator lub Cognitive Services regionalny zasób. Nie będzie on działał z zasobem globalnym.
2. Włączono tożsamość zarządzaną w Azure Portal i Dodaj informacje o kluczu zarządzanym przez klienta.
3. Utwórz nowy obszar roboczy w usłudze tłumaczenia niestandardowego i skojarz te informacje z subskrypcją.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Zasób Cognitive Services i Magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nowy zasób Cognitive Services jest zawsze szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie utworzenia zasobu nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z zasobem Cognitive Services. Tożsamość zarządzana jest dostępna zaraz po utworzeniu zasobu.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta do Azure Key Vault szyfrowania Cognitive Services, zobacz:

- [Skonfiguruj klucze zarządzane przez klienta przy użyciu Key Vault na potrzeby szyfrowania Cognitive Services z Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Włączenie kluczy zarządzanych przez klienta spowoduje również włączenie tożsamości zarządzanej przypisanej do systemu, funkcji usługi Azure AD. Gdy jest włączona tożsamość zarządzana przypisana przez system, ten zasób zostanie zarejestrowany w Azure Active Directory. Po zarejestrowaniu tożsamość zarządzana będzie mieć dostęp do Key Vault wybranej podczas instalacji klucza zarządzanego przez klienta. Możesz dowiedzieć się więcej o [tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do magazynu kluczy zostanie usunięty, a wszystkie dane zaszyfrowane za pomocą kluczy klienta nie będą już dostępne. Wszystkie funkcje zależne od tych danych przestaną działać. Wszystkie wdrożone modele również zostaną cofnięte. Wszystkie przekazane dane zostaną usunięte z translatora niestandardowego. Jeśli zarządzane tożsamości są ponownie włączane, nie będzie automatycznie wdrażać modelu.

> [!IMPORTANT]
> Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. W przypadku konfigurowania kluczy zarządzanych przez klienta w Azure Portal, zarządzana tożsamość zostanie automatycznie przypisana w obszarze okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub zasób z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z zasobem nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **transfer subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowuj klucze zarządzane przez klienta w Azure Key Vault

Aby włączyć klucze zarządzane przez klienta, należy użyć Azure Key Vault do przechowywania kluczy. Należy włączyć zarówno właściwości **nietrwałego usuwania** , jak i **nie przeczyszczania** w magazynie kluczy.

Szyfrowanie Cognitive Services obsługuje tylko klucze RSA o rozmiarze 2048. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Jeśli cały magazyn kluczy zostanie usunięty, dane nie będą już wyświetlane i wszystkie Twoje modele zostaną cofnięte. Wszystkie przekazane dane zostaną usunięte z translatora niestandardowego. 

### <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) lub [interfejs wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie Cognitive Services i Twoje modele zostaną cofnięte, ponieważ klucz szyfrowania jest niedostępny przez Cognitive Services. Wszystkie przekazane dane zostaną również usunięte z translatora niestandardowego.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
