---
title: Zamiana mowy na tekst często zadawanych pytań
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi zamiany mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168460"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Zamiana mowy na tekst często zadawanych pytań

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym często zadawane pytania, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: Jaka jest różnica między modelem linii bazowej i niestandardową zamianą mowy na model tekstu?**

Odp **.: model**linii bazowej został przeszkolony przy użyciu danych należących do firmy Microsoft i jest już wdrożony w chmurze. Możesz użyć niestandardowego modelu, aby dostosować model do lepszego dopasowania do określonego środowiska, które ma określony szum lub język otoczenia. Piętra fabryki, samochody i zakłócenia! wymagają dostosowania modelu akustycznego. Tematy, takie jak biologia, fizyka, Radiologia, nazwy produktów i niestandardowe akronimy, wymagają przystosowanego modelu języka.

**P: co mogę zrobić, jeśli chcę użyć modelu linii bazowej?**

Odp **.: najpierw**Pobierz [klucz subskrypcji](get-started.md). Jeśli chcesz, aby wywołania REST były nawiązywane w ramach prewdrażanych modeli bazowych, zobacz [interfejsy API REST](rest-apis.md). Jeśli chcesz używać obiektów WebSockets, [Pobierz zestaw SDK](speech-sdk.md).

**P: Czy zawsze muszę utworzyć niestandardowy model mowy?**

Odp **.: nie**. Jeśli aplikacja używa ogólnego, codziennego języka, nie musisz dostosowywać modelu. Jeśli aplikacja jest używana w środowisku, w którym nie ma żadnego szumu w tle, nie musisz dostosowywać modelu.

Możesz wdrożyć linie bazowe i dostosowane modele w portalu, a następnie uruchomić na nich testy dokładności. Ta funkcja służy do mierzenia dokładności modelu linii bazowej w porównaniu z modelem niestandardowym.

**P: Jak mogę się dowiedzieć, gdy trwa przetwarzanie dla mojego zestawu danych lub modelu?**

Odp **.: obecnie**jest to jedyny sposób, w jaki stan modelu lub zestawu danych w tabeli. Po zakończeniu przetwarzania stan zostanie **zakończony pomyślnie**.

**P: Czy mogę utworzyć więcej niż jeden model?**

Odp **.: nie**ma żadnego limitu liczby modeli, które mogą znajdować się w kolekcji.

**P: zgłoszono pomyłkę. Jak mogę anulować Importowanie danych lub tworzenie modelu, który jest w toku?**

Odp **.: obecnie**nie można wycofać procesu adaptacji akustycznej lub językowej. Zaimportowane dane i modele można usunąć, gdy są one w stanie terminalu.

**P: Jaka jest różnica między modelami wyszukiwania i rozdyktowania a modelem konwersacji?**

Odp **.: możesz**wybrać spośród więcej niż jednego modelu linii bazowej w usłudze Speech. Model konwersacji jest przydatny do rozpoznawania mowy, która jest wypowiadana w stylu konwersacji. Ten model jest idealny dla połączeń telefonicznych jego przepisywania. Model wyszukiwania i dyktowania są idealnym rozwiązaniem dla aplikacji wyzwalanych przez głos. Model uniwersalny jest nowym modelem, który ma na celu rozwiązanie obu scenariuszy. Model uniwersalny jest obecnie co najmniej na poziomie jakości modelu konwersacji w większości ustawień regionalnych.

**P: Czy mogę zaktualizować istniejący model (stos modelu)?**

Odp **.: nie**można zaktualizować istniejącego modelu. Jako rozwiązanie Połącz stary zestaw danych z nowym zestawem danych i dostosuj go.

Stary zestaw danych i nowy zestaw danych muszą być połączone w pojedynczym pliku zip (dla danych akustycznych) lub w pliku txt (dla danych języka). Po zakończeniu adaptacji nowy, zaktualizowany model musi zostać ponownie wdrożony w celu uzyskania nowego punktu końcowego

**P: gdy dostępna jest nowa wersja linii bazowej, czy moje wdrożenie jest automatycznie aktualizowane?**

Odp **.: wdrożenia**nie będą automatycznie aktualizowane.

Jeśli dostosowano i wdrożono model z linią bazową 1.0, to wdrożenie pozostanie bez zmian. Klienci mogą zlikwidować wdrożony model, ponownie dostosować ją przy użyciu nowszej wersji planu bazowego i ponownie wdrożyć.

**P: Czy można pobrać model i uruchomić go lokalnie?**

Odp **.: nie**można pobrać i wykonać lokalnie modeli.

**P: Czy moje żądania są zarejestrowane?**

