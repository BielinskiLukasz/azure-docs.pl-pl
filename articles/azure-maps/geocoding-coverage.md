---
title: Pokrycie geokodowaniem w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o pokrycie Geokodowaniem w usługi Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 664d52a96f1e724502f82f5f72567f87c74e716b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444260"
---
# <a name="azure-maps-geocoding-coverage"></a>Pokrycie geokodowaniem usługi Azure Maps

Podczas wyszukiwania lokalizacji za pomocą usługi Azure Maps, Usługa wyszukiwania przyjmuje wyszukiwane terminy i zwraca współrzędne długości i szerokości geograficznej w procesie zwanym geokodowania. Jednakże mapy nie ma takiego samego poziomu informacji i dokładność dla wszystkich regionów i krajów. Użyj w tym artykule, aby określić, jakiego rodzaju lokalizacji można wiarygodnie wyszukiwać w każdym regionie. 

Możliwość geokodowania w kraju, zależy od zakresu danych podróży i dokładności geokodowania usługi geokodowania. Używane są następujące kategoryzacje Określ poziom obsługi geokodowania w każdym kraju.
* **Adres punktów** — adresy danych może być rozpoznany Współrzędna szerokości/długości geograficznej w ramach działka adresu (właściwość granic). Czasami określane jako "Antenowej" dokładne. Jest to najwyższy poziom dokładności dostępnych adresów. 
* **Lokalne numery** — adresy są interpolowane do domu Współrzędna szerokości/długości geograficznej.
* **Poziom ulicy** — adresy są rozwiązywane do Współrzędna szerokości/długości geograficznej ulicy, która zawiera adres. Numer domu nie mogą być przetwarzane.
* **Poziom Miasto** -Miasto nazw są obsługiwane.

## <a name="americas"></a>Ameryki

| Region             | Adres punktów | Numery lokalne | Poziom ulicy | Poziom Miasto | Punktów orientacyjnych |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktyda                                          |                 |                |              |      ✓     |          ✓         |
| Antigua i Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentyna                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudy                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Boliwia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius i Saba|                 |                |              |      ✓     |          ✓         |
| Brazylia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kajmany                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspa Clipperton                                   |                 |                |              |      ✓     |                    |
| Kolumbia                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kostaryka                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominika                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ekwador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Salwador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandy                                    |                 |                |              |      ✓     |          ✓         |
| Gujana Francuska                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Gwadelupa|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Gujana        |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamajka                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martynika                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Meksyk                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nikaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paragwaj                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Portoryko                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts i Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre i Miquelon|                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent i Grenadyny                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Georgia Południowa i Sandwich Południowy        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trynidad i Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Odległe Mniejsze Wyspy Stanów Zjednoczonych                |                 |                |              |      ✓     |          ✓         |
| Stany Zjednoczone Ameryki                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Urugwaj                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wenezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Dziewicze — Kolumbia                            |                 |                |              |      ✓     |          ✓         |
| Wyspy Dziewicze — Stany Zjednoczone                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Azja i Pacyfik

