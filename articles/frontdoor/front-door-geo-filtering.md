---
title: Filtrowanie geograficzne domeny w usłudze Azure Front Door Service | Microsoft Docs
description: W tym artykule poznasz zasady filtrowania geograficznego usługi Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: sharadag
ms.openlocfilehash: a2ba0fb34dd34129a134aa2639d06816f3523408
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865509"
---
# <a name="geo-filtering-geographic-based-access-control-to-azure-front-door-service-frontends"></a>Filtrowanie geograficzne: geograficzna kontrola dostępu do frontonów usługi Azure Front Door Service

Domyślnie usługa Azure Front Door Service odpowiada na żądania użytkowników niezależnie od lokalizacji użytkownika wysyłającego żądanie. Jednak czasami może być konieczne ograniczenie dostępu do aplikacji internetowych według kraju. Zabezpieczenia warstwy aplikacji usługi Azure Front Door pozwalają zdefiniować zasady zezwalania lub blokowania dostępu z określonych krajów przy użyciu niestandardowych reguł ochrony dla określonej ścieżki w punkcie końcowym. 

Zasady zabezpieczeń aplikacji zwykle obejmują zestaw reguł niestandardowych. Reguła składa się z warunków dopasowań, akcji i priorytetu. W warunku dopasowania należy zdefiniować zmienną dopasowania, operator i wartość dopasowania.  W przypadku reguły filtrowania geograficznego zmienna dopasowania to REMOTE_ADDR, operator to GeoMatch, a wartość to dwuliterowy kod kraju. Możesz połączyć warunek GeoMatch i warunek dopasowania ciągu REQUEST_URI, aby utworzyć regułę filtrowania geograficznego opartą na ścieżkach.

Możesz skonfigurować zasady filtrowania geograficznego dla usługi Front Door za pomocą programu [Azure PowerShell](front-door-tutorial-geo-filtering.md) lub naszego [szablonu Szybki start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Odwołanie do kodu kraju

|Kod kraju | Nazwa kraju |
| ----- | ----- |
| AD | Andora |
| AE | Zjednoczone Emiraty Arabskie|
| AF | Afganistan|
| AG | Antigua i Barbuda|
| AL | Albania|
| AM | Armenia|
| AO | Angola|
| AR | Argentyna|
| AS | Samoa Amerykańskie|
| AT | Austria|
| AU | Australia|
| AZ | Azerbejdżan|
| BA | Bośnia i Hercegowina|
| BB | Barbados|
| BD | Bangladesz|
| BE | Belgia|
| BF | Burkina Faso|
| BG | Bułgaria|
| BH | Bahrajn|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthelemy|
| BN | Brunei Darussalam|
| BO | Boliwia|
| BR | Brazylia|
| BS | Bahamy|
| BT | Bhutan|
| BW | Botswana|
| BY | Białoruś|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratyczna Republika Konga|
| CF | Republika Środkowoafrykańska|
| CH | Szwajcaria|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Chiny|
| CO | Kolumbia|
| CR | Kostaryka|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Cypr|
| CZ | Czechy|
| DE | Niemcy|
| DK | Dania|
| DO | Republika Dominikańska|
| DZ | Algieria|
| EC | Ekwador|
| EE | Estonia|
| EG | Egipt|
| ES | Hiszpania|
| ET | Etiopia|
| FI | Finlandia|
| FJ | Fidżi|
| FM | Federalne Stany Mikronezji|
| PW | Francja|
| GB | Wielka Brytania|
| GE | Gruzja|
| GF | Gujana Francuska|
| GH | Ghana|
| GN | Gwinea|
| GP | Gwadelupa|
| GR | Grecja|
| GT | Gwatemala|
| GY | Gujana|
| HK | Hongkong|
| HN | Honduras|
| HR | Chorwacja|
| HT | Haiti|
| HU | Węgry|
| ID | Indonezja|
| IE | Irlandia|
| IL | Izrael|
| IN | Indie|
| IQ | Irak|
| IR | Islamska Republika Iranu|
| IS | Islandia|
| IT | Włochy|
| JM | Jamajka|
| JO | Jordania|
| JP | Japonia|
| KE | Kenia|
| KG | Kirgistan|
| KH | Kambodża|
| KI | Kiribati|
| KN | Saint Kitts i Nevis|
| KP | Koreańska Republika Ludowo-Demokratyczna|
| KR | Korea Południowa|
| KW | Kuwejt|
| KY | Kajmany|
| KZ | Kazachstan|
| LA | Laotańska Republika Ludowo-Demokratyczna|
| LB | Liban|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litwa|
| LU | Luksemburg|
| LV | Łotwa|
| LY | Libia|
| MA | Maroko|
| MD | Republika Mołdawii|
| MG | Madagaskar|
| MK | Macedonia|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolia|
| MO | Makau|
| MQ | Martynika|
| MR | Mauretania|
| MT | Malta|
| MV | Malediwy|
| MW | Malawi|
| MX | Meksyk|
| MY | Malezja|
| MZ | Mozambik|
| Nie dotyczy | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nikaragua|
| NL | Holandia|
| NO | Norwegia|
| NP | Nepal|
| NR | Nauru|
| NZ | Nowa Zelandia|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filipiny|
| PK | Pakistan|
| PL | Polska|
| PR | Portoryko|
| PT | Portugalia|
| PW | Palau|
| PY | Paragwaj|
| QA | Katar|
| RE | Reunion|
| RO | Rumunia|
| RS | Serbia|
| RU | Federacja Rosyjska|
| RW | Rwanda|
| SA | Arabia Saudyjska|
| SD | Sudan|
| SE | Szwecja|
| SG | Singapur|
| SI | Słowenia|
| SK | Słowacja|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sudan Południowy|
| SV | Salwador|
| SY | Arabska Republika Syryjska|
| SZ | Suazi|
| TC | Wyspy Turks i Caicos|
| TG | Togo|
| TH | Tajlandia|
| TN | Tunezja|
| TR | Turcja|
| TT | Trynidad i Tobago|
| TW | Tajwan|
| TZ | Zjednoczona Republika Tanzanii|
| UA | Ukraina|
| UG | Uganda|
| USA | Stany Zjednoczone|
| UY | Urugwaj|
| UZ | Uzbekistan|
| VC | Saint Vincent i Grenadyny|
| VE | Wenezuela|
| VG | Brytyjskie Wyspy Dziewicze|
| VI | Wyspy Dziewicze Stanów Zjednoczonych|
| VN | Wietnam|
| ZA | Republika Południowej Afryki|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje o [zabezpieczeniach warstwy aplikacji w usłudze Front Door](front-door-application-security.md).
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
