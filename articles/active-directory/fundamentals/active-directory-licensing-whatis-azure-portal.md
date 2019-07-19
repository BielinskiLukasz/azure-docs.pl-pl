---
title: Co to jest oparte na grupach Licencjonowanie — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Active Directory na podstawie grupy licencji, tym, jak działa i najlepszych rozwiązań.
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41c8edf3985e77e0f67a54013d1b59abbaf72f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297558"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?

Płatne usługi w chmurze firmy Microsoft, takie jak usługa Office 365, pakiet Enterprise Mobility + Security, usługa Dynamics 365 i inne podobne produkty firmy Microsoft, wymagają licencji. Te licencje są przypisywane do każdego użytkownika, który potrzebuje dostępu do tych usług. Aby zarządzać licencjami, administratorzy korzystają z jednego z portali zarządzania (Office lub Azure) i poleceń cmdlet programu PowerShell. Azure Active Directory (Azure AD) to podstawowa infrastruktura, która obsługuje zarządzanie tożsamościami dla wszystkich usług w chmurze firmy Microsoft. Usługa Azure AD przechowuje informacje o stanach przypisania licencji dla użytkowników.

Do tej pory licencje mogły być przypisywane tylko na poziomie pojedynczego użytkownika, co może utrudniać zarządzanie na dużą skalę. Na przykład aby dodać lub usunąć licencje użytkowników na podstawie zmian w organizacji, takich jak dołączenie użytkownika do organizacji lub działu albo opuszczenie organizacji lub działu przez użytkownika, administrator często musi napisać złożony skrypt programu PowerShell. Ten skrypt wykonuje poszczególne wywołania usługi w chmurze.

Aby rozwiązać te problemy, obecnie usługa Azure AD udostępnia licencjonowanie oparte na grupach. Do grupy można przypisać jedną lub więcej licencji produktu. Usługa Azure AD gwarantuje, że licencje są przypisywane do wszystkich członków grupy. Nowym członkom, którzy dołączają do grupy, są przypisywane odpowiednie licencje. Podczas opuszczania grupy te licencje są usuwane. To zarządzanie Licencjonowanie eliminuje konieczność automatyzowania zarządzania licencjami za pośrednictwem programu PowerShell w celu odzwierciedlenia zmian w strukturze organizacji i działu dla poszczególnych użytkowników.

## <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania
Aby korzystać z licencjonowania opartego na grupach, musisz mieć jedną z następujących licencji:

- Subskrypcja płatna lub wersja próbna dla Azure AD — wersja Podstawowa i nowszych

- Płatna lub w wersji próbnej pakietu Office 365 Enterprise E3 lub pakietu Office 365 a3 lub Office 365 i nowszego

### <a name="required-number-of-licenses"></a>Wymagana liczba licencji
W przypadku wszystkich grup przypisanych do licencji trzeba również mieć licencję dla każdego unikatowego elementu członkowskiego. Chociaż nie musisz przypisywać poszczególnych członków grupy licencji, musisz mieć co najmniej wystarczającą liczbę licencji, aby uwzględnić wszystkie elementy członkowskie. Na przykład jeśli masz 1 000 unikatowych członków, którzy są częścią licencjonowanych grup w Twojej dzierżawie, musisz mieć co najmniej 1 000 licencji, aby spełnić warunki umowy licencyjnej.

## <a name="features"></a>Funkcje

Poniżej przedstawiono główne funkcje licencjonowania opartego na grupach:

- Licencje można przypisać do dowolnej grupy zabezpieczeń w usłudze Azure AD. Grupy zabezpieczeń mogą być synchronizowane ze środowiska lokalnego, za pomocą usługi Azure AD Connect. Można również utworzyć grupy zabezpieczeń bezpośrednio w usłudze Azure AD (tak zwane grupy tylko w chmurze) lub automatycznie, za pomocą funkcji grupy dynamicznej usługi Azure AD.

- Po przypisaniu licencji produktu do grupy administrator może wyłączyć poszczególne plany usług w ramach produktu. Zwykle to przypisanie jest wykonywane, gdy organizacja nie jest jeszcze gotowa do rozpoczęcia korzystania z usługi zawartej w produkcie. Na przykład administrator może przypisać usługę Office 365 do działu, ale tymczasowo wyłączyć usługę Yammer.

- Obsługiwane są wszystkie usługi w chmurze firmy Microsoft, które wymagają licencjonowania na poziomie użytkownika. Ta obsługa obejmuje wszystkie produkty pakietu Office 365, Enterprise Mobility + Security i Dynamics 365.

- Licencjonowanie oparte na grupach jest obecnie dostępne tylko za pośrednictwem [Azure Portal](https://portal.azure.com). Jeśli korzystasz głównie z innych portali zarządzania na potrzeby zarządzania użytkownikami i grupami, takimi jak [Microsoft 365 centrum administracyjnym](https://admin.microsoft.com), możesz kontynuować tę czynność. Jednak do zarządzania licencjami na poziomie grupy należy używać witryny Azure Portal.

- Usługa Azure AD automatycznie zarządza modyfikacjami licencji wynikającymi ze zmian członkostwa w grupie. Zazwyczaj modyfikacje licencji zaczynają obowiązywać w ciągu minut od zmiany członkostwa.

- Użytkownik może należeć do wielu grup z określonymi zasadami licencji. Użytkownik może również mieć niektóre licencje przypisane bezpośrednio, poza jakimikolwiek grupami. Wynikowy stan użytkownika jest połączeniem wszystkich przypisanych licencji produktów i usług. Jeśli użytkownik jest przypisany ten sam licencji z wielu źródeł, licencja będzie możliwe tylko raz.

- W niektórych przypadkach nie można przypisać licencji do użytkownika. Na przykład w dzierżawie może być za mało dostępnych licencji lub usługi powodujące konflikt mogły zostać przypisane w tym samym czasie. Administratorzy mają dostęp do informacji o użytkownikach, dla których usługa Azure AD nie mogła w pełni przetworzyć licencji grup. Na podstawie tych informacji mogą następnie wykonać akcję naprawczą.

## <a name="your-feedback-is-welcome"></a>Chętnie poznamy Twoją opinię!

Jeśli masz opinię lub wnioski dotyczące funkcji, udostępnij je za pomocą [forum administratora usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Jak przeprowadzić migrację użytkowników między licencjami produktów za pomocą licencjonowania opartego na grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
