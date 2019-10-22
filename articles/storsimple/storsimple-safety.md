---
title: Bezpieczeństwo urządzenia StorSimple | Microsoft Docs
description: Opisuje konwencje bezpieczeństwa, wskazówki i zagadnienia i wyjaśnia, jak bezpiecznie instalować i obsługiwać urządzenie StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 4622a8575d7b6a38226ee3a980c05f143c128356
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "68963532"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpieczne Instalowanie i obsługiwanie urządzenia StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Ikona ![Warning ](./media/storsimple-safety/IC740879.png)
 ikona powiadomienia o bezpieczeństwie ![Read ](./media/storsimple-safety/IC740885.png) **informacji o bezpieczeństwie i kondycji**

Przeczytaj wszystkie informacje dotyczące bezpieczeństwa i kondycji w tym artykule odnoszące się do urządzenia Microsoft Azure StorSimple. Zadbaj o to, aby wszystkie drukowane przewodniki były dostarczane z urządzeniem StorSimple w przyszłości. Nieprzestrzeganie instrukcji i prawidłowe skonfigurowanie, użycie i opieka nad tym produktem może zwiększyć ryzyko poważnej urazu lub zgonu lub uszkodzenia urządzenia lub urządzeń. Dostępna jest również [wersja do pobrania tego przewodnika](https://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa
Poniżej znajdują się ikony, które zostaną znalezione podczas przeglądania środków bezpieczeństwa, które należy zaobserwować podczas konfigurowania i uruchamiania urządzenia Microsoft Azure StorSimple.

| Ikona | Opis |
|:--- |:--- |
| ![Danger ikonę ](./media/storsimple-safety/IC740879.png) **niebezpieczeństwo!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów spowoduje śmierć lub poważną szkodę. Ten znak sygnału jest ograniczony do najbardziej skrajnych sytuacji. |
| ![Warning ikonę ](./media/storsimple-safety/IC740879.png) **Ostrzeżenie!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może spowodować śmierć lub poważną szkodę. |
| ![Warning ikonę ](./media/storsimple-safety/IC740879.png) **Przestroga!** |Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może skutkować drobną lub średnią szkodą. |
| ![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:** |Wskazuje informacje uznawane za ważne, ale nie związane z zagrożeniami. |
| Ikona wstrząsu ![Electrical ](./media/storsimple-safety/IC740882.png) **zagrożenie porażeniem elektrycznym** |Wysoki poziom napięcia |
| Ikona ![Heavy wagi ](./media/storsimple-safety/IC740883.png) **duże wagi** | |
| Ikona części ![No z obsługą użytkowników ](./media/storsimple-safety/IC740879.png) **Brak części z obsługą użytkownika** |Nie należy uzyskiwać dostępu, o ile nie jest prawidłowo szkolony. |
| Ikona powiadomienia o zabezpieczeniach ![Read](./media/storsimple-safety/IC740885.png)**najpierw Przeczytaj wszystkie instrukcje** | |
| Ikona zagrożenia ![Tip ](./media/storsimple-safety/IC740886.png) **niebezpieczeństwo Porada** | |

## <a name="handling-precautions"></a>Obsługa środków ostrożności
Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikonie ![Heavy wagi ](./media/storsimple-safety/IC740883.png) **Ostrzeżenie!** 

Aby zmniejszyć ryzyko powstania szkody:

* W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów); nie należy próbować go podnieść przez siebie.
* Przed przeniesieniem obudowy zawsze upewnij się, że dwie osoby są dostępne do obsługi wagi. Należy pamiętać, że jedna osoba próbująca podnieść wagę może utrzymywać urazy.
* Nie należy podłączać obudowy przez uchwyty dla modułów mocy i chłodzenia (PCMs) znajdujących się w tylnej części jednostki. Nie są one przeznaczone do rozważenia.

## <a name="connection-precautions"></a>Środki ostrożności dotyczące połączenia
Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikona wstrząsania ![Electrical ](./media/storsimple-safety/IC740882.png) **Ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo powstania szkody, porażenia energii elektrycznej lub zgonu:

* Jeśli jest obsługiwany przez wiele źródeł AC, odłącz wszystkie zasilanie dostaw, aby uzyskać pełną izolację.
* Trwale Odłącz jednostkę przed jej przeniesieniem lub jeśli uważasz, że została ona uszkodzona w jakikolwiek sposób.
* Zapewnij bezpieczne połączenie elektryczne naziemne z przedłużaczami zasilania. Przed zastosowaniem zasilania należy sprawdzić, czy uziemienie obudowy spełnia wymagania krajowe i lokalne.
* Upewnij się, że połączenie zasilania jest zawsze rozłączone przed usunięciem modułu PCM z obudowy.
* Mając na względzie, że wtyk odbiornika zasilania jest głównym urządzeniem odłączania, upewnij się, że są one zlokalizowane blisko sprzętu i są łatwo dostępne.

Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikona wstrząsania ![Electrical ](./media/storsimple-safety/IC740882.png) **Ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo przegrzania lub wygaszenia z połączeń elektrycznych:

* Zapewnienie odpowiedniego źródła zasilania z ochroną przed przeciążeniem elektrycznym w celu spełnienia wymagań szczegółowych dotyczących specyfikacji technicznej.
* Nie używaj bifurcated napięcia (potencjalni klienci "Y").
* Aby zapewnić zgodność z odpowiednimi wymaganiami dotyczącymi bezpieczeństwa, emisji i termicznej, nie należy usuwać żadnych okładek, a wszystkie kieszenie muszą być wypełniane za pomocą modułów wtyczek lub pustych dysków.
* Upewnij się, że urządzenie jest używane w sposób określony przez producenta. Jeśli ten sprzęt jest używany w sposób nieokreślony przez producenta, ochrona zapewniona przez urządzenie może być niesparowana.

![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:**

W celu zapewnienia prawidłowej obsługi sprzętu i zapobiegania uszkodzeniu produktu:

* Porty RJ45 z tyłu urządzenia są tylko dla połączenia Ethernet. Nie mogą one być połączone z siecią telekomunikacyjną.
* Pamiętaj, aby zainstalować urządzenie w stojaku, które może obsłużyć projekt chłodzenia z przodu do tyłu.
* Wszystkie moduły dodatków plug-in i puste płytki są częścią obudowy systemowej. Te dane muszą zostać usunięte tylko wtedy, gdy można je dodać natychmiast. System nie może być uruchamiany bez wszystkich modułów i miejsc.

## <a name="rack-system-precautions"></a>Środki ostrożności systemu stojaka
Podczas instalowania urządzenia w szafie w stojaku należy wziąć pod uwagę następujące wymagania dotyczące zabezpieczeń.

Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikona zagrożenia ![Tip ](./media/storsimple-safety/IC740886.png) **Ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo powstania szkód z pozostałej Porady:

* Projekt stojaka powinien obsługiwać łączną wagę zainstalowanych obudów i powinien zawierać funkcje stabilizacji odpowiednie do zapobiegania przerzucaniu lub wypychaniu stojaka w trakcie instalacji lub normalnego użycia.
* Podczas ładowania stojaka Wypełnij stojak od dołu do góry i pustego w dół.
* Nie przesuwaj więcej niż jeden obudowa poza stojakiem, aby uniknąć niebezpieczeństwa stojaka toppling.

Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikona wstrząsania ![Electrical ](./media/storsimple-safety/IC740882.png) **Ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo powstania szkody, porażenia energii elektrycznej lub zgonu:

* Stojak powinien mieć bezpieczny system dystrybucji elektrycznej. Musi on zapewnić nadmierną ochronę dla obudowy i nie może być przeciążony przez łączną liczbę zainstalowanych obudów. Należy przestrzegać oceny zużycia energii elektrycznej pokazanej w nameplate.
* System dystrybucji elektrycznej musi zapewnić niezawodne uziemienie dla każdej obudowy w stojaku.
* Projekt systemu dystrybucji elektrycznej musi uwzględniać całkowity wyciek ziemi z wszystkich zasilaczy we wszystkich obudowach. Należy zauważyć, że każdy zasilacz w każdej obudowie ma wyciek ziemi prądu o wartości 1,0, która ma maksimum o 60 Hz, 264 woltów. Stojak może wymagać etykietowania z "PRĄDem o dużym WYCIEKu. Połączenie naziemne jest niezbędne przed połączeniem dostawy ".
* Stojak, gdy jest skonfigurowany przy użyciu obudów, musi spełniać wymagania w zakresie bezpieczeństwa z UL 60950-1 i IEC 60950-1/EN 60950-1.

![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:**

Aby zapewnić prawidłowe chłodzenie systemu stojaka:

* Upewnij się, że konstrukcja stojaka uwzględnia maksymalną temperaturę otoczenia w otoczeniu 35 stopni Celsjusza (95 stopni Fahrenheita).
* System działa z instalacją z niską ilością danych (w przypadku niewielkim ciśnieniu), które są używane przez drzwi stojaków i przeszkody, które nie przekraczają 5 Pascalów [0,5 mm-miernika wody]).

## <a name="power-cooling-module-pcm-precautions"></a>Środki zabezpieczające modułu chłodzenia (PCM)
Urządzenie zostało zaprojektowane do obsługi dwóch PCMs. Każdy PCMs ma zasilacz i wentylator dwuosiowy. W sytuacji krytycznej system umożliwia nieprzerwanie jednego zasilacza awaryjnego w czasie wykonywania normalnych operacji. Muszą być zawsze zainstalowane dwa PCMs (i w związku z tym zasilacze). Pojedynczy moduł PCM nie zapewnia nadmiarowej mocy. W związku z tym awaria nawet jednego modułu PCM może spowodować przestoje lub utratę danych.

Ikona ![Warning ](./media/storsimple-safety/IC740879.png) ikona wstrząsania ![Electrical ](./media/storsimple-safety/IC740882.png) **Ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo powstania szkody, porażenia energii elektrycznej lub zgonu:

* Nie usuwaj okładek z modułu PCM. Występuje niebezpieczeństwo porażenia prądem. Aby zwrócić moduł PCM i uzyskać zamiennik, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-contact-microsoft-support.md).

![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:**

W celu zapewnienia prawidłowej obsługi sprzętu i zapobiegania uszkodzeniu produktu:

* Należy zastąpić moduł PCM zakończony niepowodzeniem w ciągu 24 godzin. Po usunięciu modułu PCM do zamiany, zastąpienie musi zostać ukończone w ciągu 10 minut od usunięcia.
* Nie usuwaj modułu PCM, chyba że zastępowanie można zainstalować natychmiast. Obudowa nie może działać bez wszystkich modułów.

## <a name="electrostatic-discharge-esd-precautions"></a>Środki zaradcze wyładowania elektrostatyczne (ESD)
![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:**

Obserwuj następujące środki ostrożności związane z ESD.

* Upewnij się, że zainstalowano i sprawdzono odpowiedni pasek antystatyczny nadgarstka lub kostka.
* Obserwuj wszystkie konwencjonalne środki ostrożności ESD podczas obsługi modułów i składników.
* Unikaj kontaktu ze składnikami planu i łącznikami modułów.
* ESD uszkodzenie nie jest objęte gwarancją.

## <a name="battery-disposal-precautions"></a>Środki ostrożności dotyczące usuwania baterii
Zasilacz zużywa specjalną baterię do ochrony zawartości pamięci w trakcie tymczasowego, krótkoterminowej przerwy w zasilaniu. Bateria jest umieszczona w module PCM. Należy pamiętać o następujących kwestiach dotyczących baterii.

![Warning ikonę ](./media/storsimple-safety/IC740879.png) **Ostrzeżenie!**

Aby zmniejszyć ryzyko związane z szorty, pożarem, wybuchem, urazem lub zgonem:

* Usuń zużyte baterie zgodnie z przepisami krajowymi/regionalnymi.
* Nie należy wyłączać, zgniatać ani podgrzewać powyżej 60 stopni Celsjusza (140 stopni Fahrenheita) ani spopielać. Zastąp baterię PCM tylko dostarczaną baterią. Korzystanie z innej baterii może stanowić ryzyko pożaru lub wybuchu.
* Użyj ochrony końcowej na bateriach, jeśli są one usuwane z zasilacza.

![Notice ikonę ](./media/storsimple-safety/IC740881.png) **Uwaga:**

W przypadku wysyłania lub przenoszenia baterii przez powietrze należy postępować zgodnie z dokumentem wskazówek dotyczących baterii IATA litu dostępnym w [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po przejrzeniu tych informacji o zabezpieczeniach następnym etapem jest rozpakowanie, stojak i podłączenie urządzenia.

## <a name="next-steps"></a>Następne kroki
* W przypadku urządzenia z 8100 przejdź do pozycji [Zainstaluj urządzenie z StorSimple 8100](storsimple-8100-hardware-installation.md).
* W przypadku urządzenia z 8600 przejdź do pozycji [Zainstaluj urządzenie z StorSimple 8600](storsimple-8600-hardware-installation.md).

