---
title: Dowiedz się, gdy określony użytkownik będzie można uzyskać dostęp do aplikacji | Dokumentacja firmy Microsoft
description: Jak ustalić, kiedy znaczenie użytkownik będzie mógł uzyskać dostęp do aplikacji skonfigurowanych do aprowizacji użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: 2f6ebd6d9024c912e1af427ac5a5b62a817651ab
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964376"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Dowiedz się, gdy określony użytkownik będzie można uzyskać dostęp do aplikacji
Korzystając z automatycznej aprowizacji użytkowników z aplikacją, usługi Azure AD automatycznie aprowizować i zaktualizuj kont użytkowników w aplikacji na podstawie elementów, takich jak [przypisania użytkowników i grup](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) w zaplanowany czas interwału, zazwyczaj co 10 minut.

## <a name="how-long-does-it-take"></a>Jak długo trwa?

Czas potrzebnego dla danego użytkownika do udostępnienia zależy od głównie czy już wystąpiła początkowej synchronizacji "pełnej".

Pierwsza synchronizacja między usługą Azure AD i aplikacji może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. 

Kolejne synchronizacje po synchronizacji początkowej można szybciej (np. w ciągu 10 minut), jak usługa aprowizowania magazyny znaki wodne, które reprezentują stanu obu systemów po początkowej synchronizacji, zwiększanie wydajności kolejne synchronizacje.

## <a name="how-to-check-the-status-of-a-user"></a>Jak sprawdzić stan użytkownika

Aby wyświetlić stan inicjowania obsługi administracyjnej dla wybranego użytkownika, sprawdź w dziennikach inspekcji w usłudze Azure AD.

Inicjowania obsługi dzienników inspekcji można uzyskać w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; dzienników inspekcji** kartę. Filtruj dzienniki na **Inicjowanie obsługi administracyjnej konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla tej aplikacji. Możesz wyszukać użytkowników na podstawie "Pasującego Identyfikatora" który został skonfigurowany dla nich w mapowania atrybutów. 

Na przykład, jeśli skonfigurowano "główna nazwa użytkownika" lub "adres e-mail" jako atrybutu zgodnego po stronie usługi Azure AD, a użytkownik nie aprowizacji ma wartość "audrey@contoso.com", następnie przeszukiwanie dzienników inspekcji dla "audrey@contoso.com", a następnie przejrzyj wpisy zwracane.

Inicjowania obsługi dzienników inspekcji rejestrowania wszystkich operacji wykonywanych przez usługę aprowizacji, w tym:

* Tworzenie zapytań usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie do inicjowania obsługi
* Podczas badania aplikacji docelowej, aby istnienie tych użytkowników
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączenie konta użytkownika w systemie docelowym na podstawie porównania

## <a name="next-steps"></a>Kolejne kroki
[Automatyzowanie użytkownika aprowizacji i cofania aprowizacji do aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
