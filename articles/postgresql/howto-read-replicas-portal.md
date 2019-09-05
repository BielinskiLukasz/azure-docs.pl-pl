---
title: Zarządzanie replikami odczytów dla Azure Database for PostgreSQL-pojedynczego serwera z Azure Portal
description: Dowiedz się, jak zarządzać replikami odczytu Azure Database for PostgreSQL-pojedynczym serwerem z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bd1317242f84b4d8bd039da89d991a73f6cd3781
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309309"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for PostgreSQL-pojedynczym serwerze z Azure Portal

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Azure Database for PostgreSQL z Azure Portal. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).


## <a name="prerequisites"></a>Wymagania wstępne
[Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) być serwerem głównym.

## <a name="prepare-the-master-server"></a>Przygotowywanie serwera głównego
Te kroki muszą być używane do przygotowywania serwera głównego w warstwach zoptymalizowanych pod kątem Ogólnego przeznaczenia lub pamięci. Serwer główny jest przygotowany do replikacji przez ustawienie parametru Azure. replication_support. Po zmianie parametru replikacji wymagane jest ponowne uruchomienie serwera, aby zmiany zaczęły obowiązywać. W Azure Portal te dwa kroki są hermetyzowane przez pojedynczy przycisk, **Włącz obsługę replikacji**.

1. W Azure Portal wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako główny.

2. Na pasku bocznym serwera w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz pozycję **Włącz obsługę replikacji**. 

   ![Włącz obsługę replikacji](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Potwierdź, że chcesz włączyć obsługę replikacji. Ta operacja spowoduje ponowne uruchomienie serwera głównego. 

   ![Potwierdź włączenie obsługi replikacji](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Po zakończeniu operacji otrzymasz dwie Azure Portal powiadomienia. Istnieje jedno powiadomienie dotyczące aktualizowania parametru serwera. Istnieje inne powiadomienie dotyczące ponownego uruchomienia serwera, które następuje natychmiast.

   ![Powiadomienia o powodzeniu — Włączanie](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Odśwież stronę Azure Portal, aby zaktualizować pasek narzędzi replikacji. Teraz można tworzyć repliki odczytu dla tego serwera.

   ![Zaktualizowany pasek narzędzi](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Włączanie obsługi replikacji jest operacją jednorazową na serwerze głównym. Przycisk **Wyłącz obsługę replikacji** jest dostępny dla wygody użytkownika. Nie zalecamy wyłączania obsługi replikacji, chyba że masz pewność, że nie utworzysz repliki na tym serwerze głównym. Nie można wyłączyć obsługi replikacji, gdy serwer główny ma istniejące repliki.


## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Aby utworzyć replikę odczytu, wykonaj następujące kroki:

1. Wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako serwer główny. 

2. Na pasku bocznym serwera w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz pozycję **Dodaj replikę**.

   ![Dodaj replikę](./media/howto-read-replicas-portal/add-replica.png)

4. Wprowadź nazwę repliki do odczytu. 

    ![Nazwij replikę](./media/howto-read-replicas-portal/name-replica.png)

5. Wybierz lokalizację dla repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera głównego.

    ![Wybierz lokalizację](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

6. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

Replika jest tworzona przy użyciu tych samych ustawień obliczeniowych i magazynu co główny. Po utworzeniu repliki można zmienić kilka ustawień niezależnie od serwera głównego: generowanie obliczeń, rdzeni wirtualnych, magazyn i okres przechowywania zapasowego. Warstwę cenową można także zmienić niezależnie, z wyjątkiem warstwy Podstawowa lub z niej.

> [!IMPORTANT]
> Przed zaktualizowaniem ustawienia serwera głównego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

Po utworzeniu repliki odczytu można ją wyświetlić w oknie **replikacja** :

![Wyświetl nową replikę w oknie replikacji](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zatrzymaj replikację
Można zatrzymać replikację między serwerem głównym a repliką odczytu.

> [!IMPORTANT]
> Po zatrzymaniu replikacji na serwer główny i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

Aby zatrzymać replikację między serwerem głównym i repliką odczytu z Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz serwer główny Azure Database for PostgreSQL.

2. W menu serwer w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   ![Wybierz replikę](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz pozycję **Zatrzymaj replikację**.

   ![Wybieranie opcji Zatrzymaj replikację](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Wybierz **przycisk OK** , aby zatrzymać replikację.

   ![Potwierdź zatrzymanie replikacji](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Usuń serwer główny
Aby usunąć serwer główny, należy wykonać te same czynności co w celu usunięcia autonomicznego serwera Azure Database for PostgreSQL. 

> [!IMPORTANT]
> Po usunięciu serwera głównego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

Aby usunąć serwer z Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz serwer główny Azure Database for PostgreSQL.

2. Otwórz stronę **Przegląd** dla serwera. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd serwera wybierz opcję usunięcia serwera głównego.](./media/howto-read-replicas-portal/delete-server.png)
 
3. Wprowadź nazwę serwera głównego do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie serwera głównego.

   ![Potwierdź, aby usunąć serwer główny](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Usuwanie repliki
Replikę odczytu można usunąć podobnie jak w przypadku usuwania serwera głównego.

- W Azure Portal Otwórz stronę **Przegląd** repliki odczytu. Wybierz pozycję **Usuń**.

   ![Na stronie Przegląd repliki wybierz opcję usunięcia repliki](./media/howto-read-replicas-portal/delete-replica.png)
 
Możesz również usunąć replikę odczytu z okna **replikacji** , wykonując następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for PostgreSQL.

2. W menu serwer w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz replikę do odczytania do usunięcia.

   ![Wybierz replikę do usunięcia](./media/howto-read-replicas-portal/select-replica.png)
 
4. Wybierz pozycję **Usuń replikę**.

   ![Wybierz pozycję Usuń replikę](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Wprowadź nazwę repliki do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie repliki.

   ![Potwierdź, aby usunąć replikę](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorowanie repliki
Do monitorowania replik odczytu są dostępne dwie metryki.

### <a name="max-lag-across-replicas-metric"></a>Metryka maksymalnego opóźnienia między replikami
Metryka **maks. opóźnienie między replikami** pokazuje opóźnienie w bajtach między serwerem głównym a repliką najbardziej opóźnioną. 

1.  W Azure Portal wybierz serwer główny Azure Database for PostgreSQL.

2.  Wybierz pozycję **Metryki**. W oknie **metryki** wybierz pozycję **maks. opóźnienie między replikami**.

    ![Monitoruj maksymalne opóźnienie między replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Dla **agregacji**wybierz **wartość Max**.


### <a name="replica-lag-metric"></a>Metryka opóźnienia repliki
Metryka **opóźnienia repliki** przedstawia czas od ostatniego odtworzenia transakcji w replice. Jeśli na wzorcu nie ma żadnych transakcji, Metryka uwzględnia ten czas opóźnienia.

1. W Azure Portal wybierz Azure Database for PostgreSQL Odczytaj replikę.

2. Wybierz pozycję **Metryki**. W oknie **metryki** wybierz pozycję **opóźnienie repliki**.

   ![Monitorowanie opóźnienia repliki](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Dla **agregacji**wybierz **wartość Max**. 
 
## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytaniu replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie wiersza polecenia platformy Azure](howto-read-replicas-cli.md).