---
title: Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace | Microsoft Docs
description: Ten artykuł zawiera zalecenia dotyczące obrazów uwzględnionych w miejscu na rynku
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795808"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace

Obraz musi spełniać te zalecenia dotyczące konfiguracji zabezpieczeń. Pozwala to zachować wysoki poziom zabezpieczeń obrazów rozwiązań partnerskich w portalu Azure Marketplace.

Przed przesłaniem obrazu należy zawsze uruchomić Wykrywanie luk w zabezpieczeniach. Jeśli wykryjesz lukę w zabezpieczeniach w publikowanym obrazie, musisz powiadomić klientów w odpowiednim czasie o lukę i sposobach jego naprawy.

## <a name="open-source-based-images"></a>Obrazy na podstawie otwartych źródeł

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategoria**                                                 | **Niezaznaczone**                                                                                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Zainstaluj wszystkie najnowsze poprawki zabezpieczeń dla dystrybucji systemu Linux.                                                                                                                                                                                                              |
| Bezpieczeństwo                                                     | Postępuj zgodnie z wytycznymi branżowymi, aby zabezpieczyć obraz maszyny wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Bezpieczeństwo                                                     | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na niezbędną rolę, funkcje, usługi i porty sieci systemu Windows Server.                                                                                                                                               |
| Bezpieczeństwo                                                     | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Bezpieczeństwo                                                     | Obraz VHD obejmuje tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne. Brak tylnych drzwi.                                                                                                                                           |
| Bezpieczeństwo                                                     | Wyłącz reguły zapory, chyba że aplikacja opiera się na nich, na przykład urządzenie zapory.                                                                                                                                                                             |
| Bezpieczeństwo                                                     | Usuń wszystkie informacje poufne z obrazu wirtualnego dysku twardego, takie jak testowanie kluczy SSH, plików hostów i plików dzienników oraz niepotrzebnych certyfikatów.                                                                                                                                       |
| Bezpieczeństwo                                                     | Unikaj używania LVM.                                                                                                                                                                                                                                            |
| Bezpieczeństwo                                                     | Uwzględnij najnowsze wersje wymaganych bibliotek: </br> -OpenSSL v 1.0 lub nowszy </br> — Python 2,5 lub nowszy (zdecydowanie zalecane jest środowisko Python 2.6 +) </br> — Pakiet pyasn1 języka Python, jeśli nie został jeszcze zainstalowany </br> -d. OpenSSL v 1,0 lub nowszy                                                                |
| Bezpieczeństwo                                                     | Wyczyść wpisy historii bash/Shell.                                                                                                                                                                                                                                             |
| Networking                                                   | Domyślnie Dołącz serwer SSH. Ustaw opcję utrzymywanie aktywności SSH na sshd konfiguracji z następującą opcją: ClientAliveInterval 180.                                                                                                                                                        |
| Networking                                                   | Usuń niestandardową konfigurację sieci z obrazu. Usuń plik resolv. conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Wdrożenie                                                   | Zainstaluj najnowszego agenta systemu Azure Linux.</br> -Zainstaluj przy użyciu pakietu RPM lub deb.  </br> — Można również użyć procesu instalacji ręcznej, ale pakiety Instalatora są zalecane i preferowane. </br> — Jeśli ręcznie Zainstaluj agenta z repozytorium GitHub, najpierw skopiuj plik `waagent` do `/usr/sbin` i uruchom (jako główny): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracji agenta jest umieszczony w `/etc/waagent.conf`. |
| Wdrożenie                                                   | Upewnij się, że pomoc techniczna systemu Azure zapewnia naszym partnerom dane wyjściowe w konsoli szeregowej, jeśli jest to możliwe, i podaj odpowiedni limit czasu dla instalowania dysków systemu operacyjnego z magazynu Dodaj następujące parametry do wiersza rozruchowego jądra obrazu: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Wdrożenie                                                   | Brak partycji wymiany na dysku systemu operacyjnego. Można zażądać wymiany na lokalnym dysku zasobu przez agenta systemu Linux.         |
| Wdrożenie                                                   | Należy utworzyć jedną partycję główną dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | 64-bitowy tylko system operacyjny.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazy systemu Windows Server

|||
|-------------| -------------------------|
| **Kategoria**                                                     | **Niezaznaczone**                                                                                                                                                                |
| Bezpieczeństwo                                                         | Użyj bezpiecznego obrazu podstawowego systemu operacyjnego. Wirtualny dysk twardy używany do źródła dowolnego obrazu na podstawie systemu Windows Server musi pochodzić z obrazów systemu operacyjnego Windows Server dostarczonych za pośrednictwem Microsoft Azure. |
| Bezpieczeństwo                                                         | Zainstaluj wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Bezpieczeństwo                                                         | Aplikacje nie powinny być zależne od nazw użytkowników z ograniczeniami, takich jak administrator, root lub administrator.                                                                |
| Bezpieczeństwo                                                         | Włącz szyfrowanie dysków funkcją BitLocker zarówno dla dysków twardych systemu operacyjnego, jak i dysków twardych z danymi.                                                             |
| Bezpieczeństwo                                                         | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na tylko niezbędne role, funkcje, usługi i porty sieci systemu Windows Server.                         |
| Bezpieczeństwo                                                         | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Bezpieczeństwo                                                         | Ustaw aktualizację zabezpieczeń obrazów systemu Windows Server na autoaktualizację.                                                                                                                |
| Bezpieczeństwo                                                         | Obraz VHD obejmuje tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne. Brak tylnych drzwi.                             |
| Bezpieczeństwo                                                         | Wyłącz reguły zapory, chyba że aplikacja opiera się na nich, na przykład urządzenie zapory.                                                               |
| Bezpieczeństwo                                                         | Usuń wszystkie informacje poufne z obrazu VHD, w tym pliki hostów, pliki dzienników i niepotrzebne certyfikaty.                                              |
| Wdrożenie                                                       | 64-bitowy tylko system operacyjny.                            |

Nawet jeśli Twoja organizacja nie ma obrazów w portalu Azure Marketplace, rozważ sprawdzenie konfiguracji obrazów systemów Windows i Linux przed zaleceń.

## <a name="contacting-customers"></a>Kontaktowanie się z klientami

Aby zidentyfikować klientów i ich kontaktowe adresy e-mail:

1.  W portal Cloud Partner na lewej szyny wybierz pozycję **szczegółowe informacje**.
2.  Na karcie **zamówienia i użycie** Użyj pól **Data rozpoczęcia** i **Data zakończenia** , aby zbadać użycie w wymaganym zakresie dat. Pokazuje, które subskrypcje platformy Azure są codziennie używane na potrzeby oferty. Wyeksportuj te dane. 
3.  Na przykład na karcie **Klient** należy wykonać zapytanie i wyeksportować bazę klientów.
4.  Dopasuj Identyfikator subskrypcji z kroku 2 do identyfikatora subskrypcji z kroku 3, aby znaleźć niezbędne informacje o klientach.
