---
title: Środowisko pulpitu wirtualnego systemu Windows — Azure
description: Podstawowe elementy środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127916"
---
# <a name="windows-virtual-desktop-environment"></a>Środowisko usługi Windows Virtual Desktop

Windows Virtual Desktop to usługa zapewniająca użytkownikom łatwy i bezpieczny dostęp do swoich zwirtualizowanych pulpitów i programów RemoteApp. W tym temacie przedstawiono nieco więcej informacji na temat ogólnej struktury środowiska pulpitu wirtualnego systemu Windows.

## <a name="tenants"></a>Dzierżawcy

Dzierżawa pulpitu wirtualnego systemu Windows to podstawowy interfejs do zarządzania środowiskiem pulpitu wirtualnego systemu Windows. Każda dzierżawa pulpitu wirtualnego systemu Windows musi być skojarzona z Azure Active Directoryą zawierającą użytkowników, którzy będą logować się do środowiska. Z poziomu dzierżawy pulpitu wirtualnego systemu Windows można rozpocząć tworzenie pul hostów w celu uruchamiania obciążeń użytkowników.

## <a name="host-pools"></a>Pule hostów

Pula hostów jest kolekcją maszyn wirtualnych platformy Azure, które są rejestrowane na pulpicie wirtualnym systemu Windows jako hosty sesji podczas uruchamiania agenta pulpitu wirtualnego systemu Windows. Wszystkie maszyny wirtualne hosta sesji w puli hostów powinny być źródłem tego samego obrazu w celu zapewnienia spójnego środowiska użytkownika.

Pula hostów może być jednym z dwóch typów:

- Osobisty, gdzie każdy Host sesji jest przypisany do poszczególnych użytkowników.
- W puli, gdzie hosty sesji mogą akceptować połączenia od dowolnego użytkownika autoryzowanego do grupy aplikacji w puli hostów.

Można ustawić dodatkowe właściwości puli hostów, aby zmienić zachowanie równoważenia obciążenia, ile sesji może upłynąć każdy Host sesji i co użytkownik może zrobić, aby hosty sesji w puli hostów były zalogowane do sesji pulpitu wirtualnego systemu Windows. Możesz kontrolować zasoby publikowane dla użytkowników za pomocą grup aplikacji.

## <a name="app-groups"></a>Grupy aplikacji

Grupa aplikacji to logiczna Grupa aplikacji zainstalowanych na hostach sesji w puli hostów. Grupa aplikacji może być jednym z dwóch typów:

- Funkcja RemoteApp, w której użytkownicy uzyskują dostęp do wybranych przez siebie programów RemoteApp i publikuje je w grupie aplikacji
- Komputery stacjonarne, w których użytkownicy uzyskują dostęp do pełnego pulpitu

Domyślnie grupa aplikacji klasycznych (o nazwie "aplikacja klasyczna") jest tworzona automatycznie za każdym razem, gdy tworzona jest pula hostów. Tę grupę aplikacji można usunąć w dowolnym momencie. Nie można jednak utworzyć innej grupy aplikacji klasycznych w puli hostów, gdy istnieje grupa aplikacji klasycznych. Aby opublikować usługi RemoteApp, należy utworzyć grupę aplikacji usługi RemoteApp. Można utworzyć wiele grup aplikacji usługi RemoteApp, aby uwzględnić różne scenariusze procesów roboczych. Różne grupy aplikacji RemoteApp mogą również zawierać nakładające się usługi RemoteApp.

Aby opublikować zasoby dla użytkowników, należy przypisać je do grup aplikacji. Podczas przypisywania użytkowników do grup aplikacji należy wziąć pod uwagę następujące kwestie:

- Nie można przypisać użytkownika do grupy aplikacji pulpitu i grupy aplikacji RemoteApp w tej samej puli hostów.
- Użytkownik może być przypisany do wielu grup aplikacji w ramach tej samej puli hostów, a ich źródło danych to nagromadzenie obu grup aplikacji.

## <a name="tenant-groups"></a>Grupy dzierżaw

W systemie Windows Virtual Desktop dzierżawca usług pulpitu wirtualnego systemu Windows to miejsce, w którym występuje większość instalacji i konfiguracji. Dzierżawa pulpitu wirtualnego systemu Windows zawiera pule hostów, grupy aplikacji i przypisania użytkowników grupy aplikacji. Mogą jednak wystąpić sytuacje, w których konieczne jest zarządzanie wieloma dzierżawcami pulpitów wirtualnych systemu Windows jednocześnie, szczególnie jeśli jesteś dostawcą usług w chmurze (CSP) lub partnerem hostingu. W takich sytuacjach można użyć niestandardowej grupy dzierżawców pulpitów wirtualnych systemu Windows, aby umieścić każdą dzierżawę pulpitów wirtualnych systemu Windows dla klientów i centralnie zarządzać dostępem. Jeśli jednak zarządzasz tylko jedną dzierżawą usług pulpitu wirtualnego systemu Windows, pojęcie grupy dzierżawców nie ma zastosowania i możesz nadal korzystać z dzierżawy, która istnieje w domyślnej grupie dzierżawców i zarządzać nią.

## <a name="end-users"></a>Użytkownicy końcowi

Po przypisaniu użytkowników do grup aplikacji mogą oni łączyć się z wdrożeniem pulpitu wirtualnego systemu Windows przy użyciu dowolnego klienta pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępie delegowanym oraz o sposobach przypisywania ról użytkownikom z [dostępem delegowanym na pulpicie wirtualnym systemu Windows](delegated-access-virtual-desktop.md).

Aby dowiedzieć się, jak skonfigurować dzierżawcę pulpitów wirtualnych systemu Windows, zobacz [Tworzenie dzierżawy w systemie Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Aby dowiedzieć się, jak nawiązać połączenie z pulpitem wirtualnym systemu Windows, zobacz jeden z następujących artykułów:

- [Łączenie z systemem Windows 10 lub Windows 7](connect-windows-7-and-10.md)
- [Nawiązywanie połączenia z przeglądarki sieci Web](connect-web.md)
