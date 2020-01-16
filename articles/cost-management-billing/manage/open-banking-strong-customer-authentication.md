---
title: Otwarta bankowość (PSD2) i silne uwierzytelnianie klienta (SCA) dla klientów platformy Azure
description: W tym artykule wyjaśniono, dlaczego uwierzytelnianie wieloskładnikowe jest wymagane w przypadku niektórych zakupów na platformie Azure i jak ukończyć uwierzytelnianie.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: aff5e1e707980c9a63988c22653fa5fba0fc236b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75996042"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Otwarta bankowość (PSD2) i silne uwierzytelnianie klienta (SCA) dla klientów platformy Azure

Od 14 września 2019 r. banki w 31 krajach [Europejskiego Obszaru Gospodarczego](https://en.wikipedia.org/wiki/European_Economic_Area) są zobowiązane do zweryfikowania tożsamości osoby dokonującej zakupu w trybie online przed przetworzeniem płatności. Ta weryfikacja wymaga uwierzytelniania wieloskładnikowego, aby pomóc w zagwarantowaniu, że zakupy online są bezpieczne i chronione. W niektórych krajach data wejścia w życie tych wymagań zostanie przesunięta. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące dyrektywy PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Jakie znaczenie ma dyrektywa PSD2 dla klientów platformy Azure

Jeśli płacisz za platformę Azure za pomocą karty kredytowej wydanej przez bank w [Europejskim Obszarze Gospodarczym](https://en.wikipedia.org/wiki/European_Economic_Area), może być konieczne przeprowadzenie uwierzytelniania wieloskładnikowego dla formy płatności Twojego konta. Prośba o przeprowadzenie uwierzytelniania wieloskładnikowego może być też wyświetlana podczas tworzenia lub podwyższania poziomu konta na platformie Azure — nawet jeśli w danym momencie nie są dokonywane żadne zakupy. Wymóg dokonania uwierzytelniania wieloskładnikowego może też się pojawić w innych sytuacjach, na przykład podczas zmiany formy płatności na koncie platformy Azure, usuwania limitu wydatków lub dokonywania natychmiastowych płatności z poziomu witryny Azure Portal, takich jak rozliczanie zaległych sald lub kupowanie środków na korzystanie z platformy Azure.

Jeśli bank odrzuci miesięczne opłaty za korzystanie z platformy Azure, otrzymasz wiadomość e-mail z informacją o upłynięciu terminu płatności i instrukcjami dotyczącymi rozwiązania tego problemu. W takim przypadku możesz przeprowadzić proces uwierzytelniania wieloskładnikowego i rozliczyć zaległe opłaty w witrynie Azure Portal.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Przeprowadzanie uwierzytelniania wieloskładnikowego w witrynie Azure Portal

W poniższych sekcjach opisano sposób przeprowadzania uwierzytelniania wieloskładnikowego w witrynie Azure Portal. Skorzystaj z tych informacji, które mają zastosowanie w Twoim przypadku.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Zmiana aktywnej formy płatności dla konta platformy Azure

Wykonaj następujące czynności, aby zmienić aktywną formę płatności dla konta platformy Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator konta i przejdź do obszaru **Zarządzanie kosztami i rozliczenia**.
2. Na stronie **Przegląd** wybierz odpowiednią subskrypcję z siatki **Moje subskrypcje**.
3. W obszarze Rozliczenia wybierz pozycję **Formy płatności**. Możesz dodać nową kartę kredytową lub ustawić istniejącą kartę jako aktywną formę płatności dla subskrypcji. Jeśli Twój bank wymaga uwierzytelniania wieloskładnikowego, zostanie wyświetlony monit o ukończenie uwierzytelniania.

Aby uzyskać więcej informacji, zobacz [Dodawanie, aktualizowanie lub usuwanie karty kredytowej dla platformy Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Rozliczanie zaległych opłat za usługi platformy Azure

Jeśli Twój bank odrzuci opłaty, stan konta platformy Azure w witrynie Azure Portal zmieni się na **Upłynął termin**. Wykonaj następujące czynności, aby sprawdzić stan konta:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.
2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
3. Na stronie **przegląd** **Cost Management i rozliczeń** Przejrzyj kolumnę Stan w siatce **Moje subskrypcje** .
4. Jeśli Twoja subskrypcja ma etykietę **Upłynął termin**, kliknij pozycję **Ureguluj saldo**. Zostanie wyświetlony monit o ukończenie uwierzytelniania wieloskładnikowego.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Rozliczanie zaległych opłat za zakupy w witrynie Marketplace i rezerwacje

Zakupy w witrynie Marketplace i opłaty za rezerwacje są rozliczane niezależnie od opłat za usługi platformy Azure. Jeśli Twój bank odrzuci opłaty za zakupy w witrynie Marketplace lub za rezerwacje, stan faktury w witrynie Azure Portal będzie widoczny jako **Upłynął termin**. Aby sprawdzić stan faktur za zakupy w witrynie Marketplace i za rezerwacje, wykonaj następujące czynności:

1. Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) jako administrator konta.
2. Wyszukaj pozycję **Zarządzanie kosztami i rozliczenia**.
3. W obszarze Rozliczenia wybierz pozycję **Faktury**.
4. Kliknij kartę **Azure Marketplace i rezerwacje** po prawej stronie.
5. Wybierz odpowiednią subskrypcję.
6. W siatce faktur przejrzyj kolumnę Stan. Jeśli faktura jest oznaczona jako **Termin** lub **Upłynął termin**, kliknij pozycję **Płatność natychmiastowa**. Zostanie wyświetlony monit o ukończenie uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
- Jeśli chcesz uregulować opłaty za korzystanie z platformy Azure, zobacz [Rozwiązywanie problemu z zaległym saldem za subskrypcję platformy Azure](resolve-past-due-balance.md).
