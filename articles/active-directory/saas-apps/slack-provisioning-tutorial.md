---
title: 'Samouczek: Konfigurowanie Slack do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizacji i usuwanie kont użytkowników Slack.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 7d6056987ee05f68eecf026e954327a2f62cf886
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348909"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Slack do automatycznej aprowizacji użytkowników


Celem tego samouczka jest Wam kroki należy wykonać w Slack i usługi Azure AD, aby automatycznie aprowizować i anulować obsługę kont użytkowników z usługi Azure AD do Slack. 

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawa usługi Azure Active Directory
*   Slack dzierżawy z [oraz plan](https://aadsyncfabric.slack.com/pricing) lub lepiej nie są włączone 
*   Konto użytkownika Slack z uprawnieniami administratora zespołu 

Uwaga: Inicjowanie obsługi administracyjnej integracji usługi Azure AD zależy od [API Standard SCIM Slack](https://api.slack.com/scim) dostępnej w usłudze Slack, teams na znak Plus zaplanować lub Lepsza.

## <a name="assigning-users-to-slack"></a>Przypisywanie użytkowników do Slack

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście konta użytkownika automatycznego inicjowania obsługi administracyjnej tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane. 

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Slack. Po decyzję, możesz przypisać użytkowników do aplikacji Slack, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Slack

*   Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do Slack do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do Slack, należy wybrać **użytkownika** lub rola "Group" w oknie dialogowym przydział. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.


## <a name="configuring-user-provisioning-to-slack"></a>Konfigurowaniem aprowizowania użytkowników w Slack 

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w Slack aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć przypisany kontach użytkownika w programie Slack, w oparciu o użytkownika i przypisanie do grupy w usłudze Azure AD.

**Porada:** można też włączyć opartej na SAML logowania jednokrotnego dla Slack, zgodnie z instrukcjami podanymi w (witryna Azure portal) [https://portal.azure.com]. Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do Slack w usłudze Azure AD:


1)  W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2) Jeśli już skonfigurowano Slack dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Slack, korzystając z pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Slack** w galerii aplikacji. Wybierz Slack z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3)  Wybierz wystąpienie programu Slack, a następnie wybierz **aprowizacji** kartę.

4)  Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

![Slack, inicjowanie obsługi administracyjnej](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  W obszarze **poświadczeń administratora** kliknij **Autoryzuj**. Spowoduje to otwarcie okna dialogowego Slack autoryzacji w nowym oknie przeglądarki. 

6) W nowym oknie Zaloguj Slack przy użyciu konta administratora zespołu. w oknie dialogowym autoryzacji wynikowe, wybierz Slack zespołu, który chcesz włączyć udostępnianie, a następnie wybierz **Autoryzuj**. Po zakończeniu wróć do witryny Azure portal, aby zakończyć konfigurowanie inicjowania obsługi administracyjnej.

![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/Slack3.PNG)

7) W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z aplikacji Slack. Jeśli połączenie nie powiedzie się, upewnij się, że usługi Slack konto ma uprawnienia administratora zespołu i spróbuj ponownie w kroku "Autoryzuj".

8) Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

9) Kliknij pozycję **Zapisz**. 

10) W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Slack**.

11) W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które będą synchronizowane z usługi Azure AD z usługą Slack. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania kont użytkowników w Slack w przypadku operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12) Aby włączyć usługi Azure AD, inicjowania obsługi usługi Slack, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

13) Kliknij pozycję **Zapisz**. 

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do Slack w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa może potrwać dłużej niż kolejne synchronizacje, które występują co około 10 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do udostępniania raportów działań, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcjonalnie] Konfigurowanie grupy obiektu aprowizacji Slack 

Opcjonalnie można włączyć, aprowizacja obiekty grupy z usługi Azure AD w celu Slack. To różni się od "przypisywanie grup użytkowników", w tym, że rzeczywiste grupy obiektów poza jej składowych będą replikowane z usługi Azure AD do Slack. Na przykład jeśli istnieje grupa o nazwie "My" w usłudze Azure AD, identitical grupy o nazwie "My" zostanie utworzony wewnątrz Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Aby włączyć udostępnianie obiektów grupy:

1) W sekcji mapowania, wybierz **synchronizacji Azure grup usługi Active Directory do Slack**.

2) W bloku mapowanie atrybutu ustawić włączone Yes (tak).

3) W **mapowania atrybutów** Przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD z usługą Slack. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania grup w Slack dla operacji aktualizowania. 

4) Kliknij pozycję **Zapisz**.

Ten wynik w wszystkie obiekty grupy przypisane do Slack w **użytkowników i grup** sekcji, w pełni są synchronizowane z usługi Azure AD do Slack. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Slack.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
