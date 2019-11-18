---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna wbudowanych mechanizmów zabezpieczeń do oceny usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: b2916a87c5cef01605054569285362fcf1c649c8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147569"
---
# <a name="security-controls-for-azure-resource-manager"></a>Kontrolki zabezpieczeń dla Azure Resource Manager

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Yes |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | HTTPS/TLS. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy | Azure Resource Manager nie przechowuje zawartości klienta, tylko kontrolują dane. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Yes | |
| Wywołania interfejsu API są szyfrowane| Yes | |

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie | |
| Obsługa iniekcji sieci wirtualnej| Yes | |
| Izolacja sieci i obsługa zapór| Nie |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Nie | |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dzienniki aktywności uwidaczniają wszystkie operacje zapisu (PUT, POST, DELETE) wykonywane na zasobach; Zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji na zasobach](resource-group-audit.md). |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie dotyczy | |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Na podstawie [Azure Active Directory](/azure/active-directory) .|
| Autoryzacja| Yes | |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).