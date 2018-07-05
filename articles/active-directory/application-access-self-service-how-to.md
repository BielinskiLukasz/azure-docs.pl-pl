---
title: Jak skonfigurować przypisanie aplikacji samoobsługowej | Dokumentacja firmy Microsoft
description: Włącz samoobsługowego dostępu do aplikacji umożliwia użytkownikom znajdowanie własnych aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: barbkess
ms.openlocfilehash: 89eb80dbb749b2f50ca3f1fc097c205b62b6b4a4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445300"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Jak skonfigurować przypisanie aplikacji samoobsługowej

Zanim użytkownicy mogą odnajdować samodzielnie aplikacje z panelu dostępu, musisz włączyć **samoobsługowego dostępu do aplikacji** do dowolnych aplikacji, które użytkownicy mogą samodzielnie odnajdywanie i żądania dostępu do.

Ta funkcja jest doskonałym sposobem do umożliwia oszczędność czasu i pieniędzy jako grupa IT i zdecydowanie zaleca się jako część wdrożenia nowoczesnych aplikacji w usłudze Azure Active Directory.

Dzięki tej funkcji można wykonywać następujące czynności:

-   Zezwala użytkownikom na własnym odnajdywanie aplikacji z [panelu dostępu do aplikacji](https://myapps.microsoft.com/) bez bothering grupę IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, dzięki czemu można zobaczyć, który zażądał dostępu, usunięcie dostępu i zarządzać przypisane role.

-   Opcjonalnie zezwolić na osobę zatwierdzającą w firmie można zatwierdzić żądań dostępu do aplikacji, dzięki czemu nie trzeba grupę IT.

-   Opcjonalnie można skonfigurować maksymalnie 10 osób, które mogą zatwierdzać dostęp do tej aplikacji.

-   Opcjonalnie zezwolić na działalność osoby zatwierdzającej do ustawiania hasła tych użytkowników, można użyć zalogować się do aplikacji, po prawej od osoby zatwierdzającej firm [panelu dostępu do aplikacji](https://myapps.microsoft.com/).

-   Opcjonalnie automatycznie przypisywać samoobsługi bezpośrednio przypisać użytkowników do roli aplikacji.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz samoobsługowego dostępu do aplikacji umożliwia użytkownikom znajdowanie własnych aplikacji

Opcjonalnie samoobsługowego dostępu do aplikacji jest to doskonały sposób, aby zezwolić użytkownikom na własnym odnajdywanie aplikacji, umożliwiają grupie biznesowej zatwierdzać dostęp do tych aplikacji. Możesz zezwolić grupie biznesowej do zarządzania poświadczeniami przypisane do tych użytkowników po prawej stronie hasło logowania jednokrotnego w aplikacji w swoich panelach dostępu.

Aby włączyć samoobsługowego dostępu do aplikacji do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **witryny Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu nawigacji głównego po lewej stronie ekranu.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z poziomu menu nawigacji po lewej stronie ekranu w usłudze Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, aby umożliwić Samoobsługowe dostęp do, z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **samoobsługi** z poziomu menu nawigacji po lewej stronie ekranu w aplikacji.

8.  Aby włączyć samoobsługowego dostępu do aplikacji dla tej aplikacji, należy wyłączyć **zezwalać użytkownikom na żądanie dostępu do tej aplikacji?** Przełącz, aby **tak.**

9.  Następnie, aby wybrać grupę, do których użytkownicy, którzy żądają dostępu do tej aplikacji można dodać, kliknij selektor obok etykiety **grupę, do której należy dodać przypisanych użytkowników?** i wybrać grupę.
  
  > [!NOTE]
  > Grupy zsynchronizowane ze środowiska lokalnego nie są obsługiwane do użytku z grupy, do którego należy dodać użytkowników, którzy żądają dostępu do tej aplikacji.
  
10. **Opcjonalnie:** Jeśli użytkownik chce wymagane zatwierdzenie firmy, zanim użytkownicy mogą dostęp, należy ustawić **wymagają zatwierdzenia, zanim zostanie przyznany dostęp do tej aplikacji?** Przełącz, aby **tak**.

11. **Opcjonalnie: dla aplikacji za pomocą logowania jednokrotnego hasła na tylko** Jeśli chcesz zezwolić na te osoby zatwierdzające w firmie określić hasła, które są wysyłane do tej aplikacji dla zatwierdzonych użytkowników, ustawić **zezwalać osobom zatwierdzającym Ustawianie użytkownika hasła dla tej aplikacji?**  Przełącz, aby **tak**.

12. **Opcjonalnie:** do określenia osoby zatwierdzające w firmie, którzy mogą zatwierdzać dostęp do tej aplikacji, kliknij selektor obok etykiety **kto może zatwierdzać dostęp do tej aplikacji?** wybrać maksymalnie 10 osoby osoby zatwierdzające w firmie.

   >[!NOTE]
   >Grupy nie są obsługiwane.
   >
   >

13. **Opcjonalnie:** **dla aplikacji, które ujawniają role**, jeśli chcesz przypisać samoobsługi dla zatwierdzonych użytkowników do roli, kliknij selektor **do jakiej roli powinni być przypisani użytkownicy w tej aplikacji?** Aby wybrać rolę, do której można przypisać tych użytkowników.

14. Kliknij przycisk **Zapisz** znajdujący się u góry bloku, aby zakończyć.

Po ukończeniu konfiguracji samoobsługowego aplikacji użytkownicy mogą przejść do ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/) i kliknij przycisk **+ Dodaj** przycisk, aby znaleźć aplikacje, dla których włączono Samoobsługowe dostęp. Osoby zatwierdzające w firmie również wyświetlone powiadomienie w ich [panelu dostępu do aplikacji](https://myapps.microsoft.com/). Można włączyć wiadomość e-mail z powiadomieniem je, gdy użytkownik poprosi o dostęp do aplikacji, która wymaga zatwierdzenia. 

Te zatwierdzenia obsługuje pojedynczy przepływów pracy, co oznacza, że jeśli określisz wiele osób zatwierdzających, wszelkie jedną osobą zatwierdzającą może osoba zatwierdzająca dostęp do aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](users-groups-roles/groups-self-service-management.md)
