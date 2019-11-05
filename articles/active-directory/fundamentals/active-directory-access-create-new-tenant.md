---
title: Szybki start — uzyskiwanie dostępu do usługi Azure Active Directory i tworzenie nowej dzierżawy | Microsoft Docs
description: Instrukcje umożliwiające znalezienie usługi Azure Active Directory oraz utworzenie nowej dzierżawy dla organizacji.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8310b6fc33f1d03e1ccc9ccddfcd7b9e6c35d37
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473350"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory
Wszystkie zadania administracyjne, w tym tworzenie nowej dzierżawy dla swojej organizacji, możesz wykonywać przy użyciu portalu usługi Azure Active Directory (Azure AD). 

W tym przewodniku Szybki start dowiesz się, jak uzyskać dostęp do witryny Azure Portal i usługi Azure Active Directory oraz jak utworzyć podstawową dzierżawę dla organizacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Tworzenie nowej dzierżawy dla organizacji
Po zalogowaniu się do witryny Azure Portal możesz utworzyć nową dzierżawę dla swojej organizacji. Nowa dzierżawa reprezentuje Twoją organizację i pomaga w zarządzaniu konkretnym wystąpieniem usług firmy Microsoft w chmurze na potrzeby użytkowników wewnętrznych i zewnętrznych.

### <a name="to-create-a-new-tenant"></a>Aby utworzyć nową dzierżawę

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) Twojej organizacji przy użyciu konta administratora globalnego.

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.  

    ![Azure Active Directory utworzyć strony](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Wybierz pozycję **tożsamość**, a następnie wybierz pozycję **Azure Active Directory**.

    Zostanie wyświetlona strona **Tworzenie katalogu**.

    ![Strona tworzenia usługi Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na stronie **Tworzenie katalogu** wprowadź następujące informacje:
    
    - Wpisz wartość _Contoso_ w polu **Nazwa organizacji**.

    - Wpisz wartość _Contoso_ w polu **Początkowa nazwa domeny**.

    - Pozostaw opcję _Stany Zjednoczone_ w polu **Kraj lub region**.

1. Wybierz pozycję **Utwórz**.

Twoja nowa dzierżawa zostanie utworzona i będzie miała domenę contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć dzierżawę, wykonując następujące czynności:

- Wybierz usługę **Azure Active Directory**, a następnie na stronie **Contoso — przegląd** wybierz pozycję **Usuń katalog**.

    Dzierżawa zostanie usunięta wraz ze skojarzonymi informacjami.

    ![Strona przegląd z wyróżnionym przyciskiem Usuń katalog](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Następne kroki
- Zmiana lub dodawanie kolejnych nazw domen — zobacz [Jak dodać nazwę domeny niestandardowej do usługi Azure Active Directory](add-custom-domain.md)

- Dodawanie użytkowników — zobacz [Dodawanie lub usuwanie nowego użytkownika](add-users-azure-active-directory.md)

- Dodawanie grup i członków — zobacz [Tworzenie podstawowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)

- Informacje na temat [dostępu opartego na rolach przy użyciu Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) i [dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md) w celu ułatwienia zarządzania dostępem do aplikacji i zasobów organizacji.

- Uzyskaj informacje o usłudze Azure AD, w tym [podstawowe informacje o licencjonowaniu oraz informacje o terminologii i skojarzonych funkcjach](active-directory-whatis.md).
