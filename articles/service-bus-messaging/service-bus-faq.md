---
title: Azure Service Bus często zadawanych pytań (FAQ) | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące Azure Service Bus.
ms.topic: article
ms.date: 07/15/2020
ms.openlocfilehash: 01d7869a158a3c2b5418f38f2a5d88fc161796c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083858"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus — często zadawane pytania

W tym artykule omówiono kilka często zadawanych pytań dotyczących Microsoft Azure Service Bus. Możesz również odwiedzić pytania dotyczące [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/) , aby uzyskać ogólne informacje o cenach i pomocy technicznej platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Ogólne pytania dotyczące Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) to asynchroniczna platforma do obsługi komunikatów w chmurze, która umożliwia wysyłanie danych między oddzielonymi systemami. Firma Microsoft oferuje tę funkcję jako usługę, co oznacza, że nie musisz hostować własnego sprzętu, aby go używać.

### <a name="what-is-a-service-bus-namespace"></a>Co to jest Service Bus przestrzeń nazw?
[Przestrzeń nazw](service-bus-create-namespace-portal.md) zapewnia kontener określania zakresu do adresowania zasobów Service Bus w aplikacji. Tworzenie przestrzeni nazw jest niezbędne do użycia Service Bus i jest jednym z pierwszych kroków w temacie Wprowadzenie.

### <a name="what-is-an-azure-service-bus-queue"></a>Co to jest kolejka Azure Service Bus?
[Kolejka Service Bus](service-bus-queues-topics-subscriptions.md) jest jednostką, w której są przechowywane komunikaty. Kolejki są przydatne w przypadku wielu aplikacji lub wielu części aplikacji rozproszonej, która musi komunikować się ze sobą. Kolejka jest podobna do centrum dystrybucji, w którym odbierane są wiele produktów (wiadomości), a następnie wysyłane z tej lokalizacji.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co to są tematy Azure Service Bus i subskrypcje?
Temat może być wizualny jako kolejka i w przypadku korzystania z wielu subskrypcji stanie się bardziej zaawansowanym modelem obsługi komunikatów. zasadniczo narzędzie do komunikacji typu "jeden do wielu". Ten model publikowania/subskrybowania (lub *pub/sub*) umożliwia aplikacji, która wysyła komunikat do tematu z wieloma subskrypcjami, aby ten komunikat został odebrany przez wiele aplikacji.

### <a name="what-is-a-partitioned-entity"></a>Co to jest jednostka partycjonowana?
Konwencjonalne kolejki lub tematy są obsługiwane przez jednego brokera komunikatów i przechowywane w jednym magazynie obsługi komunikatów. Obsługiwane tylko w warstwach Podstawowa i Standardowa obsługa komunikatów, [kolejki partycjonowane lub tematu](service-bus-partitioning.md) są obsługiwane przez wielu brokerów komunikatów i są przechowywane w wielu magazynach komunikatów. Ta funkcja oznacza, że ogólna przepływność partycjonowanej kolejki lub tematu nie jest już ograniczona przez wydajność jednego brokera komunikatów lub magazynu komunikatów. Ponadto tymczasowa awaria magazynu obsługi komunikatów nie powoduje niedostępności partycjonowanej kolejki lub tematu.

