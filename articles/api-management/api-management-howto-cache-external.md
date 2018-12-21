---
title: Używanie zewnętrznej pamięci podręcznej w usłudze Azure API Management | Microsoft Docs
description: Dowiedz się, jak skonfigurować zewnętrzną pamięć podręczną i używać jej w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06052bd0cba6d119d07e86ed6aed833dec9f1f92
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014569"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Używanie zewnętrznej pamięci podręcznej Azure Cache for Redis w usłudze Azure API Management

Oprócz korzystania z wbudowanej pamięci podręcznej, usługa Azure API Management umożliwia także buforowanie odpowiedzi w zewnętrznej pamięci podręcznej Azure Cache for Redis.

Dzięki zewnętrznej pamięci podręcznej można obejść kilka ograniczeń wbudowanej pamięci podręcznej. Jest ona szczególnie przydatna, jeśli chcesz:

* uniknąć okresowego czyszczenia pamięci podręcznej podczas aktualizacji usługi API Management;
* mieć większą kontrolę nad konfiguracją pamięci podręcznej;
* buforować większe ilości danych, niż pozwala warstwa usługi API Management;
* używać buforowania z warstwą Zużycie usługi API Management.

Aby uzyskać bardziej szczegółowe informacje na temat buforowania, zobacz [API Management caching policies](api-management-caching-policies.md) (Zasady buforowania w usłudze API Management) i [Custom caching in Azure API Management](api-management-sample-cache-by-key.md) (Buforowanie niestandardowe w usłudze Azure API Management).

![Dodawanie własnej pamięci podręcznej do usługi APIM](media/api-management-howto-cache-external/overview.png)

Zawartość:

> [!div class="checklist"]
> * Dodawanie zewnętrznej pamięci podręcznej w usłudze API Management

## <a name="availability"></a>Dostępność

> [!NOTE]
> Ta funkcja jest obecnie dostępna tylko w warstwie **Zużycie** usługi Azure API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

+ [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
+ Zrozumienie [buforowania w usłudze Azure API Management](api-management-howto-cache.md)

## <a name="create-cache"> </a> Tworzenie pamięci podręcznej Azure Cache for Redis

W tej sekcji opisano, jak utworzyć pamięć podręczną Azure Cache for Redis na platformie Azure. Jeśli masz już pamięć podręczną Azure Cache for Redis w obrębie platformy Azure lub poza nią, możesz <a href="#add-external-cache">pominąć</a> te kroki i przejść do następnej sekcji.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Dodawanie zewnętrznej pamięci podręcznej

Wykonaj poniższe kroki, aby dodać zewnętrzną pamięć podręczną Azure Cache for Redis w usłudze Azure API Management.

![Dodawanie własnej pamięci podręcznej do usługi APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Ustawienie **Używane z** określa, które wdrożenie regionalne usługi API Management będzie komunikować się za pomocą skonfigurowanej pamięci podręcznej w przypadku konfiguracji usługi API Management w wielu regionach. Pamięci podręczne określone jako **Domyślne** zostaną zastąpione przez pamięci podręczne z wartością regionalną.
>
> Jeśli na przykład usługa API Management jest hostowana w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia, i skonfigurowano dwie pamięci podręczne, jedną jako **domyślną** i jedną dla regionu **Azja Południowo-Wschodnia**, to usługa API Management w regionie **Azja Południowo-Wschodnia** będzie używać własnej pamięci podręcznej, a dwa pozostałe regiony będą używać **domyślnej** pamięci podręcznej.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Dodawanie pamięci podręcznej Azure Cache for Redis z tej samej subskrypcji

1. Przejdź do wystąpienia usługi API Management w witrynie Azure Portal.
2. Wybierz kartę **Zewnętrzna pamięć podręczna** z menu po lewej stronie.
3. Kliknij przycisk **+ Dodaj**.
4. Wybierz swoją pamięć podręczną w polu rozwijanym **Wystąpienie pamięci podręcznej**.
5. Wybierz pozycję **Domyślne** lub określ żądany region w polu rozwijanym **Używane z**.
6. Kliknij pozycję **Zapisz**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Dodawanie pamięci podręcznej Azure Cache for Redis hostowanej poza bieżącą subskrypcją platformy Azure lub poza samą platformą Azure

1. Przejdź do wystąpienia usługi API Management w witrynie Azure Portal.
2. Wybierz kartę **Zewnętrzna pamięć podręczna** z menu po lewej stronie.
3. Kliknij przycisk **+ Dodaj**.
4. Wybierz pozycję **Niestandardowe** w polu rozwijanym **Wystąpienie pamięci podręcznej**.
5. Wybierz pozycję **Domyślne** lub określ żądany region w polu rozwijanym **Używane z**.
6. W polu **Parametry połączenia** podaj parametry połączenia swojej pamięci podręcznej Azure Cache for Redis.
7. Kliknij pozycję **Zapisz**.

## <a name="use-the-external-cache"></a>Używanie zewnętrznej pamięci podręcznej

Gdy pamięć zewnętrzna zostanie skonfigurowana w usłudze API Management, można jej używać za pośrednictwem zasad buforowania. Zobacz [Dodawanie buforowania w celu poprawy wydajności usługi Azure API Management](api-management-howto-cache.md), aby zapoznać się ze szczegółowymi instrukcjami.

## <a name="next-steps"> </a>Następne kroki
* Aby uzyskać więcej informacji na temat zasad buforowania, zobacz [Caching policies][Caching policies] (Zasady buforowania) w artykule [API Management policy reference][API Management policy reference] (Dokumentacja zasad usługi API Management).
* Aby poznać informacje na temat buforowania elementów według kluczy przy użyciu wyrażeń zasad, zobacz artykuł [Custom caching in Azure API Management](api-management-sample-cache-by-key.md) (Niestandardowe buforowanie w usłudze Azure API Management).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx