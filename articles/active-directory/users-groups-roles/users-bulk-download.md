---
title: Pobierz listę użytkowników w portalu Azure Active Directory | Microsoft Docs
description: Pobieranie rekordów użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00dfac7c3036c4fd457c5d3a6bb4eb7bd4e56998
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731328"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Pobieranie listy użytkowników w portalu Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje importowania użytkowników zbiorczych (tworzenia).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby pobrać listę użytkowników z centrum administracyjnego usługi Azure AD, należy zalogować się przy użyciu konta użytkownika przypisanego do co najmniej jednej roli administratora na poziomie organizacji w usłudze Azure AD (administrator użytkowników jest minimalnym wymaganą rolą). Osoby zapraszające gościa i deweloperzy aplikacji nie są uznawane za role administratorów.

## <a name="to-download-a-list-of-users"></a>Aby pobrać listę użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika w organizacji.
2. Przejdź do Azure Active Directory > użytkowników. Następnie wybierz użytkowników, którzy mają zostać dołączeni do pobierania, zaznaczając pole w lewej kolumnie obok każdego użytkownika. Uwaga: w tej chwili nie ma możliwości wybrania opcji Wszyscy użytkownicy do eksportowania. Każdy z nich musi być wybrany indywidualnie.
3. W usłudze Azure AD wybierz opcję **Użytkownicy**  >  **pobierają użytkowników**.
4. Na stronie **pobieranie użytkowników** wybierz pozycję **Rozpocznij** , aby wyświetlić plik CSV z listą właściwości profilu użytkownika. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

   ![Wybierz, gdzie chcesz wyświetlić listę użytkowników, których chcesz pobrać](./media/users-bulk-download/bulk-download.png)

   Plik do pobrania będzie zawierać przefiltrowaną listę użytkowników.

   Uwzględniane są następujące atrybuty użytkownika:

   - userPrincipalName
   - displayName
   - surname
   - mail (poczta)
   - givenName
   - Obiektu
   - userType
   - Stanowiska
   - działu,
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - Pocztowy
   - telephoneNumber
   - telefon komórkowy
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - grupa_wiekowa
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Sprawdzanie stanu

Stan oczekujących żądań zbiorczych można zobaczyć na stronie **wyników operacji zbiorczej** .

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze służące do tworzenia listy użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Umożliwia to utworzenie i pobranie listy dla co najmniej 500 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Zbiorcze przywracanie użytkowników](users-bulk-restore.md)
