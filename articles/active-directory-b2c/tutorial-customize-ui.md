---
title: 'Samouczek: Dostosowywanie interfejsu użytkownika'
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika aplikacji w Azure Active Directory B2C przy użyciu Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b0ad7049375cb20d2daa0e6f920b680d03530a5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840132"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Samouczek: Dostosowywanie interfejsu środowiska użytkownika w Azure Active Directory B2C

Aby zapoznać się z bardziej typowymi środowiskami użytkowników, takimi jak rejestrowanie, logowanie i edytowanie profilów, można użyć [przepływów użytkowników](user-flow-overview.md) w Azure Active Directory B2C (Azure AD B2C). Informacje przedstawione w tym samouczku ułatwiają zapoznanie się z informacjami na temat [dostosowywania interfejsu użytkownika](customize-ui-overview.md) do tych środowisk przy użyciu własnych plików HTML i CSS.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie plików dostosowania interfejsu użytkownika
> * Aktualizowanie przepływu użytkownika w celu użycia plików
> * Testowanie dostosowanego interfejsu użytkownika

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz przepływ użytkownika](tutorial-create-user-flows.md) , aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie się do niej.

## <a name="create-customization-files"></a>Tworzenie plików dostosowania

Tworzysz konto usługi Azure Storage i kontener, a następnie umieścisz podstawowe pliki HTML i CSS w kontenerze.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Chociaż pliki można przechowywać na wiele sposobów, w tym samouczku można je przechowywać w [usłudze Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, który zawiera twoją subskrypcję. Ten katalog jest inny niż ten, który zawiera dzierżawę usługi Azure B2C.
3. Wybierz pozycję Wszystkie usługi w lewym górnym rogu Azure Portal, Wyszukaj i wybierz pozycję **konta magazynu**.
4. Wybierz pozycję **Dodaj**.
5. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**, wprowadź nazwę nowej grupy zasobów, a następnie kliknij przycisk **OK**.
6. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie platformy Azure, musi mieć długość od 3 do 24 znaków oraz może zawierać tylko cyfry i małe litery.
7. Wybierz lokalizację konta magazynu lub zaakceptuj lokalizację domyślną.
8. Zaakceptuj wszystkie pozostałe wartości domyślne, wybierz pozycję **Przegląd + Utwórz**, a następnie kliknij pozycję **Utwórz**.
9. Po utworzeniu konta magazynu wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-container"></a>Tworzenie kontenera

1. Na stronie Przegląd konta magazynu wybierz pozycję **obiekty blob**.
2. Wybierz pozycję **kontener**, wprowadź nazwę kontenera, wybierz pozycję **obiekt BLOB (Anonimowy dostęp do odczytu tylko dla obiektów BLOB)** , a następnie kliknij przycisk **OK**.

### <a name="enable-cors"></a>Włączanie mechanizmu CORS

 Azure AD B2C kod w przeglądarce korzysta z nowoczesnego i standardowego podejścia do załadowania niestandardowej zawartości z adresu URL określonego w przepływie użytkownika. Współużytkowanie zasobów między źródłami (CORS) umożliwia zażądanie zasobów na stronie sieci Web z innych domen.

1. Z menu wybierz pozycję **CORS**.
2. W przypadku **dozwolonych źródeł**wprowadź `https://your-tenant-name.b2clogin.com`. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. Podczas wprowadzania nazwy dzierżawy należy używać wszystkich małych liter.
3. Dla **dozwolonych metod**wybierz `GET`,`PUT`i `OPTIONS`.
4. Dla **dozwolonych nagłówków**Wprowadź gwiazdkę (*).
5. W przypadku **widocznych nagłówków**Wprowadź gwiazdkę (*).
6. W obszarze **Maksymalny wiek**wprowadź 200.

    ![Strona konfiguracji funkcji CORS w usłudze Azure Blob Storage w Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Kliknij pozycję **Zapisz**.

### <a name="create-the-customization-files"></a>Tworzenie plików dostosowania

Aby dostosować interfejs użytkownika środowiska rejestracji, Zacznij od utworzenia prostego pliku HTML i CSS. Możesz skonfigurować kod HTML w dowolny sposób, ale musi on mieć element **DIV** z identyfikatorem `api`. Na przykład `<div id="api"></div>`. Azure AD B2C wprowadza elementy do kontenera `api`, gdy zostanie wyświetlona strona.

1. W folderze lokalnym Utwórz następujący plik i upewnij się, że zmieniono `your-storage-account` na nazwę konta magazynu i `your-container` na nazwę utworzonego kontenera. Na przykład `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Strona może być zaprojektowana w dowolny sposób, ale element DIV **API** jest wymagany dla dowolnego tworzonego pliku dostosowania html.

3. Zapisz plik jako *Custom-UI. html*.
4. Utwórz następujący prosty kod CSS obejmujący wszystkie elementy na stronie tworzenia konta lub logowania, w tym elementy, które Azure AD B2C wstrzyknąć.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Zapisz plik jako *Style. css*.

### <a name="upload-the-customization-files"></a>Przekaż pliki dostosowania

W tym samouczku zostaną zapisane pliki utworzone na koncie magazynu, dzięki czemu Azure AD B2C mogą uzyskiwać do nich dostęp.

1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, Wyszukaj i wybierz pozycję **konta magazynu**.
2. Wybierz utworzone konto magazynu, wybierz pozycję **obiekty blob**, a następnie wybierz utworzony kontener.
3. Wybierz pozycję **Przekaż**, przejdź do i wybierz plik *Custom-UI. html* , a następnie kliknij przycisk **Przekaż**.

    ![Przekaż stronę obiektu BLOB w portalu, używając przycisku przekazywania i plików wyróżnionych](./media/tutorial-customize-ui/upload-blob.png)

4. Skopiuj adres URL pliku, który został przekazany do użycia w dalszej części tego samouczka.
5. Powtórz kroki 3 i 4 dla pliku *Style. css* .

## <a name="update-the-user-flow"></a>Aktualizowanie przepływu użytkownika

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
2. Wybierz pozycję **przepływy użytkownika (zasady)** , a następnie wybierz pozycję *B2C_1_signupsignin1* przepływ użytkownika.
3. Wybierz pozycję **układy stron**, a następnie w obszarze **ujednolicone rejestrowanie lub logowanie**kliknij pozycję **tak** , aby **użyć niestandardowej zawartości strony**.
4. W polu **Identyfikator URI strony niestandardowej**wprowadź identyfikator URI pliku *Custom-UI. html* , który został zarejestrowany wcześniej.
5. W górnej części strony wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika** i wybierz *B2C_1_signupsignin1* przepływ użytkownika.
2. W górnej części strony kliknij pozycję **Uruchom przepływ użytkownika**.
3. Kliknij przycisk **Uruchom przepływ użytkownika** .

    ![Uruchom stronę przepływu użytkownika dla przepływu użytkownika dotyczącego rejestracji lub logowania](./media/tutorial-customize-ui/run-user-flow.png)

    Powinna zostać wyświetlona strona podobna do poniższego przykładu z elementami wyśrodkowanymi na podstawie utworzonego pliku CSS:

    ![Przeglądarka sieci Web przedstawiająca rejestrowanie lub logowanie na stronie z niestandardowymi elementami interfejsu użytkownika](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie plików dostosowania interfejsu użytkownika
> * Aktualizowanie przepływu użytkownika w celu użycia plików
> * Testowanie dostosowanego interfejsu użytkownika

> [!div class="nextstepaction"]
> [Dostosowanie języka w Azure Active Directory B2C](user-flow-language-customization.md)
