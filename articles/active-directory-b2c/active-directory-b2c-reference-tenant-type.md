---
title: Region dostępność rezydencja danych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat na typach dzierżaw usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67f87729dbfb792b88f28a772f15e233f0fa60dc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452117"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Dostępność w poszczególnych regionach i rezydencja danych
Dostępność w poszczególnych regionach i rezydencja danych są dwa bardzo różne zagadnienia dotyczące inaczej usługi Azure AD B2C z pozostałą część platformy Azure. Spowoduje to w tym artykule wyjaśniono różnice między tymi dwoma pojęciami i porównać, jak odnoszą się do platformy Azure i usługi Azure AD B2C.

## <a name="summary"></a>Podsumowanie
Usługa Azure AD B2C jest **jest ogólnie dostępna na całym świecie** z opcją dla **przechowywaniem danych w Stanach Zjednoczonych lub Europy**.

## <a name="concepts"></a>Pojęcia
* **Dostępność w poszczególnych regionach** odwołuje się do której usługa jest dostępna do użycia.
* **Rezydencja** odwołuje się do przechowywania danych użytkownika.

## <a name="region-availability"></a>Dostępność w danym regionie
Usługa Azure AD B2C jest dostępna na całym świecie za pośrednictwem chmury publicznej platformy Azure. 

To różni się od modelu wykonaj większości innych usług platformy Azure, która Połącz dostępności z rezydencją danych. Widać to przykłady w obu Azure [produktów dostępne w regionie](https://azure.microsoft.com/regions/services/) strony i [Kalkulator cen programu Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezydencja danych
Usługa Azure AD B2C przechowuje dane użytkowników w Stanach Zjednoczonych lub Europy.

Rezydencja jest ustalana na kraj/region, który wybrano podczas [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).

![Zrzut ekranu przedstawiający dzierżawy (wersja zapoznawcza)](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Dane znajdują się w Stanach Zjednoczonych w następujących krajach/regionach:

> Stanów Zjednoczonych, Kanadzie, Kostaryka, Dominikana, Salwador, Gwatemala, Meksyk, Panama, Portoryko i Trinidad i Tobago

Dane znajdują się w Europie w następujących krajach/regionach:

> Algierii, Austrii, Azerbejdżanu, Bahrajn, Białorusi, Belgii, Bułgarii, Chorwacji, Cypru, Czechy, Dania, Egiptu, Estonii, Finlandia, Francja, Niemcy, Grecja, Węgierskiej, Islandii, Irlandii, Izrael, Włochy, Jordania, Kazachstanu, Kenii, Kuwejt, Lativa, Liban, Liechtenstein, Lituania, Luksemburg, Macedonia BJR, m Alta, Czarnogóry, Maroka, Holandia, Nigerii, Norwegia, Oman, Pakistanu, Polski, Portugalia, Katar, Rumunii, Rosji, Arabia Saudyjska, Serbii, Słowacji, Słowenii, Republika Południowej Afryki, Hiszpanii, Szwecja, Szwajcarii, Tunezji, Turcja, Ukrainy, Zjednoczone Emiraty Arabskie i Zjednoczone Królestwo.

Pozostałe kraje/regiony są właśnie dodawane do listy.  Teraz można nadal używać usługi Azure AD B2C, wybierając jedną z krajów/regionów powyżej.

> Afganistan, Argentyny, Australii, Brazylia, Chile, Kolumbia, Ekwador, SRA Hongkong, Indie, Indonezja, Irak, Japonia, Korea, Malezja, Nowej Zelandii, Paragwaj, Peru, Filipiny, Singapur, Sri Lanka, Tajwan, Tajlandia, Urugwaj i Wenezuela.

## <a name="preview-tenant"></a>Podgląd dzierżawy
Jeśli dzierżawa B2C została utworzona w okresie zapoznawczym usługa Azure AD B2C, istnieje duże prawdopodobieństwo, Twoje **dzierżawy typu** mówi **Podgląd dzierżawy**. Jeśli jest to możliwe, należy użyć swojej dzierżawy, tylko w przypadku tworzenia i testowania, a nie dla aplikacji produkcyjnych.

> [!IMPORTANT]
> Istnieje nie ścieżki migracji z dzierżawy usługi B2C w wersji zapoznawczej do dzierżawy usługi B2C w skali produkcyjnej. Należy zauważyć, że istnieją znane problemy podczas usuwania dzierżawy B2C w wersji zapoznawczej i ponownie utwórz skali produkcyjnej dzierżawy B2C, z tą samą nazwą domeny. Trzeba utworzyć dzierżawę B2C skali produkcyjnej o nazwie innej domeny.


![Zrzut ekranu przedstawiający dzierżawy (wersja zapoznawcza)](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
