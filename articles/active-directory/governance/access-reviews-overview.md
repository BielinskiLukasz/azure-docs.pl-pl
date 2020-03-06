---
title: Co to są przeglądy dostępu? — Azure Active Directory | Microsoft Docs
description: Za pomocą Azure Active Directory przeglądy dostępu można kontrolować członkostwo w grupach i dostęp do aplikacji, aby zaspokoić inicjatywy dotyczące zarządzania, ryzyka i zgodności w organizacji.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376797"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co to są przeglądy dostępu w usłudze Azure AD?

Przeglądy dostępu Azure Active Directory (Azure AD) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika może być regularnie przeglądany, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.

Oto film wideo, który zawiera krótkie omówienie przeglądów dostępu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Dlaczego przeglądy dostępu są ważne?

Usługa Azure AD umożliwia współpracę wewnętrznie w organizacji oraz z użytkownikami z organizacji zewnętrznych, takich jak partnerzy. Użytkownicy mogą dołączać do grup, zapraszać Gości, łączyć się z aplikacjami w chmurze i zdalnie korzystać z urządzeń służbowych lub osobistych. Wygoda korzystania z możliwości samoobsługi w celu uzyskania lepszych możliwości zarządzania dostępem.

- Jak są dołączeni nowi pracownicy, w jaki sposób upewnić się, że mają odpowiednie prawa dostępu do produktywności?
- Jak ludzie przemieszczają zespoły lub opuszczają firmę, w jaki sposób upewnić się, że ich stary dostęp jest usuwany, szczególnie w przypadku, gdy obejmuje Gości?
- Nadmierne prawa dostępu mogą prowadzić do inspekcji wyników i naruszeń, ponieważ wskazują one brak kontroli dostępu.
- Musisz aktywnie współpracować z właścicielami zasobów, aby zapewnić ich regularne sprawdzenie, kto ma dostęp do swoich zasobów.

## <a name="when-to-use-access-reviews"></a>Kiedy korzystać z przeglądów dostępu?

