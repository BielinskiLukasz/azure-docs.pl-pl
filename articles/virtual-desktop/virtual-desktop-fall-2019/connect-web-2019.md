---
title: Łączenie z klientem sieci Web Windows Virtual Desktop (klasyczny) — Azure
description: Jak nawiązać połączenie z pulpitem wirtualnym systemu Windows (klasycznym) przy użyciu klienta sieci Web.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008427"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Nawiązywanie połączenia z pulpitem wirtualnym systemu Windows (klasycznym) z klientem sieci Web

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../connect-web.md).

Klient sieci Web umożliwia dostęp do zasobów pulpitu wirtualnego systemu Windows z przeglądarki sieci Web bez długotrwałego procesu instalacji.

>[!NOTE]
>Klient sieci Web nie ma obecnie obsługi systemu operacyjnego dla urządzeń przenośnych.

## <a name="supported-operating-systems-and-browsers"></a>Obsługiwane systemy operacyjne i przeglądarki

Chociaż każda przeglądarka z obsługą języka HTML5 powinna działać, oficjalnie obsługujemy następujące systemy operacyjne i przeglądarki.

| Przeglądarka           | Obsługiwane systemy operacyjne                     | Uwagi               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Wersja 55 lub nowsza |
| Google Chrome     | Windows, macOS, Linux, system operacyjny Chrome |                     |

## <a name="access-remote-resources-feed"></a>Uzyskiwanie dostępu do źródeł danych zdalnych

W przeglądarce przejdź do klienta sieci Web pulpitu wirtualnego systemu Windows pod adresem <https://rdweb.wvd.microsoft.com/webclient> i zaloguj się przy użyciu konta użytkownika.

>[!NOTE]
>Jeśli używasz pulpitu wirtualnego systemu Windows z integracją Azure Resource Manager, w zamian Połącz się z zasobami <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Jeśli zalogowano się już przy użyciu innego konta Azure Active Directory niż ten, który ma być używany dla pulpitu wirtualnego systemu Windows, należy się wylogować lub użyć prywatnego okna przeglądarki.

Po zalogowaniu powinna zostać wyświetlona lista zasobów. Zasoby można uruchamiać, wybierając takie jak normalna aplikacja na karcie **wszystkie zasoby** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o używaniu klienta sieci Web, zapoznaj się z tematem Rozpoczynanie [pracy z klientem sieci Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
