---
title: Zalecenia dotyczące zabezpieczeń dla obrazów z witryny Azure Marketplace | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zalecenia dotyczące obrazów zawartych w portalu Marketplace
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 9c02dc386852a32814669d38df6260822a5e4f99
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308799"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń dla obrazów z witryny Azure Marketplace

Zaleca się, że każde z tych rozwiązań spełniają poniższe zalecenia dotyczące konfiguracji zabezpieczeń. Ułatwi to utrzymanie wysokiego poziomu zabezpieczeń dla obrazów rozwiązanie partnera w portalu Azure Marketplace.

Zalecenia te mogą być również przydatne w przypadku organizacji, które nie mają obrazy w witrynie Azure marketplace. Można sprawdzić konfiguracje firmy Windows i Linux obrazu względem wytycznych w poniższych tabelach.

## <a name="open-source-based-images"></a>Open Source-based obrazów

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategoria**                                                 | **Sprawdź**                                                                                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Zainstalowano wszystkie najnowsze poprawki zabezpieczeń dla dystrybucji systemu Linux.                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Wykonano branży wytyczne do zabezpieczania obrazu maszyny Wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Bezpieczeństwo                                                     | Ogranicz obszar narażony na ataki przy zachowaniu minimalnego zasięgiem dzięki tylko wymagane role systemu Windows Server, funkcji, usług i portów sieciowych.                                                                                                                                               |
| Bezpieczeństwo                                                     | Zeskanuj kod źródłowy i Wynikowy obraz maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Bezpieczeństwo                                                     | Obraz wirtualnego dysku twardego zawiera tylko niezbędne zablokowane konta, które nie mają haseł domyślne, które pozwoliłoby logowania interakcyjnego; nie drzwi Wstecz.                                                                                                                                           |
| Bezpieczeństwo                                                     | Reguły zapory są wyłączone, chyba że aplikacji funkcjonalnie opiera się na nich, takich jak urządzenia zapory.                                                                                                                                                                             |
| Bezpieczeństwo                                                     | Informacje poufne zostało usunięte z obrazu wirtualnego dysku twardego, takie jak klucze SSH testu, znane pliku hosts, pliki dziennika i niepotrzebnych certyfikatów.                                                                                                                                       |
| Bezpieczeństwo                                                     | Zaleca się, że LVM nie powinny być używane.                                                                                                                                                                                                                                            |
| Bezpieczeństwo                                                     | Najnowsze wersje wymaganych bibliotek należy dołączyć: </br> -OpenSSL w wersji 1.0 lub nowszej </br> -Python w wersji 2.5 lub nowszej (zdecydowanie zaleca Python 2.6) </br> — Pakiet pyasn1 języka Python Jeśli jeszcze nie zainstalowano </br> -v d.OpenSSL 1.0 lub nowszy                                                                |
| Bezpieczeństwo                                                     | Powłoka bash/wpisów historii muszą zostać wyczyszczone.                                                                                                                                                                                                                                             |
| Networking                                                   | Serwer SSH powinny być włączone domyślnie. Ustaw aktywny Zachowaj SSH do konfiguracji sshd przy użyciu następujących opcji: ClientAliveInterval 180                                                                                                                                                        |
| Networking                                                   | Obraz nie może zawierać żadnych niestandardowa konfiguracja sieci. Usuń resolv.conf: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Wdrożenie                                                   | Należy zainstalować najnowszą wersję agenta systemu Linux platformy Azure </br> — Agenta należy zainstalować przy użyciu pakietu RPM lub Deb.  </br> — Można także użyć procesu ręcznej instalacji, ale pakiety instalacyjne są zalecane i preferowane. </br> -W przypadku ręcznego instalowania agenta z repozytorium GitHub, najpierw skopiować `waagent` plik `/usr/sbin` i uruchomić (root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracji agenta będzie umiejscowiony `/etc/waagent.conf`.    |
| Wdrożenie                                                   | Zapewnia, że pomoc techniczna systemu Azure może zapewnić naszym partnerom dane wyjściowe konsoli szeregowej potrzebne i podaj odpowiedni limit czasu instalowania dysku systemu operacyjnego z magazynu w chmurze. Obraz należy dodać następujące parametry do wiersza rozruchu jądra: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Wdrożenie                                                   | Nie partycji wymiany na dysku systemu operacyjnego. Swap, można żądać tworzenia na lokalnym dysku zasobów przez agenta systemu Linux.         |
| Wdrożenie                                                   | Zalecane jest, utworzony jedną partycję główną dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | 64-bitowym systemie operacyjnym tylko.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazy na serwerze systemu Windows

|||
|-------------| -------------------------|
| **Kategoria**                                                     | **Sprawdź**                                                                                                                                                                |
| Bezpieczeństwo                                                         | Użyj bezpiecznego obrazu podstawowego systemu operacyjnego. Wirtualny dysk twardy używany dla źródła dowolnego obrazu, w oparciu o system Windows Server musi być z obrazów systemu operacyjnego Windows Server, oferowana w ramach Microsoft Azure. |
| Bezpieczeństwo                                                         | Zainstaluj wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Bezpieczeństwo                                                         | Aplikacje nie powinny mieć zależność o nazwach użytkowników z ograniczeniami, takie jak Administrator, główny i administratora.                                                                |
| Bezpieczeństwo                                                         | Szyfrowanie dysków funkcją BitLocker nie jest obsługiwana na dysku twardym systemu operacyjnego. Funkcja BitLocker może być używana dla dysków z danymi.                                                            |
| Bezpieczeństwo                                                         | Ogranicz obszar narażony na ataki przy zachowaniu minimalnego zasięgiem dzięki tylko wymagane role systemu Windows Server, funkcji, usług i porty sieciowe, włączone.                         |
| Bezpieczeństwo                                                         | Zeskanuj kod źródłowy i Wynikowy obraz maszyny Wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Bezpieczeństwo                                                         | Ustaw aktualizacji zabezpieczeń obrazów systemu Windows Server w celu automatycznej aktualizacji.                                                                                                                |
| Bezpieczeństwo                                                         | Obraz wirtualnego dysku twardego zawiera tylko niezbędne zablokowane konta, które nie mają haseł domyślne, które pozwoliłoby logowania interakcyjnego; nie drzwi Wstecz.                             |
| Bezpieczeństwo                                                         | Reguły zapory są wyłączone, chyba że aplikacji funkcjonalnie opiera się na nich, takich jak urządzenia zapory.                                                               |
| Bezpieczeństwo                                                         | Informacje poufne zostało usunięte z obrazu wirtualnego dysku twardego. Na przykład można usunąć pliku hostów, pliki dziennika i niepotrzebnych certyfikatów.                                              |
| Wdrożenie                                                       | 64-bitowym systemie operacyjnym tylko.                            |
