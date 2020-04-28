---
title: Nieoczekiwany błąd podczas wyrażania zgody na aplikację | Microsoft Docs
description: Omawia błędy, które mogą wystąpić podczas procesu wyrażania zgody na aplikację i co można zrobić z nimi
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea14e02920cf7ba6c5e0a7b415cb92137c915576
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519704"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Nieoczekiwany błąd podczas wyrażania zgody na aplikację

W tym artykule omówiono błędy, które mogą wystąpić podczas procesu wyrażania zgody na aplikację. W przypadku rozwiązywania nieoczekiwanych monitów o zgodę, które nie zawierają żadnych komunikatów o błędach, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Wiele aplikacji, które integrują się z Azure Active Directory wymaga uprawnień dostępu do innych zasobów w celu działania. Gdy te zasoby są również zintegrowane z Azure Active Directory, uprawnienia dostępu do nich są często wymagane przy użyciu wspólnej struktury wyrażania zgody. Zostanie wyświetlony monit o zgodę, który zwykle występuje, gdy aplikacja jest używana po raz pierwszy, ale może również wystąpić przy następnym użyciu aplikacji.

Niektóre warunki muszą być spełnione, aby użytkownik mógł wyrazić zgodę na uprawnienia wymagane przez aplikację. Jeśli te warunki nie są spełnione, mogą wystąpić następujące błędy.

## <a name="requesting-not-authorized-permissions-error"></a>Błąd żądania nieautoryzowanych uprawnień
* **AADSTS90093:** &lt;clientAppDisplayName&gt; żąda co najmniej jednego uprawnienia, które nie jest autoryzowane do przyznania. Skontaktuj się z administratorem, który może wyrazić zgodę na tę aplikację w Twoim imieniu.
* **AADSTS90094:** &lt;clientAppDisplayName&gt; wymaga uprawnień dostępu do zasobów w organizacji, które mogą przyznawać tylko Administratorzy. Poproś administratora o udzielenie uprawnienia do tej aplikacji, aby można było z niej korzystać.

Ten błąd występuje, gdy użytkownik, który nie jest administratorem firmy, próbuje użyć aplikacji, która żąda uprawnień, które może udzielić tylko administrator. Ten błąd może zostać rozwiązany przez administratora, który udziela dostępu do aplikacji w imieniu swojej organizacji.

Ten błąd może również wystąpić, gdy użytkownik nie jest w stanie przesłać do aplikacji z powodu wykrycia przez firmę Microsoft, że żądanie uprawnień jest ryzykowne. W takim przypadku zdarzenie inspekcji zostanie również zarejestrowane z kategorią "ApplicationManagement", typ działania "Zgoda na aplikację" i Przyczyna stanu "wykryto ryzykowną aplikację".

## <a name="policy-prevents-granting-permissions-error"></a>Zasady uniemożliwiają udzielenie błędu uprawnień
* **AADSTS90093:** Administrator usługi &lt;tenantDisplayName&gt; ustawił zasady, które uniemożliwiają przyznanie &lt;nazwy aplikacji&gt; , do której żądają uprawnienia. Skontaktuj się z administratorem &lt;tenantDisplayName&gt;, który może udzielić uprawnień do tej aplikacji w Twoim imieniu.

Ten błąd występuje, gdy administrator firmy wyłącza możliwość wyrażania zgody użytkowników na aplikacje, a następnie użytkownik niebędący administratorem próbuje użyć aplikacji, która wymaga zgody. Ten błąd może zostać rozwiązany przez administratora, który udziela dostępu do aplikacji w imieniu swojej organizacji.

## <a name="intermittent-problem-error"></a>Błąd sporadycznego problemu
* **AADSTS90090:** Wygląda na to, że proces logowania napotkał sporadyczny problem podczas rejestrowania uprawnień, które próbowano udzielić &lt;clientAppDisplayName.&gt; Spróbuj ponownie później.

Ten błąd wskazuje, że wystąpił sporadyczny problem po stronie usługi. Można rozwiązać ten problem, próbując ponownie wyrazić zgodę na aplikację.

## <a name="resource-not-available-error"></a>Błąd niedostępnego zasobu
* **AADSTS65005:** Aplikacja &lt;clientAppDisplayNamea&gt; żądane uprawnienia dostępu do resourceAppDisplayName &lt;&gt; zasobów, która jest niedostępna. 

Skontaktuj się z deweloperem aplikacji.

##  <a name="resource-not-available-in-tenant-error"></a>Zasób nie jest dostępny w przypadku błędu dzierżawy
* **AADSTS65005:** &lt;clientAppDisplayName&gt; żąda dostępu do &lt;zasobu resourceAppDisplayName&gt; , który nie jest dostępny w Twojej organizacji &lt;tenantDisplayName.&gt; 

Upewnij się, że ten zasób jest dostępny, lub skontaktuj &lt;się&gt;z administratorem tenantDisplayName.

## <a name="permissions-mismatch-error"></a>Błąd niezgodności uprawnień
* **AADSTS65005:** Aplikacja zażądała zgody na dostęp &lt;do&gt;resourceAppDisplayName zasobów. To żądanie nie powiodło się, ponieważ nie pasuje do sposobu, w jaki aplikacja została wstępnie skonfigurowana podczas rejestracji aplikacji. Skontaktuj się z dostawcą aplikacji. * *

Te błędy są wykonywane, gdy aplikacja, którą użytkownik próbuje wyrazić zgodę, żąda uprawnień dostępu do aplikacji zasobów, której nie można znaleźć w katalogu organizacji (dzierżawy). Ta sytuacja może wystąpić z kilku powodów:

-   Deweloper aplikacji klienta nieprawidłowo skonfigurował aplikację, powodując żądanie dostępu do nieprawidłowego zasobu. W takim przypadku Deweloper aplikacji musi zaktualizować konfigurację aplikacji klienckiej, aby rozwiązać ten problem.

-   Nazwa główna usługi reprezentująca docelową aplikację do zasobów nie istnieje w organizacji lub nie istniała w przeszłości, ale została usunięta. Aby rozwiązać ten problem, nazwa główna usługi dla aplikacji zasobów musi być obsługiwana w organizacji, aby aplikacja kliencka mogła zażądać uprawnień do niej. Nazwa główna usługi może być obsługiwana na wiele sposobów, w zależności od typu aplikacji, w tym:

    -   Pobieranie subskrypcji dla aplikacji zasobów (aplikacje opublikowane przez firmę Microsoft)

    -   Wyrażanie zgody na aplikację zasobów

    -   Przyznawanie uprawnień aplikacji za pośrednictwem Azure Portal

    -   Dodawanie aplikacji z galerii aplikacji usługi Azure AD

## <a name="next-steps"></a>Następne kroki 

[Aplikacje, uprawnienia i zgoda w Azure Active Directory (punkt końcowy v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Zakresy, uprawnienia i zgody w Azure Active Directory (punkt końcowy v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


