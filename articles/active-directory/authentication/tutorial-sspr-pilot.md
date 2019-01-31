---
title: Włączanie pilotażu usługi Azure AD SSPR
description: W tym samouczku włączysz samoobsługowe resetowanie hasła usługi Azure AD dla pilotażowej grupy użytkowników
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 7b84e36ae447adcf36fae90b61564bc06c3bae89
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081555"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Samouczek: przeprowadzanie pilotażowego wdrożenia samoobsługowego resetowania hasła usługi Azure AD

W tym samouczku przeprowadzisz pilotażowe wdrożenie samoobsługowego resetowania hasła (SSPR) usługi Azure AD w swojej organizacji i przetestujesz je przy użyciu konta bez uprawnień administratora.

Należy pamiętać, że testowanie samoobsługowego resetowania hasła trzeba przeprowadzać za pomocą konta bez uprawnień administratora. Zasadami resetowania hasła dla kont administratorów zarządza firma Microsoft, która wymaga użycia silniejszych metod uwierzytelniania. Te zasady nie zezwalają na zastosowanie pytań zabezpieczających i odpowiedzi oraz wymagają użycia dwóch metod resetowania.

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania hasła
> * Testowanie funkcji SSPR jako użytkownik

## <a name="prerequisites"></a>Wymagania wstępne

* Konto administratora globalnego

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do usługi **Azure Active Directory** i wybierz pozycję **Resetowanie hasła**.
1. Zacznij od grupy pilotażowej, włączając samoobsługowe hasła dla podzbioru użytkowników w Twojej organizacji.
   * Na stronie **Właściwości** w ramach opcji **Funkcja samoobsługowego resetowania hasła jest włączona** wybierz pozycję **Wybrano** i wybierz grupę pilotażową.
      * Z funkcji samoobsługowego resetowania hasła mogą korzystać tylko członkowie określonej, wybranej przez Ciebie grupy usługi Azure AD. Zaleca się zdefiniowanie grupy użytkowników i korzystanie z tego ustawienia podczas wdrażania tej funkcji w celu weryfikacji koncepcji. W tym miejscu jest obsługiwane zagnieżdżanie grup zabezpieczeń.
      * Upewnij się, że użytkownicy w wybranej grupie posiadają prawidłowe licencje.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Metody uwierzytelniania**
   * Dla opcji **Liczba metod wymaganych do zresetowania** ustaw wartość **2**
   * W obszarze **Metody dostępne dla użytkowników** wybierz metody, które będą dozwolone przez organizację. Na potrzeby tego samouczka zaznacz pola **Wiadomość e-mail**, **Telefon komórkowy** i **Telefon biurowy**.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Rejestracja**
   * Wybierz wartość **Tak** dla opcji **Czy wymagać od użytkowników rejestrowania się podczas logowania?**.
   * Dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** ustaw wartość **180**.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Powiadomienia**
   * Dla opcji **Czy powiadamiać użytkowników o resetowaniu hasła?** ustaw wartość **Tak**.
   * Dla opcji **Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło?** ustaw wartość **Tak**.
1. Na stronie **Dostosowywanie**
   * Firma Microsoft zaleca, aby dla opcji **Dostosuj link do pomocy technicznej** ustawić wartość **Tak** i w polu **Niestandardowy adres e-mail lub adres URL pomocy technicznej** podać adres e-mail lub adres URL strony internetowej, gdzie użytkownicy będą mogli uzyskać dodatkową pomoc od organizacji.
   * Na potrzeby tego samouczka dla opcji **Dostosuj link do pomocy technicznej** pozostawimy ustawioną wartość **Nie**.

Samoobsługowe resetowanie hasła dla użytkowników chmury w grupie pilotażowej zostało skonfigurowane.

## <a name="test-sspr-as-a-user"></a>Testowanie funkcji SSPR jako użytkownik

Przetestuj samoobsługowe resetowanie hasła za pomocą konta użytkownika testowego bez uprawnień administratora, będącego członkiem grupy pilotażowej. **Pamiętaj, że jeśli użyjesz konta z przypisanymi jakimikolwiek rolami administratora, metody uwierzytelniania i ich liczba mogą być inne, niż to, co zostało przez Ciebie wybrane, ponieważ zasadami dotyczącymi administratorów zarządza firma Microsoft.**

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito.
1. Korzystając z testowego konta użytkownika, zarejestruj się na potrzeby samoobsługowego resetowania hasła, używając portalu rejestracji pod adresem [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Przy użyciu tego samego testowego konta użytkownika przejdź do portalu samoobsługowego resetowania hasła [https://aka.ms/sspr](https://aka.ms/sspr) i spróbuj zresetować swoje hasło, korzystając z informacji podanych w poprzednim kroku.
1. Próba zresetowania hasła powinna zakończyć się pomyślnie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zdecydujesz, że nie chcesz już korzystać z funkcji skonfigurowanych w ramach tego samouczka, wprowadź następujące zmiany.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do usługi **Azure Active Directory** i wybierz pozycję **Resetowanie hasła**.
1. Na stronie **Właściwości** w ramach opcji **Funkcja samoobsługowego resetowania hasła jest włączona** wybierz pozycję **Brak**.
1. Kliknij pozycję **Zapisz**

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono samoobsługowe resetowanie hasła usługi Azure AD. Przejdź do następnego samouczka, aby zobaczyć, jak można zintegrować lokalną infrastrukturę usług Active Directory Domain Services ze środowiskiem samoobsługowego resetowania hasła.

> [!div class="nextstepaction"]
> [Włączanie integracji zapisu zwrotnego samoobsługowego resetowania hasła ze środowiskiem lokalnym](tutorial-enable-writeback.md)
