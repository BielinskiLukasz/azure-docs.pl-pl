---
title: Konfiguracja protokołu TLS — Azure Portal — Azure Database for MySQL
description: Dowiedz się, jak skonfigurować konfigurację protokołu TLS przy użyciu Azure Portal dla Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 46eaa6a3b97967da9c4743d0cf1f6edc8f90b1ce
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119788"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Konfigurowanie ustawień protokołu TLS w Azure Database for MySQL przy użyciu Azure Portal

W tym artykule opisano, jak można skonfigurować serwer Azure Database for MySQL, aby wymusić minimalną wersję protokołu TLS dozwoloną dla połączeń i odmówić wszystkich połączeń z niższą wersją protokołu TLS niż skonfigurowana minimalna wersja protokołu TLS w celu zwiększenia bezpieczeństwa sieci.

W celu nawiązania połączenia z ich Azure Database for MySQL można wymusić stosowanie protokołu TLS. Klienci mają teraz możliwość ustawienia minimalnej wersji protokołu TLS dla serwera bazy danych. Na przykład ustawienie minimalnej wersji protokołu TLS na 1,0 oznacza, że klienci mogą łączyć się przy użyciu protokołów TLS 1.0, 1.1 i 1,2. Alternatywnie ustawienie tego ustawienia na 1,2 oznacza, że zezwalasz tylko klientom na łączenie się z protokołem TLS 1.2 + i wszystkimi połączeniami przychodzącymi z protokołem TLS 1,0 i TLS 1,1 zostanie odrzucone.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Ustawianie konfiguracji protokołu TLS dla Azure Database for MySQL

Wykonaj następujące kroki, aby ustawić minimalną wersję protokołu TLS dla serwera MySQL:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL.

1. Na stronie serwer MySQL w obszarze **Ustawienia**kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **minimalna wersja protokołu TLS**wybierz **1,2** , aby odmówić połączeń z protokołem TLS w wersji niższej niż TLS 1,2 dla serwera MySQL.

    ![Konfiguracja protokołu TLS Azure Database for MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Pomyślna Konfiguracja protokołu TLS Azure Database for MySQL](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat tworzenia alertów dotyczących metryk](howto-alert-on-metric.md)