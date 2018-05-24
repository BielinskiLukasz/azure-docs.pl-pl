---
title: Informacje o działaniach inspekcji usługi Azure Active Directory (Azure AD) | Microsoft Docs
description: Omówienie działań inspekcji, które mogą być rejestrowane w dziennikach inspekcji usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: be19130f7a2f45049cd7e08bb961ac6cb5aa14ff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-audit-activity-reference"></a>Informacje o działaniach inspekcji usługi Azure AD

Dzięki raportom w usłudze Azure Active Directory (Azure AD) możesz uzyskać wszystkie informacje, które pomogą ustalić działanie środowiska.

Architektura raportowania w usłudze Azure AD obejmuje następujące składniki:

- **Działanie** 
    - **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
    - **Dzienniki inspekcji** — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takich jak użytkownicy, aplikacje, grupy, role, zasady, uwierzytelnienia itp.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz Ryzykowne logowania.
    - **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz Użytkownicy oflagowani w związku z ryzykiem.

W tym artykule wymieniono działania inspekcji, które mogą być rejestrowane w dziennikach inspekcji.
 


## <a name="access-reviews"></a>Przeglądy dostępu

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie katalogami|Dodawanie jednostki administracyjnej|
|Zarządzanie katalogami|Dodawanie elementu członkowskiego do jednostki administracyjnej|
|Zarządzanie katalogami|Usuwanie jednostki administracyjnej|
|Zarządzanie katalogami|Usuwanie elementu członkowskiego z jednostki administracyjnej|
|Zarządzanie katalogami|Aktualizowanie jednostki administracyjnej|
|Zarządzanie katalogami|Administracja|
|Zarządzanie użytkownikami|Operacja katalogu|
|Zarządzanie użytkownikami|Eksportowanie|
|Zarządzanie użytkownikami|Import|
|Zarządzanie użytkownikami|Inne|
|Zarządzanie użytkownikami|Przetwarzanie depozytu|
|Zarządzanie użytkownikami|Akcja reguły synchronizacji|
|Zarządzanie użytkownikami|Dodawanie aplikacji|
|Zarządzanie użytkownikami|Usuwanie aplikacji|
|Zarządzanie użytkownikami|Aktualizowanie aplikacji|
|Zarządzanie użytkownikami|Aktualizowanie trybu logowania jednokrotnego do aplikacji|
|Zarządzanie użytkownikami|Automatyczne przenoszenie haseł|
|Zarządzanie użytkownikami|Dodawanie uprawnień aplikacji w wersji 2|
|Zarządzanie użytkownikami|Tworzenie aplikacji w wersji 1|
|Zarządzanie użytkownikami|Tworzenie aplikacji w wersji 2|
|Zarządzanie użytkownikami|Usuwanie aplikacji w wersji 1|
|Zarządzanie użytkownikami|Usuwanie aplikacji w wersji 2|
|Zarządzanie użytkownikami|Usuwanie udzielenia uprawnień aplikacji w wersji 2|
|Zarządzanie użytkownikami|Pobieranie aplikacji w wersji 1 i 2|
|Zarządzanie użytkownikami|Pobieranie aplikacji w wersji 1|
|Zarządzanie użytkownikami|Pobieranie aplikacji w wersji 1|
|Zarządzanie użytkownikami|Pobieranie aplikacji w wersji 2|
|Zarządzanie użytkownikami|Pobieranie aplikacji w wersji 2|




## <a name="account-provisioning"></a>Aprowizacja kont

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji w wersji 1|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji w wersji 2|
|Zarządzanie aplikacjami|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Dodawanie elementu OAuth2PermissionGrant|
|Zarządzanie aplikacjami|Dodawanie przypisania roli aplikacji do jednostki usługi|

## <a name="application-proxy"></a>Serwer proxy aplikacji

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Dodawanie aplikacji|
|Zarządzanie aplikacjami|Dodawanie właściciela do aplikacji|
|Zarządzanie aplikacjami|Dodawanie właściciela do jednostki usługi|
|Zarządzanie aplikacjami|Dodawanie zasad do jednostki usługi|
|Zarządzanie katalogami|Dodawanie jednostki usługi|
|Zarządzanie katalogami|Dodawanie poświadczeń jednostki usługi|
|Zarządzanie katalogami|Zgoda na aplikację|
|Zarządzanie katalogami|Usuwanie aplikacji|
|Zarządzanie katalogami|Całkowite usuwanie aplikacji|
|Zarządzanie katalogami|Usuwanie elementu OAuth2PermissionGrant|
|Zarządzanie katalogami|Usuwanie przypisania roli aplikacji z jednostki usługi|
|Zarządzanie katalogami|Usuwanie właściciela z aplikacji|
|Zasób|Usuwanie właściciela z jednostki usługi|
|Zasób|Usuwanie zasad z jednostki usługi|
|Zasób|Usuwanie jednostki usługi|