- **Zbyt wielu użytkowników w uprzywilejowanych rolach:** Dobrym pomysłem jest sprawdzenie, ilu użytkowników ma dostęp administracyjny, ilu z nich są administratorzy globalni, a jeśli istnieją wszyscy zaproszeni goście lub partnerzy, którzy nie zostali usunięci po przypisaniu do wykonywania zadań administracyjnych. Możesz zatwierdzić użytkowników przypisania roli w [rolach usługi Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , takich jak Administratorzy globalni lub [role zasobów platformy Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , takie jak administrator dostępu użytkowników w środowisku [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Gdy Automatyzacja jest niewykonalna:** Możesz tworzyć reguły dynamicznego członkostwa w grupach zabezpieczeń lub grupach pakietu Office 365, ale co zrobić, jeśli dane KADRowe nie są przechowywane w usłudze Azure AD lub jeśli użytkownicy nadal potrzebują dostępu po opuszczeniu grupy w celu przeprowadzenia ich zamiany? Następnie można utworzyć przegląd tej grupy, aby upewnić się, kto nadal musi mieć dostęp.
- **Gdy grupa jest używana w nowym przeznaczeniu:** Jeśli masz grupę, która ma być synchronizowana z usługą Azure AD, lub jeśli planujesz włączenie usługi Salesforce dla wszystkich użytkowników w grupie zespołu sprzedaży, warto polecić właścicielowi grupy przeglądanie członkostwa w grupie przed grupą używaną w innej treści ryzyka.
- **Dostęp do danych o kluczowym znaczeniu dla firmy:** w przypadku niektórych zasobów może być konieczne zaproszenie osób poza nim, aby regularnie się wylogować i uzasadnić, dlaczego potrzebują dostępu do celów inspekcji.
- **Aby zachować listę wyjątków zasad:** W idealnym świecie wszyscy użytkownicy będą korzystać z zasad dostępu w celu zabezpieczenia dostępu do zasobów organizacji. Czasami jednak istnieją przypadki biznesowe, które wymagają wprowadzenia wyjątków. Jako administrator IT możesz zarządzać tym zadaniem, unikać nadzoru wyjątków zasad i zapewniać audytorom dowód, że te wyjątki są regularnie przeglądane.
- **Poproszenie właścicieli grupy o potwierdzenie, że nadal potrzebują Gości w swoich grupach:** Dostęp pracownika może być zautomatyzowany przy użyciu niektórych lokalnych IAM, ale nie zaproszonych Gości. Jeśli grupa daje gościom dostęp do zawartości poufnej, to jest odpowiedzialność właściciela grupy o potwierdzenie, że Goście nadal mają uzasadnione potrzeby biznesowe.
- **Okresowe przeglądy powtarzają się:** Można skonfigurować cykliczne przeglądy dostępu użytkowników przy użyciu skonfigurowanych częstotliwości, takich jak co tydzień, co miesiąc, co kwartał lub co rok, a Recenzenci będą powiadamiani na początku każdego przeglądu. Recenzenci mogą zatwierdzać lub odrzucać dostęp za pomocą przyjaznego interfejsu oraz z pomocą inteligentnych zaleceń.

## <a name="where-do-you-create-reviews"></a>Gdzie można tworzyć przeglądy?

W zależności od tego, co chcesz przejrzeć, możesz utworzyć przegląd dostępu w przeglądach dostępu usługi Azure AD, aplikacjach Azure AD Enterprise (w wersji zapoznawczej) lub w usłudze Azure AD PIM.

| Prawa dostępu użytkowników | Recenzenci mogą być | Przegląd utworzony w | Środowisko recenzenta |
| --- | --- | --- | --- |
| Członkowie grupy zabezpieczeń</br>Członkowie grupy pakietu Office | Określeni recenzenci</br>Właściciele grupy</br>Samoobsługowe przeglądanie | Przeglądy dostępu w usłudze Azure AD</br>Grupy usługi Azure AD | Panel dostępu |
| Przypisane do połączonej aplikacji | Określeni recenzenci</br>Samoobsługowe przeglądanie | Przeglądy dostępu w usłudze Azure AD</br>Aplikacje dla przedsiębiorstw usługi Azure AD (w wersji zapoznawczej) | Panel dostępu |
| Rola usługi Azure AD | Określeni recenzenci</br>Samoobsługowe przeglądanie | [Usługa Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portalu Azure |
| Rola zasobów platformy Azure | Określeni recenzenci</br>Samoobsługowe przeglądanie | [Usługa Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portalu Azure |


## <a name="create-access-reviews"></a>Tworzenie przeglądów dostępu

Aby utworzyć przeglądy dostępu, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać recenzjami dostępu i zalogować się jako Administrator globalny lub administrator użytkowników.

1. Wyszukaj i wybierz **Azure Active Directory**.

      ![Azure Portal Wyszukaj Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Wybierz pozycję **Zarządzanie tożsamościami**.

1. Na stronie Wprowadzenie kliknij przycisk **Utwórz przegląd dostępu** .

   ![Strona początkowa przeglądów dostępu](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Dowiedz się więcej o przeglądach dostępu

Aby dowiedzieć się więcej o tworzeniu i wykonywaniu przeglądów dostępu, Obejrzyj tę krótką wersję demonstracyjną:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Jeśli wszystko jest gotowe do wdrożenia przeglądów dostępu w organizacji, wykonaj następujące kroki w filmie wideo, aby dołączać do nich, szkolić administratorów i utworzyć pierwszy przegląd dostępu.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Ile licencji musi mieć?

Upewnij się, że katalog ma co najmniej tyle licencji na Azure AD — wersja Premium P2, ponieważ masz pracowników, którzy będą wykonywać następujące zadania:

- Użytkownicy i Goście przypisani jako recenzenci
- Użytkownicy i Goście, którzy wykonują własne przeglądy
- Właściciele grup, którzy wykonują przegląd dostępu
- Właściciele aplikacji, którzy wykonują przegląd dostępu

Licencje na Azure AD — wersja Premium P2 **nie** są wymagane dla następujących zadań:

- Dla użytkowników z rolami administratora globalnego lub administratora użytkownika nie są wymagane żadne licencje, które konfigurują przeglądy dostępu, konfigurują ustawienia lub stosują decyzje z przeglądu.

W przypadku każdej płatnej Azure AD — wersja Premiumej licencji P2 przypisanej do jednej z użytkowników Twojej organizacji możesz użyć usługi Azure AD Business-to-Business (B2B), aby zaprosić do pięciu użytkowników-Gości w ramach doliczenia użytkowników zewnętrznych. Ci użytkownicy-Goście mogą również korzystać z funkcji Azure AD — wersja Premium P2. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące licencjonowania współpracy B2B usługi Azure AD](../b2b/licensing-guidance.md).

Aby uzyskać więcej informacji na temat licencji, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Poniżej przedstawiono kilka przykładowych scenariuszy licencjonowania, które mogą pomóc w ustaleniu liczby posiadanych licencji.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Administrator tworzy przegląd dostępu grupy A o 75 użytkowników i 1 właściciela grupy i przypisuje właściciela grupy jako recenzenta. | 1 Licencja dla właściciela grupy jako recenzent | 1 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkowników i 3 grup właścicieli, a także przypisuje trzech właścicieli grup jako recenzentów. | 3 licencje dla każdego właściciela grupy jako recenzentów | 3 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkowników. Sprawia, że jest to samodzielna przegląd. | 500 licencji dla każdego użytkownika jako samoprzeglądający | 500 |
| Administrator tworzy przegląd dostępu grupy C z 50 użytkowników-członków i 25 użytkowników-Gości. Sprawia, że jest to samodzielna przegląd. | 50 licencji dla każdego użytkownika jako samoprzeglądający.<br/>(Goście są objęci wymaganym wskaźnikiem 1:5) | 50 |
| Administrator tworzy przegląd dostępu grupy D z 6 użytkownikami-członkami i 108 użytkowników-Gości. Sprawia, że jest to samodzielna przegląd. | 6 licencji dla każdego użytkownika jako samorecenzentów + 16 dodatkowych licencji obejmujących wszystkich użytkowników-Gości 108 w wymaganym wskaźniku 1:5. 6 licencji, które obejmują 6\*5 = 30 użytkowników-Gości. Pozostałe (108-6\*5) = 78 użytkowników-Gości, 78/5 = 16 dodatkowych licencji są wymagane. W tym przypadku wymagane są 6 + 16 = 22 licencje. | 22 |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