Odp **.: masz**możliwość wyboru podczas tworzenia wdrożenia w celu wyłączenia śledzenia. W tym momencie żadne audio ani transkrypcje nie będą rejestrowane. W przeciwnym razie żądania są zwykle rejestrowane na platformie Azure w bezpiecznym magazynie.

**P: Czy moje żądania są ograniczone?**

Odp **.: interfejs**API REST ogranicza liczbę żądań do 25 na 5 sekund. Szczegółowe informacje znajdują się na stronach na potrzeby [zamiany mowy na tekst](speech-to-text.md).

**P: jak opłaty są naliczane za dwa kanały audio?**

Odp **.: Jeśli**przesyłasz każdy kanał osobno (każdy kanał w osobnym pliku), zostanie naliczona opłata za cały plik. Jeśli przesyłasz pojedynczy plik z każdym kanałem, który zostanie przyładowany, zostanie naliczona opłata za czas trwania pojedynczego pliku. Szczegółowe informacje o cenach można znaleźć na [stronie cennika usługi Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Jeśli masz jeszcze inne problemy z ochroną prywatności, które uniemożliwiają korzystanie z usługi Custom Speech Service, skontaktuj się z jednym z kanałów pomocy technicznej.

## <a name="increasing-concurrency"></a>Zwiększanie współbieżności

**P: co zrobić, jeśli potrzebuję wyższego współbieżności dla mojego wdrożonego modelu niż to, co jest oferowane w portalu?**

Odp **.: można**skalować w górę modelu w przyrostach 20 współbieżnych żądań.

Korzystając z wymaganych informacji, Utwórz żądanie pomocy technicznej w [portalu pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nie Publikuj informacji na żadnym z publicznych kanałów (GitHub, StackOverflow,...) wymienionym na [stronie pomocy technicznej](support.md).

Aby zwiększyć współbieżność ***modelu niestandardowego***, potrzebne są następujące informacje:

- Region, w którym jest wdrożony model,
- Identyfikator punktu końcowego wdrożonego modelu:
  - Czy masz do [portalu Custom Speech](https://aka.ms/customspeech),
  - Zaloguj się (w razie potrzeby),
  - Wybierz projekt i wdrożenie,
  - Wybierz punkt końcowy, dla którego chcesz zwiększyć współbieżność,
  - Skopiuj `Endpoint ID`.

Aby zwiększyć współbieżność ***modelu podstawowego***, potrzebne są następujące informacje:

- Region usługi,

i albo

- token dostępu do subskrypcji (patrz [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)),

lub

- Identyfikator zasobu dla subskrypcji:
  - Przejdź do [Azure Portal](https://portal.azure.com),
  - Wybierz `Cognitive Services` w polu wyszukiwania
  - z wyświetlanych usług wybierz usługę mowy, dla której chcesz zwiększyć współbieżność,
  - Wyświetl `Properties` dla tej usługi,
  - Skopiuj kompletną `Resource ID`.

## <a name="importing-data"></a>Importowanie danych

**P: jaki jest limit rozmiaru zestawu danych i dlaczego jest to limit?**

Odp **.: bieżący**limit zestawu danych wynosi 2 GB. Limit jest spowodowany przez ograniczenie rozmiaru pliku na potrzeby przekazywania HTTP.

**P: Czy mogę zip moje pliki tekstowe, aby można było przekazać większy plik tekstowy?**

Odp **.: nie**. Obecnie dozwolone są tylko nieskompresowane pliki tekstowe.

**P: Raport dotyczący danych wskazuje, że wyrażenia długości nie powiodło się. Jaki jest problem?**

Odp **.:** niepowodzenie przekazania 100 procent wyrażenia długości w pliku nie jest problemem. Jeśli większość wyrażenia długości w zestawie danych akustycznych lub w języku (na przykład więcej niż 95 procent) została pomyślnie zaimportowana, zestaw danych może być użyteczny. Zaleca się jednak, aby poznać przyczynę niepowodzenia wyrażenia długości i rozwiązać problemy. Najczęstsze problemy, takie jak błędy formatowania, są łatwe do naprawienia.

## <a name="creating-an-acoustic-model"></a>Tworzenie modelu akustycznego

**P: jak dużo danych akustycznych jest potrzebnych?**

Odp **.: zalecamy**rozpoczęcie od 30 minut i godzinę akustyczną danych.

**P: jakie dane należy zebrać?**

Odp **.: Zbierz**dane, które są blisko scenariusza aplikacji i przypadku użycia, jak to możliwe. Zbieranie danych powinno być zgodne z aplikacją docelową i użytkownikami w odniesieniu do urządzeń lub urządzeń, środowisk i typów głośników. Ogólnie rzecz biorąc, należy zbierać dane z szerokiego zakresu głośników, jak to możliwe.

**P: jak zbierać dane akustyczne?**

Odp.: możesz utworzyć autonomiczną **aplikację do zbierania**danych lub użyć oprogramowania do nagrywania dźwięku na półkę. Możesz również utworzyć wersję aplikacji, która rejestruje dane audio, a następnie używa tych danych.

**P: Czy muszę transkrypcja dane adaptacyjne?**

Odp **.:** tak! Możesz transkrypcja go samodzielnie lub korzystać z profesjonalnej usługi transkrypcji. Niektórzy użytkownicy preferują profesjonalne transcribers i inne korzystają z crowdsourcing lub same transkrypcji.

## <a name="accuracy-testing"></a>Testowanie dokładności

**P: Czy można przeprowadzić testowanie w trybie offline niestandardowego modelu akustycznego za pomocą niestandardowego modelu języka?**

Odp **.: tak**, po prostu wybierz niestandardowy model języka z menu rozwijanego podczas konfigurowania testu w trybie offline.

**P: Czy można przeprowadzić testowanie w trybie offline mojego niestandardowego modelu języka przy użyciu niestandardowego modelu akustycznego?**

Odp **.: tak**, po prostu wybierz niestandardowy model akustyczny z menu rozwijanego podczas konfigurowania testu w trybie offline.

**P: co to jest współczynnik błędów wyrazów (WER) i jak jest obliczana?**

Odp **.: funkcja**raportowanie błędów systemu Windows to metryka oceny dla funkcji rozpoznawania mowy. Raportowanie błędów systemu Windows jest zliczane jako łączna liczba błędów, które obejmują wstawienia, usunięcia i podstawienia, podzieloną przez łączną liczbę słów w transkrypcji odwołania. Aby uzyskać więcej informacji, zobacz temat [częstotliwość błędów w programie Word](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Jak mogę określić czy wyniki testu dokładności są dobre?**

Odp **.: wyniki**zawierają porównanie między modelem linii bazowej i dostosowanym modelem. Należy dążyć do naniesienia poziomu modelu linii bazowej, aby wartościowa dostosowania.

**P: Jak mogę ustalić wartości błędów dla modelu podstawowego, aby zobaczyć, czy nastąpiła poprawa?**

Odp **.: wyniki**testu w trybie offline pokazują dokładność linii bazowej modelu niestandardowego i ulepszenie względem linii bazowej.

## <a name="creating-a-language-model"></a>Tworzenie modelu języka

**P: ile danych tekstowych jest potrzebnych do przekazania?**

Odp **.: to**zależy od tego, jak różne słownictwo i frazy używane w aplikacji pochodzą z modeli języka początkowego. W przypadku wszystkich nowych słów warto podać dowolną liczbę przykładów użycia tych słów. W przypadku typowych fraz, które są używane w aplikacji, w tym również zwroty w danych języka, są przydatne, ponieważ instruuje system, aby nasłuchiwać tych warunków. Często istnieje co najmniej 100 i zwykle kilka wyrażenia długości w zestawie danych języka. Ponadto, jeśli oczekuje się, że niektóre typy zapytań będą bardziej typowe niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**P: Czy mogę po prostu przekazać listę słów?**

Odp **.:** przekazanie listy wyrazów spowoduje dodanie wyrazów do słownika, ale nie uczy się, w jaki sposób wyrazy są zwykle używane. Dostarczając pełne lub częściowe wyrażenia długości (zdania lub frazy, które użytkownicy mogą powiedzieć), model języka może poznać nowe wyrazy i sposób ich użycia. Niestandardowy model języka jest dobry nie tylko w przypadku dodawania nowych słów do systemu, ale również do dostosowywania prawdopodobieństwa znanych wyrazów dla aplikacji. Zapewnienie pełnej wyrażenia długości pomaga lepiej poznać system.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Model dzierżawców (Custom Speech z danymi pakietu Office 365)

**P: jakie informacje znajdują się w modelu dzierżawców i jak zostało utworzone?**

Odp **.:** Model dzierżawy jest tworzony przy użyciu [publicznych grup](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) wiadomości e-mail i dokumentów, które mogą być widoczne dla wszystkich użytkowników w organizacji.

**P: jakie środowiska mowy zostały ulepszone przez model dzierżawców?**

Odp **.:** Gdy model dzierżawy jest włączony, tworzony i publikowany, służy do ulepszania rozpoznawania dla dowolnych aplikacji przedsiębiorstwa utworzonych przy użyciu usługi Speech. to również przekazanie tokenu usługi AAD użytkownika wskazującego na członkostwo w przedsiębiorstwie.

Funkcje mowy wbudowane w pakiet Office 365, takie jak dyktowanie i podpisy programu PowerPoint, nie ulegają zmianie podczas tworzenia modelu dzierżawców dla aplikacji usługi Speech.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)
