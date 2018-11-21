---
title: Przypisywanie dostępu do danych usługi Azure Cost Management | Dokumentacja firmy Microsoft
description: W tym artykule opisano jednak przypisywanie uprawnień do danych w usłudze Azure Cost Management różne zakresy dostępu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: ''
ms.openlocfilehash: 0c34b44bb0dc5458bab842e62a88403db08dc07a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275422"
---
# <a name="assign-access-to-cost-management-data"></a>Przypisywanie dostępu do danych rozwiązania Cost Management

Dla większości użytkowników kombinacji uprawnienia udzielone w witrynie Azure portal i portalu przedsiębiorstwa (EA) zdefiniuj poziom dostępu użytkownika do danych usługi Azure Cost Management. Ten artykuł przeprowadzi przypisywanie dostępu do danych rozwiązania Cost Management. Po przypisaniu kombinację uprawnień dane użytkownika widoków w Cost Management na podstawie zakresu dostępu i na zakres, wybierają w witrynie Azure portal.

Zakres, który użytkownik wybierze jest używana w całej Cost Management, zapewnienie konsolidacji danych i kontrolować dostęp do informacji o kosztach. Podczas korzystania z zakresów, użytkownicy nie zaznacz je. Zamiast tego wybierz większy zakres, który zakresy podrzędne Przywracanie do, a następnie ich filtru w celu mają być wyświetlane. Konsolidacja danych ważne jest, aby zrozumieć, ponieważ niektórzy użytkownicy nie powinien mieć dostęp do zakresu nadrzędnego, który zakresy podrzędne Przywracanie do.

## <a name="cost-management-scopes"></a>Usługa Cost Management zakresów

Aby wyświetlić dane kosztów, użytkownik musi mieć co najmniej odczytu dostęp do co najmniej jeden z następujących zakresów.