## <a name="automated-password-rollover"></a>Automatyczne przenoszenie haseł

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Usuwanie poświadczeń jednostki usługi|


## <a name="b2c"></a>B2C

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie aplikacjami|Przywracanie aplikacji|
|Zarządzanie aplikacjami|Wycofywanie zgody|
|Zarządzanie aplikacjami|Aktualizowanie aplikacji|
|Zarządzanie aplikacjami|Aktualizowanie zewnętrznych wpisów tajnych|
|Zarządzanie aplikacjami|Aktualizowanie jednostki usługi|
|Zarządzanie aplikacjami|Wystawianie tokenu dostępu dla aplikacji|
|Zarządzanie aplikacjami|Wystawianie kodu autoryzacji dla aplikacji|
|Zarządzanie aplikacjami|Wystawianie elementu id_token dla aplikacji|
|Zarządzanie aplikacjami|Weryfikacja poświadczeń konta lokalnego|
|Zarządzanie aplikacjami|Weryfikacja uwierzytelniania użytkownika|
|Zarządzanie aplikacjami|Dodawanie uprawnień aplikacji w wersji 2|
|Zarządzanie aplikacjami|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|Zarządzanie aplikacjami|Dodawanie klucza do kontenera kluczy CPIM|
|Zarządzanie aplikacjami|AdminPolicyDatas-SetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-GetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Tworzenie dostawcy IdentityProvider|
|Authentication|Tworzenie aplikacji w wersji 1|
|Authentication|Tworzenie aplikacji w wersji 2|
|Authentication|Tworzenie domen niestandardowych w ramach dzierżawy|
|Autoryzacja|Tworzenie nowego elementu AdminUserJourney|
|Autoryzacja|Tworzenie zlokalizowanego pliku JSON zasobu|
|Autoryzacja|Tworzenie nowego niestandardowego dostawcy tożsamości|
|Autoryzacja|Tworzenie nowego dostawcy tożsamości|
|Autoryzacja|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|Autoryzacja|Tworzenie zasad|
|Autoryzacja|Tworzenie zasad trustFramework|
|Autoryzacja|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|Autoryzacja|Tworzenie atrybutu użytkownika|
|Autoryzacja|CreateTrustFrameworkPolicy|
|Autoryzacja|Tworzenie lub aktualizowanie nowego elementu AdminUserJourney|
|Autoryzacja|Usuwanie dostawcy tożsamości|
|Autoryzacja|Usuwanie dostawcy tożsamości|
|Autoryzacja|Usuwanie aplikacji w wersji 1|
|Autoryzacja|Usuwanie aplikacji w wersji 2|
|Autoryzacja|Usuwanie udzielenia uprawnień aplikacji w wersji 2|
|Autoryzacja|Usuwanie zasobu katalogu B2C|
|Autoryzacja|Usuwanie kontenera kluczy CPIM|
|Autoryzacja|Usuwanie zasad trustFramework|
|Autoryzacja|Usuwanie atrybutu użytkownika|
|Autoryzacja|Włączanie funkcji B2C|
|Autoryzacja|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|Autoryzacja|Pobieranie niestandardowego dostawcy tożsamości|
|Autoryzacja|Pobieranie dostawcy tożsamości|
|Autoryzacja|Pobieranie aplikacji w wersji 1 i 2|
|Autoryzacja|Pobieranie aplikacji w wersji 1|
|Autoryzacja|Pobieranie aplikacji w wersji 1|
|Autoryzacja|Pobieranie aplikacji w wersji 2|
|Autoryzacja|Pobieranie aplikacji w wersji 2|
|Autoryzacja|Pobieranie zasobu katalogu B2C|
|Autoryzacja|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|Autoryzacja|Pobieranie podróży użytkownika|
|Autoryzacja|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|Autoryzacja|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|Autoryzacja|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|Autoryzacja|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|Autoryzacja|Pobieranie definicji zawartości dla podróży użytkownika|
|Autoryzacja|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|Autoryzacja|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|Autoryzacja|Pobieranie listy wszystkich przepływów administracyjnych|
|Autoryzacja|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|Autoryzacja|Pobieranie listy dzierżaw dla użytkownika|
|Autoryzacja|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|Autoryzacja|Pobieranie zlokalizowanego pliku JSON zasobu|
|Autoryzacja|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|Autoryzacja|Pobieranie zasad|
|Autoryzacja|Pobranie zasad|
|Autoryzacja|Pobieranie właściwości zasobów dzierżawy|
|Autoryzacja|Pobieranie listy obsługiwanych dostawców tożsamości|
|Autoryzacja|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|Autoryzacja|Pobieranie informacji o dzierżawie|
|Autoryzacja|Pobieranie dozwolonych funkcji dzierżawy|
|Autoryzacja|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Autoryzacja|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Autoryzacja|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Autoryzacja|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|Autoryzacja|Pobieranie listy dzierżaw|
|Autoryzacja|Pobieranie domen dzierżaw|
|Autoryzacja|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|Autoryzacja|Pobieranie szczegółów przepływu administracyjnego|
|Autoryzacja|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|Autoryzacja|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|Autoryzacja|Pobieranie zasad trustFramework|
|Autoryzacja|Pobieranie zasad trustFramework w formacie XML|
|Autoryzacja|Pobieranie atrybutu użytkownika|
|Autoryzacja|Pobieranie atrybutów użytkowników|
|Autoryzacja|Pobieranie listy podróży użytkowników|
|Autoryzacja|GetIEFPolicies|
|Autoryzacja|GetIdentityProviders|
|Autoryzacja|GetTrustFrameworkPolicy|
|Autoryzacja|Pobiera kontener kluczy CPIM w formacie JWK|
|Autoryzacja|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|Autoryzacja|Pobiera typ dzierżawy|
|Autoryzacja|MigrateTenantMetadata|
|Autoryzacja|Stosowanie poprawek względem dostawcy tożsamości|
|Autoryzacja|PutTrustFrameworkPolicy|
|Autoryzacja|PutTrustFrameworkpolicy|
|Autoryzacja|Usuwanie podróży użytkownika|
|Autoryzacja|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|Autoryzacja|Pobieranie udzieleń uprawnień aplikacji w wersji 2|
|Autoryzacja|Pobieranie jednostek usług aplikacji w wersji 2 w ramach bieżącej dzierżawy|
|Autoryzacja|Aktualizowanie niestandardowego dostawcy tożsamości|
|Autoryzacja|Aktualizowanie dostawcy tożsamości|
|Autoryzacja|Aktualizowanie lokalnego dostawcy tożsamości|
|Autoryzacja|Aktualizowanie aplikacji w wersji 1|
|Autoryzacja|Aktualizowanie aplikacji w wersji 2|
|Autoryzacja|Aktualizowanie udzielenia uprawnień aplikacji w wersji 2|
|Autoryzacja|Aktualizowanie zasad|
|Autoryzacja|Aktualizowanie atrybutu użytkownika|
|Autoryzacja|Przekazywanie zaszyfrowanego klucza CPIM|
|Autoryzacja|Autoryzacja użytkownika: interfejs API jest wyłączony dla zestawu funkcji dzierżawy|
|Autoryzacja|Autoryzacja użytkownika: użytkownik udzielił dostępu jako „Administrator dzierżawy”|
|Autoryzacja|Autoryzacja użytkownika: użytkownikowi udzielono praw dostępu „Uwierzytelnieni użytkownicy”|
|Autoryzacja|Weryfikowanie, czy funkcja B2C jest włączona|
|Autoryzacja|Weryfikowanie, czy funkcja jest włączona|
|Autoryzacja|Tworzenie programu|
|Autoryzacja|Usuwanie programu|
|Autoryzacja|Połączenie kontroli programu|
|Autoryzacja|Dodawanie do przeglądów dostępu usługi Azure AD|
|Autoryzacja|Odłączanie kontroli programu|
|Autoryzacja|Aktualizowanie programu|
|Autoryzacja|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych|
|Autoryzacja|Wyłączanie logowania jednokrotnego dla aplikacji klasycznych na potrzeby określonej domeny|
|Autoryzacja|Wyłączanie serwera proxy aplikacji|
|Autoryzacja|Wyłączanie uwierzytelniania przekazującego|
|Autoryzacja|Włączanie logowania jednokrotnego dla aplikacji klasycznych|
|Zarządzanie katalogami|Włączanie logowania jednokrotnego dla aplikacji klasycznych dla określonej domeny|
|Zarządzanie katalogami|Włączanie serwera proxy aplikacji|
|Zarządzanie katalogami|Włączanie uwierzytelniania przekazującego|
|Zarządzanie katalogami|Tworzenie domen niestandardowych w ramach dzierżawy|
|Zarządzanie katalogami|Włączanie funkcji B2C|
|Zarządzanie katalogami|Pobieranie listy domen niestandardowych w ramach dzierżawy|
|Zarządzanie katalogami|Pobieranie właściwości zasobów dzierżawy|
|Zarządzanie katalogami|Pobieranie informacji o dzierżawie|
|Zarządzanie katalogami|Pobieranie dozwolonych funkcji dzierżawy|
|Zarządzanie katalogami|Pobieranie domen dzierżaw|
|Klucz|Pobiera typ dzierżawy|
|Klucz|Weryfikowanie, czy funkcja B2C jest włączona|
|Klucz|Weryfikowanie, czy funkcja jest włączona|
|Klucz|Dodawanie partnera do firmy|
|Klucz|Dodawanie niezweryfikowanej domeny|
|Klucz|Dodawanie zweryfikowanej domeny|
|Klucz|Tworzenie firmy|
|Klucz|Tworzenie ustawień firmy|
|Klucz|Usuwanie ustawień firmy|
|Klucz|Obniżanie poziomu partnera|
|Klucz|Usunięto katalog|
|Inne|Trwale usunięto katalog|
|Inne|Katalog zaplanowany do usunięcia|
|Zasób|Podwyższanie poziomu firmy do partnera|
|Zasób|Przeczyszczanie właściwości usługi Rights Management|
|Zasób|Usuwanie partnera z firmy|
|Zasób|Usuwanie niezweryfikowanej domeny|
|Zasób|Usuwanie zweryfikowanej domeny|
|Zasób|Ustaw informacje o firmie|
|Zasób|Ustawianie funkcji Dirsync|
|Zasób|Ustawianie flagi Dirsyncenabled|
|Zasób|Ustaw partnerstwo|
|Zasób|Ustawianie progu przypadkowego usunięcia|
|Zasób|Ustawianie lokalizacji danych dozwolonych przez firmę|
|Zasób|Włączanie funkcji międzynarodowej firmy|
|Zasób|Ustawianie funkcji katalogu w dzierżawie|
|Zasób|Ustawianie uwierzytelniania domeny|
|Zasób|Określanie ustawień federacyjnych w domenie|
|Zasób|Ustawianie zasad haseł|
|Zasób|Ustawianie właściwości usługi Rights Management|
|Zasób|Aktualizowanie firmy|
|Zasób|Aktualizowanie ustawień firmy|
|Zasób|Aktualizowanie domeny|
|Zasób|Weryfikowanie domeny|
|Zasób|Sprawdzanie domeny zweryfikowanej za pomocą wiadomości e-mail|
|Zasób|Dołączanie|
|Zasób|Aktualizowanie ustawień alertów|
|Zasób|Aktualizowanie ustawień podsumowania tygodniowego|
|Zasób|Wyłączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Zasób|Włączanie funkcji zapisywania zwrotnego haseł dla katalogu|
|Zasób|Dodawanie przypisania roli aplikacji do grupy|
|Zasób|Dodawanie grupy|
|Zasób|Dodawanie elementu członkowskiego do grupy|
|Zasób|Dodawanie właściciela do grupy|
|Zasób|Tworzenie ustawień grupy|
|Zasób|Usuwanie grupy|
|Zasób|Usuwanie ustawień grupy|
|Zasób|Kończenie stosowania licencji opartej na grupie wobec użytkowników|
|Zasób|Całkowite usuwanie grupy|
|Zasób|Usuwanie przypisania roli aplikacji z grupy|
|Zasób|Usuwanie elementu członkowskiego z grupy|
|Zasób|Usuwanie właściciela z grupy|
|Zasób|Przywracanie grupy|
|Zasób|Ustawianie licencji grupy|
|Zasób|Ustawianie grupy do zarządzania przez użytkownika|
|Zasób|Rozpoczynanie stosowania licencji opartej na grupie wobec użytkowników|
|Zasób|Wyzwalanie ponownego obliczania licencji grupy|
|Zasób|Aktualizowanie grupy|
|Zasób|Aktualizowanie ustawień grupy|
|Zasób|Dodaj członka|
|Zasób|Tworzenie grupy|
|Zasób|Usuń grupę|
|Zasób|Usuń członka|
|Zasób|Zaktualizuj grupę|
|Zasób|Zatwierdzanie oczekującego żądania dołączenia do grupy|
|Zasób|Anulowanie oczekującego żądania dołączenia do grupy|
|Zasób|Tworzenie zasad zarządzania cyklem życia|
|Zasób|Usuwanie oczekującego żądania dołączenia do grupy|
|Zasób|Odrzucanie oczekującego żądania dołączenia do grupy|
|Zasób|Odnawianie grupy|
|Zasób|Żądanie dołączenia do grupy|
|Zasób|Ustawianie właściwości grupy dynamicznej|
|Zasób|Aktualizowanie zasad zarządzania cyklem życia|
|Zasób|Dodawanie klucza opartego na wpisie tajnym w formacie ASCII do kontenera kluczy CPIM|
|Zasób|Dodawanie klucza do kontenera kluczy CPIM|
|Zasób|Usuwanie kontenera kluczy CPIM|
|Zasób|Usuwanie kontenera kluczy|
|Zasób|Pobieranie metadanych klucza aktywnego kontenera kluczy w formacie JWK|
|Zasób|Pobranie metadanych kontenera kluczy|
|Zasób|Pobiera kontener kluczy CPIM w formacie JWK|
|Zasób|Pobiera listę kontenerów kluczy w ramach dzierżawy|
|Zasób|Przywracanie kopii zapasowej kontenera kluczy CPIM|
|Zasób|Zapisywanie kontenera kluczy|
|Zasób|Przekazywanie zaszyfrowanego klucza CPIM|
|Zasób|Wystawianie kodu autoryzacji dla aplikacji|
|Zasób|Wystawianie elementu id_token dla aplikacji|


