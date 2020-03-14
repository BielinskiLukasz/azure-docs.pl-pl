---
title: Odmowa dostępu do sieci publicznej — Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować odmowę dostępu do sieci publicznej za pomocą Azure Portal dla Azure Database for PostgreSQL jednego serwera
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375124"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odmowa dostępu do sieci publicznej w Azure Database for PostgreSQL pojedynczym serwerze przy użyciu Azure Portal

W tym artykule opisano sposób konfigurowania Azure Database for PostgreSQL pojedynczego serwera, aby odmówić wszystkich publicznych konfiguracji i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, potrzebne są:

* [Azure Database for PostgreSQL pojedynczy serwer](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ustaw Odmów dostępu do sieci publicznej

Wykonaj następujące kroki, aby ustawić PostgreSQL jeden serwer Odmów dostępu do sieci publicznej:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący Azure Database for PostgreSQL pojedynczy serwer.

1. Na stronie PostgreSQL Single Server w obszarze **Ustawienia**kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę Konfiguracja zabezpieczeń połączenia.

1. W polu **Odmów dostępu do sieci publicznej**wybierz pozycję **tak** , aby włączyć opcję Odmów dostępu publicznego dla pojedynczego serwera PostgreSQL.

    ![Azure Database for PostgreSQL dostęp do sieci przy pojedynczym serwerze](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

1. Powiadomienie zostanie potwierdzone, że ustawienie zabezpieczeń połączenia zostało pomyślnie włączone.

    ![Odmowa dostępu do sieci przez pojedynczy serwer Azure Database for PostgreSQL](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-on-metric.md).