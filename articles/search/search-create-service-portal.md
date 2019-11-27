---
title: 'Szybki Start: Tworzenie usługi wyszukiwania w portalu'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować zasób platformy Azure Wyszukiwanie poznawcze w Azure Portal. Wybierz grupy zasobów, regiony i jednostki SKU lub warstwę cenową.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: ec794f66866f9773f8b7c0fc57b644d8a13d697a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534567"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Szybki Start: Tworzenie usługi Azure Wyszukiwanie poznawcze w portalu

Usługa Azure Wyszukiwanie poznawcze to autonomiczny zasób służący do podłączenia środowiska wyszukiwania w aplikacjach niestandardowych. Mimo że platforma Azure Wyszukiwanie poznawcze łatwo integruje się z innymi usługami platformy Azure, można także użyć jej jako składnika autonomicznego lub zintegrować ją z aplikacjami na serwerach sieciowych lub z oprogramowaniem działającym na innych platformach w chmurze.

W tym artykule dowiesz się, jak utworzyć zasób w [Azure Portal](https://portal.azure.com/).

[![Animowany obraz GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Preferujesz program PowerShell? Użyj [szablonu usługi](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Aby uzyskać pomoc dotyczącą rozpoczynania pracy, zobacz [Zarządzanie usługą Azure wyszukiwanie poznawcze przy użyciu programu PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subskrypcja (bezpłatna lub płatna)

[Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) i skorzystaj z bezpłatnych środków, aby wypróbować płatne usługi platformy Azure. Po wyczerpaniu środków zachowaj konto i nadal korzystaj z bezpłatnych usług platformy Azure, takich jak Websites. Karta kredytowa nie zostanie obciążona, chyba że jawnie zmienisz ustawienia i poprosisz o jej obciążenie.

Możesz również [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). W ramach subskrypcji MSDN co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure. 

## <a name="find-azure-cognitive-search"></a>Znajdź Wyszukiwanie poznawcze platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij znak plus („+ Utwórz zasób”) w lewym górnym rogu.
3. Użyj paska wyszukiwania, aby znaleźć "Wyszukiwanie poznawcze platformy Azure" lub przejdź do zasobu za pomocą Wyszukiwanie poznawcze **sieci Web** > **Azure**.

![Tworzenie zasobu w portalu](./media/search-create-service-portal/find-search3.png "Tworzenie zasobu w portalu")

## <a name="choose-a-subscription"></a>Wybierz subskrypcję

Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich dla usługi wyszukiwania.

## <a name="set-a-resource-group"></a>Ustawianie grupy zasobów

Grupa zasobów jest wymagana i jest przydatna do zarządzania zasobami, w tym do kosztów. Grupa zasobów może składać się z jednej usługi lub wielu używanych jednocześnie usług. Na przykład, jeśli używasz usługi Azure Wyszukiwanie poznawcze do indeksowania bazy danych Azure Cosmos DB, możesz udostępnić obie usługi w tej samej grupie zasobów na potrzeby zarządzania. 

Jeśli zasoby nie są łączone w pojedynczą grupę, a istniejące grupy zasobów są wypełniane zasobami używanymi w niepowiązanych rozwiązaniach, Utwórz nową grupę zasobów tylko dla zasobu usługi Azure Wyszukiwanie poznawcze. 

![Utwórz nową grupę zasobów](./media/search-create-service-portal/new-resource-group.png "Utworzenie nowej grupy zasobów")

Wraz z upływem czasu można śledzić bieżące i przewidywane koszty wszystkie (jak pokazano na zrzucie ekranu) lub przewijać w dół, aby wyświetlić opłaty dla poszczególnych zasobów. Poniższy zrzut ekranu przedstawia rodzaj informacji o kosztach, które mogą być widoczne w przypadku łączenia wielu zasobów w jedną grupę.

![Zarządzanie kosztami na poziomie grupy zasobów](./media/search-create-service-portal/resource-group-cost-management.png "Zarządzanie kosztami na poziomie grupy zasobów")

> [!TIP]
> Grupy zasobów upraszczają czyszczenie, ponieważ usunięcie grupy spowoduje również usunięcie w niej usług. Umieszczenie w tej samej grupie zasobów wszystkich projektów będących prototypami korzystającymi z wielu usług ułatwia proces czyszczenia po zakończeniu projektu.

## <a name="name-the-service"></a>Nazwij usługę

W obszarze Szczegóły wystąpienia Podaj nazwę usługi w polu **adres URL** . Nazwa jest częścią punktu końcowego adresu URL, względem którego są wydawane wywołania interfejsu API: `https://your-service-name.search.windows.net`. Na przykład jeśli chcesz, aby punkt końcowy znajdował się pod adresem `https://myservice.search.windows.net`, wpisz `myservice`.

Wymagania dotyczące nazwy usługi:

* Musi być unikatowa w obrębie przestrzeni nazw search.windows.net
* Długość musi zawierać się w przedziale od 2 do 60 znaków
* Musi składać się z małych liter, cyfr lub łączników („-”)
* Nie może zawierać łączników („-”) na pierwszych dwóch miejscach ani na ostatnim miejscu
* Nie może zawierać następujących po sobie łączników („--”)

> [!TIP]
> Jeśli uważasz, że będziesz korzystać z wielu usług, zalecamy uwzględnienie regionu (lub lokalizacji) w nazwie usługi jako konwencji nazewnictwa. Usługi w ramach tego samego regionu mogą bezpłatnie wymieniać dane, więc jeśli platforma Azure Wyszukiwanie poznawcze jest w regionie zachodnie stany USA, a inne usługi znajdują się również w regionie zachodnie stany USA, nazwy takie jak `mysearchservice-westus` mogą zapisywać dane na stronie właściwości podczas podejmowania decyzji o sposobie łączenia lub dołączania zasobów.

## <a name="choose-a-location"></a>Wybierz lokalizację

Usługa Azure Wyszukiwanie poznawcze może być hostowana w centrach danych na całym świecie. Listę obsługiwanych regionów można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/search/). 

Możesz zminimalizować lub uniknąć opłat za przepustowość, wybierając tę samą lokalizację dla wielu usług. Na przykład w przypadku indeksowania danych dostarczanych przez inną usługę platformy Azure (Azure Storage, Azure Cosmos DB, Azure SQL Database) Tworzenie usługi Azure Wyszukiwanie poznawcze w tym samym regionie pozwala uniknąć naliczania opłat za przepustowość (nie są naliczane opłaty za dane wychodzące, gdy usługi znajdują się w tym samym regionie).

Ponadto, jeśli używasz wzbogacania AI, Utwórz usługę w tym samym regionie co Cognitive Services. *Współpraca z platformą Azure wyszukiwanie poznawcze i Cognitive Services w tym samym regionie jest wymagana do wzbogacania AI*.

> [!Note]
> Indie Środkowe nie są obecnie dostępne dla nowych usług. W przypadku usług już znajdujących się w centralnym Indiach można skalować w górę bez ograniczeń, a usługa jest w pełni obsługiwana w tym regionie. Ograniczenie w tym regionie jest tymczasowe i ograniczone tylko do nowych usług. Ta uwaga zostanie usunięta, jeśli ograniczenie nie zostanie już zastosowane.

## <a name="choose-a-pricing-tier-sku"></a>Wybierz warstwę cenową (SKU)

[Usługa Azure wyszukiwanie poznawcze jest obecnie oferowana w wielu warstwach cenowych](https://azure.microsoft.com/pricing/details/search/): bezpłatna, podstawowa lub standardowa. Poszczególne warstwy różnią się między sobą [pojemnością i limitami](search-limits-quotas-capacity.md). Aby uzyskać wskazówki, zobacz [Choose a pricing tier or SKU](search-sku-tier.md) (Wybieranie warstwy cenowej lub jednostek SKU).

Podstawowa i Standardowa są najbardziej typowymi opcjami dotyczącymi obciążeń produkcyjnych, ale większość klientów zaczyna się od bezpłatnej usługi. Najważniejsze różnice między warstwami to rozmiar partycji i szybkość oraz limity dotyczące liczby obiektów, które można utworzyć.

Należy pamiętać, że nie można zmienić warstwy cenowej po utworzeniu usługi. W razie konieczności przejścia do warstwy wyższej lub niższej należy ponownie utworzyć usługę.

## <a name="create-your-service"></a>Tworzenie usługi

Po podaniu niezbędnych danych wejściowych przejdź dalej i Utwórz usługę. 

![Przeglądanie i tworzenie usługi](./media/search-create-service-portal/new-service3.png "Przeglądanie i tworzenie usługi")

Twoja usługa zostanie wdrożona w ciągu kilku minut, którą można monitorować za pomocą powiadomień platformy Azure. Rozważ możliwość przypięcia usługi do pulpitu nawigacyjnego w celu ułatwienia dostępu w przyszłości.

![Monitorowanie i Przypinanie usługi](./media/search-create-service-portal/monitor-notifications.png "Monitorowanie i Przypinanie usługi")

## <a name="get-a-key-and-url-endpoint"></a>Pobieranie klucza i punktu końcowego adresu URL

Jeśli nie korzystasz z portalu, dostęp programistyczny do nowej usługi wymaga podania punktu końcowego adresu URL oraz klucza API uwierzytelniania.

1. Na stronie **Przegląd** zlokalizuj i skopiuj punkt końcowy adresu URL po prawej stronie strony.

2. Na stronie **klucze** Skopiuj jeden z kluczy administratora (są one równoważne). Klucze interfejsu API administratora są wymagane do tworzenia, aktualizowania i usuwania obiektów w usłudze. Z kolei klucze zapytań zapewniają dostęp do odczytu do zawartości indeksu.

   ![Strona przeglądu usługi z punktem końcowym adresu URL](./media/search-create-service-portal/get-url-key.png "Punkt końcowy adresu URL i inne szczegóły usługi")

Klucz i punkt końcowy nie są wymagane do zadań wykonywanych w portalu. Portal jest już połączony z zasobem usługi Azure Wyszukiwanie poznawcze z uprawnieniami administratora. Przewodnik po samouczku można rozpocząć od [przewodnika Szybki Start: Tworzenie indeksu wyszukiwanie poznawcze platformy Azure w portalu](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skalowanie usługi

Po aprowizacji usługi można ją skalować stosownie do potrzeb. W przypadku wybrania warstwy Standardowa usługi Azure Wyszukiwanie poznawcze można skalować usługę w dwóch wymiarach: Replicas i partitions. Wybór warstwy podstawowej oznaczałby, że można dodawać wyłącznie repliki. W razie aprowizacji bezpłatnej usługi skalowanie nie jest dostępne.

***Partycje*** umożliwiają usłudze przechowywanie i przeszukiwanie większej liczby dokumentów.

***Repliki*** umożliwiają usłudze obsługę większego obciążenia zapytaniami wyszukiwania.

Dodawanie zasobów wiąże się z dodaniem opłat do rachunku miesięcznego. [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) pomaga w określeniu, jaki wpływ na rozliczenia będzie miało dodanie zasobów. Pamiętaj, że możesz też dostosowywać zasoby do obciążenia. Możesz na przykład zwiększyć ilość zasobów na czas początkowego tworzenia pełnego indeksu, a następnie zmniejszyć ją do poziomu dostosowanego do potrzeb indeksowania przyrostowego.

> [!Important]
> Usługa musi mieć [2 repliki w ramach umowy SLA tylko do odczytu oraz 3 repliki w ramach umowy SLA do odczytu/zapisu](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Przejdź do strony usługi wyszukiwania w witrynie Azure Portal.
2. W okienku nawigacji po lewej wybierz kolejno pozycje **Ustawienia** > **Skalowanie**.
3. Użyj suwaka, aby dodać zasoby wybranego typu.

![Dodaj pojemność](./media/search-create-service-portal/settings-scale.png "Dodawanie pojemności przy użyciu replik i partycji")

> [!Note]
> Magazyn na partycję i szybkość zwiększają się o wyższe warstwy. Aby uzyskać więcej informacji, zobacz [pojemność i limity](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Kiedy należy dodać drugą usługę

Większość klientów używa tylko jednej usługi aprowizowanej dla danej warstwy, która zapewnia [właściwe rozmieszczenie zasobów](search-sku-tier.md). Jedna usługa może obsługiwać wiele indeksów, z zastrzeżeniem [maksymalnych limitów wybranej warstwy](search-capacity-planning.md), przy czym każdy indeks jest odizolowany od innych. Na platformie Azure Wyszukiwanie poznawcze żądania mogą być kierowane tylko do jednego indeksu, co minimalizuje ryzyko przypadkowego lub zamierzonego pobrania danych z innych indeksów w tej samej usłudze.

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

* Odzyskiwanie po awarii (awaria centrum danych). Usługa Azure Wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku awarii. Aby uzyskać wskazówki i zalecenia, zobacz [Service administration](search-manage.md) (Administrowanie usługą).
* W trakcie badania modelowania wielodostępnego ustalono, że dodatkowe usługi stanowią optymalne rozwiązanie dla danego projektu. Aby uzyskać więcej informacji, zobacz [Design for multi-tenancy](search-modeling-multitenant-saas-applications.md) (Projektowanie na potrzeby wielodostępu).
* W przypadku aplikacji wdrażanych globalnie może zajść potrzeba wystąpienia platformy Azure Wyszukiwanie poznawcze w wielu regionach w celu zminimalizowania opóźnień ruchu międzynarodowego aplikacji.

> [!NOTE]
> Na platformie Azure Wyszukiwanie poznawcze nie można rozdzielić operacji indeksowania i wykonywania zapytań. w ten sposób nigdy nie utworzysz wielu usług dla rozdzielonych obciążeń. Zapytania względem indeksu zawsze dotyczą usługi, w której został utworzony (nie można utworzyć indeksu w jednej usłudze, by następnie skopiować go do innej).

Druga usługa nie jest wymagana w celu zapewnienia wysokiej dostępności. Wysoka dostępność zapytań ma miejsce wtedy, gdy używane są co najmniej 2 repliki w tej samej usłudze. Aktualizacje repliki są sekwencyjne, co oznacza, że co najmniej jeden z nich działa po przewróceniu aktualizacji usługi. Aby uzyskać więcej informacji na temat czasu [pracy, zobacz umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Następne kroki

Po zainicjowaniu obsługi administracyjnej można kontynuować w portalu, aby utworzyć swój pierwszy indeks.

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w portalu](search-get-started-portal.md)
