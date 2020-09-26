---
title: Integrowanie z usługą Logic Apps
titleSuffix: Azure Digital Twins
description: Zobacz, jak połączyć Logic Apps z usługą Azure Digital bliźniaczych reprezentacji przy użyciu łącznika niestandardowego
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 6726dab6f1037f01eda316968e3c5b503aa9dbfb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326584"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integracja z usługą Logic Apps przy użyciu łącznika niestandardowego

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) to usługa w chmurze, która ułatwia automatyzację przepływów pracy między aplikacjami i usługami. Łącząc Logic Apps z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, możesz tworzyć takie zautomatyzowane przepływy na platformie Azure Digital bliźniaczych reprezentacji i ich danych.

Usługa Azure Digital bliźniaczych reprezentacji nie ma obecnie certyfikowanego (wstępnie utworzonego) łącznika dla Logic Apps. Zamiast tego bieżący proces używania Logic Apps z usługą Azure Digital bliźniaczych reprezentacji polega na utworzeniu [**niestandardowego łącznika Logic Apps**](../logic-apps/custom-connector-overview.md)przy użyciu niestandardowego programu [Azure Digital bliźniaczych reprezentacji Swagger](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , który został zmodyfikowany do pracy z Logic Apps.

Ten artykuł zawiera [Azure Portal](https://portal.azure.com) do **tworzenia łącznika niestandardowego** , który może służyć do nawiązywania połączenia Logic Apps z wystąpieniem Digital bliźniaczych reprezentacji platformy Azure. Następnie utworzysz **aplikację logiki** , która używa tego połączenia w przykładowym scenariuszu, w którym zdarzenia wyzwalane przez czasomierz będą automatycznie aktualizować sznurki w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem **Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** .
Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą tego konta. 

Należy również wykonać następujące czynności w ramach konfiguracji wymagań wstępnych. Pozostała część tej sekcji przeprowadzi Cię przez następujące kroki:
- Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji
- Pobierz klucz tajny klienta rejestracji aplikacji
- Dodaj dwuosiową cyfrę

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Aby połączyć Logic Apps wystąpienie usługi Azure Digital bliźniaczych reprezentacji w tym artykule, musisz mieć już skonfigurowane **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . 

Najpierw skonfiguruj wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wymagane uwierzytelnianie, aby móc z nich korzystać. Aby to zrobić, postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md). W zależności od preferowanego środowiska, artykuł instalacyjny jest oferowany dla [przykładowego skryptu wdrażania](how-to-set-up-instance-scripted.md) [Azure Portal](how-to-set-up-instance-portal.md), [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md)lub Cloud Shell. Wszystkie wersje instrukcji zawierają również kroki umożliwiające sprawdzenie, czy każdy krok został pomyślnie wykonany i jest gotowy do przejścia do korzystania z nowego wystąpienia.

W tym samouczku będziesz potrzebować kilku wartości z podczas konfigurowania wystąpienia. Aby ponownie zebrać te wartości, Skorzystaj z poniższych linków, aby znaleźć je w [Azure Portal](https://portal.azure.com).
* **_Nazwa hosta_** wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([Znajdź w portalu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Identyfikator aplikacji rejestracji aplikacji usługi Azure AD **_(klient)_** ([Znajdź w portalu](how-to-set-up-instance-portal.md#collect-important-values))
* Identyfikator rejestracji w usłudze Azure AD ( **_dzierżawa)_** ([Znajdź w portalu](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Pobierz klucz tajny klienta rejestracji aplikacji

Należy również utworzyć **_klucz tajny klienta_** dla rejestracji aplikacji usługi Azure AD. W tym celu przejdź do strony [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal (możesz użyć tego linku lub poszukać go na pasku wyszukiwania portalu). Wybierz swoją rejestrację z listy, aby otworzyć jej szczegóły. 

Trafij *Certyfikaty i wpisy tajne* z menu Rejestracja i wybierz pozycję *+ nowy klucz tajny klienta*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość "certyfikaty i wpisy tajne", a na stronie znajduje się wartość "nowy wpis tajny klienta".":::

Wprowadź wszelkie wartości, które mają być stosowane w opisie i wygaśnie, i kliknij przycisk *Dodaj*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Dodaj klucz tajny klienta":::

Teraz sprawdź, czy klucz tajny klienta jest widoczny na stronie _certyfikaty & wpisy tajne_ z polami _Expires_ i _Value_ . Zanotuj jego _wartość_ , aby użyć jej później (można także skopiować ją do schowka za pomocą ikony kopiowania)

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Kopiuj wartość klucza tajnego klienta":::

### <a name="add-a-digital-twin"></a>Dodaj dwuosiową cyfrę

W tym artykule jest używane Logic Apps do aktualizowania sznurka w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Aby można było wykonać operację, należy dodać co najmniej jedną dwuosiową w wystąpieniu. 

Możesz dodać bliźniaczych reprezentacji za pomocą [interfejsów API DigitalTwins](how-to-use-apis-sdks.md), [zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)lub [interfejsu wiersza polecenia platformy Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia bliźniaczych reprezentacji przy użyciu tych metod, zobacz [*How to: Manage Digital bliźniaczych reprezentacji*](how-to-manage-twin.md).

Będziesz potrzebować **_identyfikatora sznurka_** w utworzonym wystąpieniu.

## <a name="create-custom-logic-apps-connector"></a>Tworzenie łącznika Logic Apps niestandardowego

W tym kroku utworzysz [niestandardowy łącznik Logic Apps](../logic-apps/custom-connector-overview.md) dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Po wykonaniu tej czynności będzie można podłączyć usługę Azure Digital bliźniaczych reprezentacji podczas tworzenia aplikacji logiki w następnej sekcji.

Przejdź do strony [Logic Apps łącznika niestandardowego](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) w Azure Portal (możesz użyć tego linku lub wyszukać go na pasku wyszukiwania portalu). Trafij *i Dodaj*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Strona "Logic Apps łącznika niestandardowego" w Azure Portal. Zaznacz wokół przycisku "Dodaj"":::

Na poniższej stronie *utwórz Logic Apps łącznika niestandardowego* wybierz subskrypcję i grupę zasobów oraz nazwę i lokalizację wdrożenia dla nowego łącznika. *Przejrzyj i Utwórz*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Strona "Tworzenie Logic Apps łącznika niestandardowego" w Azure Portal.":::

Spowoduje to przejście do karty *Recenzja + tworzenie* , w której można utworzyć *zasób w dolnej* części.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Karta "Recenzja + tworzenie" na stronie "przegląd Logic Apps łącznika niestandardowego" w Azure Portal. Zaznacz wokół przycisku "Utwórz"":::

Nastąpi przekierowanie do strony wdrożenia łącznika. Po zakończeniu wdrażania naciśnij przycisk *Przejdź do zasobu* , aby wyświetlić szczegóły łącznika w portalu.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurowanie łącznika dla usługi Azure Digital bliźniaczych reprezentacji

Następnie skonfigurujesz utworzony łącznik, aby uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji.

Najpierw pobierz niestandardową strukturę Swagger usługi Azure Digital bliźniaczych reprezentacji, która została zmodyfikowana w celu współpracy z Logic Apps. Pobierz przykład **niestandardowego struktury Swagger platformy Azure Digital bliźniaczych reprezentacji** z [tego linku](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , naciskając przycisk *Pobierz plik zip* . Przejdź do pobranego folderu *Azure_Digital_Twins_Custom_Swaggers.zip* i rozpakuj go. Niestandardowa struktura Swagger dla tego samouczka znajduje się w *Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.js*.

Następnie przejdź do strony omówienia łącznika w [Azure Portal](https://portal.azure.com) i kliknij przycisk *Edytuj*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Strona "przegląd" dla łącznika utworzonego w poprzednim kroku. Zaznacz wokół przycisku "Edytuj"":::

Na stronie *edytuj Logic Apps łącznika niestandardowego* skonfiguruj następujące informacje:
* **Łączniki niestandardowe**
    - Punkt końcowy interfejsu API: REST (pozostaw wartość domyślną)
    - Tryb importu: plik OpenAPI (pozostaw domyślny)
    - Plik: będzie to plik niestandardowego pliku struktury Swagger pobrany wcześniej. Kliknij przycisk *Importuj*, Znajdź plik na maszynie (*Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.json*), a następnie kliknij przycisk *Otwórz*.
* **Informacje ogólne**
    - Ikona: Przekaż ikonę, którą lubisz
    - Kolor tła ikony: Wprowadź kod szesnastkowy w formacie "#xxxxxx" dla koloru.
    - Opis: Wypełnij wszystkie wartości, które chcesz.
    - Schemat: HTTPS (pozostaw domyślny)
    - Host: *Nazwa hosta* wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
    - Podstawowy adres URL:/(pozostaw wartość domyślną)

Następnie naciśnij przycisk *zabezpieczeń* w dolnej części okna, aby przejść do kolejnego kroku konfiguracji.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Zrzut ekranu przedstawiający dolną stronę "Edytuj Logic Apps łącznika niestandardowego". Zaznacz wokół przycisku, aby kontynuować zabezpieczenia":::

W kroku zabezpieczenia naciśnij pozycję *Edytuj* i skonfiguruj następujące informacje:
* **Typ uwierzytelniania**: OAuth 2,0
* **OAuth 2,0**:
    - Dostawca tożsamości: Azure Active Directory
    - Identyfikator klienta: *Identyfikator aplikacji (klienta)* dla rejestracji aplikacji usługi Azure AD
    - Wpis tajny klienta: *klucz tajny klienta* utworzony w [*wymaganiach wstępnych*](#prerequisites) rejestracji aplikacji usługi Azure AD
    - Adres URL logowania: https://login.windows.net (pozostaw wartość domyślną)
    - Identyfikator dzierżawy: *Identyfikator katalogu (dzierżawcy)* dla rejestracji aplikacji usługi Azure AD
    - Adres URL zasobu: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Zakres: Directory. AccessAsUser. All
    - Adres URL przekierowania: (pozostaw teraz wartość domyślną)

Zwróć uwagę, że w polu adres URL przekierowania zostanie *zapisany łącznik niestandardowy w celu wygenerowania adresu URL przekierowania*. Zrób to teraz przez naciśnięcie przycisku *Aktualizuj łącznik* w górnej części okienka, aby potwierdzić ustawienia łącznika.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Zrzut ekranu przedstawiający początek strony "Edytuj Logic Apps łącznika niestandardowego". Zaznacz wokół przycisku "Aktualizuj łącznik"":::

<!-- Success message? didn't see one -->

Wróć do pola adres URL przekierowania i skopiuj wygenerowaną wartość. Zostanie ona użyta w następnym kroku.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Pole adresu URL przekierowania na stronie "Edytuj Logic Apps łącznika niestandardowego" ma teraz wartość " https://logic-apis-westus2.consent.azure-apim.net/redirect ". Przycisk służący do kopiowania wartości jest wyróżniony.":::

Są to wszystkie informacje wymagane do utworzenia łącznika (nie ma potrzeby dalszej ochrony przed poprzednimi zabezpieczeniami w kroku definicji). Możesz zamknąć okienko *edytowanie Logic Apps łącznika niestandardowego* .

>[!NOTE]
>Wróć do strony omówienia łącznika, w której pierwotnie trafisz *Edytowanie*, pamiętaj, że naciśnięcie opcji *Edytuj* ponownie spowoduje ponowne uruchomienie całego procesu wprowadzania konfiguracji. Nie spowoduje to wypełnienia wartości z ostatniego przekroczenia tego czasu, dlatego jeśli chcesz zapisać zaktualizowaną konfigurację z dowolnymi zmienionymi wartościami, musisz ponownie wprowadzić wszystkie pozostałe wartości, aby uniknąć ich zastąpienia przy użyciu wartości domyślnych.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Przyznawanie uprawnień łącznika w aplikacji usługi Azure AD

Następnie użyj wartości *adresu URL przekierowania* łącznika niestandardowego skopiowanej w ostatnim kroku, aby przyznać uprawnienia łącznika w rejestracji aplikacji usługi Azure AD.

Przejdź do strony [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal i wybierz swoją rejestrację z listy. 

W obszarze *uwierzytelnianie* z menu Rejestracja Dodaj identyfikator URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Strona uwierzytelniania dla rejestracji aplikacji w Azure Portal. Element "Authentication" w menu jest wyróżniony, a na stronie jest wyróżniony przycisk "Dodaj identyfikator URI"."::: 

Wprowadź *adres URL przekierowania* łącznika niestandardowego do nowego pola i kliknij ikonę *Zapisz* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Strona uwierzytelniania dla rejestracji aplikacji w Azure Portal. Nowy adres URL przekierowania zostanie wyróżniony i przycisk "Zapisz" dla strony.":::

Teraz po skonfigurowaniu łącznika niestandardowego, który będzie mógł uzyskać dostęp do interfejsów API Digital bliźniaczych reprezentacji platformy Azure. 

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

Następnie utworzysz aplikację logiki, która będzie używać nowego łącznika do automatyzowania aktualizacji usługi Azure Digital bliźniaczych reprezentacji.

W [Azure Portal](https://portal.azure.com)Wyszukaj *Aplikacje logiki* na pasku wyszukiwania portalu. Wybranie tej opcji powinno spowodować przejście na stronę *Aplikacje logiki* . Naciśnij przycisk *Utwórz aplikację logiki* , aby utworzyć nową aplikację logiki.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Strona "Logic Apps" w Azure Portal. Naciśnij przycisk "Dodaj"":::

Na stronie *aplikacja logiki* poniżej wprowadź subskrypcję i grupę zasobów. Ponadto wybierz nazwę aplikacji logiki i wybierz lokalizację wdrożenia.

Naciśnij przycisk _Recenzja + Utwórz_ .

Spowoduje to przejście do karty *Recenzja i tworzenie* , gdzie można przejrzeć szczegóły i kliknąć przycisk *Utwórz* u dołu, aby utworzyć zasób.

Nastąpi przekierowanie do strony wdrożenia aplikacji logiki. Po zakończeniu wdrażania naciśnij przycisk *Przejdź do zasobu* , aby przejść do *projektanta Logic Apps*, w którym zostanie wypełniona logika przepływu pracy.

### <a name="design-workflow"></a>Projekt przepływu pracy

W *projektancie Logic Apps*w obszarze *Rozpocznij ze wspólnym wyzwalaczem*wybierz pozycję _**cykl**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Strona "Logic Apps Designer" w Azure Portal. Zaznacz wokół typowego wyzwalacza "cykl"":::

Na poniższej stronie *projektanta Logic Apps* Zmień częstotliwość **cyklu** na *sekundę*, aby zdarzenie było wyzwalane co 3 sekundy. Dzięki temu będzie można łatwo zobaczyć wyniki później, bez konieczności oczekiwania.

Naciśnij pozycję *+ nowy krok*.

Spowoduje to otwarcie pola *Wybierz akcję* . Przejdź do karty *niestandardowej* . Łącznik niestandardowy powinien być widoczny wcześniej w górnej części okna.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Tworzenie przepływu w projektancie Logic Apps w Azure Portal. W polu "Wybierz akcję" wybrana jest karta "niestandardowe". Łącznik niestandardowy użytkownika z wcześniejszych zostanie wyświetlony w polu z wyróżnionym wyróżnieniem.":::

Wybierz go, aby wyświetlić listę interfejsów API zawartych w tym łączniku. Użyj paska wyszukiwania lub przewiń listę, aby zaznaczyć **DigitalTwins_Add**. (Jest to interfejs API używany w tym artykule, ale można również wybrać dowolny inny interfejs API jako prawidłowy wybór dla połączenia Logic Apps).

Może zostać wyświetlony monit o zalogowanie się przy użyciu poświadczeń platformy Azure w celu nawiązania połączenia z łącznikiem. Jeśli zostanie wyświetlone okno dialogowe z *prośbą o uprawnienia* , postępuj zgodnie z monitami, aby udzielić zgody na aplikację i zaakceptować ją.

W polu Nowy *DigitalTwinsAdd* Wypełnij pola w następujący sposób:
* _ID_: Wypełnij *Identyfikator sznurka* cyfrowego w wystąpieniu, które ma być aktualizowane przez aplikację logiki.
* _sznurek_: to pole służy do wprowadzania treści wymaganej przez wybrane żądanie interfejsu API. W przypadku *DigitalTwinsUpdate*ta treść jest w postaci kodu poprawki JSON. Aby uzyskać więcej informacji o tworzeniu struktury poprawki JSON w celu zaktualizowania sznurka, zapoznaj się z sekcją " [Aktualizowanie cyfrowej sieci](how-to-manage-twin.md#update-a-digital-twin) " w artykule *How to: Manage Digital bliźniaczych reprezentacji*.
* _interfejs API-Version_: w bieżącej publicznej wersji zapoznawczej ta wartość to *2020-05-31 — wersja zapoznawcza*

Naciśnij klawisz *Save* w projektancie Logic Apps.

Możesz wybrać inne operacje, wybierając pozycję _+ nowy krok_ w tym samym oknie.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Zakończono widok aplikacji w łączniku aplikacji logiki. Pole DigitalTwinsAdd jest wypełnione wartościami opisanymi powyżej, łącznie z przykładową treścią poprawek JSON. Przycisk "Zapisz" dla okna jest wyróżniony.":::

## <a name="query-twin-to-see-the-update"></a>Zapytanie bliźniaczye, aby zobaczyć aktualizację

Teraz, gdy aplikacja logiki została utworzona, zdarzenie aktualizacji z przędzą zdefiniowane w projektancie Logic Apps powinno odbywać się w cyklu co trzy sekundy. Oznacza to, że po upływie trzech sekund powinno być możliwe przetworzenie zapytania dotyczącego sznurka i wyświetlenie nowych poprawionych wartości.

Możesz wysyłać zapytania do przędzy za pomocą wybranej metody (takiej jak [niestandardowa aplikacja kliencka](tutorial-command-line-app.md), [Przykładowa aplikacja programu Azure Digital bliźniaczych reprezentacji Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [zestawy SDK i interfejsy API](how-to-use-apis-sdks.md)lub [interfejs wiersza polecenia](how-to-use-cli.md)). 

Aby uzyskać więcej informacji o wysyłaniu zapytań do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, zobacz [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono aplikację logiki, która regularnie aktualizuje dwuosiowe w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji za pomocą podanej poprawki. Możesz wypróbować inne interfejsy API w łączniku niestandardowym, aby utworzyć Logic Apps dla różnych akcji w wystąpieniu.

Aby dowiedzieć się więcej na temat dostępnych operacji API i wymaganych szczegółów, odwiedź stronę [*How to: use Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md).