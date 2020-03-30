---
title: Najważniejsze wskazówki dotyczące korzystania z usługi Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: W tym dokumencie wyjaśniono niektóre z najlepszych rozwiązań w zakresie korzystania z usługi Key Vault
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270994"
---
# <a name="best-practices-to-use-key-vault"></a>Najważniejsze wskazówki dotyczące korzystania z usługi Key Vault

## <a name="control-access-to-your-vault"></a>Sterowanie dostępem do skarbca

Usługa Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla firmy, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowanym aplikacjom i użytkownikom. Ten [artykuł](key-vault-secure-your-key-vault.md) zawiera omówienie modelu dostępu usługi Key Vault. Wyjaśniono uwierzytelnianie i autoryzację oraz opisano sposób bezpiecznego dostępu do magazynów kluczy.

Sugestie podczas kontrolowania dostępu do przechowalni są następujące:
1. Blokowanie dostępu do subskrypcji, grupy zasobów i magazynów kluczy (RBAC)
2. Tworzenie zasad programu Access dla każdego przechowalni
3. Używanie jednostki dostępu z najmniejszymi uprawnieniami do udzielania dostępu
4. Włączanie [punktów końcowych zapory](key-vault-overview-vnet-service-endpoints.md) i sieci wirtualnej

## <a name="use-separate-key-vault"></a>Użyj oddzielnego magazynu kluczy

Naszym zaleceniem jest użycie magazynu dla aplikacji na środowisko (programistyczne, przedprodukcjowe i produkcyjne). Pomaga to nie udostępniać wpisów tajnych w środowiskach, a także zmniejsza zagrożenie w przypadku naruszenia.

## <a name="backup"></a>Tworzenie kopii zapasowych

Upewnij się, że regularnie korzystasz z kopii zapasowych [skarbca](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) podczas aktualizacji/usuwania/tworzenia obiektów w skarbcu.

## <a name="turn-on-logging"></a>Włączanie rejestrowania

[Włącz rejestrowanie](key-vault-logging.md) skarbca. Skonfiguruj również alerty.

## <a name="turn-on-recovery-options"></a>Włączanie opcji odzyskiwania

1. Włącz [usuwanie trędeksuj](key-vault-ovw-soft-delete.md).
2. Włącz ochronę przed przeczyszczaniem, jeśli chcesz zabezpieczyć się przed wymuszaniem usunięcia klucza tajnego / skarbca nawet po włączeniu miękkiego usuwania.
