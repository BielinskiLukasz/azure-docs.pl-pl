---
title: Jak utworzyć żądanie pomocy technicznej platformy Azure | Microsoft Docs
description: Klienci, którzy potrzebują pomocy, mogą korzystać z Azure Portal, aby znaleźć rozwiązania samoobsługowe i tworzyć żądania pomocy technicznej i zarządzać nimi.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 11/13/2019
ms.author: kfollis
ms.openlocfilehash: 35d70d37b881c610d1a38a312d79f39c351c65a5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897418"
---
# <a name="how-to-create-an-azure-support-request"></a>Jak utworzyć żądanie obsługi na platformie Azure

## <a name="overview"></a>Przegląd

Klienci platformy Azure mogą tworzyć żądania pomocy technicznej i zarządzać nimi w [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Adres URL Azure Portal jest specyficzny dla chmury platformy Azure, w której wdrożono organizację. 
>* Azure Portal do użytku komercyjnego: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal dla Niemiec: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal dla instytucji rządowych Stany Zjednoczone: [https://portal.azure.us](https://portal.azure.us)
>
>

W oparciu o opinie klientów Zaktualizowaliśmy środowisko żądania pomocy technicznej, aby skoncentrować się na trzech głównych celach:

* **Usprawnione**: zapewnianie pomocy technicznej i rozwiązywania problemów łatwo znaleźć i Uprość sposób przesyłania żądania pomocy technicznej.
* **Zintegrowane**: można łatwo otworzyć żądanie pomocy technicznej w przypadku rozwiązywania problemu z zasobem platformy Azure bez przełączania kontekstu.
* **Wydajne**: Zbierz informacje o kluczu, którego potrzebuje Agent pomocy technicznej, aby skutecznie rozwiązać problem.

## <a name="getting-started"></a>Wprowadzenie

Możesz uzyskać **Pomoc techniczną i wsparcie** w Azure Portal. Jest on dostępny z globalnego nagłówka lub z menu zasobów dla usługi. Aby można było wysłać żądanie pomocy technicznej, musisz mieć odpowiednie uprawnienia.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby utworzyć żądanie pomocy technicznej, musisz być administratorem lub być przypisana do roli [współautor żądania pomocy technicznej](../../role-based-access-control/built-in-roles.md#support-request-contributor) .

### <a name="go-to-help--support-from-the-global-header"></a>Przejdź do pomocy i obsługi technicznej z nagłówka globalnego

Aby uruchomić żądanie pomocy technicznej z dowolnego miejsca w Azure Portal:

1. Wybierz ikonę **?** w nagłówku globalnym. Następnie wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Pomoc i obsługa techniczna](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Wybierz pozycję **+ nowe żądanie obsługi**. Postępuj zgodnie z monitami, aby przekazać nam informacje o problemie. Zasugerujemy niektóre możliwe rozwiązania, zebranie szczegółowych informacji o problemie i przesłanie i śledzenie żądania pomocy technicznej.

   ![Nowe żądanie pomocy technicznej](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Przejdź do pomocy i obsługi technicznej z menu zasobów

Aby uruchomić żądanie obsługi w kontekście zasobu, obecnie pracujesz z:

1. Z menu zasób w sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **nowe żądanie obsługi**.

   ![W kontekście](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Postępuj zgodnie z monitami, aby podać nam informacje o występującym problemie. Po uruchomieniu procesu żądania obsługi z zasobu niektóre opcje są wstępnie wybrane dla Ciebie.

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

Przeprowadzimy Cię przez kilka kroków, aby zebrać informacje o problemie i pomóc w jego rozwiązaniu. Każdy krok został opisany w poniższych sekcjach.

### <a name="basics"></a>Podstawy

Pierwszy krok procesu żądania pomocy technicznej gromadzi podstawowe informacje o problemie i planie pomocy technicznej.

Na karcie **podstawy** **nowego żądania obsługi**Użyj selektorów, aby zacząć powiedzieć nam o problemie. Najpierw określisz niektóre ogólne kategorie dla typu problemu i wybierz powiązaną subskrypcję. Wybranie usługi (na przykład "maszyna wirtualna z systemem Windows") i zasobu (Nazwa maszyny wirtualnej) jest następnym krokiem w celu uzyskania pomocy. Zwięźle opisz problem we własnych słowach, a następnie **Wybierz pozycję typ problemu** , aby uzyskać bardziej szczegółowe informacje.

![Blok Podstawowe](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Platforma Azure zapewnia nieograniczoną obsługę zarządzania subskrypcjami (np. rozliczeniami, korektami przydziałów i transferami kont). Aby uzyskać pomoc techniczną, musisz mieć plan pomocy technicznej. [Dowiedz się więcej o planach pomocy technicznej](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Rozwiązania

Po zebraniu podstawowych informacji w następnej kolejności pokazująmy rozwiązania do samodzielnego wypróbowania. W niektórych przypadkach może nawet uruchomić szybką diagnostykę. Rozwiązania są tworzone przez inżynierów platformy Azure i rozwiązują najczęstsze problemy.

### <a name="details"></a>Szczegóły

Następnie zbieramy dodatkowe informacje o problemie. Dokładne i szczegółowe informacje w tym kroku ułatwiają kierowanie żądania pomocy technicznej do właściwego agenta i pozwalają im rozpocząć diagnozowanie problemu.

Dobrym pomysłem jest poinformowanie nas o rozpoczęciu problemu i wszelkich krokach jego odtworzenia. Możesz również przekazać plik, taki jak plik dziennika lub dane wyjściowe z diagnostyki.

Po dodaniu wszystkich informacji o problemie możesz wybrać, jak uzyskać pomoc techniczną. W sekcji **Metoda pomocy technicznej** na karcie **szczegóły** wybierz ważność wpływu. Podaj preferowaną metodę kontaktu, dobry czas, aby skontaktować się z Tobą i językiem pomocy technicznej.

Następnie wypełnij sekcję **informacje kontaktowe** , aby dowiedzieć się, jak skontaktować się z Tobą.

### <a name="review--create"></a>Przeglądanie i tworzenie

Wykonaj wszystkie wymagane informacje na każdej karcie, a następnie wybierz pozycję **Przegląd + Utwórz**. Sprawdź szczegóły, które zostaną wysłane do pomocy technicznej. Wróć do dowolnej karty, aby wprowadzić zmianę w razie potrzeby. Gdy zostanie spełnione żądanie pomocy technicznej, wybierz pozycję **Utwórz**.

Agent pomocy technicznej skontaktuje się z Tobą przy użyciu wskazanej metody. Aby uzyskać informacje o początkowym czasie reakcji, zapoznaj się [z zakresem pomocy technicznej i](https://azure.microsoft.com/support/plans/response/) czasem odpowiedzi.

## <a name="all-support-requests"></a>Wszystkie żądania pomocy technicznej

Szczegóły i stan żądań pomocy technicznej można wyświetlić, przechodząc do **pomocy** technicznej i >  **wszystkie żądania pomocy technicznej**.

![Wszystkie żądania pomocy technicznej](./media/how-to-create-azure-support-request/allrequestslower.png)

Na tej stronie można filtrować żądania obsługi według subskrypcji, daty utworzenia (UTC) i stanu. Ponadto można sortować i wyszukiwać żądania pomocy technicznej na tej stronie.

Wybierz żądanie obsługi, aby wyświetlić szczegóły, w tym ważność i oczekiwany czas odpowiedzi przez agenta obsługi.

Jeśli chcesz zmienić ważność żądania, wybierz pozycję **wpływ na działalność**. Zostanie wyświetlona lista serwerów dostępnych do przypisania.

> [!NOTE]
> Maksymalny poziom ważności zależy od Twojego planu pomocy technicznej. [Dowiedz się więcej o planach pomocy technicznej](https://azure.microsoft.com/support/plans).
>
>
Aby dowiedzieć się więcej o opcjach samoobsługowego rozwiązywania problemów na platformie Azure, Obejrzyj ten klip wideo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Następne kroki
* [Wyślij nam swoją opinię i sugestie](https://feedback.azure.com/forums/266794-support-feedback)
* Zaangażuj nas w serwisie [Twitter](https://twitter.com/azuresupport)
* Uzyskaj pomoc od swoich elementów równorzędnych na [forach MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Dowiedz się więcej na temat [często zadawanych pytań](https://azure.microsoft.com/support/faq)
