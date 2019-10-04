---
title: Skonfiguruj i uzyskaj dostęp do dzienników serwera w Azure Database for PostgreSQL-pojedynczym serwerze z Azure Portal
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników serwera w Azure Database for PostgreSQL-pojedynczym serwerze z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841521"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurowanie i dostęp do Azure Database for PostgreSQL-pojedynczych dzienników serwera z Azure Portal

Można skonfigurować, wyświetlić i pobrać [dzienniki Azure Database for PostgreSQL](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dzienników zapytań i dzienników błędów. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 

   ![Zrzut ekranu przedstawiający opcje dzienników serwera](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Aby wyświetlić parametry serwera, wybierz **pozycję kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Zmień parametry, które należy dostosować. Wszystkie zmiany wprowadzone w tej sesji są wyróżnione kolorem purpurowym.

   Po zmianie parametrów wybierz pozycję **Zapisz**. Możesz też odrzucić zmiany. 

   ![Zrzut ekranu przedstawiający opcje parametrów serwera](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stronie **parametry serwera** możesz powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych dzienników i pobrać poszczególne pliki dziennika. 

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika.

   ![Zrzut ekranu strony dzienników serwera z wyróżnioną listą dzienników](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **PostgreSQL-yyyy-mm-dd_hh0000. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dziennika obracają się co godzinę lub 100 MB, w zależności od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

   ![Zrzut ekranu strony dzienników serwera z wyróżnionym polem wyszukiwania i wynikami](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Aby pobrać pojedyncze pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   ![Zrzut ekranu strony Dzienniki serwera z wyróżnioną ikoną Strzałka w dół](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Następne kroki
- Zobacz [Dzienniki serwera dostępu w interfejsie wiersza polecenia](howto-configure-server-logs-using-cli.md) , aby dowiedzieć się, jak programowo pobierać dzienniki.
- Dowiedz się więcej o [dziennikach serwera](concepts-server-logs.md) w Azure Database for PostgreSQL. 
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania PostgreSQL, zapoznaj się z dokumentacją PostgreSQL w temacie [raportowanie błędów i rejestrowanie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

