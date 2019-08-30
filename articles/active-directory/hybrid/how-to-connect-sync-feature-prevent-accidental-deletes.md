---
title: 'Azure AD Connect synchronizacji: Zapobiegaj przypadkowemu usuwaniu | Microsoft Docs'
description: W tym temacie opisano funkcję zapobiegania przypadkowym usunięciem (zapobieganie przypadkowemu usuwaniu) w Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c09a751119c1d6effa5795f2dbf7da422b7806
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135796"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect synchronizacji: Zapobieganie przypadkowemu usuwaniu
W tym temacie opisano funkcję zapobiegania przypadkowym usunięciem (zapobieganie przypadkowemu usuwaniu) w Azure AD Connect.

W przypadku instalowania Azure AD Connect Zapobiegaj przypadkowemu usuwaniu jest domyślnie włączone i skonfigurowane tak, aby nie zezwalać na eksport z ponad 500 usunięć. Ta funkcja została zaprojektowana po to, aby zapewnić ochronę przed przypadkowymi zmianami konfiguracji, a także zmianami w katalogu lokalnym, które miałyby wpływ na wielu użytkowników i inne obiekty.

## <a name="what-is-prevent-accidental-deletes"></a>Co uniemożliwia przypadkowe usuwanie
Typowe scenariusze w przypadku wyświetlenia wielu usunięć obejmują:

* Zmiany [filtrowania](how-to-connect-sync-configure-filtering.md) , w których cała [jednostka organizacyjna](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) lub [domena](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nie jest zaznaczona.
* Wszystkie obiekty w jednostce organizacyjnej zostały usunięte.
* Nazwa jednostki organizacyjnej została zmieniona, więc wszystkie obiekty w tej jednostce są uznawane za znajdujące się poza zakresem synchronizacji.

Wartość domyślna z 500 obiektów można zmienić przy użyciu `Enable-ADSyncExportDeletionThreshold`programu PowerShell, który jest częścią modułu AD Sync zainstalowanego z Azure Active Directory Connect. Tę wartość należy skonfigurować w celu dopasowania jej do rozmiaru organizacji. Ponieważ harmonogram synchronizacji jest uruchamiany co 30 minut, wartość jest liczbą usunięć wyświetloną w ciągu 30 minut.

W przypadku zbyt dużej liczby usuniętych etapów, które zostaną wyeksportowane do usługi Azure AD, eksport zostanie zatrzymany i otrzymasz wiadomość e-mail w następujący sposób:

![Zapobieganie przypadkowemu usuwaniu wiadomości e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Witaj (kontakt techniczny). At (Time) usługa Synchronizacja tożsamości wykryła, że liczba usunięć przekroczyła skonfigurowany próg usuwania dla (nazwa organizacji). Całkowita liczba obiektów (Number), które zostały wysłane do usunięcia w tym Synchronizacja tożsamości uruchomieniu. Osiągnięto lub przekroczono skonfigurowaną wartość progową usuwania dla obiektów (Number). Musimy podać potwierdzenie, że te usunięcia powinny zostać przetworzone przed kontynuowaniem. Aby uzyskać więcej informacji o błędzie wymienionym w tej wiadomości e-mail, zobacz zapobieganie przypadkowemu usuwaniu.*
>
> 

Możesz również sprawdzić stan `stopped-deletion-threshold-exceeded` podczas wyszukiwania w **Synchronization Service Manager** interfejsie użytkownika dla profilu eksportu.
![Zapobiegaj przypadkowemu usunięciu interfejsu użytkownika Service Manager synchronizacji](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Jeśli jest to nieoczekiwane, zbadaj i wykonaj akcje naprawcze. Aby zobaczyć, które obiekty mają zostać usunięte, wykonaj następujące czynności:

1. Uruchom **usługę synchronizacji** z menu Start.
2. Przejdź do **łączników**.
3. Wybierz łącznik z typem **Azure Active Directory**.
4. W obszarze **Akcje** po prawej stronie wybierz pozycję **obszar łącznika wyszukiwania**.
5. W podręcznym obszarze **zakres**wybierz opcję **Rozłączono od momentu** , a następnie wybierz godzinę w przeszłości. Kliknij polecenie **Wyszukaj**. Ta strona zawiera widok wszystkich obiektów, które zostaną usunięte. Klikając każdy element, można uzyskać dodatkowe informacje na temat obiektu. Możesz również kliknąć pozycję **ustawienie kolumny** , aby dodać dodatkowe atrybuty, które mają być widoczne w siatce.

![Wyszukaj w obszarze łącznika](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Jeśli nie masz pewności, że wszystkie operacje usuwania są odpowiednie i chcesz przejść do bezpieczniejszej trasy. Można użyć polecenia cmdlet programu PowerShell: `Enable-ADSyncExportDeletionThreshold` do ustawienia nowego progu zamiast wyłączania wartości progowej, co może pozwolić na niepożądane operacje usuwania. 

Jeśli wszystkie usunięcia są odpowiednie, wykonaj następujące czynności:

1. Aby pobrać bieżący próg usunięcia, uruchom polecenie cmdlet `Get-ADSyncExportDeletionThreshold`programu PowerShell. Podaj konto i hasło administratora globalnego usługi Azure AD. Wartość domyślna to 500.
2. Aby tymczasowo wyłączyć tę ochronę i zezwolić na ich usunięcie, uruchom polecenie cmdlet programu PowerShell: `Disable-ADSyncExportDeletionThreshold`. Podaj konto i hasło administratora globalnego usługi Azure AD.
   ![Poświadczenia](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Po wybraniu łącznika Azure Active Directory wybierz akcję **Uruchom** i wybierz pozycję **Eksportuj**.
4. Aby ponownie włączyć ochronę, uruchom polecenie cmdlet programu PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Zastąp 500 wartością zauważoną podczas pobierania bieżącego progu usuwania. Podaj konto i hasło administratora globalnego usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Synchronizacja w programie Azure AD Connect: Omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
