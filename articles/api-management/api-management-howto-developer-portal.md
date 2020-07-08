---
title: Omówienie portalu dla deweloperów platformy Azure API Management
titleSuffix: Azure API Management
description: Dowiedz się więcej o portalu dla deweloperów w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: ecbd4d97bb092ccbdb286e4865bf04e770ca9473
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207893"
---
# <a name="azure-api-management-developer-portal-overview"></a>Portal deweloperów API Management platformy Azure — omówienie

Portal dla deweloperów to automatycznie generowana, w pełni dostosowywalna witryna sieci Web z dokumentacją interfejsów API. W przypadku, gdy konsumenci interfejsu API mogą odnajdywać interfejsy API, dowiedzieć się, jak ich używać, zażądać dostępu i wypróbować.

W tym artykule opisano różnice między samodzielnymi i zarządzanymi wersjami portalu dla deweloperów w API Management. Wyjaśniono w nim także swoją architekturę i przedstawiono odpowiedzi na często zadawane pytania.

![Portal dla deweloperów API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Zarządzane i samodzielne wersje

Portal dla deweloperów można skompilować na dwa sposoby:

- **Wersja zarządzana** — edytując i dostosowując Portal, który jest wbudowany w wystąpienie API Management i jest dostępny za pomocą adresu URL `<your-api-management-instance-name>.developer.azure-api.net` . Zapoznaj się z [tym artykułem dokumentacji](api-management-howto-developer-portal-customize.md) , aby dowiedzieć się, jak uzyskać dostęp do portalu zarządzanego i dostosować go.
- **Samodzielna wersja** — przez wdrożenie i samodzielne hostowanie portalu poza wystąpieniem API Management. Takie podejście umożliwia edytowanie bazy kodu portalu i poszerzanie dostarczonych funkcji podstawowych — na przykład zaimplementowanie niestandardowych widżetów dla integracji z systemami innych firm. W tym scenariuszu użytkownik jest odpowiedzialny za aktualizowanie portalu do najnowszej wersji. Aby uzyskać szczegółowe informacje i instrukcje, zapoznaj się z [repozytorium GitHub za pomocą kodu źródłowego portalu][1] i [samouczka dotyczącego implementacji widżetu][3]. Samouczek dotyczący [zarządzanej wersji](api-management-howto-developer-portal-customize.md) przeprowadzi Cię przez panel administracyjny portalu, który jest typowy dla zarządzanych i samodzielnych wersji.

## <a name="portal-architectural-concepts"></a>Koncepcje architektury portalu

Składniki portalu można logicznie podzielić na dwie kategorie: *kod* i *zawartość*.

*Kod* jest przechowywany w [repozytorium GitHub][1] i obejmuje:

- Widżety, które reprezentują elementy wizualne i łączą kod HTML, JavaScript, możliwość stylów, ustawienia i mapowanie zawartości. Przykłady to obraz, akapit tekstu, formularz, lista interfejsów API itp.
- Definicje stylów, które określają, w jaki sposób można stylować widżety
- Aparat, który generuje statyczne strony sieci Web z zawartości portalu i jest zapisywana w języku JavaScript
- Edytor wizualny, który umożliwia dostosowanie i tworzenie w przeglądarce

*Zawartość* jest dzielona na dwie podkategorie: *zawartość portalu* i *zawartość API Management*.

*Zawartość portalu* jest specyficzna dla portalu i obejmuje:

- Strony — na przykład strona docelowa, samouczki interfejsu API, wpisy w blogu
- Multimedia — obrazy, animacje i inna zawartość oparta na plikach
- Układy — szablony dopasowane do adresu URL i definiują sposób wyświetlania stron
- Style — wartości dla definicji stylów, np. czcionki, kolory, obramowania
- Ustawienia — konfiguracja, np. favicon, metadane witryny sieci Web

*Zawartość portalu*, z wyjątkiem nośników, jest wyrażana jako dokumenty JSON.

*API Management Content* zawiera jednostki, takie jak interfejsy API, operacje, produkty i subskrypcje.

Portal jest oparty na dostosowaniu rozwidlenia [struktury Paperbits](https://paperbits.io/). Oryginalne funkcje Paperbits zostały rozszerzone, aby zapewnić widżety specyficzne dla API Management (na przykład listę interfejsów API, listę produktów) i łącznik do API Management usługi do zapisywania i pobierania zawartości.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Często zadawane pytania

W tej sekcji znajdziesz odpowiedzi na często zadawane pytania dotyczące portalu dla deweloperów, który ma charakter ogólny. Pytania specyficzne dla własnej wersji hostowanej można znaleźć w [sekcji wiki repozytorium GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a>Jak przeprowadzić migrację z wersji zapoznawczej portalu?

Korzystając z wersji zapoznawczej portalu dla deweloperów, Zainicjowano obsługę zawartości w wersji zapoznawczej w usłudze API Management. Zawartość domyślna została znacząco zmodyfikowana w ogólnie dostępnej wersji w celu lepszego środowiska użytkownika. Zawiera również nowe widżety.

Jeśli używasz zarządzanej wersji, zresetuj zawartość portalu, klikając pozycję **Zresetuj zawartość** w sekcji menu **operacje** . Potwierdzenie tej operacji spowoduje usunięcie całej zawartości portalu i udostępnienie nowej zawartości domyślnej. Aparat portalu został automatycznie zaktualizowany w usłudze API Management.

![Resetuj zawartość portalu](media/api-management-howto-developer-portal/reset-content.png)

Jeśli używasz wersji samohostowanej, Użyj usługi `scripts/cleanup.bat` i `scripts/generate.bat` z repozytorium GitHub, aby usunąć istniejącą zawartość i udostępnić nową zawartość. Upewnij się, że kod portalu został uaktualniony do najnowszej wersji z repozytorium GitHub wcześniej.

Jeśli nie chcesz resetować zawartości portalu, możesz rozważyć użycie nowo dostępnych widżetów na stronach. Istniejące widżety zostały automatycznie zaktualizowane do najnowszych wersji.

Jeśli Twój portal został zainicjowany po ogólnym ogłoszeniu dostępności, powinien już obsługiwać nową zawartość domyślną. Nie jest wymagana żadna akcja ze strony użytkownika.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Jak przeprowadzić migrację ze starego portalu dla deweloperów do portalu dla deweloperów?

Portale nie są zgodne i należy przeprowadzić migrację zawartości ręcznie.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>Czy Portal ma wszystkie funkcje starego portalu?

Portal dla deweloperów nie obsługuje już *aplikacji* i *problemów*.

Uwierzytelnianie za pomocą protokołu OAuth w interaktywnej konsoli dewelopera nie jest jeszcze obsługiwane. Postęp można śledzić w ramach [problemu usługi GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>Czy stary Portal jest przestarzały?

Stary Portal deweloperów i wydawców ma teraz *starsze* funkcje — będą otrzymywać tylko aktualizacje zabezpieczeń. Nowe funkcje zostaną zaimplementowane tylko w nowym portalu dla deweloperów.

Wycofanie starszych portali zostanie ogłoszone osobno. Jeśli masz pytania, wątpliwości lub komentarze, zgłoś je [w ramach dedykowanego problemu usługi GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Wymagana funkcjonalność nie jest obsługiwana w portalu

Możesz otworzyć [żądanie funkcji](https://aka.ms/apimwish) lub [samodzielnie zaimplementować brakującą funkcjonalność][3]. W przypadku zaimplementowania funkcji samodzielnie możesz samoobsługowo zarządzać portalem deweloperów lub otworzyć żądanie ściągnięcia w usłudze GitHub, aby uwzględnić zmiany w zarządzanej wersji.

### <a name="how-can-i-automate-portal-deployments"></a>Jak można zautomatyzować wdrożenia portalu?

Możesz programowo uzyskać dostęp do zawartości portalu dla deweloperów i zarządzać nią za pośrednictwem interfejsu API REST, niezależnie od tego, czy używasz zarządzanej, czy własnej wersji.

Interfejs API jest udokumentowany w [sekcji wiki repozytorium GitHub][2]. Może służyć do automatyzowania migracji zawartości portalu między środowiskami — na przykład ze środowiska testowego do środowiska produkcyjnego. Więcej informacji o tym procesie można znaleźć [w tym artykule z dokumentacji](https://aka.ms/apimdocs/migrateportal) w witrynie GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Czy Portal obsługuje Azure Resource Manager szablonów i/lub jest zgodny z zestawem SDK API Management DevOps?

Nie.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Czy muszę włączyć dodatkową łączność sieci wirtualnej dla zależności zarządzanego portalu?

W większości przypadków — nie.

Jeśli Twoja usługa API Management znajduje się w wewnętrznej sieci wirtualnej, Portal dla deweloperów jest dostępny tylko z poziomu usługi. Nazwa hosta punktu końcowego zarządzania musi zostać rozpoznana jako wewnętrzny adres VIP usługi z komputera, który służy do uzyskiwania dostępu do interfejsu administracyjnego portalu. Upewnij się, że punkt końcowy zarządzania jest zarejestrowany w systemie DNS. W przypadku błędnej konfiguracji zostanie wyświetlony komunikat o błędzie: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Jeśli usługa API Management znajduje się w wewnętrznej sieci wirtualnej i uzyskuje do niej dostęp za pośrednictwem Application Gateway z Internetu, upewnij się, że jest włączona łączność z portalem dla deweloperów i punktami końcowymi zarządzania API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Mam przypisaną niestandardową domenę API Management i opublikowany Portal nie działa

Po zaktualizowaniu domeny należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Dodaliśmy dostawcę tożsamości i nie mogę go zobaczyć w portalu

Po skonfigurowaniu dostawcy tożsamości (na przykład AAD, AAD B2C) należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Mam konfigurację delegowania, a Portal nie używa jej

Po skonfigurowaniu delegowania należy [ponownie opublikować Portal](api-management-howto-developer-portal-customize.md#publish) , aby zmiany zaczęły obowiązywać.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>MOJE inne API Management zmiany konfiguracji nie zostały rozpropagowane w portalu dla deweloperów

Większość zmian konfiguracji (na przykład Sieć wirtualna, logowanie i warunki produktu) wymagają [ponownego opublikowania portalu](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Otrzymuję błąd CORS podczas korzystania z konsoli interaktywnej

Konsola interaktywna wykonuje żądanie interfejsu API po stronie klienta z przeglądarki. Rozwiąż problem CORS przez dodanie [zasad CORS](api-management-cross-domain-policies.md#CORS) do interfejsów API.

Stan zasad CORS można sprawdzić w sekcji **Omówienie portalu** usługi API Management w Azure Portal. Okno ostrzeżenia wskazuje nieobecne lub nieprawidłowo skonfigurowane zasady.

![Portal dla deweloperów API Management](media/api-management-howto-developer-portal/cors-azure-portal.png)

Automatyczne stosowanie zasad CORS przez kliknięcie przycisku **Włącz CORS** .

Można również włączyć funkcję CORS ręcznie.

1. Kliknij pozycję **ręcznie Zastosuj na poziomie globalnym** , aby wyświetlić wygenerowany kod zasad.
2. Przejdź do **wszystkich interfejsów API** w sekcji **interfejsy API** usługi API Management w Azure Portal.
3. Kliknij **</>** ikonę w sekcji **Przetwarzanie przychodzące** .
4. Wstaw zasady w **<inbound>** sekcji pliku XML. Upewnij się, że **<origin>** wartość jest zgodna z domeną portalu deweloperów.

> [!NOTE]
> 
> W przypadku zastosowania zasad CORS w zakresie produktu, a nie zakresu interfejsów API, a interfejs API używa uwierzytelniania klucza subskrypcji za pośrednictwem nagłówka, konsola nie będzie działała.
>
> Przeglądarka automatycznie wystawia żądanie HTTP dotyczące opcji, które nie zawiera nagłówka z kluczem subskrypcji. Ze względu na brak klucza subskrypcji API Management nie można skojarzyć opcji z produktem, dlatego nie można zastosować zasad CORS.
>
> Jako obejście można przekazać klucz subskrypcji w parametrze zapytania.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Jakie uprawnienia muszę edytować w portalu dla deweloperów?

Jeśli `Oops. Something went wrong. Please try again later.` wystąpi błąd podczas otwierania portalu w trybie administracyjnym, może brakować wymaganych uprawnień (RBAC).

Starsze portale wymagały uprawnienia do `Microsoft.ApiManagement/service/getssotoken/action` zakresu usługi ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ), aby umożliwić administratorowi użytkownika dostęp do portali. Nowy portal wymaga uprawnień `Microsoft.ApiManagement/service/users/token/action` w zakresie `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Za pomocą poniższego skryptu programu PowerShell można utworzyć rolę z wymaganym uprawnieniem. Pamiętaj, aby zmienić `<subscription-id>` parametr. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Po utworzeniu roli można udzielić jej użytkownikowi z poziomu sekcji **Access Control (IAM)** w Azure Portal. Przypisanie tej roli do użytkownika spowoduje przypisanie uprawnienia do zakresu usługi. Użytkownik będzie mógł generować tokeny SAS w imieniu *dowolnego* użytkownika w usłudze. Na minimalnym poziomie ta rola musi być przypisana do administratora usługi. Następujące polecenie programu PowerShell demonstruje sposób przypisywania roli do użytkownika `user1` w najniższym zakresie, aby uniknąć udzielania niepotrzebnych uprawnień użytkownikowi: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Po udzieleniu uprawnień użytkownikowi użytkownik musi się wylogować i zalogować się ponownie do Azure Portal, aby nowe uprawnienia zaczęły obowiązywać.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Widzę `Unable to start the portal. See if settings are specified correctly (...)` błąd

Ten błąd jest wyświetlany, gdy `GET` wywołanie `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` zakończy się niepowodzeniem. Wywołanie jest wydawane z przeglądarki przez interfejs administracyjny portalu.

Jeśli usługa API Management znajduje się w sieci wirtualnej, zapoznaj się z powyższym pytaniem dotyczącym łączności sieci wirtualnej.

Błąd wywołania może być również spowodowany przez certyfikat TLS/SSL, który jest przypisany do domeny niestandardowej i nie jest zaufany przez przeglądarkę. Jako środek zaradczy, można usunąć niestandardową domenę punktu końcowego zarządzania — API Management powróci do domyślnego punktu końcowego za pomocą zaufanego certyfikatu.

### <a name="whats-the-browser-support-for-the-portal"></a>Co to jest obsługa przeglądarki dla portalu?

| Przeglądarka                     | Obsługiwane       |
|-----------------------------|-----------------|
| Apple Safari                | Tak<sup>1</sup> |
| Google Chrome               | Tak<sup>1</sup> |
| Microsoft Edge              | Tak<sup>1</sup> |
| Microsoft Internet Explorer | Nie              |
| Mozilla Firefox             | Tak<sup>1</sup> |

 <small><sup>1</sup> obsługiwane w dwóch najnowszych wersjach produkcyjnych.</small>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o nowym portalu dla deweloperów:

- [Dostęp do zarządzanego portalu deweloperów i dostosowywanie go](api-management-howto-developer-portal-customize.md)
- [Skonfiguruj samodzielną wersję portalu][2]
- [Implementowanie własnego widżetu][3]

Przeglądaj inne zasoby:

- [Repozytorium GitHub z kodem źródłowym][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