## <a name="core-directory"></a>Katalog podstawowy

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie jednostkami administracyjnymi|Pobieranie pojedynczego typu zdarzenia o podwyższonym ryzyku|
|Zarządzanie jednostkami administracyjnymi|Pobieranie administratorów i stanu zgody na podsumowanie tygodniowe|
|Zarządzanie jednostkami administracyjnymi|Pobieranie wszystkich typów zdarzeń o podwyższonym ryzyku|
|Zarządzanie jednostkami administracyjnymi|Pobieranie zdarzeń o podwyższonym ryzyku dla użytkowników wersji bezpłatnych|
|Zarządzanie jednostkami administracyjnymi|Pobieranie użytkowników oflagowanych w związku z ryzykiem|
|Zarządzanie aplikacjami|Przetworzone zaproszenia zbiorcze |
|Zarządzanie aplikacjami|Przekazane zaproszenia zbiorcze|
|Zarządzanie aplikacjami|Dodawanie właściciela do zasad|
|Zarządzanie aplikacjami|Dodawanie zasad|
|Zarządzanie aplikacjami|Usuwanie zasad|
|Zarządzanie aplikacjami|Usuwanie poświadczeń zasad|
|Zarządzanie aplikacjami|Aktualizowanie zasad|
|Zarządzanie aplikacjami|Ustawianie zasad rejestracji usługi MFA|
|Zarządzanie aplikacjami|Ustawianie zasad dotyczących ryzyka związanego z logowaniem|
|Zarządzanie aplikacjami|Ustawianie zasad dotyczących ryzyka związanego z użytkownikiem|
|Zarządzanie aplikacjami|Akceptowanie warunków użytkowania|
|Zarządzanie aplikacjami|Tworzenie warunków użytkowania|
|Zarządzanie aplikacjami|Odrzucanie warunków użytkowania|
|Zarządzanie aplikacjami|Usuwanie warunków użytkowania|
|Zarządzanie aplikacjami|Edytowanie warunków użytkowania|
|Zarządzanie aplikacjami|Publikowanie warunków użytkowania|
|Zarządzanie aplikacjami|Cofanie publikacji warunków użytkowania|
|Zarządzanie aplikacjami|Dodawanie certyfikatu SSL aplikacji|
|Zarządzanie aplikacjami|Usuwanie powiązania SSL|
|Zarządzanie aplikacjami|Rejestrowanie łącznika|
|Zarządzanie aplikacjami|AdminPolicyDatas-RemoveResources|
|Zarządzanie aplikacjami|AdminPolicyDatas-SetResources|
|Zarządzanie aplikacjami|AdminUserJourneys-GetResources|
|Zarządzanie katalogami|AdminUserJourneys-RemoveResources|
|Zarządzanie katalogami|AdminUserJourneys-SetResources|
|Zarządzanie katalogami|Tworzenie dostawcy IdentityProvider|
|Zarządzanie katalogami|Tworzenie nowego elementu AdminUserJourney|
|Zarządzanie katalogami|Tworzenie zlokalizowanego pliku JSON zasobu|
|Zarządzanie katalogami|Tworzenie nowego niestandardowego dostawcy tożsamości|
|Zarządzanie katalogami|Tworzenie nowego dostawcy tożsamości|
|Zarządzanie katalogami|Tworzenie lub aktualizowanie zasobu katalogu B2C|
|Zarządzanie katalogami|Tworzenie zasad|
|Zarządzanie katalogami|Tworzenie zasad trustFramework|
|Zarządzanie katalogami|Tworzenie zasad trustFramework przy użyciu konfigurowalnego prefiksu|
|Zarządzanie katalogami|Tworzenie atrybutu użytkownika|
|Zarządzanie katalogami|CreateTrustFrameworkPolicy|
|Zarządzanie katalogami|Usuwanie dostawcy tożsamości|
|Zarządzanie katalogami|Usuwanie dostawcy tożsamości|
|Zarządzanie katalogami|Usuwanie zasobu katalogu B2C|
|Zarządzanie katalogami|Usuwanie zasad trustFramework|
|Zarządzanie katalogami|Usuwanie atrybutu użytkownika|
|Zarządzanie katalogami|Pobieranie zasobów katalogu B2C w grupie zasobów|
|Zarządzanie katalogami|Pobieranie zasobów katalogu B2C w ramach subskrypcji|
|Zarządzanie katalogami|Pobieranie niestandardowego dostawcy tożsamości|
|Zarządzanie katalogami|Pobieranie dostawcy tożsamości|
|Zarządzanie katalogami|Pobieranie zasobu katalogu B2C|
|Zarządzanie katalogami|Pobieranie podróży użytkownika|
|Zarządzanie katalogami|Pobieranie dozwolonych oświadczeń aplikacji dla podróży użytkownika|
|Zarządzanie katalogami|Pobieranie dozwolonych i samodzielnie określonych oświadczeń dla podróży użytkownika|
|Zarządzanie katalogami|Pobieranie dozwolonych i samodzielnie określonych oświadczeń zasad|
|Zarządzanie katalogami|Pobieranie listy dozwolonych oświadczeń wyjściowych|
|Zarządzanie katalogami|Pobieranie definicji zawartości dla podróży użytkownika|
|Zarządzanie katalogami|Pobieranie dostawców tożsamości dla określonego przepływu administracyjnego|
|Zarządzanie katalogami|Pobieranie listy wszystkich przepływów administracyjnych|
|Zarządzanie katalogami|Pobieranie listy tagów wszystkich przepływów administracyjnych dla wszystkich użytkowników|
|Zarządzanie grupami|Pobieranie listy dzierżaw dla użytkownika|
|Zarządzanie grupami|Pobieranie samodzielnie określonych oświadczeń kont lokalnych|
|Zarządzanie grupami|Pobieranie zlokalizowanego pliku JSON zasobu|
|Zarządzanie grupami|Pobieranie operacji dostawcy zasobów Microsoft.AzureActiveDirectory|
|Zarządzanie grupami|Pobieranie zasad|
|Zarządzanie grupami|Pobranie zasad|
|Zarządzanie grupami|Pobieranie listy obsługiwanych dostawców tożsamości|
|Zarządzanie grupami|Pobieranie listy obsługiwanych dostawców tożsamości podróży użytkownika|
|Zarządzanie grupami|Pobranie listy niestandardowych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Pobranie listy dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Pobranie listy lokalnych dostawców tożsamości zdefiniowanych w ramach dzierżawy|
|Zarządzanie grupami|Uzyskiwanie szczegółów dzierżawy dla użytkownika na potrzeby tworzenia zasobów|
|Zarządzanie grupami|Pobieranie domyślnej obsługiwanej kultury dla elementu CPIM|
|Zarządzanie grupami|Pobieranie szczegółów przepływu administracyjnego|
|Zarządzanie grupami|Pobieranie listy podróży użytkowników dla tej dzierżawy|
|Zarządzanie grupami|Pobieranie zestawu dostępnych i obsługiwanych kultur dla elementu CPIM|
|Zarządzanie grupami|Pobieranie zasad trustFramework|
|Zarządzanie grupami|Pobieranie zasad trustFramework w formacie XML|
|Zarządzanie grupami|Pobieranie atrybutu użytkownika|
|Zarządzanie zasadami|Pobieranie atrybutów użytkowników|
|Zarządzanie zasadami|Pobieranie listy podróży użytkowników|
|Zarządzanie zasadami|GetIEFPolicies|
|Zarządzanie zasadami|GetIdentityProviders|
|Zarządzanie zasadami|GetTrustFrameworkPolicy|
|Zasób|MigrateTenantMetadata|
|Zasób|Przenoszenie zasobów|
|Zasób|Stosowanie poprawek względem dostawcy tożsamości|
|Zasób|PutTrustFrameworkPolicy|
|Zasób|PutTrustFrameworkpolicy|
|Zasób|Usuwanie podróży użytkownika|
|Zasób|Aktualizowanie niestandardowego dostawcy tożsamości|
|Zasób|Aktualizowanie dostawcy tożsamości|
|Zasób|Aktualizowanie lokalnego dostawcy tożsamości|
|Zasób|Aktualizowanie zasobu katalogu B2C|
|Zasób|Aktualizowanie zasad|
|Zasób|Aktualizowanie stanu subskrypcji|
|Zarządzanie rolami|Aktualizowanie atrybutu użytkownika|
|Zarządzanie rolami|Weryfikacja przenoszenia zasobów|
|Zarządzanie rolami|Dodawanie urządzenia|
|Zarządzanie rolami|Dodawanie konfiguracji urządzenia|
|Zarządzanie rolami|Dodawanie zarejestrowanego właściciela do urządzenia|
|Zarządzanie rolami|Dodawanie zarejestrowanych użytkowników do urządzenia|
|Zarządzanie rolami|Usuwanie urządzenia|
|Zarządzanie rolami|Usuwanie konfiguracji urządzenia|
|Zarządzanie rolami|Urządzenie, które nie jest już zgodne|
|Zarządzanie rolami|Urządzenie, które nie jest już zarządzane|
|Zarządzanie użytkownikami|Usuwanie zarejestrowanego właściciela z urządzenia|
|Zarządzanie użytkownikami|Usuwanie zarejestrowanych użytkowników z urządzenia|
|Zarządzanie użytkownikami|Aktualizowanie urządzenia|
|Zarządzanie użytkownikami|Aktualizowanie konfiguracji urządzenia|
|Zarządzanie użytkownikami|Dodawanie kwalifikujących się elementów członkowskich do roli|
|Zarządzanie użytkownikami|Dodawanie elementu członkowskiego do roli|
|Zarządzanie użytkownikami|Dodawanie przypisania roli do definicji roli|
|Zarządzanie użytkownikami|Dodawanie roli z szablonu|
|Zarządzanie użytkownikami|Dodawanie członka w zakresie do roli|
|Zarządzanie użytkownikami|Usuwanie kwalifikującego się elementu członkowskiego z roli|
|Zarządzanie użytkownikami|Usuwanie elementu członkowskiego z roli|
|Zarządzanie użytkownikami|Usuwanie przypisania roli z definicji roli|
|Zarządzanie użytkownikami|Usuwanie elementu członkowskiego w zakresie z roli|
|Zarządzanie użytkownikami|Aktualizowanie roli|
|Zarządzanie użytkownikami|AccessReview_Review|
|Zarządzanie użytkownikami|AccessReview_Update|
|Zarządzanie użytkownikami|ActivationAborted|
|Zarządzanie użytkownikami|ActivationApproved|
|Zarządzanie użytkownikami|ActivationCanceled|
|Zarządzanie użytkownikami|ActivationRequested|
|Zarządzanie użytkownikami|Dodano|
|Zarządzanie użytkownikami|Przypisywanie|


