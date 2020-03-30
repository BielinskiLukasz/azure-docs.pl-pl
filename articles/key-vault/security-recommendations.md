---
title: Zalecenia dotyczące zabezpieczeń dla usługi Azure Key Vault
description: Zalecenia dotyczące zabezpieczeń usługi Azure Key Vault. Wdrożenie tych wskazówek pomoże Ci wypełnić swoje zobowiązania w zakresie bezpieczeństwa opisane w naszym modelu wspólnej odpowiedzialności
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 9fd41ae9b61a9c74fd5d99dd71199cfde06f5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192374"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Zalecenia dotyczące zabezpieczeń dla usługi Azure Key Vault

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń usługi Azure Key Vault. Wdrożenie tych zaleceń pomoże Ci wypełnić swoje zobowiązania w zakresie bezpieczeństwa opisane w naszym modelu wspólnej odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft robi, aby wypełniać obowiązki dostawcy usług, zobacz [Współużytkowane obowiązki związane z przetwarzaniem w chmurze](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre z zaleceń zawartych w tym artykule mogą być automatycznie monitorowane przez usługę Azure Security Center. Usługa Azure Security Center to pierwsza linia ochrony w ochronie zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu rozwiązania ich.

- Aby uzyskać więcej informacji na temat zaleceń usługi Azure Security Center, zobacz [Zalecenia dotyczące zabezpieczeń w usłudze Azure Security Center](../security-center/security-center-recommendations.md).
- Aby uzyskać informacje na temat Usługi Azure Security Center, zobacz [What is Azure Security Center?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
|Włącz usuwanie nietrwałe | [Usuwanie nietrwałe](key-vault-ovw-soft-delete.md) umożliwia odzyskanie usuniętych przechowalni i obiektów przechowalni |  - |
| Ograniczanie dostępu do danych magazynu  | Postępuj zgodnie z zasadą najmniejszych uprawnień i ograniczaj dostęp członków instytucji do danych magazynu |  - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Ograniczanie liczby użytkowników z dostępem współautora | Jeśli użytkownik ma uprawnienia współautora do płaszczyzny zarządzania przechowalnią kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych, ustawiając zasady dostępu usługi Key Vault. Należy ściśle kontrolować, kto ma dostęp do roli współautora do magazynów kluczy. Upewnij się, że tylko osoby potrzebujące dostępu do autoryzowanych osób mogą uzyskiwać dostęp do twoich magazynów i zarządzać nimi. Możesz przeczytać [Bezpieczny dostęp do magazynu kluczy](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
 Dzienniki diagnostyczne w usłudze Key Vault powinny być włączone | Włącz dzienniki i zachowaj je do roku. Dzięki temu można odtworzyć ślady aktywności do celów dochodzenia, gdy wystąpi zdarzenie zabezpieczeń lub sieć zostanie naruszona. | [Tak](../security-center/security-center-identity-access.md) |
| Ograniczanie dostępu do dzienników magazynu kluczy azure | [Dzienniki magazynu kluczy](key-vault-logging.md) zapisują informacje o działaniach wykonywanych w magazynie, takich jak tworzenie lub usuwanie magazynów, kluczy, wpisów tajnych i mogą być używane podczas badania |  - |

## <a name="networking"></a>Obsługa sieci

| Zalecenie | Komentarze | Security Center |
|-|----|--|
|Ograniczanie ekspozycji sieci | Dostęp do sieci powinien być ograniczony do sieci wirtualnych używanych przez rozwiązania wymagające dostępu do magazynu. Przeglądanie informacji o [punktach końcowych usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz [Bezpieczna dokumentacja programisty .](../security/fundamentals/abstract-develop-secure-apps.md)
