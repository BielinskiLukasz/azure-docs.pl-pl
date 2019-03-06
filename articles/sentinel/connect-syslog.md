---
title: Zbieranie danych dziennika systemu w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane usługi Syslog w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55949da97f58f1d8c1670f69d25e92d6bb4e9eef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447306"
---
# <a name="connect-your-external-solution-using-syslog"></a>Połącz swoje rozwiązanie zewnętrznych przy użyciu protokołu Syslog

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć wszystkie lokalne urządzenie, obsługującego usługi Syslog do platformy Azure przez wartownika. Odbywa się przy użyciu agenta, oparte na maszynie z systemem Linux między urządzeniem i przez wartownika Azure. W przypadku maszyną z systemem Linux na platformie Azure, można przesyłać strumieniowo dzienniki z urządzenia lub aplikacji z obszarem roboczym dedykowanym tworzenie na platformie Azure i połączyć ją. Jeśli maszyną z systemem Linux nie jest na platformie Azure, można przesyłać strumieniowo dzienniki z urządzenia do dedykowanej na lokalnej maszynie Wirtualnej lub maszyny, na którym należy zainstalować agenta dla systemu Linux. 

> [!NOTE]
> Jeśli urządzenie obsługuje format CEF Syslog, połączenie jest pełniejszy i należy wybrać tę opcję i postępuj zgodnie z instrukcjami w [zbieranie danych z formatu CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to działa

Zbieranie dzienników systemowych odbywa się przy użyciu agenta dla systemu Linux. Domyślnie agenta dla systemu Linux odbiera zdarzenia z demona usługi Syslog za pośrednictwem protokołu UDP, ale w przypadkach, w której maszyny z systemem Linux oczekuje się, zbierać dużą liczbę zdarzeń dziennika systemu, takie jak w przypadku agenta systemu Linux jest odbierania zdarzeń z innymi urządzeniami, konfiguracja zostanie zmodyfikowany na potrzeby Użyj transportu TCP między demona usługi Syslog i agenta.

## <a name="connect-your-syslog-appliance"></a>Połącz urządzenie z usługi Syslog

1. W portalu Azure przez wartownika wybierz **zbierania danych** i wybierz polecenie **Syslog** kafelka.
2. Jeśli maszyną z systemem Linux nie jest w obrębie platformy Azure, Pobierz i zainstaluj, Azure, przez wartownika **agenta dla systemu Linux** na urządzeniu. 
1. Jeśli pracujesz na platformie Azure, wybierz lub Utwórz Maszynę wirtualną, która w obrębie platformy Azure przez wartownika obszar roboczy, który jest przeznaczony do odbierania komunikatów Syslog. Wybierz maszynę Wirtualną w usłudze Azure przez wartownika w obszarach roboczych, a następnie kliknij przycisk **Connect** w górnej części okienka po lewej stronie.
3. Kliknij przycisk **konfigurowania dzienników, które mają być zbierane** w instalacji łącznika usługi Syslog. 
4. Kliknij przycisk **naciśnij tutaj, aby otworzyć blok konfiguracji**.
1. Wybierz **danych** i następnie **Syslog**.
   - Upewnij się, że każde urządzenie, która jest wysyłana przez Syslog znajduje się w tabeli. Dla każdego obiektu chcesz monitorować, Ustaw ważność. Kliknij przycisk **Zastosuj**.
1. Na komputerze Syslog upewnij się, że wysyłasz tych obiektach. 

3. Aby użyć odpowiednich schematu w usłudze Log Analytics dla dzienników Syslog, możesz wyszukać **Syslog**.




## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń lokalnych Syslog do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