## <a name="identity-protection"></a>Ochrona tożsamości

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie katalogami|Podnoszenie poziomu uprawnień|
|Zarządzanie katalogami|Usunięto|
|Zarządzanie katalogami|Zmiany ustawienia roli|
|Inne|ScanAlertsNow|
|Inne|Rejestracja|
|Inne|Obniżanie poziomu uprawnień|
|Inne|UpdateAlertSettings|
|Inne|UpdateCurrentState|
|Zarządzanie zasadami|Zakończono przegląd dostępu|
|Zarządzanie zasadami|Dodawanie osoby zatwierdzającej do zatwierdzenia żądania|
|Zarządzanie zasadami|Dodawanie recenzenta do przeglądu dostępu|
|Zarządzanie użytkownikami|Zastosowanie przeglądu dostępu|
|Zarządzanie użytkownikami|Tworzenie przeglądu dostępu|


## <a name="invited-users"></a>Zaproszeni użytkownicy

|Kategorii inspekcji|Działanie|
|---|---|
|Inne|Tworzenie zatwierdzenia żądania|
|Inne|Usuwanie przeglądu dostępu|
|Zarządzanie użytkownikami|Usuwanie recenzenta z przeglądu dostępu|
|Zarządzanie użytkownikami|Żądanie zastosowania wyniku przeglądu|
|Zarządzanie użytkownikami|Żądanie zatrzymania przeglądu|
|Zarządzanie użytkownikami|Przeglądanie przypisania aplikacji|
|Zarządzanie użytkownikami|Przeglądanie członkostwa w grupie|
|Zarządzanie użytkownikami|Przeglądanie członkostwa w roli funkcji RBAC|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie grupami|Przeglądanie żądania zatwierdzenia żądania|
|Zarządzanie grupami|Aktualizowanie przeglądu dostępu|
|Zarządzanie grupami|Aktualizowanie ustawień powiadomień e-mail dla przeglądu dostępu|
|Zarządzanie grupami|Aktualizowanie ustawienia licznika cyklu przeglądu dostępu|
|Zarządzanie grupami|Aktualizowanie ustawienia czasu trwania cyklu przeglądu dostępu (w dniach)|
|Zarządzanie użytkownikami|Aktualizowanie ustawienia typu zakończenia cyklu przeglądu dostępu|
|Zarządzanie użytkownikami|Aktualizowanie ustawienia typu cyklu przeglądu dostępu|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie rolami|Aktualizowanie ustawień przypomnienia przeglądu dostępu|
|Zarządzanie rolami|Aktualizowanie zatwierdzenia żądania|
|Zarządzanie rolami|Dodawanie udzielenia przypisania roli aplikacji do użytkownika|
|Zarządzanie rolami|Dodawanie użytkownika|
|Zarządzanie rolami|Dodawanie szczegółów dotyczących aplikacji telefonicznej do silnego uwierzytelniania użytkowników|
|Zarządzanie rolami|Zmienianie licencji użytkownika|
|Zarządzanie rolami|Zmienianie hasła użytkownika|
|Zarządzanie rolami|Konwertowanie użytkownika federacyjnego na zarządzanego|
|Zarządzanie rolami|Tworzenie hasła aplikacji dla użytkownika|
|Zarządzanie rolami|Usuwanie hasła aplikacji dla użytkownika|
|Zarządzanie rolami|Usuwanie użytkownika|
|Zarządzanie rolami|Wyłączanie konta|
|Zarządzanie rolami|Włączanie silnego uwierzytelniania|
|Zarządzanie rolami|Całkowite usuwanie użytkownika|
|Zarządzanie rolami|Usuwanie przypisania roli aplikacji z użytkownika|
|Zarządzanie rolami|Usuwanie szczegółów dotyczących aplikacji telefonicznej do silnego uwierzytelniania użytkowników|



