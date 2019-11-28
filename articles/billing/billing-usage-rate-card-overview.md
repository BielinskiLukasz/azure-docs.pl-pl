---
title: Pobieranie użycia platformy Azure za pomocą interfejsów API rozliczeń platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat interfejsów API usługi RateCard i użycia rozliczeń platformy Azure, które służą do udostępniania szczegółowych informacji o wykorzystaniu zasobów platformy Azure i trendach.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a8e189e8070de0fccf9428e26337e1091ba7f7b4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225629"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Wykorzystanie interfejsów API rozliczeń platformy Azure do uzyskiwania szczegółowych informacji o użyciu platformy Azure
Użyj interfejsów API rozliczeń platformy Azure, aby pobrać dane dotyczących użycia i zasobów do preferowanych narzędzi do analizy danych. Interfejsy API usługi RateCard i użycia zasobów platformy Azure mogą ułatwić dokładne przewidywanie kosztów i zarządzanie nimi. Interfejsy API są implementowane jako dostawca zasobów i należą do rodziny interfejsów API uwidacznianych przez usługę Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>Interfejs API pobierania faktur platformy Azure (wersja zapoznawcza)
Po [wyrażeniu zgody na uczestnictwo](billing-manage-access.md#opt-in) pobieraj faktury, korzystając z wersji zapoznawczej [interfejsu API faktur](/rest/api/billing). Dostępne funkcje to:

* **Kontrola dostępu oparta na rolach na platformie Azure** — konfiguruj zasady dostępu w witrynie [Azure Portal](https://portal.azure.com) lub przy użyciu [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview), aby określić, którzy użytkownicy lub które aplikacje mogą mieć dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
* **Filtrowanie dat** — użyj parametru `$filter`, aby pobrać wszystkie faktury w odwrotnym porządku chronologicznym według daty końcowej okresu fakturowania.

> [!NOTE]
> Ta funkcja jest w pierwszej wersji zapoznawczej i może podlegać zmianom, które będą niezgodne z poprzednimi wersjami. Obecnie nie jest ona dostępna w przypadku niektórych ofert subskrypcji (umowy EA, CSP i AIO nie są obsługiwane) i platformy Azure (Niemcy).

## <a name="azure-resource-usage-api-preview"></a>Interfejs API użycia zasobów platformy Azure (wersja zapoznawcza)
Użyj [interfejsu API użycia zasobów ](/previous-versions/azure/reference/mt219003(v=azure.100)) platformy Azure, aby uzyskać szacowane dane użycia platformy Azure. Ten interfejs API zawiera następujące składniki:

* **Kontrola dostępu oparta na rolach na platformie Azure** — konfiguruj zasady dostępu w witrynie [Azure Portal](https://portal.azure.com) lub przy użyciu [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview), aby określić, którzy użytkownicy lub które aplikacje mogą mieć dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
* **Agregacje godzinowe i dzienne** — obiekty wywołujące mogą określać, czy dane dotyczące użycia platformy Azure powinny być w przedziałach godzinowych, czy dziennych. Ustawieniem domyślnym są przedziały dzienne.
* **Metadane wystąpienia (w tym tagi zasobów)** — uzyskaj szczegóły na poziomie wystąpienia, na przykład w pełni kwalifikowany identyfikator URI zasobu (/subscriptions/{subscription-id}/..), informacje o grupie zasobów i tagi zasobów. Te metadane pomagają jednoznacznie i programowo przydzielać użycie według tagów w takich przypadkach użycia jak opłaty krzyżowe.
* **Metadane zasobu** — szczegóły zasobu, takie jak nazwa miernika, kategoria miernika, podkategoria miernika, jednostka i region, umożliwiają obiektowi wywołującemu lepszy wgląd w to, co zostało zużyte. Pracujemy również nad dopasowaniem terminologii dotyczącej metadanych zasobów w witrynie Azure Portal, pliku CSV użycia platformy Azure, pliku CSV rozliczeń umów EA i innych elementach publicznych, aby umożliwić skorelowanie danych między tymi elementami.
* **Użycie dla różnych typów ofert** — dane użycia są dostępne dla typów ofert, takich jak płatność zgodnie z rzeczywistym użyciem, MSDN, zobowiązanie pieniężne, oferta kredytowo-finansowa i umowa EA, z wyjątkiem [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Interfejs API usługi RateCard zasobów platformy Azure (wersja zapoznawcza)
Użyj [interfejsu API usługi RateCard zasobów platformy Azure](/previous-versions/azure/reference/mt219005(v=azure.100)), aby uzyskać listę dostępnych zasobów platformy Azure oraz informacje o szacowanych cenach dla każdego z nich. Ten interfejs API zawiera następujące składniki:

* **Kontrola dostępu oparta na rolach na platformie Azure** — konfiguruj zasady dostępu w witrynie [Azure Portal](https://portal.azure.com) lub przy użyciu [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview), aby określić, którzy użytkownicy lub które aplikacje mogą mieć dostęp do danych usługi RateCard. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla konkretnej subskrypcji platformy Azure.
* **Obsługa płatności zgodnie z rzeczywistym użyciem, MSDN, zobowiązania pieniężnego i ofert kredytowo-finansowych (umowy EA i [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nie są obsługiwane)** — ten interfejs API zapewnia informacje o stawkach na poziomie ofert platformy Azure.  Obiekt wywołujący ten interfejs API musi przekazać informacje o ofercie w celu uzyskania stawek i szczegółów zasobów. Obecnie nie jesteśmy w stanie udostępniać stawek dla umów EA, ponieważ oferty EA mają niestandardowe stawki obowiązujące dla konkretnych rejestracji.

## <a name="scenarios"></a>Scenariusze
Poniżej przedstawiono niektóre scenariusze, które są możliwe za pomocą kombinacji interfejsów API użycia i usługi RateCard:

* **Wydatki na platformę Azure w miesiącu** — użyj kombinacji interfejsów API użycia i usługi RateCard, aby uzyskać bardziej szczegółowe informacje o wydatkach na chmurę w danym miesiącu. Można analizować godzinowe i dzienne przedziały użycia oraz oszacowania opłat.
* **Konfigurowanie alertów** — użyj interfejsów API użycia i usługi RateCard, aby uzyskać szacowane wykorzystanie chmury i opłaty oraz skonfigurować alerty oparte na zasobach lub środkach pieniężnych.
* **Przewidywanie rachunku** — uzyskaj szacowane wykorzystanie i wydatki na chmurę, a następnie zastosuj algorytmy uczenia maszynowego, aby przewidzieć, jak będzie wyglądać rachunek na koniec cyklu rozliczeniowego.
* **Analiza kosztów przed wykorzystaniem** — użyj interfejsu API usługi RateCard, aby przewidzieć wysokość rachunku za oczekiwane użycie po przeniesieniu obciążeń na platformę Azure. W przypadku istniejących obciążeń w innych chmurach lub chmurach prywatnych można również zamapować użycie na stawki platformy Azure, aby otrzymać lepsze oszacowanie wydatków na platformę Azure. To oszacowanie pozwala analizować oferty, a także porównywać i zestawiać ze sobą inne typy ofert niż płatność zgodnie z rzeczywistym użyciem, jak na przykład zobowiązanie pieniężne i oferta kredytowo-finansowa. Interfejs API umożliwia również wyświetlanie różnic kosztowych według regionów i pozwala wykonać analizę warunkową kosztów w celu ułatwienia podejmowania decyzji dotyczących wdrażania.
* **Analiza warunkowa** -

  * Możesz określić, czy tańsze jest uruchamianie obciążeń w innym regionie, czy też przy użyciu innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure mogą się różnić w zależności od regionu platformy Azure, z którego korzystasz.
  * Możesz również określić, czy inny typ oferty platformy Azure oferuje lepszą stawkę za zasób platformy Azure.


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z przykładami kodu w usłudze GitHub:
  * [Przykład kodu dla interfejsu API faktur](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Przykład kodu dla interfejsu API użycia](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Przykład kodu dla interfejsu API usługi RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