| **Zakres** | **Lokalizacja definicji** | **Wymagany dostęp do wyświetlania danych** | **Wstępnie wymagane ustawienie umowy EA** | **Konsoliduje dane** |
| --- | --- | --- | --- | --- |
| Konto rozliczeniowe<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrator przedsiębiorstwa | Brak | Wszystkie subskrypcje z umowy Enterprise Agreement |
| Dział | [https://ea.azure.com](https://ea.azure.com/) | Administrator działu | **Akcelerator deweloperski w wersji Wyświetl opłaty** włączone | Wszystkie subskrypcje należące do konta rejestracji połączonego z działem |
| Konto rejestracji<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Właściciel konta | **Opłaty za widoku AO** włączone | Wszystkie subskrypcje z konta rejestracji |
| Grupa zarządzania | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | **Opłaty za widoku AO** włączone | Wszystkie subskrypcje poniżej grupy zarządzania |
| Subskrypcja | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | **Opłaty za widoku AO** włączone | Wszystkie zasoby lub grupy zasobów w subskrypcji |
| Grupa zasobów | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | **Opłaty za widoku AO** włączone | Wszystkie zasoby w grupie zasobów |

<sup>1</sup> konto rozliczeniowe jest również nazywany umowy Enterprise Agreement lub rejestracji.

<sup>2</sup> konta rejestracji jest również określany jako właściciel konta.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Zapewnianie dostępu do kosztów w witrynie EA portal

Zakres konta rozliczeniowego wymaga **DA Wyświetl opłaty** opcji **włączone** w witrynie EA portal. Wszystkie inne zakresy wymagają **AO Wyświetl opłaty** opcji **włączone** w witrynie EA portal.

Aby włączyć opcję:

1. Zaloguj się do portalu EA w [ https://ea.azure.com ](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. Wybierz **Zarządzaj** w okienku po lewej stronie.
3. Do zarządzania kosztami zakresów, który chcesz zapewnić dostęp, należy włączyć opcję opłaty **DA Wyświetl opłaty** i/lub **AO Wyświetl opłaty**.  
    ![Karta rejestracji wyświetlanie widoku DA i AO opłaty opcje](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po włączeniu opcji opłat widoku zakresów większość również wymagać dostępu opartej na rolach kontroli uprawnień konfiguracji w witrynie Azure portal.

## <a name="enterprise-administrator-role"></a>Rola administratora przedsiębiorstwa

Domyślnie administrator przedsiębiorstwa ma dostęp do konta rozliczeniowego (Enterprise Agreement/rejestracji) i wszystkie inne zakresy, które są zakresy podrzędne. Administrator przedsiębiorstwa przydziela dostęp do zakresów dla innych użytkowników. Najlepszym rozwiązaniem dla ciągłości działania powinny zawsze występować dwa użytkownikom dostępu administratora przedsiębiorstwa. Poniższe sekcje zawierają przykłady przewodniku dostępu przypisywanie administratora przedsiębiorstwa do zakresów dla innych użytkowników.

## <a name="assign-billing-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rozliczeniowego

Dostęp do rozliczeń zakresu konto wymaga uprawnień administratora przedsiębiorstwa w witrynie EA portal. Administrator przedsiębiorstwa ma dostęp do wyświetlania kosztów całej rejestracji EA lub wiele rejestracji. W witrynie Azure portal rozliczeń zakresu konta są wymagane żadne działania.

1. Zaloguj się do portalu EA w [ https://ea.azure.com ](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. Wybierz **Zarządzaj** w okienku po lewej stronie.
3. Na **rejestracji** , a następnie wybierz rejestracji, który chcesz zarządzać.  
    ![Portalu EA portal](./media/assign-access-acm-data/ea-portal.png)
4. Kliknij przycisk **+ Dodaj administratora**.
5. W oknie dialogowym Dodawanie administratora wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych kosztów i użycia, w obszarze **tylko do odczytu**, wybierz opcję **tak**.  W przeciwnym razie wybierz **nie**.
7. Kliknij przycisk **Dodaj** do utworzenia konta.  
    ![Dodaj pole administratora](./media/assign-access-acm-data/add-admin.png)

Może potrwać do 30 minut, zanim nowy użytkownik mają dostęp do danych w Cost Management.

### <a name="assign-department-scope-access"></a>Przypisywanie działu zakresu dostępu

Dostęp do zakresu działu wymaga dostępu (DA Wyświetl opłaty) administratora działu w witrynie EA portal. Administrator działu ma dostęp do wyświetlenia kosztów i użycia danych skojarzonych z działu lub wielu działów.  Dane dla działu obejmują wszystkie subskrypcje należące do konta rejestracji, które są połączone z działu. W witrynie Azure portal są wymagane żadne działania.

1. Zaloguj się do portalu EA w [ https://ea.azure.com ](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. Wybierz **Zarządzaj** w okienku po lewej stronie.
3. Na **rejestracji** , a następnie wybierz rejestracji, który chcesz zarządzać.
4. Kliknij przycisk **działu** kartę, a następnie kliknij przycisk **Dodawanie administratora**.
5. W oknie dialogowym Dodawanie administratora działu wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych kosztów i użycia, w obszarze **tylko do odczytu**, wybierz opcję **tak**.  W przeciwnym razie wybierz **nie**.
7. Wybierz działów, które chcesz udzielić uprawnień administracyjnych działu do.
8. Kliknij przycisk **Dodaj** do utworzenia konta.  
    ![Dodaj pole do administratora działu](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Przypisz dostęp do zakresu konta rejestracji

Dostęp do zakresu konta rejestracji wymaga dostępu (AO Wyświetl opłaty) do właściciela konta w witrynie EA portal. Właściciel konta mogą wyświetlać, kosztów i użycia danych skojarzonych z kontem rejestracji. Dane na koncie rejestracji obejmują wszystkie subskrypcje platformy Azure skojarzone z rejestracji. W witrynie Azure portal są wymagane żadne działania.

1. Zaloguj się do portalu EA w [ https://ea.azure.com ](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. Wybierz **Zarządzaj** w okienku po lewej stronie.
3. Na **rejestracji** , a następnie wybierz rejestracji, który chcesz zarządzać.
4. Kliknij przycisk **konta** kartę, a następnie kliknij przycisk **Dodaj konto**.
5. W oknie dialogowym Dodawanie konta wybierz **działu** skojarzyć konto lub pozostawić ją jako nieprzypisane.
6. Wybierz typ uwierzytelniania, a następnie wpisz nazwę konta.
7. Wpisz adres e-mail użytkownika, a następnie opcjonalnie wpisz centrum kosztów.
8. Kliknij pozycję **Dodaj** do utworzenia konta.  
    ![Dodaj pole konta](./media/assign-access-acm-data/add-account.png)

## <a name="assign-management-group-scope-access"></a>Przypisz dostęp do zakresu grupy zarządzania

Dostęp do zakresu grupy zarządzania wymaga co najmniej uprawnienia czytnika zarządzania kosztami (lub czytnika). Uprawnienia do grupy zarządzania należy skonfigurować w witrynie Azure portal. Musisz mieć co najmniej uprawnienia współautora do grupy zarządzania, aby włączyć dostęp do innych użytkowników. A także muszą być włączone **AO Wyświetl opłaty** ustawienie w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
2. Wybierz **wszystkich usług** na pasku bocznym, wyszukaj _grup zarządzania_, a następnie wybierz **grup zarządzania**.
3. Wybierz grupy zarządzania w hierarchii.
4. Obok nazwy grupy zarządzania, kliknij przycisk **szczegóły**.
5. Wybierz **kontrola dostępu (IAM)** z okienka po lewej stronie.
6. Kliknij pozycję **Add** (Dodaj).
7. W obszarze **roli**, wybierz opcję **Cost Management czytnika**.
8. W obszarze **Przypisz dostęp do**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.
9. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
10. Kliknij pozycję **Zapisz**.  
    ![Dodaj pole uprawnień](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Przypisywanie subskrypcji zakresu dostępu

Dostęp do subskrypcji wymaga co najmniej uprawnienia czytnika zarządzania kosztami (lub czytnika). Należy skonfigurować uprawnienia do subskrypcji w witrynie Azure portal. Musisz mieć co najmniej uprawnienia współautora do subskrypcji, aby włączyć dostęp do innych użytkowników. A także muszą być włączone **AO Wyświetl opłaty** ustawienie w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
2. Wybierz **wszystkich usług** na pasku bocznym, wyszukaj _subskrypcje_, a następnie wybierz **subskrypcje**.
3. Wybierz subskrypcję.
4. Wybierz **kontrola dostępu (IAM)** z okienka po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **roli**, wybierz opcję **Cost Management czytnika**.
7. W obszarze **Przypisz dostęp do**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.
8. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
9. Kliknij pozycję **Zapisz**.

## <a name="assign-resource-group-scope-access"></a>Przypisz dostęp do zakresu grupy zasobów

Dostęp do grupy zasobów wymaga co najmniej uprawnienia czytnika zarządzania kosztami (lub czytnika). Należy skonfigurować uprawnienia do grupy zasobów w witrynie Azure portal. Musisz mieć co najmniej uprawnienia współautora do grupy zasobów, aby włączyć dostęp do innych użytkowników. A także muszą być włączone **AO Wyświetl opłaty** ustawienie w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).
2. Wybierz **wszystkich usług** na pasku bocznym, wyszukaj _grup zasobów_, a następnie wybierz **grup zasobów**.
3. Wybierz grupę zasobów.
4. Wybierz **kontrola dostępu (IAM)** z okienka po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **roli**, wybierz opcję **Cost Management czytnika**.
7. W obszarze **Przypisz dostęp do**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.
8. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
9. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie została już zakończona pierwszym przewodniku Szybki Start usługi Cost Management, zapoznaj się z adresem [zacząć analizować koszty](quick-acm-cost-analysis.md).