| Region             | Adres punktów |Numery lokalne | Poziom ulicy | Poziom Miasto | Punktów orientacyjnych |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Amerykańskie                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesz                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Brytyjskie Terytorium Oceanu Indyjskiego                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodża                                            |                 |                |              |      ✓     |          ✓         |
| Chiny |                 |                |              |      ✓     |          ✓         |
| Wyspa Bożego Narodzenia                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Wyspy Kokosowe (Keelinga)|                 |                |              |      ✓     |          ✓         |
| Komory|                 |                |              |      ✓     |          ✓         |
| Wyspy Cooka                                        |                 |                |              |      ✓     |          ✓         |
| Fidżi |                 |                |              |      ✓     |          ✓         |
| Polinezja Francuska                                    |                 |                |              |      ✓     |          ✓         |
| Wyspy Heard i McDonalda                   |                 |                |              |      ✓     |          ✓         |
| SRA Hongkong                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonezja                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indie       |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japonia                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| SRA Makau                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malezja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronezja |                 |                |              |      ✓     |          ✓         |
| Mongolia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Napal|                 |                |              |      ✓     |          ✓         |
| Nowa Kaledonia                                       |                 |                |              |      ✓     |          ✓         |
| Nowa Zelandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Korea Północna                                         |                 |                |              |      ✓     |          ✓         |
| Mariany Północne                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau |                 |                |              |      ✓     |          ✓         |
| Papua Nowa Gwinea                                    |                 |                |              |      ✓     |          ✓         |
| Wyspy Paracel                                     |                 |                |              |      ✓     |                    |
| Filipiny                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapur                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Salomona                                     |                 |                |              |      ✓     |          ✓         |
| Korea Południowa                                         |                 |                |              |      ✓     |          ✓         |
| Kurils południowy                                     |        ✓        |                |              |      ✓     |          ✓         |
| Wyspy Spratly                                      |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Tajwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tajlandia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Turks i Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Wietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis i Futuna|                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Region             | Adres punktów |Numery lokalne | Poziom ulicy | Poziom Miasto | Punktów orientacyjnych |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albania                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andora                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenia                                             |                 |                |              |      ✓     |          ✓         |
| Austria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbejdżan                                          |                 |                |              |      ✓     |          ✓         |
| Belgia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bośnia i Hercegowina                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bułgaria                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Białoruś|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Chorwacja                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cypr                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Czechy                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dania                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspy Owcze                                       |                 |                |              |      ✓     |          ✓         |
| Finlandia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Francja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gruzja                                             |                 |                |              |      ✓     |          ✓         |
| Niemcy                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibralter                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grecja                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grenlandia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Węgry                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Islandia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlandia (Republika)                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wyspa Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Włochy                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstan                                          |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kosowo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgistan                                          |                 |                |              |      ✓     |          ✓         |
| Łotwa                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litwa                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Luksemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| BJR Macedonia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mołdawia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Monako                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Czarnogóra                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Holandia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norwegia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polska                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugalia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Rumunia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federacja Rosyjska                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowacja                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Słowenia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Hiszpania                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Szwecja                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Szwajcaria                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadżykistan                                          |                 |                |              |      ✓     |          ✓         |
| Turcja                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraina                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Wielka Brytania                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Watykan                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Bliski Wschód i Afryka

| Region             | Adres punktów |Numery lokalne | Poziom ulicy | Poziom Miasto | Punktów orientacyjnych |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afganistan                                         |                 |                |              |      ✓     |          ✓         |
| Algieria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrajn                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Bouveta                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Zielonego Przylądka|                 |                |       ✓      |      ✓     |          ✓         |
| Republika Środkowoafrykańska                            |                 |                |       ✓      |      ✓     |          ✓         |
| Czad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Kongo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratyczna Republika Konga                        |                 |                |       ✓      |      ✓     |          ✓         |
| Dżibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egipt                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gwinea Równikowa, Republika Iranu                      |                 |                |       ✓      |      ✓     |          ✓         |
| Erytrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Francuskie Terytoria Południowe|                 |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Gwinea Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Izrael                                              |                 |                |              |      ✓     |          ✓         |
| Jordania                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwejt                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libia|                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Malediwy |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspy Marshalla |                 |                |              |      ✓     |          ✓         |
| Mauretania                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Wyspa Majotta                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroko                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambik                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Katar                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Réunion|                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Święta Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arabia Saudyjska                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seszele                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Republika Południowej Afryki                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudan Południowy                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazi                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syria                                               |                 |                |              |      ✓     |          ✓         |
| Wyspy Świętego Tomasza i Książęca, Demokratyczna Republika       |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunezja                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zjednoczone Emiraty Arabskie                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Zachodni Brzeg                                           |                 |                |              |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Maps geokodowania, zobacz [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) odwoływać się do strony.

Dowiedz się więcej o [obszary pokrycia dla mapy ruchu usługi](traffic-coverage.md). 

