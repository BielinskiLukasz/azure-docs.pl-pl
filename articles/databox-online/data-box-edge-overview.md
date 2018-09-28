---
title: Microsoft Azure Data Box Edge — omówienie | Microsoft Docs
description: W tym artykule opisano Azure Data Box Edge — rozwiązanie magazynu, które za pomocą urządzenia fizycznego realizuje oparty na sieci transfer na platformę Azure.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: b64489912725872db971c19965ac58c84cb2fc2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993531"
---
# <a name="what-is-azure-data-box-edge-preview"></a>Czym jest rozwiązanie Azure Data Box Edge (w wersji zapoznawczej)? 

Azure Data Box Edge to rozwiązanie magazynu umożliwiające przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure. Ten artykuł zawiera omówienie rozwiązania Data Box Edge, jego zalety, najważniejsze funkcje oraz scenariusze, w których można wdrożyć to urządzenie. 

Rozwiązanie Data Box Edge przyspiesza bezpieczny transfer danych, używając urządzenia fizycznego dostarczanego przez firmę Microsoft. Urządzenie fizyczne znajduje się w środowisku lokalnym, a dane są na nim zapisywane przy użyciu protokołów NFS i SMB. 

Rozwiązanie Data Box Edge ma wszystkie funkcje bramy usługi Data Box Gateway. Rozwiązanie Data Box jest dodatkowo wyposażone w funkcje obliczeń brzegowych z obsługą sztucznej inteligencji, które ułatwiają analizowanie, przetwarzanie i filtrowanie danych podczas ich przenoszenia do blokowego obiektu blob na platformie Azure, stronicowego obiektu blob lub usługi Azure Files.  

