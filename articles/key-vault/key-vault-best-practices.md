---
title: Najlepsze rozwiązania dotyczące korzystania z Key Vault Azure Key Vault | Microsoft Docs
description: W tym dokumencie wyjaśniono niektóre najlepsze rozwiązania dotyczące korzystania z Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cb4bec8170466f0fc667b592d44b0858c41ccd84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270994"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania w zakresie używania Key Vault

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. Ponieważ te dane są poufne i krytyczne dla działania firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko na autoryzowane aplikacje i użytkowników. Ten [artykuł](key-vault-secure-your-key-vault.md) zawiera omówienie modelu dostępu Key Vault. W tym artykule wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie dotyczące kontroli dostępu do magazynu są następujące:
1. Zablokuj dostęp do subskrypcji, grupy zasobów i magazynów kluczy (RBAC)
2. Utwórz zasady dostępu dla każdego magazynu
3. Użyj najmniejszej nazwy podmiotu zabezpieczeń dostępu, aby udzielić dostępu
4. Włącz funkcję Zapora i [punkty końcowe usługi sieci wirtualnej](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Użyj oddzielnego Key Vault

Nasze zalecenie polega na użyciu magazynu dla każdej aplikacji na środowisko (projektowanie, przedprodukcyjne i produkcyjne). Ułatwia to nie udostępnianie wpisów tajnych w różnych środowiskach i zmniejsza zagrożenie w przypadku naruszenia.

## <a name="backup"></a>Backup

Zadbaj o to, aby regularnie korzystać z [magazynu](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) w celu aktualizowania/usuwania/tworzenia obiektów w magazynie.

## <a name="turn-on-logging"></a>Włącz rejestrowanie

[Włącz rejestrowanie](key-vault-logging.md) dla swojego magazynu. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

1. Włącz [usuwanie nietrwałe](key-vault-ovw-soft-delete.md).
2. Włącz ochronę przed przeczyszczeniem, jeśli chcesz chronić przed usunięciem wpisu tajnego lub magazynu, nawet po włączeniu usuwania nietrwałego.
