---
title: Usługa Azure Service Bus — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Service Bus.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: 312d92994e4932e854ec77681da2d4dbd14da2aa
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392005"
---
# <a name="service-bus-faq"></a>Service Bus — często zadawane pytania

W tym artykule omówiono niektóre często zadawane pytania dotyczące usługi Microsoft Azure Service Bus. Możesz również odwiedzić [często zadawane pytania dotyczące pomocy technicznej Azure](https://azure.microsoft.com/en-us/support/faq/) ogólne informacje dotyczące platformy Azure ceny i pomocy technicznej.

## <a name="general-questions-about-azure-service-bus"></a>Ogólne pytania dotyczące usługi Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?
[Usługa Azure Service Bus](service-bus-messaging-overview.md) asynchronicznej obsługi komunikatów platforma chmury, która umożliwia wysyłanie danych między systemami odłączony. Firma Microsoft oferuje tę funkcję jako usługa, co oznacza, że nie trzeba do obsługi każdego z własnych urządzeń, aby można było go używać.

### <a name="what-is-a-service-bus-namespace"></a>Co to jest przestrzeń nazw usługi Service Bus?
A [przestrzeni nazw](service-bus-create-namespace-portal.md) zapewnia kontener określania zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji. Tworzenie przestrzeni nazw jest niezbędne do korzystania z usługi Service Bus i jest jednym z pierwszych kroków w środowisku wprowadzenie.

### <a name="what-is-an-azure-service-bus-queue"></a>Co to jest kolejki usługi Azure Service Bus?
A [kolejki usługi Service Bus](service-bus-queues-topics-subscriptions.md) jest jednostką, w której są przechowywane komunikaty. Kolejki są przydatne w przypadku wielu aplikacji lub wieloma częściami aplikacji rozproszonej, które muszą komunikować się ze sobą. Kolejka jest podobne do Centrum dystrybucji, w tym wielu produktów (wiadomości) były odbierane i następnie wysyłane z tej lokalizacji.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co to są usługi Azure Service Bus tematów i subskrypcji?
Temat mogą być wizualizowane jako kolejka i korzystając z wieloma subskrypcjami, staje się bardziej rozbudowane modelu obsługi wiadomości; zasadniczo narzędzie komunikacji jeden do wielu. W tym modelu publikowania/subskrybowania (lub *publikowania/subskrybowania*) umożliwia aplikacji, która wysyła komunikat do tematu z wieloma subskrypcjami, aby ten komunikat odebrany przez wiele aplikacji.

### <a name="what-is-a-partitioned-entity"></a>Co to jest jednostki podzielonej na partycje?
Konwencjonalne kolejki lub tematu są obsługiwane przez brokera pojedynczy komunikat i przechowywane w jeden Magazyn obsługi komunikatów. Obsługiwane tylko w przypadku warstw podstawowa i standardowa, o których wiadomości w warstwach [podzieleniu kolejki lub tematu](service-bus-partitioning.md) jest obsługiwane przez kilku brokerami i przechowywane w wiele magazynów obsługi komunikatów. Tej funkcji oznacza, że ogólną przepływność podzieleniu kolejki lub tematu nie jest już ograniczone przez wydajności pojedynczego brokera lub magazynu komunikatów. Ponadto tymczasowe awarii magazynu komunikatów nie jest renderowana podzieleniu kolejki lub tematu niedostępny.

Kolejność nie jest zapewnione przy użyciu partycjonowane jednostki. W przypadku, gdy partycja jest niedostępny, nadal można wysyłać i odbierać komunikaty z innych partycji.

 Partycjonowane jednostki nie są już obsługiwane w [jednostki SKU Premium](service-bus-premium-messaging.md). 

## <a name="best-practices"></a>Najlepsze praktyki
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jakie są najlepsze rozwiązania usługi Azure Service Bus?
Zobacz [najlepsze rozwiązania zwiększające wydajność przy użyciu usługi Service Bus] [ Best practices for performance improvements using Service Bus] — w tym artykule opisano sposób optymalizacji wydajności podczas wymiany komunikatów.

### <a name="what-should-i-know-before-creating-entities"></a>Co należy wiedzieć przed utworzeniem jednostki?
Następujące właściwości kolejki i tematu są niezmienne. To ograniczenie podczas aprowizowania jednostek, wziąć pod uwagę te właściwości nie można zmodyfikować bez tworzenia nowego obiektu zastępczego.

* Partycjonowanie
* Sesje
* Wykrywanie duplikatów
* Jednostki ekspresowe

## <a name="pricing"></a>Cennik
W tej sekcji odpowiedzi na niektóre często zadawane pytania dotyczące cennika struktury usługi Service Bus.

[Usługi Service Bus, cen i rozliczeń](service-bus-pricing-billing.md) artykule wyjaśniono liczników rozliczeń w usłudze Service Bus. Aby uzyskać szczegółowe informacje o opcje cennika usługi Service Bus, zobacz [szczegóły cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Możesz również odwiedzić [— często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/en-us/support/faq/) for Azure ogólne informacje o cenach. 

### <a name="how-do-you-charge-for-service-bus"></a>Jak można opłaty za usługi Service Bus?
Aby uzyskać pełne informacje na temat cen usługi Service Bus, zobacz [szczegóły cennika usługi Service Bus][Pricing overview]. Oprócz ceny podane opłaty są naliczane w przypadku transferów danych skojarzone dla ruchu wychodzącego poza centrum danych, w którym zainicjowano aplikacji.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jakie użycia usługi Service Bus podlega transfer danych? Co to jest nie?
Każdy transfer danych w danym regionie platformy Azure znajduje się bez opłat, jak również wszelkie transfer danych przychodzących. Transfer danych poza obszarem podlega opłaty za ruch wychodzący, które można znaleźć [tutaj](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Czy usługi Service Bus opłaty za magazyn?
Nie, Usługa Service Bus nie opłaty za magazyn. Istnieje jednak limit przydziału i ograniczanie maksymalną ilość danych, który może być utrwalony na kolejki lub tematu. Zobacz następny często zadawane pytania.

## <a name="quotas"></a>Przydziały

Aby uzyskać listę usługi Service Bus, limity przydziału i ograniczenia, zobacz [Omówienie przydziałów usługi Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Usługa Service Bus ma przydziały użycia?
Domyślnie dla każdej chmury usługa Microsoft ustawia łączny miesięczny przydział użycia, który jest obliczany dla wszystkich subskrypcji klientów. Jeśli potrzebujesz więcej niż te limity, może skontaktuj się z działem obsługi klienta, w dowolnym momencie, zrozumienie potrzeb i odpowiednio dostosować te limity. W przypadku usługi Service Bus przydziału użycia agregacji jest 5 miliardów komunikatów na miesiąc.

Firma Microsoft zastrzega sobie prawo wyłączenia konta klienta, która przekroczyła swoje limity przydziału użycia w danym miesiącu, są wysyłane wiadomości e-mail z powiadomieniami i wielu prób do kontaktowania się z klientem, przed podjęciem działania. Przekroczenia tych limitów przydziału odpowiadają klienci nadal opłaty, które przekraczają limitów przydziału.

Podobnie jak w przypadku innych usług na platformie Azure, Usługa Service Bus wymusza zestaw określonych przydziałów, aby upewnić się, że istnieje uczciwe wykorzystanie zasobów. Można znaleźć więcej szczegółów na temat tych limitów przydziału w [Omówienie przydziałów usługi Service Bus][Quotas overview].

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Co to są wyjątki generowane przez interfejsów API usługi Azure Service Bus i ich sugerowane rozwiązania?
Aby uzyskać listę możliwych wyjątków usługi Service Bus, zobacz [Przegląd wyjątki][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co to jest sygnatura dostępu współdzielonego i języki obsługiwane przez Generowanie sygnatury?
Udostępnione sygnatur dostępu to mechanizm uwierzytelniania, w oparciu o SHA-256 skróty bezpieczne lub identyfikatory URI. Aby uzyskać informacje dotyczące generowania własnych podpisów w Node, PHP, Java i C\#, zobacz [sygnatur dostępu współdzielonego] [ Shared Access Signatures] artykułu.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i przestrzeni nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrować przestrzeni nazw do innej subskrypcji platformy Azure?

Można przenieść przestrzeni nazw z jedną subskrypcją platformy Azure do innego, za pomocą [witryny Azure portal](https://portal.azure.com) lub poleceń programu PowerShell. Aby można było wykonać operację, przestrzeń nazw musi już być aktywne. Użytkownik, wykonując polecenia musi być administratorem subskrypcji źródłowej i docelowej.

#### <a name="portal"></a>Portal

Aby przeprowadzić migrację przestrzeni nazw usługi Service Bus do innej subskrypcji, należy użyć witryny Azure portal, wykonaj czynności podane [tutaj](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Następująca sekwencja poleceń programu PowerShell przenosi przestrzeni nazw z jedną subskrypcją platformy Azure do innego. Aby wykonać tę operację, przestrzeń nazw już musi być aktywne, a użytkownik uruchamiający poleceń programu PowerShell musi być administratorem subskrypcji źródłowej i docelowej.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Service Bus, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Service Bus w warstwie Premium (wpis na blogu)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Wprowadzenie do usługi Azure Service Bus w warstwie Premium (— Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Omówienie usługi Service Bus](service-bus-messaging-overview.md)
* [Omówienie architektury usługi Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
