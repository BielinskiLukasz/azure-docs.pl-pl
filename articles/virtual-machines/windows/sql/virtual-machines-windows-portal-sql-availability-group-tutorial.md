---
title: SQL Server grupy dostępności — Azure Virtual Machines — samouczek | Microsoft Docs
description: W tym samouczku pokazano SQL Server, jak utworzyć grupę dostępności zawsze włączona na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 7683812c5ee98d21d5aa8191a88926669b2ed120
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102364"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>Samouczek: Ręczne konfigurowanie zawsze włączonych grup dostępności na maszynie wirtualnej platformy Azure

W tym samouczku pokazano SQL Server, jak utworzyć grupę dostępności zawsze włączona na platformie Azure Virtual Machines. Kompletny samouczek tworzy grupę dostępności z repliką bazy danych na dwóch serwerach SQL.

**Szacowany czas**: Po spełnieniu wymagań wstępnych trwa około 30 minut.

Na diagramie przedstawiono elementy, które można skompilować w samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Wymagania wstępne

W samouczku założono, że masz podstawową wiedzę na temat SQL Server zawsze włączonymi grupami dostępności. Jeśli potrzebujesz więcej informacji, zobacz temat [Omówienie grup dostępności zawsze włączone (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

W poniższej tabeli wymieniono wymagania wstępne, które należy wykonać przed rozpoczęciem pracy z tym samouczkiem:

|  |Wymaganie |Opis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dwa serwery SQL | — W zestawie dostępności platformy Azure <br/> — W pojedynczej domenie <br/> — Z zainstalowaną funkcją klaster trybu failover |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Udział plików dla monitora klastra |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi SQL Server | Konto domeny |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto usługi SQL Server Agent | Konto domeny |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otwarte porty zapory | -SQL Server: **1433** dla wystąpienia domyślnego <br/> -Punkt końcowy dublowania bazy danych: **5022** lub dowolny dostępny port <br/> -Sonda kondycji adresu IP modułu równoważenia obciążenia grupy dostępności: **59999** lub dowolny dostępny port <br/> — Sonda kondycji adresu IP podstawowego modułu równoważenia obciążenia klastra: **58888** lub dowolny dostępny port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Dodawanie funkcji klaster trybu failover | Oba serwery SQL wymagają tej funkcji |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Konto domeny instalacji | -Administrator lokalny na każdym SQL Server <br/> -Składowa SQL Server stałej roli serwera sysadmin dla każdego wystąpienia SQL Server  |


Przed rozpoczęciem tego samouczka należy [spełnić wymagania wstępne dotyczące tworzenia zawsze dostępnych grup dostępności na platformie Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Jeśli te wymagania wstępne zostały już wykonane, możesz przejść do [tworzenia klastra](#CreateCluster).

  >[!NOTE]
  > Wiele kroków opisanych w tym samouczku można teraz zautomatyzować za pomocą [interfejsu wiersza polecenia maszyny wirtualnej Azure SQL](virtual-machines-windows-sql-availability-group-cli.md) i [szablonów szybkiego startu platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Tworzenie klastra

Po zakończeniu wymagań wstępnych pierwszym krokiem jest utworzenie klastra trybu failover systemu Windows Server zawierającego dwa serwery SQL i serwer monitora.

1. Protokół RDP do pierwszego SQL Server przy użyciu konta domeny, które jest kontem administratora na serwerze SQL Server i serwerem monitora.

   >[!TIP]
   >Jeśli zawarto [dokument z wymaganiami wstępnymi](virtual-machines-windows-portal-sql-availability-group-prereq.md), utworzono konto o nazwie **CORP\Install**. Użyj tego konta.

2. Na pulpicie nawigacyjnym **Menedżer serwera** wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Menedżer klastra trybu failover**.
3. W lewym okienku kliknij prawym przyciskiem myszy pozycję **Menedżer klastra trybu failover**, a następnie kliknij pozycję **Utwórz klaster**.
   ![Tworzenie klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. W Kreatorze tworzenia klastra utwórz klaster z jednym węzłem, przechodząc przez strony z ustawieniami w poniższej tabeli:

   | Strona | Ustawienia |
   | --- | --- |
   | Przed rozpoczęciem |Użyj domyślnych |
   | Wybierz serwery |Wpisz nazwę pierwszej SQL Server w polu **Wprowadź nazwę serwera** , a następnie kliknij przycisk **Dodaj**. |
   | Ostrzeżenie dotyczące walidacji |Wybierz **pozycję nie. nie wymagaj pomocy technicznej firmy Microsoft dla tego klastra i dlatego nie należy uruchamiać testów weryfikacyjnych. Po kliknięciu przycisku Dalej Kontynuuj tworzenie klastra**. |
   | Punkt dostępu do administrowania klastrem |Wpisz nazwę klastra, na przykład **SQLAGCluster1** w polu **Nazwa klastra**.|
   | Potwierdzenie |Użyj wartości domyślnych, chyba że używasz funkcji miejsca do magazynowania. Zapoznaj się z uwagą poniżej tej tabeli. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ustaw adres IP klastra trybu failover systemu Windows Server

1. W **Menedżer klastra trybu failover**przewiń w dół do **zasobów podstawowe klastra** i rozwiń Szczegóły klastra. Powinny pojawić się zarówno **nazwa** i **adres IP** **zasobów** w stanu. Nie można przełączyć zasobu adresu IP do trybu online, ponieważ klaster ma przypisany ten sam adres IP co komputer, w związku z czym jest to zduplikowany adres.

2. Kliknij prawym przyciskiem myszy zasób niepowodzenie **adresu IP** , a następnie kliknij polecenie **Właściwości**.

   ![Właściwości klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Wybierz pozycję **statyczny adres IP** i określ dostępny adres z tej samej podsieci co maszyny wirtualne.

4. W sekcji **zasoby podstawowe klastra** kliknij prawym przyciskiem myszy pozycję Nazwa klastra, a następnie kliknij polecenie **Przełącz do trybu online**. Następnie poczekaj, aż oba zasoby będą w trybie online. Gdy zasób Nazwa klastra przejdzie w tryb online, aktualizuje serwer DC przy użyciu nowego konta komputera usługi AD. Użyj tego konta usługi AD, aby później uruchomić usługę klastrowaną grupy dostępności.

### <a name="addNode"></a>Dodawanie innych SQL Server do klastra

Dodaj inne SQL Server do klastra.

1. W drzewie przeglądarki kliknij prawym przyciskiem myszy klaster, a następnie kliknij polecenie **Dodaj węzeł**.

    ![Dodawanie węzła do klastra](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. W **Kreatorze dodawania węzła**kliknij przycisk **dalej**. Na stronie **Wybierz serwery** dodaj drugą SQL Server. Wpisz nazwę serwera w polu **Wprowadź nazwę serwera** , a następnie kliknij przycisk **Dodaj**. Gdy skończysz, kliknij przycisk **dalej**.

1. Na stronie **ostrzeżenie dotyczące weryfikacji** kliknij przycisk **nie** (w scenariuszu produkcyjnym należy wykonać testy weryfikacyjne). Następnie kliknij przycisk **Dalej**.

8. W przypadku korzystania z funkcji miejsca do magazynowania na stronie **potwierdzenie** Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra.**

   ![Potwierdzenie dodawania węzła](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Jeśli używasz funkcji miejsca do magazynowania i nie zaznaczaj **żadnych opcji Dodaj wszystkie odpowiednie magazyny do klastra**, system Windows odłącza dyski wirtualne podczas procesu klastrowania. W związku z tym nie są one wyświetlane w Menedżerze dysków ani w Eksploratorze, dopóki nie zostaną usunięte miejsca do magazynowania z klastra i ponownie dołączone przy użyciu programu PowerShell. Funkcja miejsca do magazynowania grupuje wiele dysków w puli magazynów. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739).

1. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

   Menedżer klastra trybu failover pokazuje, że klaster ma nowy węzeł i znajduje się w kontenerze **węzły** .

10. Wyloguj się z sesji pulpitu zdalnego.

### <a name="add-a-cluster-quorum-file-share"></a>Dodawanie udziału plików kworum klastra

W tym przykładzie klaster systemu Windows korzysta z udziału plików w celu utworzenia kworum klastra. W tym samouczku jest używana funkcja kworum większość węzłów i udziałów plików. Aby uzyskać więcej informacji, zobacz [Opis konfiguracji kworum w klastrze trybu failover](https://technet.microsoft.com/library/cc731739.aspx).

1. Nawiąż połączenie z serwerem członkowskim monitora udostępniania plików przy użyciu sesji pulpitu zdalnego.

1. Na **Menedżer serwera**kliknij pozycję **Narzędzia**. Otwórz **przystawkę Zarządzanie komputerem**.

1. Kliknij pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy pozycję **udziały**, a następnie kliknij pozycję **Nowy udział.**

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **Kreatora tworzenia folderu udostępnionego** , aby utworzyć udział.

1. W polu **ścieżka folderu**kliknij przycisk **Przeglądaj** i zlokalizuj lub Utwórz ścieżkę do folderu udostępnionego. Kliknij przycisk **Dalej**.

1. W polu **Nazwa, opis i ustawienia** Sprawdź nazwę i ścieżkę udziału. Kliknij przycisk **Dalej**.

1. W obszarze **uprawnienia do folderu udostępnionego** Ustaw **uprawnienia do dostosowywania**. Kliknij pozycję **niestandardowe...** .

1. Na **Dostosuj uprawnienia**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konto użyte do utworzenia klastra ma pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Kliknij przycisk **OK**.

1. W obszarze **uprawnienia do folderu udostępnionego**kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

1. Wyloguj się z serwera

### <a name="configure-cluster-quorum"></a>Konfigurowanie kworum klastra

Następnie skonfiguruj kworum klastra.

1. Połącz się z pierwszym węzłem klastra przy użyciu pulpitu zdalnego.

1. W **Menedżer klastra trybu failover**kliknij prawym przyciskiem myszy klaster, wskaż polecenie **więcej akcji**, a następnie kliknij polecenie **Konfiguruj ustawienia kworum klastra.**

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. W **Kreatorze konfiguracji kworum klastra**kliknij przycisk **dalej**.

1. W obszarze **wybierz konfigurację kworum**wybierz opcję **Wybierz monitor kworum**, a następnie kliknij przycisk **dalej**.

1. W obszarze **Wybierz monitor kworum**kliknij pozycję **Konfiguruj Monitor udziału plików**.

   >[!TIP]
   >System Windows Server 2016 obsługuje monitor w chmurze. W przypadku wybrania tego typu monitora nie jest potrzebny monitor udostępniania plików. Aby uzyskać więcej informacji, zobacz [wdrażanie monitora chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). W tym samouczku jest używany monitor udostępniania plików, który jest obsługiwany przez poprzednie systemy operacyjne.

1. Na **skonfigurować Monitor udostępniania plików**, wpisz ścieżkę dla utworzonego udziału. Kliknij przycisk **Dalej**.

1. Sprawdź ustawienia w obszarze **potwierdzenie**. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

Zasoby podstawowe klastra są skonfigurowane za pomocą monitora udziału plików.

## <a name="enable-availability-groups"></a>Włączanie grup dostępności

Następnie Włącz funkcję **zawsze włączone grupy dostępności** . Wykonaj te czynności na obu serwerach SQL.

1. Na ekranie **startowym** uruchom **SQL Server Configuration Manager**.
2. W drzewie przeglądarki kliknij **SQL Server usługi**, a następnie kliknij prawym przyciskiem myszy usługę **SQL Server (MSSQLSERVER)** i kliknij polecenie **Właściwości**.
3. Kliknij kartę **AlwaysOn o wysokiej dostępności** , a następnie wybierz pozycję **Włącz zawsze włączone grupy dostępności**w następujący sposób:

    ![Włącz Zawsze włączone grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Kliknij przycisk **zastosować**. W podręcznym oknie dialogowym kliknij przycisk **OK** .

5. Uruchom ponownie usługę SQL Server.

Powtórz te kroki na drugim SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Utwórz bazę danych na pierwszej SQL Server

1. Uruchom plik RDP na pierwszej SQL Server przy użyciu konta domeny, które jest członkiem stałej roli serwera sysadmin.
1. Otwórz SQL Server Management Studio i Połącz się z pierwszym SQL Server.
7. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy pozycję **bazy danych** , a następnie kliknij pozycję **Nowa baza danych**.
8. W polu **Nazwa bazy danych**wpisz **MyDB1**, a następnie kliknij przycisk **OK**.

### <a name="backupshare"></a>Utwórz udział kopii zapasowych

1. Na pierwszej SQL Server w **Menedżer serwera**kliknij pozycję **Narzędzia**. Otwórz **przystawkę Zarządzanie komputerem**.

1. Kliknij pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy pozycję **udziały**, a następnie kliknij pozycję **Nowy udział.**

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Użyj **Kreatora tworzenia folderu udostępnionego** , aby utworzyć udział.

1. W polu **ścieżka folderu**kliknij przycisk **Przeglądaj** i zlokalizuj lub Utwórz ścieżkę do folderu udostępnionego kopii zapasowej bazy danych. Kliknij przycisk **Dalej**.

1. W polu **Nazwa, opis i ustawienia** Sprawdź nazwę i ścieżkę udziału. Kliknij przycisk **Dalej**.

1. W obszarze **uprawnienia do folderu udostępnionego** Ustaw **uprawnienia do dostosowywania**. Kliknij pozycję **niestandardowe...** .

1. Na **Dostosuj uprawnienia**, kliknij przycisk **Dodaj...** .

1. Upewnij się, że konta usług SQL Server i SQL Server Agent dla obu serwerów mają pełną kontrolę.

   ![Nowy udział](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Kliknij przycisk **OK**.

1. W obszarze **uprawnienia do folderu udostępnionego**kliknij przycisk **Zakończ**. Kliknij przycisk **Zakończ** ponownie.  

### <a name="take-a-full-backup-of-the-database"></a>Wykonaj pełną kopię zapasową bazy danych

Należy wykonać kopię zapasową nowej bazy danych w celu zainicjowania łańcucha dzienników. Jeśli nie zostanie podjęta kopia zapasowa nowej bazy danych, nie można jej uwzględnić w grupie dostępności.

1. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy bazę danych, wskaż polecenie **zadania...** , a następnie kliknij pozycję **Utwórz kopię zapasową**.

1. Kliknij przycisk **OK** , aby utworzyć pełną kopię zapasową do domyślnej lokalizacji kopii zapasowej.

## <a name="create-the-availability-group"></a>Utwórz grupę dostępności
Teraz można przystąpić do konfigurowania grupy dostępności, wykonując następujące czynności:

* Utwórz bazę danych na pierwszej SQL Server.
* Wykonaj pełną kopię zapasową dziennika transakcji i wykonaj kopię zapasową bazy danych
* Przywracanie pełnych i dzienników kopii zapasowych w drugim SQL Server przy użyciu opcji **NORECOVERY**
* Utwórz grupę dostępności (**AG1**) z zatwierdzaniem synchronicznym, automatycznym trybem failover i możliwymi do odczytu replikami pomocniczymi

### <a name="create-the-availability-group"></a>Utwórz grupę dostępności:

1. W sesji pulpitu zdalnego z pierwszym SQL Server. W **Eksplorator obiektów** w programie SSMS kliknij prawym przyciskiem myszy pozycję **AlwaysOn wysoka dostępność** , a następnie kliknij pozycję **Kreator nowej grupy dostępności**.

    ![Uruchom Kreatora nowej grupy dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na stronie **wprowadzenie** kliknij przycisk **dalej**. Na stronie **Określ nazwę grupy dostępności** wpisz nazwę grupy dostępności, na przykład **AG1**, w polu **Nazwa grupy dostępności**. Kliknij przycisk **Dalej**.

    ![Kreator nowej grupy, Określanie nazwy AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na stronie **wybierz bazy** danych wybierz swoją bazę danych i kliknij przycisk **dalej**.

   >[!NOTE]
   >Baza danych spełnia wymagania wstępne dla grupy dostępności, ponieważ wykonano co najmniej jedną pełną kopię zapasową w zamierzonej replice podstawowej.

   ![Kreator nowej grupy, wybieranie baz danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na stronie **Określanie replik** kliknij pozycję **Dodaj**replikę.

   ![Kreator nowej grupy, określanie replik](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Zostanie wyświetlone okno dialogowe **łączenie z serwerem** . Wpisz nazwę drugiego serwera w polu **Nazwa serwera**. Kliknij przycisk **Połącz**.

   Z powrotem na stronie **Określanie replik** powinien zostać wyświetlony drugi serwer wymieniony w obszarze **repliki dostępności**. Skonfiguruj repliki w następujący sposób.

   ![Kreator nowej grupy dostępności, określanie replik (kompletne)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknij przycisk **punkty końcowe** , aby wyświetlić punkt końcowy dublowania bazy danych dla tej grupy dostępności. Użyj tego samego portu, który był używany podczas ustawiania [reguły zapory dla punktów końcowych dublowania bazy danych](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Kreator nowej grupy, wybieranie początkowej synchronizacji danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na stronie **Wybierz początkową synchronizację danych** wybierz pozycję **pełna** i określ udostępnioną lokalizację sieciową. Dla lokalizacji Użyj [utworzonego udziału kopii zapasowej](#backupshare). W przykładzie,  **\\ \\najpierwSQLServer\>\backup.\\ \<** Kliknij przycisk **Dalej**.

   >[!NOTE]
   >Pełna synchronizacja pobiera pełną kopię zapasową bazy danych przy pierwszym wystąpieniu SQL Server i przywraca ją do drugiego wystąpienia. W przypadku dużych baz danych nie zaleca się stosowania pełnej synchronizacji, ponieważ może to zająć dużo czasu. Możesz skrócić ten czas, ręcznie pobierając kopię zapasową bazy danych i przywracając `NO RECOVERY`ją z. Jeśli baza danych została już przywrócona `NO RECOVERY` przy użyciu programu na drugim SQL Server przed skonfigurowaniem grupy dostępności, wybierz pozycję **tylko Dołącz**. Jeśli chcesz wykonać kopię zapasową po skonfigurowaniu grupy dostępności, wybierz pozycję **Pomiń początkową synchronizację danych**.

    ![Kreator nowej grupy, wybieranie początkowej synchronizacji danych](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na stronie **Walidacja** kliknij przycisk **dalej**. Ta strona powinna wyglądać podobnie do poniższej ilustracji:

    ![Kreator nowego programu AG, sprawdzanie poprawności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Istnieje ostrzeżenie dotyczące konfiguracji odbiornika, ponieważ nie skonfigurowano odbiornika grupy dostępności. To ostrzeżenie można zignorować, ponieważ na maszynach wirtualnych platformy Azure utworzysz odbiornik po utworzeniu modułu równoważenia obciążenia platformy Azure.

10. Na stronie **Podsumowanie** kliknij przycisk **Zakończ**, a następnie zaczekaj, aż Kreator skonfiguruje nową grupę dostępności. Na stronie **postęp** możesz kliknąć **więcej szczegółów** , aby wyświetlić szczegółowy postęp. Po zakończeniu pracy kreatora przejrzyj **wyniki** , aby sprawdzić, czy grupa dostępności została pomyślnie utworzona.

     ![Kreator nowej grupy, wyniki](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kliknij przycisk **Zamknij** , aby zakończyć pracę kreatora.

### <a name="check-the-availability-group"></a>Sprawdź grupę dostępności

1. W **Eksplorator obiektów**rozwiń opcję **zawsze włączone wysokiej dostępności**, a następnie rozwiń węzeł **grupy dostępności**. Teraz powinna zostać wyświetlona nowa grupa dostępności w tym kontenerze. Kliknij prawym przyciskiem myszy grupę dostępności, a następnie kliknij pozycję **Pokaż pulpit nawigacyjny**.

   ![Pokaż AG pulpit nawigacyjny](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **Zawsze włączony pulpit nawigacyjny** powinien wyglądać podobnie do tego.

   ![Pulpit nawigacyjny AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Można wyświetlić repliki, tryb trybu failover dla każdej repliki i stan synchronizacji.

2. W **Menedżer klastra trybu failover**kliknij swój klaster. Wybierz pozycję **role**. Użyta nazwa grupy dostępności jest rolą w klastrze. Ta grupa dostępności nie ma adresu IP dla połączeń klientów, ponieważ nie skonfigurowano odbiornika. Odbiornik zostanie skonfigurowany po utworzeniu modułu równoważenia obciążenia platformy Azure.

   ![AG w Menedżer klastra trybu failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nie należy podejmować próby przełączenia grupy dostępności do trybu failover z Menedżer klastra trybu failover. Wszystkie operacje trybu failover powinny być wykonywane z poziomu **pulpitu nawigacyjnego AlwaysOn** w programie SSMS. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące używania Menedżer klastra trybu failover z grupami dostępności](https://msdn.microsoft.com/library/ff929171.aspx).
    >

W tym momencie masz grupę dostępności z replikami w dwóch wystąpieniach SQL Server. Można przenieść grupę dostępności między wystąpieniami. Nie można jeszcze nawiązać połączenia z grupą dostępności, ponieważ nie masz odbiornika. W przypadku maszyn wirtualnych platformy Azure odbiornik wymaga modułu równoważenia obciążenia. Następnym krokiem jest utworzenie modułu równoważenia obciążenia na platformie Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

W przypadku usługi Azure Virtual Machines Grupa dostępności SQL Server wymaga modułu równoważenia obciążenia. Moduł równoważenia obciążenia przechowuje adresy IP dla odbiorników grupy dostępności i klastra trybu failover systemu Windows Server. Ta sekcja zawiera podsumowanie sposobu tworzenia modułu równoważenia obciążenia w Azure Portal.

Azure Load Balancer może być usługa Load Balancer w warstwie Standardowa lub Load Balancer podstawowy. Usługa Load Balancer w warstwie Standardowa ma więcej funkcji niż Load Balancer podstawowa. W przypadku grupy dostępności usługa Load Balancer w warstwie Standardowa jest wymagana w przypadku używania strefy dostępności (zamiast zestawu dostępności). Aby uzyskać szczegółowe informacje na temat różnic między typami modułów równoważenia obciążenia, zobacz [Load Balancer porównanie jednostek SKU](../../../load-balancer/load-balancer-overview.md#skus).

1. W Azure Portal przejdź do grupy zasobów, w której znajdują się serwery SQL, a następnie kliknij pozycję **+ Dodaj**.
1. Wyszukaj **Load Balancer**. Wybierz usługę równoważenia obciążenia opublikowaną przez firmę Microsoft.

   ![AG w Menedżer klastra trybu failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Kliknij przycisk **Utwórz**.
1. Skonfiguruj następujące parametry dla modułu równoważenia obciążenia.

   | Ustawienie | Pole |
   | --- | --- |
   | **Nazwa** |Użyj nazwy tekstowej dla modułu równoważenia obciążenia, na przykład **sqlLB**. |
   | **Typ** |Wewnętrzne |
   | **Sieć wirtualna** |Użyj nazwy sieci wirtualnej platformy Azure. |
   | **Podsieć** |Użyj nazwy podsieci, w której znajduje się maszyna wirtualna.  |
   | **Przypisanie adresu IP** |Static |
   | **Adres IP** |Użyj dostępnego adresu z podsieci. Użyj tego adresu dla odbiornika grupy dostępności. Należy zauważyć, że różni się to od adresu IP klastra.  |
   | **Subskrypcja** |Użyj tej samej subskrypcji co maszyna wirtualna. |
   | **Location** |Użyj tej samej lokalizacji co maszyna wirtualna. |

   Blok Azure Portal powinien wyglądać następująco:

   ![Tworzenie modułu równoważenia obciążenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kliknij przycisk **Utwórz**, aby utworzyć moduł równoważenia obciążenia.

Aby skonfigurować moduł równoważenia obciążenia, należy utworzyć pulę zaplecza, sondę i ustawić reguły równoważenia obciążenia. Zrób to w Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Dodawanie puli zaplecza dla odbiornika grupy dostępności

1. W Azure Portal przejdź do grupy dostępności. Może być konieczne odświeżenie widoku w celu wyświetlenia nowo utworzonego modułu równoważenia obciążenia.

   ![Znajdź Load Balancer w grupie zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Kliknij pozycję Moduł równoważenia obciążenia, kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **+ Dodaj**.

1. Wpisz nazwę puli zaplecza.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **docelowe konfiguracje IP sieci**Sprawdź **maszynę wirtualną** i wybierz obie maszyny wirtualne, które będą hostować repliki grup dostępności. Nie dołączaj serwera monitora udziału plików.

   >[!NOTE]
   >Jeśli obie maszyny wirtualne nie zostaną określone, połączenia będą kończyły się powodzeniem tylko do repliki podstawowej.

1. Kliknij przycisk **OK** , aby utworzyć pulę zaplecza.

### <a name="set-the-probe"></a>Ustawianie sondy

1. Kliknij pozycję Moduł równoważenia obciążenia, kliknijpozycję sondy kondycji, a następnie kliknij pozycję **+ Dodaj**.

1. Ustaw sondę kondycji odbiornika w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokół** | Wybierz TCP | TCP |
   | **Port** | Dowolny nieużywany port | 59999 |
   | **Interval**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna mogła zostać uznana za złą  | 2 |

1. Kliknij przycisk **OK** , aby ustawić sondę kondycji.

### <a name="set-the-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. Kliknij pozycję Moduł równoważenia obciążenia, kliknij pozycję **reguły równoważenia obciążenia**, a następnie kliknij pozycję **+ Dodaj**.

1. Ustaw następujące reguły równoważenia obciążenia odbiornika.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | SQLAlwaysOnEndPointListener |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu utworzonego podczas tworzenia modułu równoważenia obciążenia. |
   | **Protokół** | Wybierz TCP |TCP |
   | **Port** | Użyj portu dla odbiornika grupy dostępności | 1433 |
   | **Port zaplecza** | To pole nie jest używane, gdy jest ustawiany swobodny adres IP dla bezpośredniego powrotu serwera | 1433 |
   | **Badane** |Nazwa określona dla sondy | SQLAlwaysOnEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty, aby można było otworzyć połączenie TCP | 4 |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** | |Włączono |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij przycisk **OK** , aby ustawić reguły równoważenia obciążenia odbiornika.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Dodawanie podstawowego adresu IP klastra dla klastra trybu failover systemu Windows Server (WSFC)

Adres IP usługi WSFC musi również znajdować się w usłudze równoważenia obciążenia.

1. W portalu w tym samym module równoważenia obciążenia platformy Azure kliknij pozycję **Konfiguracja adresu IP frontonu** , a następnie kliknij pozycję **+ Dodaj**. Użyj adresu IP skonfigurowanego dla usługi WSFC w zasobach podstawowych klastra. Ustaw adres IP jako statyczny.

1. W module równoważenia obciążenia kliknij pozycję **sondy kondycji**, a następnie kliknij pozycję **+ Dodaj**.

1. Ustaw sondę kondycji podstawowego adresu IP klastra usługi WSFC w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | WSFCEndPointProbe |
   | **Protokół** | Wybierz TCP | TCP |
   | **Port** | Dowolny nieużywany port | 58888 |
   | **Interval**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna mogła zostać uznana za złą  | 2 |

1. Kliknij przycisk **OK** , aby ustawić sondę kondycji.

1. Ustaw reguły równoważenia obciążenia. Kliknij pozycję **reguły równoważenia obciążenia**, a następnie kliknij pozycję **+ Dodaj**.

1. Ustaw następujące reguły równoważenia obciążenia podstawowego adresu IP klastra.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Text | WSFCEndPoint |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu utworzonego podczas konfigurowania adresu IP usługi WSFC. Różni się to od adresu IP odbiornika |
   | **Protokół** | Wybierz TCP |TCP |
   | **Port** | Użyj portu dla adresu IP klastra. Jest to dostępny port, który nie jest używany przez port sondy odbiornika. | 58888 |
   | **Port zaplecza** | To pole nie jest używane, gdy jest ustawiany swobodny adres IP dla bezpośredniego powrotu serwera | 58888 |
   | **Badane** |Nazwa określona dla sondy | WSFCEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty, aby można było otworzyć połączenie TCP | 4 |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** | |Włączono |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.

1. Kliknij przycisk **OK** , aby ustawić reguły równoważenia obciążenia.

## <a name="configure-listener"></a>Konfigurowanie odbiornika

Następnym krokiem jest skonfigurowanie odbiornika grupy dostępności w klastrze trybu failover.

> [!NOTE]
> W tym samouczku pokazano, jak utworzyć pojedynczy odbiornik z jednym ILB adresem IP. Aby utworzyć jeden lub więcej odbiorników przy użyciu co najmniej jednego adresu IP, zobacz [Tworzenie odbiornika i równoważenia obciążenia grupy dostępności | Na platformie Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ustaw port odbiornika

W SQL Server Management Studio Ustaw port odbiornika.

1. Uruchom SQL Server Management Studio i Połącz się z repliką podstawową.

1. Przejdź do funkcji AlwaysOn grup dostępności dla**grup** | dostępności **o wysokiej dostępności** | .

1. Powinna zostać wyświetlona nazwa odbiornika utworzona w Menedżer klastra trybu failover. Kliknij prawym przyciskiem myszy nazwę odbiornika i kliknij polecenie **Właściwości**.

1. W polu **port** Określ numer portu dla odbiornika grupy dostępności. 1433 jest wartością domyślną, a następnie kliknij przycisk **OK**.

Masz teraz SQL Server grupę dostępności w usłudze Azure Virtual Machines działającej w trybie Menedżer zasobów.

## <a name="test-connection-to-listener"></a>Test connection do odbiornika

Aby przetestować połączenie:

1. Protokół RDP do SQL Server znajdującego się w tej samej sieci wirtualnej, ale nie jest to replika. Możesz użyć innych SQL Server w klastrze.

1. Przetestuj połączenie przy użyciu narzędzia **sqlcmd** . Na przykład poniższy skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pomocą odbiornika z uwierzytelnianiem systemu Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Jeśli odbiornik używa portu innego niż domyślny port (1433), określ port w parametrach połączenia. Na przykład następujące polecenie sqlcmd nawiązuje połączenie z odbiornikiem w porcie 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Połączenie SQLCMD jest automatycznie nawiązywane z niezależnym wystąpieniem SQL Server hostuje replikę podstawową.

> [!TIP]
> Upewnij się, że określony port jest otwarty na zaporze obu serwerów SQL. Oba serwery wymagają reguły ruchu przychodzącego dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub Edytowanie reguły zapory](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Następne kroki

- [Dodaj adres IP do modułu równoważenia obciążenia dla drugiej grupy dostępności](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