## <a name="self-service-group-management"></a>Samoobsługowe zarządzanie grupami

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie grupami|Resetowanie hasła użytkownika|
|Zarządzanie grupami|Przywracanie użytkownika|
|Zarządzanie grupami|Ustawianie wymuszania zmiany hasła użytkownika|
|Zarządzanie grupami|Ustawianie menedżera użytkowników|
|Zarządzanie grupami|Włączanie metadanych tokenu OATH użytkowników|
|Zarządzanie grupami|Aktualizowanie sygnatury czasowej StsRefreshTokenValidFrom|
|Zarządzanie grupami|Aktualizowanie zewnętrznych wpisów tajnych|
|Zarządzanie grupami|Aktualizowanie użytkownika|
|Zarządzanie grupami|Administrator generuje hasło tymczasowe|


## <a name="self-service-password-management"></a>Samoobsługowe zarządzanie hasłami

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie katalogami|Administratorzy wymagają od użytkowników zresetowania ich haseł|
|Zarządzanie katalogami|Przypisywanie użytkownika zewnętrznego do aplikacji|
|Zarządzanie użytkownikami|Nie wysłano wiadomości e-mail (subskrypcja użytkownika został anulowana)|
|Zarządzanie użytkownikami|Zapraszanie użytkownika zewnętrznego|
|Zarządzanie użytkownikami|Realizowanie zaproszenia zewnętrznego użytkownika|
|Zarządzanie użytkownikami|Wirusowe tworzenie dzierżawy|
|Zarządzanie użytkownikami|Wirusowe tworzenie użytkownika|
|Zarządzanie użytkownikami|Rejestracja hasła użytkownika|
|Zarządzanie użytkownikami|Resetowanie hasła użytkownika|
|Zarządzanie użytkownikami|Zablokowano dostęp do samoobsługowego resetowania haseł|


## <a name="terms-of-use"></a>Warunki użytkowania

|Kategorii inspekcji|Działanie|
|---|---|
|Zarządzanie zasadami|Zmienianie hasła (samoobsługa)|
|Zarządzanie zasadami|Resetowanie hasła (przez administratora)|
|Zarządzanie zasadami|Resetowanie hasła (samoobsługa)|
|Zarządzanie zasadami|Postęp działania przepływu samoobsługowego resetowania hasła|
|Zarządzanie zasadami|Postęp działania przepływu samoobsługowego resetowania hasła|
|Zarządzanie zasadami|Odblokowywanie konta użytkownika (samoobsługa)|
|Zarządzanie zasadami|Użytkownik zarejestrowany do samoobsługowego resetowania haseł|




## <a name="next-steps"></a>Następne kroki

Omówienia:

- Omówienie raportowania zawiera artykuł [Azure Active Directory Reporting Guide (Przewodnik po raportach usługi Azure Active Directory)](active-directory-reporting-azure-portal.md).

- Omówienie raportów działań inspekcji zawiera artykuł [Audit activity reports in the Azure Active Directory portal (Raporty dotyczące działań inspekcji w portalu usługi Azure Active Directory)](active-directory-reporting-activity-audit-logs.md). 