> [!IMPORTANT]
> - Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Aby korzystać z tej usługi, [utwórz konto](#sign-up).
> - Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami użytkowania dotyczącymi wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="use-cases"></a>Przypadki zastosowań

Azure Data Box Edge to brzegowe urządzenie do przetwarzania danych z włączoną sztuczną inteligencją i funkcjami transferu danych sieciowych. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Edge do transferu danych.

- **Wstępne przetwarzanie danych** — analizuj dane ze środowisk lokalnych lub urządzeń IoT, aby szybko uzyskać wynik, pozostając w pobliżu miejsca generowania danych. Rozwiązanie Data Box Edge przenosi pełen zestaw danych do chmury, aby przeprowadzić bardziej zaawansowane przetwarzanie lub głębszą analizę.  Wstępne przetwarzanie umożliwia: 

    - Agregowanie danych.
    - Modyfikowanie danych, na przykład w celu usunięcia danych osobowych.
    - Dzielenie na podzestawy i transferowanie danych potrzebnych do głębszej analizy w chmurze.
    - Analizowanie zdarzeń IoT i reagowanie na nie. 

- **Usługa Azure Machine Learning z wnioskowaniem** — rozwiązanie Data Box Edge umożliwia uruchamianie modeli uczenia maszynowego (Machine Learning, ML) w celu szybkiego uzyskania wyników, na których można pracować przed wysłaniem danych do chmury. Transferowany jest pełen zestaw danych umożliwiający ciągłe trenowanie i ulepszanie modeli ML.

- **Transfer danych za pośrednictwem sieci na platformę Azure** — za pomocą rozwiązania Data Box Edge można szybko i łatwo transferować dane na platformę Azure, aby umożliwić ich dalsze przetwarzanie i analizę lub archiwizację. 

## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Edge oferuje następujące korzyści:

- **Łatwy transfer danych** — sprawia, że przenoszenie danych do i z usługi Azure Storage jest tak proste jak za pomocą lokalnego udziału sieciowego.  
- **Wysoka wydajność** — umożliwia transfery o wysokiej wydajności do i z platformy Azure. 
- **Szybki dostęp** — buforuje najnowsze pliki w celu zapewnienia szybkiego dostępu do plików lokalnych.  
- **Ograniczone użycie przepustowości** — dane mogą zostać zapisane na platformie Azure nawet przy ograniczonych możliwościach użycia sieci w czasie godzin szczytu.  
- **Przekształcanie danych** — umożliwia analizę, przetwarzanie i filtrowanie danych podczas ich przenoszenia na platformę Azure.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Rozwiązanie Data Box Edge oferuje następujące możliwości:

|Możliwości |Opis  |
|---------|---------|
|Wysoka wydajność     | W pełni zautomatyzowany i w wysokim stopniu zoptymalizowany transfer danych oraz wysoka przepustowość.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zapoznaj się z [wymaganiami systemowymi rozwiązania Data Box Edge](http://aka.ms/dbe-docs).|
|Obliczanie       |Umożliwia analizowanie, przetwarzanie i filtrowanie danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze.|
|Szybki dostęp     | Lokalna pamięć podręczna na urządzeniu zapewniająca szybki dostęp do ostatnio używanych plików.|
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*.       |
|Odporność     | Wbudowana odporność sieci.        |


## <a name="features-and-specifications"></a>Funkcje i specyfikacje

Urządzenie fizyczne Data Box Edge oferuje następujące funkcje:

| Funkcje/dane techniczne                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Wymiary   | Szerokość: 17,25 cala Głębokość: 27,25 cala Wysokość: 1,75 cala<br>(bez oczek i uchwytów zasilacza)  |            
| Miejsce w stojaku|1U po umieszczeniu w stojaku|
| Kable| 2 kable zasilające<br>2 kable RJ-45 1 GB/s<br>2 kable miedziane SFP+ 10 GB/s|
| Składniki|2 wbudowane zasilacze|
| Procesor CPU|2 10-rdzeniowe procesory Intel Xeon  |
| Memory (Pamięć)| 64 GB pamięci RAM|
| Dyski| 8 dysków SSD NVMe, każdy dysk o pojemności 1,6 TB <br> Awaria jednego dysku SSD NVMe powoduje awarię systemu. |
| Pojemność magazynu lokalnego| Całkowita pojemność 12,8 TB|
| Interfejsy sieciowe| 2 interfejsy 1 GbE — 1 do zarządzania, bez możliwości konfigurowania przez użytkownika, używany do konfiguracji początkowej. Drugi interfejs danych jest konfigurowalny przez użytkownika i domyślnie korzysta z protokołu DHCP. <br>2 interfejsy 25 GbE — mogą również działać jako interfejsy 10 GbE. Te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne. <br> 2 interfejsy 25 GbE — te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne.|

## <a name="components"></a>Składniki

Rozwiązanie Data Box Edge składa się z zasobu rozwiązania Data Box Edge, urządzenia fizycznego Data Box Edge i lokalnego internetowego interfejsu użytkownika.

* **Urządzenie fizyczne Data Box Edge** — dostarczany przez firmę Microsoft, montowany w stojaku serwer 1U, który można skonfigurować na potrzeby wysyłania danych na platformę Azure. 
    
* **Zasób rozwiązania Data Box Edge** — zasób w witrynie Azure Portal, który pozwala na zarządzanie urządzeniem Data Box Edge za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Korzystając z zasobu rozwiązania Data Box Edge, można tworzyć zasoby oraz zarządzać nimi, wyświetlać urządzenia i alerty oraz zarządzać nimi, a także zarządzać udziałami.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Aby uzyskać więcej informacji, przejdź do tematu [Administrowanie urządzeniem Data Box Edge za pomocą usługi Data Box Edge](http://aka.ms/dbe-docs).

* **Lokalny internetowy interfejs użytkownika** — korzystając z lokalnego internetowego interfejsu użytkownika, można uruchamiać diagnostykę, wyłączać i uruchamiać ponownie urządzenie Data Box Edge, wyświetlać dzienniki kopiowania oraz kontaktować się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje na temat korzystania z internetowego interfejsu użytkownika, przejdź do tematu [Administrowanie rozwiązaniem Data Box za pomocą internetowego interfejsu użytkownika](http://aka.ms/dbe-docs).


## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne Data Box Edge, zasób platformy Azure i docelowe konto magazynu, do którego są transferowane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobu** — w tej wersji zasób rozwiązania Data Box Edge jest dostępny w następujących regionach:
    - **Stany Zjednoczone** — Zachodnie stany USA 2 i Wschodnie stany USA
    - **Unia Europejska** — Europa Zachodnia
    - **Azja i Pacyfik** — Azja Południowo-Wschodnia

- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. 

    Regiony, w których znajdują się konta magazynu zawierające dane przesłane za pomocą rozwiązania Data Box, powinny mieścić się blisko lokalizacji urządzenia, aby zapewnić optymalną wydajność. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność. 


## <a name="sign-up"></a>Rejestrowanie

Rozwiązanie Data Box Edge jest dostępne w wersji zapoznawczej i musisz utworzyć jego konto. Wykonaj poniższe kroki, aby utworzyć konto usługi Data Box Gateway:

1. Zaloguj się do witryny Azure Portal pod adresem [https://aka.ms/databox-edge](https://aka.ms/databox-edge).

2. Wybierz subskrypcję, w której chcesz używać rozwiązania Data Box Edge w wersji zapoznawczej. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Edge. W opcji rozwiązania Data Box Edge kliknij pozycję **Utwórz konto**.

    ![Tworzenie konta rozwiązania Data Box Edge 3](media/data-box-edge-overview/data-box-edge-sign-up3.png)

3.  Odpowiedz na pytania dotyczące kraju przechowywania danych, przedziału czasowego, docelowej usługi platformy Azure na potrzeby transferu danych, przepustowości sieci i częstotliwości transferu danych. Przejrzyj **zasady ochrony prywatności i warunki** i zaznacz pole wyboru obok pozycji **Firma Microsoft może używać Twojego adresu e-mail do kontaktu**.

    ![Tworzenie konta rozwiązania Data Box Edge 4](media/data-box-edge-overview/data-box-edge-sign-up4.png)

4. Po utworzeniu konta i włączeniu opcji korzystania z wersji zapoznawczej można zamówić usługę Data Box Edge.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe rozwiązania Data Box Edge](http://aka.ms/dbe-docs).
- Zapoznaj się z [limitami rozwiązania Data Box Edge](http://aka.ms/dbe-docs).
- Wdróż rozwiązanie [Azure Data Box Edge](http://aka.ms/dbe-docs) w witrynie Azure Portal.




