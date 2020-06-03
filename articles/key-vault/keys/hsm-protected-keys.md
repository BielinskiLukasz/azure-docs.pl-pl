---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znany również jako BYOK lub własny klucz.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 011ef67e5dbbf5b391e8bdaad20a42688022a0a9
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84296769"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importowanie kluczy chronionych przez moduł HSM do usługi Key Vault

Aby uzyskać gwarancję, w przypadku korzystania z Azure Key Vault można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). W celu ochrony kluczy Azure Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).

Ta funkcja jest niedostępna dla platformy Azure w Chinach.

> [!NOTE]
> Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../general/overview.md)  
> Aby zapoznać się z samouczkiem wprowadzającym, które obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz [co to jest Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

Transferowanie kluczy chronionych przez moduł HSM do Key Vault jest obsługiwane za pośrednictwem dwóch różnych metod, w zależności od używanej sprzętowych modułów zabezpieczeń. Skorzystaj z poniższej tabeli, aby określić, która metoda powinna zostać użyta do generowania sprzętowych modułów zabezpieczeń, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. 

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Obsługiwana metoda modułu HSM — transfer klucza|
|---|---|---|---|
|[Oprogramowanie wspomagające nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Rodzina sprzętowego nshield sprzętowych modułów zabezpieczeń</li><li>Sprzętowego nshield jako usługa</ul>|**Metoda 1:** [oprogramowanie wspomagające nCipher BYOK](hsm-protected-keys-ncipher.md) (z silnym zaświadczeniem dotyczącym importowania kluczy i walidacji modułu HSM)<br/>**Metoda 2.** [Użyj nowej metody BYOK](hsm-protected-keys-byok.md) |
|Firmy Thales|Producent|<ul><li>Rodzina SafeNet Luna modułu HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej</li></ul>| [Użyj nowej metody BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Samoobsługowa usługa zarządzania kluczami (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Użyj nowej metody BYOK](hsm-protected-keys-byok.md)|
|Firmy|Producent|Wszystkie LiquidSecurity sprzętowych modułów zabezpieczeń with<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3,2 lub nowszej</li></ul>|[Użyj nowej metody BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|Niezależnego dostawcy oprogramowania (system zarządzania kluczami przedsiębiorstwa)|Wiele marek i modeli modułu HSM, w tym<ul><li>Oprogramowanie wspomagające nCipher</li><li>Firmy Thales</li><li>Utimaco</li></ul>[Aby uzyskać szczegółowe informacje, zobacz witrynę Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Użyj nowej metody BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [Key Vault najlepszych](../general/best-practices.md) rozwiązań, aby zapewnić bezpieczeństwo, trwałość i monitorowanie kluczy.
* Aby uzyskać pełny opis nowej metody BYOK, zobacz [specyfikację BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)
