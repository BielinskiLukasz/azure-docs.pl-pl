---
title: Zarządzanie replikami odczytu — Azure Database for MariaDB Azure Portal
description: W tym artykule opisano sposób konfigurowania replik odczytu i zarządzania nimi w Azure Database for MariaDB przy użyciu portalu
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 3ca6ef3c368a5f578cc90fae3923caa89f3b076a
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537751"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MariaDB przy użyciu Azure Portal

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi w usłudze Azure Database for MariaDB przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , który będzie używany jako serwer źródłowy.

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

> [!IMPORTANT]
> Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejący serwer Azure Database for MariaDB, który ma być używany jako główny. Ta akcja powoduje otwarcie strony **Przegląd** .

3. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

4. Wybierz pozycję **Dodaj replikę**.

   ![Azure Database for MariaDB — replikacja](./media/howto-read-replica-portal/add-replica.png)

5. Wprowadź nazwę serwera repliki.

    ![Azure Database for MariaDB — nazwa repliki](./media/howto-read-replica-portal/replica-name.png)

6. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera źródłowego.

    ![Azure Database for MariaDB — lokalizacja repliki](./media/howto-read-replica-portal/replica-location.png)

7. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co serwer główny. Konfigurację serwera repliki można zmienić po jego utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

Po utworzeniu serwera repliki można go wyświetlić w bloku **replikacja** .

   ![Repliki list Azure Database for MariaDB](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między źródłem a repliką nie można jej cofnąć. Serwer repliki stał się serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Nie można ponownie wykonać tego serwera w replice.

Aby zatrzymać replikację między źródłem a serwerem repliki z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MariaDB. 

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   ![Azure Database for MariaDB — zatrzymywanie replikacji wybierz serwer](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wybierz pozycję **Zatrzymaj replikację**.

   ![Azure Database for MariaDB — zatrzymywanie replikacji](./media/howto-read-replica-portal/stop-replication.png)

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   ![Azure Database for MariaDB — potwierdzenie zatrzymywania replikacji](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MariaDB.

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   ![Azure Database for MariaDB — usuwanie repliki wybierz serwer](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wybierz pozycję **Usuń replikę**

   ![Azure Database for MariaDB-Usuń replikę](./media/howto-read-replica-portal/delete-replica.png)

5. Wpisz nazwę repliki, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie repliki.  

   ![Potwierdzenie usunięcia repliki Azure Database for MariaDB](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Usuwanie serwera źródłowego

> [!IMPORTANT]
> Usunięcie serwera źródłowego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera źródłowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer źródłowy z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MariaDB.

2. W obszarze **Przegląd** wybierz pozycję **Usuń**.

   ![Azure Database for MariaDB-Usuń wzorzec](./media/howto-read-replica-portal/delete-master-overview.png)

3. Wpisz nazwę serwera źródłowego, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie serwera źródłowego.  

   ![Azure Database for MariaDB — Usuń główne potwierdzenie](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer repliki Azure Database for MariaDB, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **metryki** :

3. Wybierz pozycję **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   ![Wybierz opóźnienie replikacji](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obraz wybiera 30-minutowy zakres czasu.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Wyświetl zwłokę replikacji dla wybranego zakresu czasu. Na poniższej ilustracji przedstawiono ostatnie 30 minut dla dużego obciążenia.

   ![Wybierz zakres czasu 30 minut](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
