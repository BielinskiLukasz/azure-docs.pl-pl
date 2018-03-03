---
title: "Hosting serwerów na stosie Azure SQL | Dokumentacja firmy Microsoft"
description: "Jak dodać wystąpienia programu SQL dla inicjowania obsługi administracyjnej za pośrednictwem dostawcy zasobów karty SQL"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 0a29ef133a045b2828777050f2d7a204c0add4a8
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>Dodaj serwery hostingu do użycia przez kartę SQL

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można użyć wystąpienia programu SQL na maszynach wirtualnych wewnątrz sieci [stosu Azure](azure-stack-poc.md), lub wystąpienia poza środowiskiem Azure stosu, podać dostawcę zasobów można się z nim połączyć. Ogólne wymagania są:

* Wystąpienie programu SQL muszą być przeznaczone wyłącznie do użytku przez obciążeń planu odzyskiwania i użytkownika. Nie można użyć wystąpienia SQL, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.
* Karta planu odzyskiwania nie jest przyłączony do domeny i można połączyć wyłącznie przy użyciu uwierzytelniania SQL.
* Należy skonfigurować konto z odpowiednimi uprawnieniami do użycia przez planu odzyskiwania.
* Planu odzyskiwania i użytkowników, takich jak aplikacje sieci Web należy używać sieci użytkownika, dlatego łączność do wystąpienia serwera SQL w tej sieci jest wymagana. To wymaganie zwykle oznacza, że adres IP dla swoich wystąpień SQL musi być w sieci publicznej.
* Zarządzanie wystąpień programu SQL i ich hostów zależy od użytkownika; RP nie wykonać stosowania poprawek, tworzenia kopii zapasowej, poświadczeń, obracanie itp.
* Jednostki SKU może służyć do tworzenia różnych klas możliwości SQL, takich jak wydajność, zawsze na itp.


Liczba obrazów maszyn wirtualnych SQL IaaS są dostępne za pośrednictwem funkcji zarządzania Marketplace. Upewnij się, że zawsze pobieranie najnowszej wersji rozszerzenia IaaS SQL, przed wdrożeniem maszyny Wirtualnej przy użyciu elementu portalu Marketplace. Obrazy SQL są takie same jak SQL maszyn wirtualnych, które są dostępne w systemie Azure. Dla maszyn wirtualnych SQL utworzony na podstawie tych obrazów, rozszerzenia IaaS i odpowiedniego rozszerzenia portalu udostępnia funkcje, takie jak automatyczne stosowanie poprawek i możliwości tworzenia kopii zapasowej.

