---
title: Zaloguj się do notesy platformy Azure
description: Szybko zalogować się do platformy Azure, notesy i Ustaw identyfikator użytkownika, co daje możliwość dostępu do zapisanych projektów i udostępniać innym użytkownikom notesy.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 4daae4357a7a9577c48edc8643355d319b701944
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881570"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Szybki start: Logowanie się i ustawianie identyfikatora użytkownika

Mimo że można zawsze wyświetlić notesy platformy Azure, bez logowania, musisz zarejestrować się uruchamianie notesów, dostęp do zapisanych projektów i notesy i udostępniać innym użytkownikom notesów programu.

## <a name="sign-in"></a>Logowanie

1. Wybierz **Zaloguj** u góry po prawej stronie [notebooks.azure.com](https://notebooks.azure.com/).

    ![Lokalizacja polecenia Zaloguj się na notesy platformy Azure](media/accounts/sign-in-command.png)

1. Po wyświetleniu monitu wprowadź adres e-mail Account Microsoft pracy lub służbowe konto i wybierz **dalej**. Typy kont zostały opisane na [Twoje konto użytkownika dla usługi Azure notesów](azure-notebooks-user-account.md). Jeśli nie masz Account Microsoft lub chcesz wprowadzić jeden do użycia z notesów usługi Azure, wybierz opcję **utworzyć**:

    ![Utwórz nowe polecenie konta Microsoft w monit logowania](media/accounts/create-new-microsoft-account.png)

1. Wprowadź hasło po wyświetleniu monitu.

1. Jeśli logujesz się po raz pierwszy, notesów usługi Azure z pytaniem o zezwolenie na dostęp do tego konta. Wybierz **tak** aby kontynuować:

    ![Wiersz uprawnień konta](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ustaw identyfikator użytkownika

1. Po pierwszym zalogowaniu masz przypisaną Identyfikatora użytkownika tymczasowych, takich jak "anon-idrca3". Zawsze, gdy masz identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure wyświetli monit o utworzenie własnego Identyfikatora. Identyfikator użytkownika jest używany dowolny adres URL uzyskany na udostępnianie projektów i notebooki, więc wybierz element, który jest unikatowy i zrozumiała dla Ciebie.

    ![Monit o podanie Identyfikatora użytkownika dla notesów usługi Azure](media/accounts/create-user-id.png)

    Jeśli wybierzesz **nie, dziękuję**, notesy platformy Azure w dalszym ciągu wyświetlenie monitu o nazwę użytkownika każdym zalogowaniu. W dowolnym momencie można również ustawić identyfikator użytkownika usługi [profilu użytkownika](azure-notebooks-user-profile.md).

1. Po pomyślnym zalogowaniu notesy platformy Azure przechodzi do strony swojego profilu publicznego, w którym można wybrać **edytować informacje o profilu** Wypełnij pozostałe informacje (Aby uzyskać więcej informacji, zobacz [swój profil i identyfikator użytkownika](azure-notebooks-user-profile.md)):

    ![Widok początkowy strony profilu notesy platformy Azure](media/accounts/profile-page-new.png)

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat "Identyfikator użytkownika jest już w użyciu" spróbować innego identyfikatora. Nazwy użytkowników są unikatowe w obrębie wszystkich kont platformy Azure, notesy i notesy Azure rezerwuje również niektórych identyfikatory użytkownika, takich jak nazwy marki firmy Microsoft.

## <a name="sign-out"></a>Wyloguj

Aby się wylogować, wybierz swoją nazwę użytkownika w prawym górnym rogu strony, a następnie wybierz **Wyloguj**:

![Lokalizacja polecenia wylogowania w notesy platformy Azure](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: Tworzenie i udostępnianie notesu](quickstart-create-share-jupyter-notebook.md)
