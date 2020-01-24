---
title: Pomoc techniczna i opcje pomocy dla deweloperów aplikacji usługi Azure AD | Microsoft Docs
description: Dowiedz się, jak uzyskać pomoc i pomoc techniczną dotyczącą pytań i problemów związanych z programowaniem podczas tworzenia aplikacji, która integruje się z tożsamościami firmy Microsoft (Azure Active Directory i konto Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: e85a768d8e4f11ca3637bc8f0b394cfb1ea9744a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697476"
---
# <a name="support-and-help-options-for-developers"></a>Pomoc techniczna i opcje pomocy dla deweloperów

Jeśli dopiero zaczynasz integrację z usługą Azure Active Directory (Azure AD), tożsamościami firmy Microsoft lub interfejsem API Microsoft Graph lub po wdrożeniu nowej funkcji w aplikacji, istnieją sytuacje, w których trzeba uzyskać pomoc od społeczności lub zrozumieć Opcje pomocy technicznej dostępne dla deweloperów. Ten artykuł ułatwia zapoznanie się z tymi opcjami, w tym:

> [!div class="checklist"]
> * Jak wyszukać, czy Twoje pytanie nie zostało odebrane przez społeczność, czy też istniejąca dokumentacja funkcji, którą próbujesz zaimplementować, już istnieje
> * W niektórych przypadkach wystarczy użyć naszych narzędzi do obsługi, aby debugować konkretny problem
> * Jeśli nie możesz znaleźć potrzebnej odpowiedzi, możesz zadać pytanie na *Stack Overflow*
> * Jeśli znajdziesz problem z jedną z naszych bibliotek uwierzytelniania, zgłoś problem w usłudze *GitHub*
> * Na koniec Jeśli musisz skontaktować się z osobą, możesz chcieć otworzyć żądanie pomocy technicznej

## <a name="search"></a>Search

Jeśli masz pytanie związane z programowaniem, możesz znaleźć odpowiedź w dokumentacji, w witrynie [GitHub przykłady](https://github.com/azure-samples)lub odpowiedzi na pytania [Stack Overflow](https://www.stackoverflow.com) .

### <a name="scoped-search"></a>Wyszukiwanie w zakresie

Aby przyspieszyć wyniki, należy ograniczyć wyszukiwanie do Stack Overflow, dokumentacji i przykładów kodu przy użyciu następującego zapytania w ulubionym aparacie wyszukiwania:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Gdzie *{Twoje terminy wyszukiwania}* odpowiadają Twoim słów kluczowych wyszukiwania.

## <a name="use-the-development-support-tools"></a>Korzystanie z narzędzi obsługi deweloperskiej

| Narzędzie  | Opis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Wklej identyfikator lub token dostępu, aby zdekodować nazwy i wartości oświadczeń. |
| [Eksplorator Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które pozwala na wykonywanie żądań i wyświetlanie odpowiedzi dotyczących interfejsu API Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Opublikuj pytanie w Stack Overflow

Stack Overflow jest preferowanym kanałem dla pytań związanych z programowaniem. W tym miejscu członkowie społeczności deweloperów i członków zespołu firmy Microsoft są bezpośrednio włączeni do rozwiązywania problemów.

Jeśli nie możesz znaleźć odpowiedzi na swoje pytanie za pomocą wyszukiwania, Prześlij nowe pytanie do Stack Overflow. Skorzystaj z jednego z następujących tagów, aby zadawać pytania ułatwiające społeczności identyfikację i szybkie oddawanie odpowiedzi:

|Składnik/obszar  | Tagi |
|---------|---------|
| Biblioteka ADAL | [ADAL](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteka MSAL     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| Oprogramowanie pośredniczące OWIN  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Każdy inny obszar związany z uwierzytelnianiem lub tematami autoryzacji | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Poniższe wpisy z Stack Overflow zawierają wskazówki dotyczące sposobu zadawania pytań i dodawania kodu źródłowego. Postępuj zgodnie z poniższymi wskazówkami, aby zwiększyć szanse, aby członkowie społeczności mogli szybko ocenić swoje pytania i odpowiedzieć na nie:

* [Jak mogę zadać dobre pytanie](https://stackoverflow.com/help/how-to-ask)
* [Jak utworzyć przykład minimalny, pełny i możliwy do zweryfikowania](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Utwórz problem w usłudze GitHub

Jeśli znajdziesz błąd lub problem związany z naszymi bibliotekami, zgłoś problem w naszych repozytoriach usługi GitHub. Ponieważ nasze biblioteki są typu open source, można również przesłać żądanie ściągnięcia.

Aby zapoznać się z listą bibliotek i repozytoriów usługi GitHub, zobacz następujące tematy:

* Biblioteki [ADAL](active-directory-authentication-libraries.md) i repozytoria GitHub
* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. Systemy Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)i [MSAL. obj_c](https://github.com/AzureAD/microsoft-authentication-library-for-objc) i repozytoria GitHub

## <a name="open-a-support-request"></a>Otwórz żądanie obsługi

Jeśli musisz skontaktować się z osobą, możesz otworzyć żądanie pomocy technicznej. Jeśli jesteś klientem platformy Azure, dostępne są różne opcje pomocy technicznej. Aby porównać plany, zobacz [Tę stronę](https://azure.microsoft.com/support/plans/). Pomoc techniczna dla deweloperów jest również dostępna dla klientów platformy Azure. Informacje o sposobach zakupu planów pomocy technicznej dla deweloperów można znaleźć na [tej stronie](https://azure.microsoft.com/support/plans/developer/).

* Jeśli masz już plan pomocy technicznej platformy Azure, [Otwórz tutaj żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Jeśli nie jesteś klientem platformy Azure, możesz również otworzyć żądanie pomocy technicznej w firmie Microsoft za pośrednictwem [pomocy technicznej komercyjnej](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Możesz również wypróbować [agenta wirtualnego](https://support.microsoft.com/contactus/?ws=support) , aby uzyskać pomoc techniczną lub zadawać pytania.
