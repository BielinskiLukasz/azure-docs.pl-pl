---
title: Jak skonfigurować logowanie jednokrotne do aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak można skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji szybko
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: c27649bc92b9874c6a669911d2b86273cf7adf0f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136336"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak skonfigurować logowanie jednokrotne do aplikacji serwera Proxy aplikacji

Logowanie jednokrotne (SSO) umożliwia użytkownikom dostęp do aplikacji bez uwierzytelniania wiele razy. Umożliwia pojedynczego uwierzytelniania w chmurze w usłudze Azure Active Directory oraz umożliwia usługi lub łącznik do personifikacji użytkownika, aby zakończyć wszelkie problemy dodatkowego uwierzytelniania z aplikacji.

## <a name="how-to-configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego na
Aby skonfigurować logowanie Jednokrotne, najpierw upewnij się, że aplikacja jest skonfigurowana do uwierzytelniania wstępnego za pomocą usługi Azure Active Directory. Aby przeprowadzić tę konfigurację, przejdź do **usługi Azure Active Directory**  - &gt; **aplikacje dla przedsiębiorstw**  - &gt; **wszystkie aplikacje**   - &gt; Aplikacji  **- &gt; serwera Proxy aplikacji**. Na tej stronie można znaleźć w polu "Uwierzytelniania Pre" i upewnij się, która jest ustawiona na "Azure Active Directory. 

Aby uzyskać więcej informacji na temat metod uwierzytelniania wstępnego, zobacz krok 4 [dokumentu publikowania aplikacji](application-proxy-add-on-premises-application.md).

   ![Metoda uwierzytelniania wstępnego w witrynie Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurowanie trybów rejestracji jednokrotnej dla aplikacji serwera Proxy aplikacji
Skonfiguruj typ logowania jednokrotnego. Metody logowania są klasyfikowane, oparte na korzysta z rodzaju uwierzytelniania aplikacji zaplecza. Aplikacje serwera Proxy aplikacji obsługują trzy rodzaje logowania jednokrotnego:

-   **Logowanie na podstawie hasła**: logowanie na podstawie hasła może służyć do każdej aplikacji korzystającej z pola Nazwa użytkownika i hasło do logowania się na. Czynności konfiguracyjne znajdują się w [skonfigurować hasło logowania jednokrotnego dla aplikacji z galerii usługi Azure AD](configure-password-single-sign-on-gallery-applications.md).

-   **Zintegrowane uwierzytelnianie Windows**: dla aplikacji używających zintegrowanego Windows Authentication (Zintegrowane), logowanie jednokrotne jest włączone za pomocą protokołu Kerberos ograniczone delegowanie (ograniczonego delegowania protokołu Kerberos). Ta metoda zapewnia uprawnieniu łączników serwera Proxy aplikacji w usłudze Active Directory na personifikowanie użytkowników oraz do wysyłania i odbierania tokenów w ich imieniu. Szczegółowe informacje na temat konfigurowania ograniczonego delegowania protokołu Kerberos znajduje się w [logowanie jednokrotne z dokumentacją ograniczonego delegowania protokołu Kerberos](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logowanie na podstawie nagłówka**: logowanie na podstawie nagłówka jest włączana za pomocą powiązania i wymagać dodatkowej konfiguracji. Szczegółowe informacje na temat partnerstwa i instrukcje krok po kroku dotyczące konfigurowania logowania jednokrotnego do aplikacji, która używa nagłówków do uwierzytelniania, [PingAccess dla usługi Azure AD dokumentacji](application-proxy-configure-single-sign-on-with-ping-access.md).

Każda z tych opcji można znaleźć, przechodząc do aplikacji w "Aplikacje dla przedsiębiorstw" i otwierania **logowania jednokrotnego** strony w menu po lewej stronie. należy pamiętać, że jeśli aplikacja została utworzona w starym portalu, może być niewidoczna tych opcji.

Na tej stronie zostanie również wyświetlony jedną dodatkową opcję logowania jednokrotnego: połączonego logowania jednokrotnego. Ta opcja jest również obsługiwana przez serwer Proxy aplikacji. Jednak ta opcja nie dodaje logowanie jednokrotne do aplikacji. Inaczej mówiąc, że aplikacja może już logowanie jednokrotne implementowane za pomocą innej usługi, takie jak Active Directory Federation Services. 

Ta opcja umożliwia administratora do tworzenia użytkowników gruntów pierwszy link do aplikacji na podczas uzyskiwania dostępu do aplikacji. Na przykład w przypadku aplikacji, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2.0, administrator może użyć opcji "połączone logowanie" Aby utworzyć link do niego w panelu dostępu.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
