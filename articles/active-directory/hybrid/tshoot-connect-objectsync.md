---
title: 'Program Azure AD Connect: Rozwiązywanie problemów z synchronizacją obiektów | Dokumentacja firmy Microsoft'
description: Ten temat zawiera procedurę rozwiązywania problemów z synchronizacją obiektu za pomocą zadania rozwiązywania problemów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82139178d4c1db4774d539180e41e49699d8ee12
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174218"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Rozwiązywanie problemów z synchronizacją obiektu za pomocą usługi Azure AD Connect sync
Ten artykuł zawiera instrukcje dotyczące rozwiązywania problemów z synchronizacją obiektu za pomocą zadania rozwiązywania problemów. Aby zobaczyć, jak rozwiązywanie problemów z działa w usłudze Azure Active Directory (Azure AD) Connect, obejrzyj [ten krótki film wideo](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Rozwiązywanie problemów z zadania
Dla usługi Azure AD łączenie wdrożenia z wersji 1.1.749.0 lub wyższą od nich, korzystając z rozwiązywania problemów z zadania w Kreatorze Rozwiązywanie problemów z synchronizacją obiektu. We wcześniejszych wersjach, należy rozwiązać ręcznie zgodnie z opisem [tutaj](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Uruchamianie zadania dotyczące rozwiązywania problemów w Kreatorze
Aby uruchomić zadanie dotyczące rozwiązywania problemów w kreatorze, wykonaj następujące czynności:

1.  Otwieranie nowej sesji programu Windows PowerShell na serwerze programu Azure AD Connect przy użyciu opcji Uruchom jako administrator.
2.  Uruchom `Set-ExecutionPolicy RemoteSigned` lub `Set-ExecutionPolicy Unrestricted`.
3.  Uruchom Kreatora programu Azure AD Connect.
4.  Przejdź do strony dodatkowych zadań, wybrać Rozwiązywanie problemów i kliknij przycisk Dalej.
5.  Na stronie Rozwiązywanie problemów kliknij przycisk Uruchom, aby uruchomić menu rozwiązywania problemów w programie PowerShell.
6.  W menu głównym wybierz Rozwiązywanie problemów z synchronizacją obiektu.
![](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Rozwiązywanie problemów z parametrów wejściowych
Następujące parametry wejściowe są wymagane przez zadanie dotyczące rozwiązywania problemów:
1.  **Nazwa wyróżniająca obiektu** — jest to nazwa wyróżniająca obiektu, który wymaga rozwiązywania problemów
2.  **Nazwa łącznika AD** — jest to nazwa lasu usługi AD, w którym znajduje się obiekt powyżej.
3.  Poświadczenia administratora globalnego dzierżawy usługi Azure AD ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Zrozumienie wyników zadania dotyczące rozwiązywania problemów
Zadanie dotyczące rozwiązywania problemów wykonuje następujące testy:

1.  Wykrycia niezgodności głównej nazwy użytkownika, jeśli obiekt jest synchronizowana z usługą Azure Active Directory
2.  Sprawdź, jeśli obiekt jest filtrowana ze względu na filtrowanie domeny
3.  Sprawdź, jeśli obiekt jest filtrowana powodu w celu filtrowania jednostek organizacyjnych
4.  Sprawdź, czy obiekt synchronizacji jest zablokowana z powodu połączona Skrzynka pocztowa
5. Sprawdź, czy obiekt jest grupy dynamicznej dystrybucji, który nie powinien być synchronizowane

W pozostałej części tej sekcji opisano określone wyniki, które są zwracane przez zadanie. W każdym przypadku zadania zawiera analizę następuje zalecane działania, aby rozwiązać ten problem.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Wykrycia niezgodności głównej nazwy użytkownika, jeśli obiekt jest synchronizowana z usługą Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Sufiks głównej nazwy użytkownika nie została zweryfikowana za pomocą dzierżawy usługi Azure AD
Gdy UserPrincipalName (UPN) / sufiksu alternatywnego Identyfikatora logowania nie jest weryfikowany za pomocą dzierżawy usługi Azure AD, a następnie usługi Azure Active Directory zastępuje sufiksy nazw UPN nazwy domeny domyślnej "onmicrosoft.com".

![](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Zmiana sufiks głównej nazwy użytkownika z jednej domeny federacyjnej do innej domeny federacyjnej
Usługa Azure Active Directory nie zezwala na synchronizację UserPrincipalName (UPN) / Zmiana sufiksu alternatywnego Identyfikatora logowania z jednej domeny federacyjnej do innego federacyjnego domeny. Dotyczy to domen, które są weryfikowane za pomocą dzierżawy usługi Azure AD i korzystają z tego typu uwierzytelniania, co Sfederowane.

![](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Usługa Azure AD dzierżawy DirSync funkcja "SynchronizeUpnForManagedUsers" jest wyłączona
Po wyłączeniu funkcji DirSync dzierżawy usługi Azure AD "SynchronizeUpnForManagedUsers" Azure Active Directory nie zezwala na synchronizację aktualizacji UserPrincipalName/alternatywny identyfikator logowania dla kont licencjonowanego użytkownika przy użyciu uwierzytelniania zarządzanych.

![](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Obiekt jest filtrowana ze względu na filtrowanie domeny
### <a name="domain-is-not-configured-to-sync"></a>Domeny nie jest skonfigurowany do synchronizacji
Obiekt jest poza zakres z powodu domeny nie jest skonfigurowane. W poniższym przykładzie obiekt jest zsynchronizowany zakresu, zgodnie z filtrowania należącą do domeny z synchronizacji.

![](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domena jest skonfigurowany do synchronizacji, ale nie ma profilów/uruchamianie wykonywania kroków
Obiekt jest poza zakresem ponieważ brakuje domeny uruchamiane profile/uruchamianie kroków. W poniższym przykładzie obiekt jest zsynchronizowany zakresu należącego do domeny jest brak pełne importowanie profilu uruchamiania wykonywania czynności.
![](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Obiekt jest filtrowana powodu w celu filtrowania jednostek organizacyjnych
Obiekt jest zsynchronizowany zakres z powodu konfiguracji filtrowania jednostek organizacyjnych. W poniższym przykładzie obiekt należy do jednostki Organizacyjnej = NoSync, DC = bvtadwbackdc, DC = com.  Tej jednostki Organizacyjnej nie znajduje się w zakresie synchronizacji.</br>

![JEDNOSTKI ORGANIZACYJNEJ](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Połączone problem skrzynki pocztowej
Połączona Skrzynka pocztowa powinna być skojarzona z kontem zewnętrznym główny znajduje się w innym lesie konto zaufane. Jeśli nie ma takiego zewnętrznego głównego konta, a następnie program Azure AD Connect nie będzie synchronizować użytkownika konta odpowiada połączoną skrzynkę pocztową w lesie programu Exchange do dzierżawy usługi Azure AD.</br>
![Połączona Skrzynka pocztowa](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problem dynamicznej grupy dystrybucji
Z powodu różnych różnic między lokalnej usługi Active Directory i Azure Active Directory, Azure AD Connect nie synchronizuje grupy dynamicznej dystrybucji do dzierżawy usługi Azure AD.

![Dynamiczne grupy dystrybucji](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Raport w formacie HTML
Oprócz analizowania obiektu, rozwiązywania problemów zadanie generuje raport HTML, który zawiera wszystko, co jest znane o obiekcie. Ten raport HTML, mogą być współużytkowane z zespołem pomocy technicznej w celu dalszego rozwiązywania problemów, jeśli to konieczne.

![](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