Określanie kolejności nie jest zapewnione w przypadku korzystania z partycjonowanych jednostek. W przypadku niedostępności partycji można nadal wysyłać i odbierać komunikaty z innych partycji.

 Partycjonowane jednostki nie są już obsługiwane w [jednostce SKU Premium](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jakie porty muszę otworzyć na zaporze? 
Za pomocą następujących protokołów można Azure Service Bus wysyłać i odbierać komunikaty:

- Zaawansowane usługi kolejkowania Protocol (AMQP)
- Service Bus Messaging Protocol (SBMP)
- HTTP

Zapoznaj się z poniższą tabelą dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikowania się z usługą Azure Event Hubs. 

| Protokół | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [Przewodnik po protokole AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 do 9354 | Zobacz [tryb łączności](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Jakie adresy IP muszę dodać do listy dozwolonych?
Aby znaleźć odpowiednie adresy IP do dodania do listy dozwolonych połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.cloudapp.net
    ```
2. Zanotuj adres IP zwrócony w `Non-authoritative answer` . Ten adres IP jest statyczny. Jedyną zmianą jest to, że w przypadku przywracania przestrzeni nazw w innym klastrze.

Jeśli używasz nadmiarowości strefy dla przestrzeni nazw, musisz wykonać kilka dodatkowych czynności: 

1. Najpierw uruchom polecenie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.cloudapp.net
    ```
2. Zanotuj nazwę w sekcji **Nieautorytatywna odpowiedź** , która znajduje się w jednym z następujących formatów: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Uruchom polecenie nslookup dla każdego z sufiksów S1, S2 i S3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności, 

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Gdzie mogę znaleźć adres IP klienta wysyłającego/otrzymującego komunikaty do/z przestrzeni nazw? 
Nie rejestrujemy adresów IP klientów wysyłających lub otrzymujących komunikaty do/z przestrzeni nazw. Wygeneruj ponownie klucze, aby uniemożliwić wszystkim istniejącym klientom uwierzytelnianie i przeglądanie ustawień kontroli dostępu opartej na rolach ([RBAC](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)), aby upewnić się, że tylko użytkownicy lub aplikacje mają dostęp do przestrzeni nazw. 

Jeśli używasz przestrzeni nazw **Premium** , użyj funkcji [filtrowania adresów IP](service-bus-ip-filtering.md), [punktów końcowych usługi sieci wirtualnej](service-bus-service-endpoints.md)i [prywatnych punktów końcowych](private-link-service.md) , aby ograniczyć dostęp do przestrzeni nazw. 

## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jakie są niektóre Azure Service Bus najlepszych praktykach?
Zobacz [najlepsze rozwiązania dotyczące ulepszeń wydajności przy użyciu Service Bus][Best practices for performance improvements using Service Bus] — w tym artykule opisano sposób optymalizowania wydajności podczas wymiany komunikatów.

### <a name="what-should-i-know-before-creating-entities"></a>Co muszę wiedzieć przed utworzeniem jednostek?
Następujące właściwości kolejki i tematu są niezmienne. To ograniczenie należy wziąć pod uwagę podczas aprowizacji jednostek, ponieważ nie można modyfikować tych właściwości bez tworzenia nowej jednostki zastępczej.

* Partycjonowanie
* Sesje
* Wykrywanie duplikatów
* Jednostka Express

## <a name="pricing"></a>Cennik
Ta sekcja zawiera odpowiedzi na kilka często zadawanych pytań dotyczących struktury cenowej Service Bus.

W artykule dotyczącym [cen i rozliczeń Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) opisano liczniki rozliczeń w programie Service Bus. Aby uzyskać szczegółowe informacje na temat opcji cennika Service Bus, zobacz [Service Bus szczegóły cennika](https://azure.microsoft.com/pricing/details/service-bus/).

Możesz również odwiedzić pytania dotyczące [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/) , aby uzyskać ogólne informacje o cenach platformy Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Jak naliczane są opłaty za Service Bus?
Aby uzyskać pełne informacje na temat cennika Service Bus, zobacz [Service Bus szczegóły cennika][Pricing overview]. Oprócz zanotowanych cen opłaty są naliczane za powiązane transfery danych dla ruchu wychodzącego poza centrum danych, w którym Zainicjowano obsługę aplikacji.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Jakie użycie Service Bus podlega transferowi danych? Co to nie?
Wszystkie transfery danych w danym regionie świadczenia usługi Azure są udostępniane bezpłatnie, a także w przypadku transferu danych przychodzących. Transfer danych poza region podlega opłatom za ruch wychodzący, które można znaleźć [tutaj](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Czy Service Bus opłaty za magazyn?
Nie. Service Bus nie nalicza opłat za magazyn. Jednak limit przydziału ogranicza maksymalną ilość danych, które mogą być utrwalane dla kolejki lub tematu. Zobacz następne często zadawane pytania.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Mam Service Bus przestrzeni nazw w warstwie Standardowa. Dlaczego widzę opłaty w ramach grupy zasobów "$system"?
Azure Service Bus ostatnio uaktualnione składniki rozliczeń. Ze względu na tę zmianę, jeśli masz Service Bus przestrzeni nazw w warstwie Standardowa, możesz zobaczyć elementy wierszy dla zasobu "/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" w grupie zasobów "$system".

Opłaty te przedstawiają podstawową opłatę za subskrypcję platformy Azure, która udostępnia Service Bus standardową przestrzeń nazw. 

Należy pamiętać, że opłaty te nie są nowe, czyli istniejące w poprzednim modelu rozliczeń. Jedyną zmianą jest to, że są one teraz wymienione w obszarze "$system". Jest to spowodowane ograniczeniami w nowym systemie rozliczeniowym, które grupuje opłaty na poziomie subskrypcji, które nie są powiązane z określonym zasobem w ramach identyfikatora zasobu "$system".

## <a name="quotas"></a>Limity przydziału

Aby uzyskać listę limitów Service Bus i przydziałów, zobacz [Omówienie przydziałów Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Jak obsługiwać komunikaty o rozmiarze > 1 MB?
Usługi Service Bus Messaging (kolejki i tematy/subskrypcje) umożliwiają aplikacji wysyłanie komunikatów o rozmiarze do 256 KB (warstwa standardowa) lub 1 MB (warstwa Premium). Jeśli zamierzasz korzystać z komunikatów o rozmiarze większym niż 1 MB, użyj wzorca sprawdzania roszczeń opisanego w [tym wpisie w blogu](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć przestrzeni nazw po usunięciu jej z innej subskrypcji? 
Po usunięciu przestrzeni nazw z subskrypcji poczekaj 4 godziny, a następnie utwórz ją z tą samą nazwą w innej subskrypcji. W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jakie są wyjątki generowane przez interfejsy API Azure Service Bus i ich sugerowane akcje?
Listę możliwych wyjątków Service Bus można znaleźć w temacie [wyjątki — Omówienie][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co to jest sygnatura dostępu współdzielonego i które Języki obsługują generowanie podpisu?
Sygnatury dostępu współdzielonego są mechanizmem uwierzytelniania opartym na bezpiecznych skrótach i identyfikatorach URI SHA-256. Aby uzyskać informacje o sposobach generowania własnych podpisów w Node.js, PHP, Java, Python i C#, zobacz artykuł [sygnatury dostępu współdzielonego][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcjami i przestrzenią nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak mogę zmigrować przestrzeni nazw do innej subskrypcji platformy Azure?

Możesz przenieść przestrzeń nazw z jednej subskrypcji platformy Azure do innej przy użyciu poleceń [Azure Portal](https://portal.azure.com) lub PowerShell. Aby wykonać operację, przestrzeń nazw musi już być aktywna. Użytkownik wykonujący polecenia musi być administratorem zarówno dla subskrypcji źródłowej, jak i docelowej.

#### <a name="portal"></a>Portal

Aby użyć Azure Portal do migracji przestrzeni nazw Service Bus do innej subskrypcji, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Poniższa sekwencja poleceń programu PowerShell przenosi przestrzeń nazw z jednej subskrypcji platformy Azure do innej. Aby wykonać tę operację, przestrzeń nazw musi być już aktywna, a użytkownik uruchamiający polecenia programu PowerShell musi być administratorem zarówno dla subskrypcji źródłowej, jak i docelowej.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Service Bus, zobacz następujące artykuły:

* [Wprowadzenie do Azure Service Bus Premium (wpis w blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Wprowadzenie do Azure Service Bus Premium (channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Przegląd Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
