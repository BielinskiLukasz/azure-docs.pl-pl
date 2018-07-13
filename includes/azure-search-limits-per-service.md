---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756291"
---
Magazyn jest ograniczony przez ilość miejsca na dysku lub stały limit na *maksymalną liczbę* indeksów, dokumentu lub inne zasoby ogólne osiągnięta jako pierwsza. Poniższej tabeli opisano limity przestrzeni dyskowej. Maksymalne limity indeksów, dokumentów i innych obiektów, zobacz [limity przez zasób](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Zasób | Bezpłatna | Podstawowe&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Poziom umowy dotyczącej usług (SLA) <sup>3</sup>  |Nie |Yes |Yes |Yes |Yes |Yes |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partycje na usługę |ND |1 |12 |12 |12 |3 |
| Rozmiar partycji |ND |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliki |ND |3 |12 |12 |12 |12 |

<sup>1</sup> podstawowa ma jedną partycję stały. W tej warstwie SUs dodatkowe służą do przydzielania większa liczba replik dla obciążeń związanych z zapytaniami zwiększone.

<sup>2</sup> Usługa S3 (wysoka gęstość) ma stały limit 3 partycji, który jest niższy niż limit partycji dla usługi S3. Niższy limit partycji wynika z tego, że liczba indeksów w przypadku usługi S3 (wysoka gęstość) jest znacznie wyższa. Biorąc pod uwagę, że istnieją limity usługi zarówno dla zasobów obliczeniowych (magazyn i przetwarzanie), jak i zawartości (indeksy i dokumenty), limit zawartości jest osiągany jako pierwszy.

<sup>3</sup> umów dotyczących poziomu usług (SLA) są oferowane w przypadku płatnych usług na dedykowanych zasobów. Bezpłatnych usług i w wersji zapoznawczej funkcji mają żadne umowy SLA. W przypadku usług płatnych umowy SLA zaczną obowiązywać podczas aprowizowania nadmiarowości wystarczające dla Twojej usługi. Co najmniej dwiema replikami są wymagane dla zapytania (odczyt) umowy SLA. Co najmniej trzy repliki są wymagane dla zapytań i indeksowania SLA (odczyt zapis). Liczba partycji nie jest brany pod uwagę umowy SLA. 