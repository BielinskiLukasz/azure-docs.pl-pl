---
title: Zarządzane przez klienta szyfrowanie danych przezroczystych (TDE)
description: Bring Your Own Key (BYOK) obsługa Transparent Data Encryption (TDE) z Azure Key Vault dla SQL Database i Azure Synapse. TDE z BYOK Omówienie, korzyści, jak to działa, zagadnienia i zalecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: a29466ad5b261e1e2ce818d7b4a18260e35caaec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255654"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Usługa Azure SQL Transparent Data Encryption z kluczem zarządzanym przez klienta

Usługa Azure SQL [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) z kluczem zarządzanym przez klienta umożliwia Bring Your Own Key (BYOK) scenariusz ochrony danych w spoczynku i umożliwia organizacjom wdrażanie rozdzieleń obowiązków związanych z zarządzaniem kluczami i danymi. Dzięki funkcji transparent Data Encryption zarządzanej przez klienta klient jest odpowiedzialny za i w pełnej kontroli nad zarządzaniem cyklem życia kluczy (tworzenie kluczy, przekazywanie, obracanie, usuwanie), uprawnienia do użycia kluczy oraz Inspekcja operacji na kluczach.

W tym scenariuszu klucz używany do szyfrowania klucza szyfrowania bazy danych (w tym przypadku TDE) jest kluczem asymetrycznym zarządzanym przez klienta przechowywanym w ramach klienta i zarządzanego przez klienta [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), który jest oparty na chmurze w zewnętrznym systemie zarządzania kluczami. Key Vault jest wysoce dostępny i skalowalny bezpieczny magazyn dla kluczy kryptograficznych RSA, opcjonalnie obsługiwane w przypadku FIPS 140-2 poziom 2 sprawdzone sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale udostępnia usługi szyfrowania/odszyfrowywania przy użyciu klucza do autoryzowanych jednostek. Klucz może być generowany przez Magazyn kluczy, importowany lub [transferowany do magazynu kluczy z urządzenia Premium HSM](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

W przypadku Azure SQL Database i usługi Azure Synapse funkcja ochrony TDE jest ustawiana na poziomie serwera logicznego i jest dziedziczona przez wszystkie zaszyfrowane bazy danych skojarzone z tym serwerem. W przypadku wystąpienia zarządzanego usługi Azure SQL funkcja ochrony TDE jest ustawiana na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin *serwer* odnosi się zarówno do SQL Database serwera logicznego, jak i wystąpienia zarządzanego w tym dokumencie, chyba że określono inaczej.

> [!IMPORTANT]
> W przypadku korzystania z usługi TDE zarządzanej przez usługę, która chce zacząć korzystać z TDE zarządzanych przez klienta, dane pozostają zaszyfrowane podczas procesu przełączania i nie ma żadnych przestojów ani ponownego szyfrowania plików bazy danych. Przełączenie z klucza zarządzanego przez usługę do klucza zarządzanego przez klienta wymaga ponownego włączenia szyfrowania danych, które jest szybką i online operacją.

## <a name="benefits-of-the-customer-managed-tde"></a>Zalety TDE zarządzanych przez klienta

TDE zarządzane przez klienta zapewnia klientom następujące korzyści:

- Pełna i szczegółowa kontrola nad użyciem i zarządzaniem ochroną TDE;

- Przejrzystość użycia funkcji ochrony TDE;

- Możliwość wdrożenia separacji obowiązków w zakresie zarządzania kluczami i danymi w organizacji;

- Administrator Key Vault może odwołać uprawnienia dostępu do klucza, aby uniemożliwić dostęp do zaszyfrowanej bazy danych;

- Centralne zarządzanie kluczami w AKV;

- Większe zaufanie od klientów końcowych, ponieważ AKV zaprojektowano w taki sposób, aby firma Microsoft nie widziała ani nie wyodrębni kluczy szyfrowania;

## <a name="how-customer-managed-tde-works"></a>Jak działa TDE zarządzane przez klienta

![Konfiguracja i działanie TDE zarządzanego przez klienta](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Aby serwer mógł korzystać z funkcji ochrony TDE przechowywanej w AKV na potrzeby szyfrowania, należy przyznać następujące prawa dostępu do serwera przy użyciu unikatowej tożsamości usługi AAD:

- **Get** -do pobierania publicznej części i właściwości klucza w Key Vault

- **wrapKey** — aby można było chronić (szyfrować)

- **unwrapKey** — aby można było wyłączyć ochronę (odszyfrowywać)

Administrator magazynu kluczy może również [włączyć rejestrowanie zdarzeń inspekcji magazynu kluczy](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault), aby mogły być później poddane inspekcji.

Gdy serwer jest skonfigurowany do korzystania z funkcji ochrony TDE z AKV, serwer wysyła do magazynu kluczy klucz szyfrowania danych z każdej TDE z włączoną obsługą systemu, aby przeprowadzić szyfrowanie. Magazyn kluczy zwraca zaszyfrowane szyfrowanie, które jest następnie przechowywane w bazie danych użytkownika.

W razie potrzeby serwer wysyła do magazynu kluczy chroniony klucz szyfrowania danych.

Audytorzy mogą używać Azure Monitor do przeglądania dzienników AuditEvent magazynu kluczy, jeśli rejestrowanie jest włączone.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Wymagania dotyczące konfigurowania TDE zarządzanych przez klienta

### <a name="requirements-for-configuring-akv"></a>Wymagania dotyczące konfigurowania AKV

- Magazyn kluczy i SQL Database/wystąpienie zarządzane muszą należeć do tej samej dzierżawy Azure Active Directory. Magazyn kluczy między dzierżawcami i interakcje serwera nie są obsługiwane. Aby później przenieść zasoby, należy ponownie skonfigurować TDE z AKV. Dowiedz się więcej o [przenoszeniu zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

- Funkcja [usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączona w magazynie kluczy, aby chronić przed utratą danych (lub magazynu kluczy). Zasoby usunięte nietrwale są przechowywane przez 90 dni, chyba że w międzyczasie zostanie odzyskany lub usunięty przez klienta. Akcje *odzyskania* i *przeczyszczania* mają własne uprawnienia skojarzone z zasadami dostępu magazynu kluczy. Funkcja usuwania nietrwałego jest domyślnie wyłączona i można ją włączyć za pomocą [programu PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Nie można jej włączyć za pośrednictwem Azure Portal.  

- Przyznaj serwerowi SQL Database lub zarządzanemu wystąpieniu dostęp do magazynu kluczy (Get, wrapKey, unwrapKey) przy użyciu jego tożsamości Azure Active Directory. W przypadku korzystania z Azure Portal tożsamość usługi Azure AD zostanie utworzona automatycznie. W przypadku korzystania z programu PowerShell lub interfejsu wiersza polecenia tożsamość usługi Azure AD musi być jawnie utworzona, a ukończenie powinna zostać zweryfikowana. Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące korzystania z programu PowerShell, zobacz [Configure TDE with BYOK](transparent-data-encryption-byok-azure-sql-configure.md) i [Configure TDE with BYOK for Managed instance](https://aka.ms/sqlmibyoktdepowershell) .

- W przypadku korzystania z zapory z AKV należy włączyć opcję *Zezwalaj zaufanym usługom firmy Microsoft na pominięcie zapory*.

### <a name="requirements-for-configuring-tde-protector"></a>Wymagania dotyczące konfigurowania funkcji ochrony TDE

- Funkcja ochrony TDE może być kluczem asymetryczny, RSA 2048 lub 2048 RSA.

- Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być przyszłą datą i godziną.

- Klucz musi być w stanie *włączony* .

- W przypadku importowania istniejącego klucza do magazynu kluczy upewnij się, że jest on udostępniany w obsługiwanych formatach plików (PFX,. BYOK lub. Backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Zalecenia dotyczące konfigurowania TDE zarządzanych przez klienta

### <a name="recommendations-when-configuring-akv"></a>Zalecenia dotyczące konfigurowania AKV

- Skojarz maksymalnie 500 Ogólnego przeznaczenia lub Krytyczne dla działania firmy 200 bazy danych w sumie z magazynem kluczy w ramach jednej subskrypcji, aby zapewnić wysoką dostępność, gdy serwer uzyskuje dostęp do funkcji ochrony TDE w magazynie kluczy. Te dane są oparte na doświadczeniu i udokumentowane w [limitach usługi magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits). Zamiarem tego problemu jest zapobieganie problemom po przejściu serwera w tryb failover, ponieważ spowoduje to wyzwolenie tylu operacji na magazyn, ponieważ na tym serwerze znajdują się bazy danych.

- Ustaw blokadę zasobów w magazynie kluczy, aby kontrolować, kto może usunąć ten zasób krytyczny i zapobiegać przypadkowemu lub nieautoryzowanemu usunięciu. Dowiedz się więcej o [blokadach zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

- Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania: Magazyn kluczy udostępnia dzienniki, które można łatwo wstrzyknąć do innych informacji o zabezpieczeniach i narzędzi do zarządzania zdarzeniami. Pakiet Operations Management Suite [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) to jeden przykład usługi, która jest już zintegrowana.

- Połącz każdy serwer z dwoma magazynami kluczy, które znajdują się w różnych regionach i przechowują ten sam klucz materiału, aby zapewnić wysoką dostępność zaszyfrowanych baz danych. Oznacz tylko klucz z magazynu kluczy w tym samym regionie, w którym znajduje się funkcja ochrony TDE. System będzie używać

### <a name="recommendations-when-configuring-tde-protector"></a>Zalecenia dotyczące konfigurowania funkcji ochrony TDE
- Zachowaj kopię funkcji ochrony TDE w bezpiecznym miejscu lub zażądaj jej w usłudze Escrow.

- Jeśli klucz jest generowany w magazynie kluczy, Utwórz kopię zapasową klucza przed użyciem klucza w AKV po raz pierwszy. Kopię zapasową można przywrócić tylko do Azure Key Vault. Dowiedz się więcej na temat polecenia [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .

- Utwórz nową kopię zapasową po każdym wprowadzeniu zmian w kluczu (np. atrybuty klucza, znaczniki, listy ACL).

- **Zachowaj poprzednie wersje** klucza w magazynie kluczy podczas obracania kluczy, aby można było przywrócić starsze kopie zapasowe bazy danych. Gdy funkcja ochrony TDE została zmieniona dla bazy danych, stare kopie zapasowe bazy danych **nie są aktualizowane** do korzystania z najnowszej funkcji ochrony TDE. W czasie przywracania każda kopia zapasowa wymaga ochrony TDE, która została zaszyfrowana przy użyciu czasu utworzenia. Rotacje kluczy można wykonać zgodnie z instrukcjami w [obszarze obróć transparent Data Encryption funkcję ochrony przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Zachowaj wszystkie poprzednio używane klucze w AKV, nawet po przełączeniu na klucze zarządzane przez usługę. Gwarantuje to, że kopie zapasowe bazy danych można przywrócić przy użyciu funkcji ochrony TDE przechowywanych w AKV.  Funkcje ochrony TDE utworzone przy użyciu Azure Key Vault muszą być utrzymywane, dopóki wszystkie pozostałe zapisane kopie zapasowe nie zostały utworzone za pomocą kluczy zarządzanych przez usługę. Wykonaj odzyskiwalne kopie zapasowe tych kluczy za pomocą polecenia [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Aby usunąć potencjalnie złamany klucz podczas zdarzenia zabezpieczeń bez ryzyka utraty danych, wykonaj kroki opisane w sekcji [usuwanie potencjalnie złamanego klucza](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Niedostępna funkcja ochrony TDE

W przypadku skonfigurowania funkcji transparent Data Encryption do korzystania z klucza zarządzanego przez klienta, aby baza danych mogła pozostać w trybie online, wymagana jest ciągły dostęp do usługi TDE. Jeśli serwer utraci dostęp do funkcji ochrony TDE zarządzanej przez klienta w AKV, w ciągu maksymalnie 10 minut, baza danych zacznie odrzucać wszystkie połączenia z odpowiednim komunikatem o błędzie i zmienić jej stan na *niedostępny*. Jedyną akcją, którą można wykonać w bazie danych w stanie niedostępnym, jest usunięcie jej.

> [!NOTE]
> Jeśli baza danych jest niedostępna z powodu sporadycznej awarii sieci, nie jest wymagana żadna akcja, a bazy danych zostaną automatycznie przywrócone do trybu online.

Po przywróceniu dostępu do klucza przełączenie bazy danych w tryb online wymaga dodatkowego czasu i kroków, które mogą się różnić w zależności od czasu, który upłynął bez dostępu do klucza i rozmiaru danych w bazie danych:

- Jeśli dostęp do klucza zostanie przywrócony w ciągu 8 godzin, baza danych zostanie przewarta w ciągu następnej godziny.

- Jeśli dostęp do klucza zostanie przywrócony po upływie ponad 8 godzin, funkcja Autokorekty nie jest możliwa i przełączenie bazy danych wymaga wykonania dodatkowych czynności w portalu i może zająć znaczną ilość czasu w zależności od rozmiaru bazy danych. Gdy baza danych zostanie przywrócona w trybie online, wcześniej skonfigurowane ustawienia na poziomie serwera, takie jak konfiguracja [grupy trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) , historia przywracania do punktu w czasie i Tagi **zostaną utracone**. W związku z tym zaleca się zaimplementowanie systemu powiadomień, który pozwala identyfikować i rozwiązywać podstawowe problemy z dostępem do klucza w ciągu 8 godzin.

### <a name="accidental-tde-protector-access-revocation"></a>Przypadkowe odwoływanie dostępu do funkcji ochrony TDE

Może się zdarzyć, że ktoś mający wystarczające prawa dostępu do magazynu kluczy przypadkowo wyłącza dostęp serwera do klucza przez:

- Odwoływanie uprawnień *Get*, *wrapKey*i *unwrapKey* magazynu kluczy z serwera

- Usuwanie klucza

- Usuwanie magazynu kluczy

- zmiana reguł zapory magazynu kluczy

- Usuwanie tożsamości zarządzanej serwera w Azure Active Directory

Dowiedz się więcej o [typowych przyczynach niedostępności bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorowanie TDE zarządzanych przez klienta

Aby monitorować stan bazy danych i włączyć alert o utracie dostępu do funkcji ochrony TDE, skonfiguruj następujące funkcje platformy Azure:
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Niedostępna baza danych, która utraciła dostęp do funkcji ochrony TDE, będzie wyświetlana jako "niedostępna" po odmowie pierwszego połączenia z bazą danych.
- [Dziennik aktywności](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) , gdy dostęp do funkcji ochrony TDE w magazynie kluczy zarządzanych przez klienta nie powiedzie się, wpisy są dodawane do dziennika aktywności.  Utworzenie alertów dla tych zdarzeń umożliwi przywrócenie dostępu tak szybko, jak to możliwe.
- [Grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) można definiować w celu wysyłania powiadomień i alertów na podstawie preferencji, np. wiadomości E-mail/SMS/wypychania/głosu, aplikacji logiki, elementu webhook, narzędzia ITSM lub elementu Runbook usługi Automation.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Tworzenie kopii zapasowej i przywracanie bazy danych za pomocą TDE zarządzanych przez klienta

Po zaszyfrowaniu bazy danych za pomocą usługi TDE przy użyciu klucza z Key Vault wszystkie nowo wygenerowane kopie zapasowe są również szyfrowane za pomocą tej samej funkcji ochrony TDE. Po zmianie funkcji ochrony TDE stare kopie zapasowe bazy danych **nie są aktualizowane** , aby można było używać najnowszej funkcji ochrony TDE.

Aby przywrócić kopię zapasową zaszyfrowaną przy użyciu funkcji ochrony TDE z Key Vault, upewnij się, że materiał klucza jest dostępny dla serwera docelowego. Dlatego zalecamy zachowanie wszystkich starych wersji funkcji ochrony TDE w magazynie kluczy, aby można było przywrócić kopie zapasowe bazy danych.

> [!IMPORTANT]
> W każdej chwili dla serwera nie może być więcej niż jeden zestaw funkcji ochrony TDE. Jest to klucz oznaczony przy użyciu funkcji "Ustaw klucz jako domyślną ochronę TDE" w bloku Azure Portal. Jednak wiele dodatkowych kluczy można połączyć z serwerem bez oznaczania ich jako funkcji ochrony TDE. Te klucze nie są używane do ochrony klucza szyfrowania danych, ale mogą być używane podczas przywracania z kopii zapasowej, jeśli plik kopii zapasowej jest szyfrowany przy użyciu klucza o odpowiednim odcisku palca.

Jeśli klucz, który jest wymagany do przywracania kopii zapasowej, nie jest już dostępny dla serwera docelowego, zwracany jest następujący komunikat o błędzie: "serwer docelowy `<Servername>` nie ma dostępu do wszystkich identyfikatorów URI AKV utworzonych między \<sygnatury czasowej #1 > i \<sygnatura czasowa #2 >. Spróbuj ponownie wykonać operację po przywróceniu wszystkich identyfikatorów URI AKV ".

Aby rozwiązać ten problem, uruchom polecenie cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) dla SQL Database docelowego serwera logicznego lub [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) dla docelowego wystąpienia zarządzanego, aby zwrócić listę dostępnych kluczy i zidentyfikować brakujące. Aby mieć pewność, że można przywrócić wszystkie kopie zapasowe, upewnij się, że serwer docelowy do przywracania ma dostęp do wszystkich wymaganych kluczy. Klucze te nie muszą być oznaczone jako funkcja ochrony TDE.

Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej SQL Database, zobacz [odzyskiwanie bazy danych Azure SQL Database](sql-database-recovery-using-backups.md). Aby dowiedzieć się więcej o odzyskiwaniu kopii zapasowej dla puli SQL, zobacz [odzyskiwanie puli SQL](../sql-data-warehouse/backup-and-restore.md). Aby uzyskać SQL Server natywnej kopii zapasowej/przywracania z wystąpieniem zarządzanym, zobacz [Szybki Start: Przywracanie bazy danych do wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Dodatkowe zagadnienia dotyczące plików dziennika: kopia zapasowa plików dziennika pozostaje zaszyfrowana przy użyciu oryginalnej funkcji ochrony TDE, nawet jeśli została obrócona, a baza danych używa teraz nowej funkcji ochrony TDE.  W czasie przywracania do przywrócenia bazy danych są konieczne oba klucze.  Jeśli plik dziennika używa funkcji ochrony TDE przechowywanej w Azure Key Vault, ten klucz będzie wymagany w czasie przywracania, nawet jeśli baza danych została zmieniona tak, aby korzystała z usługi TDE zarządzanej w międzyczasie.

## <a name="high-availability-with-customer-managed-tde"></a>Wysoka dostępność dzięki TDE zarządzanemu przez klienta

Nawet w przypadkach, gdy nie ma skonfigurowanej nadmiarowości geograficznej serwera, zdecydowanie zaleca się skonfigurowanie serwera tak, aby korzystał z dwóch różnych magazynów kluczy w dwóch różnych regionach z tym samym kluczem. Można to zrobić, tworząc TDE funkcję ochrony przy użyciu podstawowego magazynu kluczy, który znajduje się w tym samym regionie co serwer, i klonować klucz do magazynu kluczy w innym regionie świadczenia usługi Azure, dzięki czemu serwer ma dostęp do drugiego magazynu kluczy, który powinien być głównym magazynem kluczy exper ience awarię, gdy baza danych jest uruchomiona.

Użyj polecenia cmdlet Backup-AzKeyVaultKey, aby pobrać klucz w szyfrowanym formacie z magazynu kluczy podstawowych, a następnie użyć polecenia cmdlet Restore-AzKeyVaultKey i określić Magazyn kluczy w drugim regionie, aby sklonować klucz. Alternatywnie możesz użyć Azure Portal, aby utworzyć kopię zapasową i przywrócić klucz. Klucz w magazynie kluczy pomocniczych w innym regionie nie powinien być oznaczony jako funkcja ochrony TDE i nie jest jeszcze dozwolony.

 Jeśli wystąpi awaria wpływająca na podstawowy Magazyn kluczy, system automatycznie przejdzie do innego połączonego klucza z tym samym odciskiem palca w magazynie kluczy pomocniczych, jeśli istnieje. Należy pamiętać, że ten przełącznik nie zostanie wykonany, jeśli funkcja ochrony TDE jest niedostępna z powodu odwołanych praw dostępu lub klucz lub Magazyn kluczy został usunięty, ponieważ może to wskazywać, że klient chce ograniczyć dostęp serwera do klucza.

![HA na jednym serwerze](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geograficznie i TDE zarządzane przez klienta

W scenariuszach [aktywnej replikacji geograficznej](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) i [grup trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) każdy serwer ma wymagany osobny Magazyn kluczy, który musi znajdować się na serwerze w tym samym regionie świadczenia usługi Azure. Klient jest odpowiedzialny za utrzymywanie kluczowych materiałów w ramach spójnych magazynów kluczy, dzięki czemu pomocnicza lokacja jest zsynchronizowana i może przejąć użycie tego samego klucza z lokalnego magazynu kluczy, jeśli podstawowa stanie się niedostępna z powodu awarii w regionie i zostanie wyzwolone przejście w tryb failover . Można skonfigurować maksymalnie cztery pomocnicze serwery, a Łączenie łańcuchowe (serwery pomocnicze serwerów pomocniczych) nie jest obsługiwane.

Aby uniknąć problemów podczas ustanawiania lub podczas przeprowadzania replikacji geograficznej ze względu na niekompletny materiał klucza, ważne jest przestrzeganie tych reguł podczas konfigurowania TDE zarządzanych przez klienta:

- Wszystkie magazyny kluczy muszą mieć takie same właściwości, jak te same prawa dostępu dla odpowiednich serwerów.

- Wszystkie uwzględnione magazyny kluczy muszą zawierać identyczny materiał klucza. Ma to zastosowanie nie tylko do bieżącej funkcji ochrony TDE, ale do wszystkich wcześniejszych ochrony TDE, które mogą być używane w plikach kopii zapasowej.

- Najpierw należy wykonać początkową konfigurację i rotację funkcji ochrony TDE na serwerze pomocniczym, a następnie na serwerze podstawowym.

![Grupy trybu failover i geograficznie](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Aby przetestować tryb failover, wykonaj kroki opisane w temacie [Aktywna replikacja geograficzna](sql-database-geo-replication-overview.md). Należy regularnie przeprowadzać potwierdzenie uprawnień dostępu dla programu SQL do obu magazynów kluczy.

## <a name="next-steps"></a>Następne kroki

Warto również sprawdzić następujące przykładowe skrypty programu PowerShell dla typowych operacji przy użyciu TDE zarządzanych przez klienta:

- [Obróć funkcję ochrony Transparent Data Encryption dla SQL Database przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Usuwanie funkcji ochrony Transparent Data Encryption (TDE) dla SQL Database przy użyciu programu PowerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Zarządzanie Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza programu PowerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