Istnieją inne opcje wdrażania maszyn wirtualnych SQL, w tym szablony w [galerii Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Wszystkie serwery hostingu zainstalowane na stosie Azure wielowęzłowego musi zostać utworzona z subskrypcji użytkownika. Nie można ich utworzyć z subskrypcji domyślny dostawca. Musi zostać utworzony za pomocą portalu użytkownika lub w sesji programu PowerShell z odpowiednią nazwą logowania. Wszystkie serwery hostingu maszyn wirtualnych mogą być obciążane i musi mieć odpowiednie licencje SQL. Administrator usługi _można_ właściciela tej subskrypcji.


### <a name="required-privileges"></a>Wymagane uprawnienia

Można utworzyć nowego użytkownika administracyjnego z uprawnień administratora mniej niż pełne. Określonych operacji, które muszą być dozwolone są:

- Baza danych: Tworzenie, Alter Containment (zawsze włączone tylko), listy, kopia zapasowa
- Grupy dostępności: Alter, Dołącz do dodawania i usuwania bazy danych
- Logowania: Tworzenie, wybierz, Alter, Drop, odwoływanie
- Wybierz operacje: \[wzorca\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[wzorca\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[wzorca\].\[ sys\].\[ availability_groups\] (AlwaysOn), widoku sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Zapewniają pojemność, łącząc się z autonomicznego hostowany program SQL server
Można użyć autonomicznej (z systemem innym niż — HA) serwerów SQL przy użyciu dowolnej wersji programu SQL Server 2014 lub SQL Server 2016. Upewnij się, że masz poświadczenia dla konta z uprawnieniami administratora systemu.

Aby dodać autonomicznego hostem serwera, która została już przydzielona, wykonaj następujące kroki:

1. Zaloguj się do portalu administracyjnego platformy Azure stosu administrator usługi

2. Kliknij przycisk **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **SQL serwerów hosta**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  **Hosting serwerów SQL** bloku jest podłączenia dostawcy zasobów programu SQL Server do rzeczywistego wystąpień programu SQL Server, które służyć jako wewnętrznej bazy danych dostawcy zasobów.

  ![Serwerów hosta](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Wypełnienie formularza Szczegóły połączenia z wystąpieniem programu SQL Server.

  ![Nowy serwer hostingu](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Można opcjonalnie dołączyć nazwę wystąpienia oraz numer portu może zostać podany, jeśli wystąpienie nie jest przypisany do domyślnego portu 1433.

  > [!NOTE]
  > Tak długo, jak przez użytkownika, a administrator usługi Azure Resource Manager można uzyskać dostępu do wystąpienia serwera SQL, mogą być umieszczone pod kontrolą dostawcy zasobów. Wystąpienie programu SQL __musi__ można przydzielić wyłącznie do planu odzyskiwania.

4. W miarę dodawania serwerów, należy je przypisać do nowej lub istniejącej jednostki SKU rozróżnianie oferty usług. Na przykład może mieć, zapewniając wystąpienia SQL Enterprise:
  - wydajność bazy danych
  - Automatyczne kopie zapasowe
  - Zarezerwuj serwerów o wysokiej wydajności dla poszczególnych działów
  - i tak dalej.

  Nazwa jednostki SKU powinien odzwierciedlać właściwości, dzięki czemu użytkownicy mogą powodować odpowiednio swoje bazy danych. Wszystkie serwery hostingu w jednostce SKU powinny mieć takie same możliwości.

    Przykład:

![Jednostki SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> Jednostki SKU może potrwać do godziny mają być wyświetlane w portalu. Użytkownicy nie można utworzyć bazy danych, dopóki nie zostanie całkowicie utworzona jednostka SKU.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Zapewniają pojemność przy użyciu SQL zawsze włączone grupy dostępności
Konfigurowanie SQL zawsze włączone wystąpienia wymaga wykonania dodatkowych kroków i obejmuje co najmniej trzech maszyn wirtualnych (lub maszyn fizycznych).

> [!NOTE]
> Karta SQL RP _tylko_ obsługuje Enterprise programu SQL 2016 z dodatkiem SP1 lub nowszym wystąpień zawsze włączone, ponieważ wymaga ona nowych funkcji programu SQL, takich jak automatyczne wstępne wypełnianie. Oprócz powyższej listy typowych wymagania:

* Należy podać serwer plików, oprócz komputerów SQL AlwaysOn. Brak [szablonów Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) aby tworzenia tego środowiska dla Ciebie. On również może służyć jako przewodnik dotyczący tworzenia własnego wystąpienia.

* Należy skonfigurować serwery SQL. W szczególności należy włączyć [automatyczne wstępne wypełnianie](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) w każdej grupie dostępności dla każdego wystąpienia programu SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

W wystąpieniach dodatkowej
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Aby dodać SQL AlwaysOn serwerami hostingu, wykonaj następujące kroki:

1. Zaloguj się do portalu administracyjnego stosu Azure jako administrator usługi

2. Kliknij przycisk **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **SQL serwerów hosta** &gt; **+ Dodaj**.

    **Hosting serwerów SQL** bloku jest podłączenia dostawcy zasobów programu SQL Server do rzeczywistego wystąpień programu SQL Server, które służyć jako wewnętrznej bazy danych dostawcy zasobów.


3. Wypełnienie formularza Szczegóły połączenia z wystąpienia programu SQL Server, jest użycie nazwy FQDN lub IPv4 adres zawsze na odbiornika (i opcjonalnie port numer). Podaj informacje o koncie dla konta skonfigurowanego z uprawnieniami administratora systemu.

4. Zaznacz to pole, aby włączyć obsługę wystąpienia SQL zawsze włączonej grupy dostępności.

    ![Serwerów hosta](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Dodaj wystąpienie SQL AlwaysOn do jednostki SKU. Nie można mieszać serwerów autonomicznych z wystąpieniami zawsze włączone w tej samej jednostki SKU. Który będzie określana podczas dodawania pierwszego serwera hostingu. Podjęto próbę później mieszać typów spowoduje błąd.


## <a name="making-sql-databases-available-to-users"></a>Udostępnianie użytkownikom bazy danych SQL

Tworzenie planów i oferty, aby udostępnić baz danych SQL dla użytkowników. Dodaj usługę Microsoft.SqlAdapter planu i dodawania istniejącego przydziału lub Utwórz nową. Jeśli tworzysz przydział, należy określić pojemność, aby zezwolić użytkownikowi.

![Tworzenie planów i ofert do dołączenia bazy danych](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>Obsługa SQL karty planu odzyskiwania

Konserwacja wystąpień programu SQL nie pasuje do, z wyjątkiem informacji o hasłach obrotu. Jest odpowiedzialny za stosowanie poprawek i tworzenia kopii zapasowej/odzyskiwania serwerów bazy danych używane z kartą SQL.

### <a name="patching-and-updating"></a>Stosowanie poprawek i aktualizacji
 Karta SQL nie jest obsługiwany jako część stosu Azure jako dodatkowy składnik jest. Microsoft będzie udostępniać aktualizacje karty SQL w razie potrzeby. Karta SQL zostanie uruchomiony na _użytkownika_ maszyny wirtualnej w ramach subskrypcji domyślny dostawca. W związku z tym jest niezbędne do zapewnienia poprawki systemu Windows, sygnatur oprogramowania antywirusowego,... itd. Systemu Windows aktualizację pakietów, które są dostarczane jako część cyklu poprawek i aktualizacji mogą być używane do stosowania aktualizacji do maszyny Wirtualnej systemu Windows. Po wydaniu zaktualizowanej karty skrypt jest dostarczany w celu zastosowania aktualizacji. Ten skrypt tworzy nową maszynę Wirtualną planu odzyskiwania i migracji tutaj stan, który już istnieje.

 ### <a name="backuprestoredisaster-recovery"></a>Odzyskiwanie kopii zapasowej/przywracania/po awarii
 Karta SQL nie jest kopii zapasowej jako część procesu Azure stosu BC-odzyskiwania po awarii, ponieważ jest dodatkowy składnik. Skrypty będzie świadczona w celu ułatwienia:
- Tworzenie kopii zapasowej informacji niezbędnych stanu (przechowywane na koncie magazynu Azure stosu)
- Przywracanie planu odzyskiwania, w przypadku odzyskiwania cały stos staje się koniecznością.
Serwery bazy danych musi zostać odzyskana najpierw (Jeśli to konieczne), zanim zostanie przywrócone planu odzyskiwania.

### <a name="updating-sql-credentials"></a>Aktualizowanie poświadczeń programu SQL

Użytkownik jest odpowiedzialny za tworzenie i obsługę kont administratora systemu na serwerach SQL. RP potrzebuje konta z tych uprawnień do zarządzania bazami danych w imieniu użytkowników — nie wymaga dostępu do danych w tych baz danych. Jeśli musisz zaktualizować hasła administratora systemu na serwerach SQL, można użyć możliwości aktualizacji interfejsu administratora RP zmiany przechowywane hasła używane przez planu odzyskiwania. Te hasła są przechowywane w Key Vault w wystąpieniu usługi Azure stosu.

Aby zmodyfikować ustawienia, kliknij przycisk **Przeglądaj** &gt; **zasobów administracyjnych** &gt; **Hosting serwerów SQL** &gt; **Kont logowania SQL** i wybierz nazwę logowania. Zmiana musi zostać wykonana w wystąpieniu programu SQL najpierw (i wszystkie repliki, jeśli to konieczne). W **ustawienia** panelu, kliknij pozycję **hasło**.

![Zaktualizuj hasło administratora](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>Kolejne kroki

[Dodawanie baz danych](azure-stack-sql-resource-provider-databases.md)
