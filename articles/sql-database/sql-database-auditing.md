---
title: Rozpoczynanie pracy z inspekcją bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Użyj opcji inspekcji usługi Azure SQL database, aby śledzić zdarzenia bazy danych w dzienniku inspekcji.
services: sql-database
author: giladmit
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: giladm
ms.reviewer: vanto
ms.openlocfilehash: 935baf791d9244f2fa4f5be9c02d4778244754de
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543753"
---
# <a name="get-started-with-sql-database-auditing"></a>Rozpoczynanie pracy z inspekcją bazy danych SQL
Inspekcji usługi Azure SQL database śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure. Inspekcja również:

* Pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.

* Włącza i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności. Aby uzyskać więcej informacji na temat usługi Azure programy zgodność ze standardami tej pomocy technicznej, zobacz [Centrum zaufania systemu Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Omówienie inspekcji usługi Azure SQL database
Można użyć inspekcji usługi SQL database do:


* **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań bazy danych powinien być poddany inspekcji.
* **Raport** aktywność bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczynanie pracy z działaniem i raportowanie zdarzeń.
* **Analizowanie** raportów. Możesz znaleźć podejrzanych zdarzeń, nietypowej aktywności i trendów.

Można skonfigurować inspekcję dla różnych rodzajów kategorie zdarzeń, jak wyjaśniono w [konfigurowania inspekcji dla bazy danych](#subheading-2) sekcji.

> [!IMPORTANT]
> Dzienniki inspekcji są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure.
>
> * **Usługa Premium Storage** jest obecnie **nieobsługiwane** przez obiekty BLOB dołączania.
> * **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.

## <a id="subheading-8"></a>Zdefiniuj poziom serwera, a zasady inspekcji na poziomie bazy danych

Można zdefiniować zasady inspekcji dla konkretnej bazy danych lub jako domyślne zasady serwera:

* Zasady serwera ma zastosowanie do wszystkich istniejących i nowo utworzony baz danych na serwerze.

* Jeśli *Inspekcja obiektów blob serwera jest włączona*, jego *zawsze ma zastosowanie do bazy danych*. Baza danych będzie monitorowane, niezależnie od ustawienia inspekcji bazy danych.

* Włączenie inspekcji obiektów blob w bazie danych, oprócz Włączanie go na serwerze jest *nie* zastąpić lub zmienić ustawienia inspekcji obiektów blob serwera. Zarówno inspekcji będą istnieć obok siebie. Innymi słowy bazy danych jest różna dwa razy w sposób równoległy; raz przez zasady serwera banku i raz przez zasady bazy danych.

   > [!NOTE]
   > Należy unikać włączenie inspekcji obiektów blob serwera i inspekcji obiektów blob dla bazy danych ze sobą, chyba że:
    > * Aby użyć innego *konta magazynu* lub *okres przechowywania* dla konkretnej bazy danych.
    > * Chcesz inspekcji zdarzeń typów lub kategorii dla konkretnej bazy danych, które różnią się od pozostałej części bazy danych na serwerze. Na przykład może być wstawia tabeli, które należy przeprowadzić inspekcję tylko dla konkretnej bazy danych.
   >
   > W przeciwnym razie zalecane jest włączanie inspekcji obiektów blob tylko poziom serwera i pozostawić inspekcji poziomu bazy danych wyłączone dla wszystkich baz danych.


## <a id="subheading-2"></a>Inspekcja bazy danych
W poniższej sekcji opisano konfigurację inspekcji przy użyciu witryny Azure portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do **inspekcji** w pozycji zabezpieczeń, w okienku serwera/bazy danych SQL.

    <a id="auditing-screenshot"></a> ![Okienko nawigacji][1]

3. Jeśli wolisz skonfigurować zasady inspekcji serwera, możesz wybrać **Wyświetl ustawienia serwera** łącze na stronie inspekcji bazy danych. Można następnie wyświetlić lub zmodyfikować ustawień inspekcji serwera. Zasady inspekcji serwera mają zastosowanie do wszystkich istniejących i nowo utworzonej bazy danych na tym serwerze.

    ![Okienko nawigacji][2]

4. Jeśli wolisz włączyć inspekcję na poziomie bazy danych, Przełącz **inspekcji** do **ON**.

    Jeśli inspekcja serwera jest włączona, skonfigurowana bazy danych inspekcji będzie istnieć side-by-side przy użyciu inspekcji serwera.

    ![Okienko nawigacji][3]

5. **Nowe** — masz teraz wiele opcji dotyczących konfigurowania której będą zapisywane dzienniki inspekcji. Konto usługi Azure storage, obszaru roboczego pakietu OMS do użycia przez usługę Log Analytics lub Centrum zdarzeń do użycia z Centrum zdarzeń, można napisać dzienników. Można skonfigurować dowolną kombinację tych opcji, a do każdego będą zapisywane w dziennikach inspekcji.

    ![Opcje magazynu](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Aby skonfigurować zapisywanie inspekcji dzienniki na koncie magazynu, wybierz opcję **magazynu** , a następnie otwórz **szczegóły magazynu**. Wybierz konto magazynu platformy Azure, której będą zapisywane dzienniki, a następnie wybierz okres przechowywania. Stare dzienniki zostaną usunięte. Następnie kliknij przycisk **OK**.

    ![konto magazynu](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Aby skonfigurować zapisywanie inspekcji dzienników z obszarem roboczym pakietu OMS, wybierz opcję **usługi Log Analytics (wersja zapoznawcza)** , a następnie otwórz **szczegóły usługi Log Analytics**. Wybierz lub Utwórz obszar roboczy OMS, którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**.

    ![OMS](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Aby skonfigurować zapisywanie inspekcji dzienników do Centrum zdarzeń, wybierz opcję **Centrum zdarzeń (wersja zapoznawcza)** , a następnie otwórz **szczegóły Centrum zdarzeń**. Wybierz Centrum zdarzeń, w którym będą zapisywane dzienniki, a następnie kliknij przycisk **OK**. Pamiętaj, że Centrum zdarzeń znajduje się w tym samym regionie, co bazy danych i serwera.

    ![Centrum zdarzeń](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Kliknij pozycję **Zapisz**.
10. Jeśli chcesz dostosować zdarzenia poddawane inspekcji, to zrobić za pomocą [poleceń cmdlet programu PowerShell](#subheading-7) lub [interfejsu API REST](#subheading-9).
11. Po skonfigurowaniu ustawień inspekcji można włączyć funkcji wykrywania zagrożeń i skonfigurować wiadomości e-mail, aby otrzymywać alerty zabezpieczeń. Korzystając z wykrywania zagrożeń, otrzymujesz alerty proaktywne na nietypowe działania bazy danych, które mogą wskazywać potencjalne zagrożenia bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [wprowadzenie do wykrywania zagrożeń](sql-database-threat-detection-get-started.md). 

## <a id="subheading-3"></a>Analizowanie dzienników inspekcji i raporty
Jeśli wybrano zapisywanie dzienników inspekcji na koncie usługi Azure storage, istnieje kilka metod, których można użyć, aby wyświetlić dzienniki:
- Dzienniki inspekcji są agregowane w ramach konta, który został wybrany podczas instalacji. Możesz zapoznać się z dziennikami inspekcji przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](http://storageexplorer.com/). W usłudze Azure storage, dzienniki inspekcji są zapisywane jako kolekcja plików obiektów blob w kontenerze o nazwie **sqldbauditlogs**. Aby uzyskać więcej informacji o hierarchii folderu przechowywania konwencji nazewnictwa i format dziennika zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Użyj [witryny Azure portal](https://portal.azure.com).  Otwórz odpowiedniej bazy danych. W górnej części bazy danych **inspekcji i wykrywania zagrożeń** kliknij **Wyświetl dzienniki inspekcji**.

    ![Okienko nawigacji][7]

    **Rekordy inspekcji** zostanie otwarta, z których będziesz mieć możliwość wyświetlenia dzienników.

    - Określone daty można wyświetlić, klikając **filtru** w górnej części **rekordy inspekcji** strony.
    - Możesz przełączać się między rekordy inspekcji, które zostały utworzone przez *zasady inspekcji serwera* i *bazy danych zasady inspekcji* przełączając **źródło inspekcji**.
    - Można wyświetlić tylko wstrzyknięcie kodu SQL powiązane rekordy inspekcji, sprawdzając **Pokaż tylko inspekcji rekordów dla wstrzyknięć kodu SQL** pola wyboru.

       ![Okienko nawigacji][8]

- Użyj funkcji systemowej **sys.fn_get_audit_file** (T-SQL), aby zwrócić dane z dziennika inspekcji w formacie tabelarycznym. Aby uzyskać więcej informacji na temat korzystania z tej funkcji, zobacz [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


- Użyj **scalania plików inspekcji** w SQL Server Management Studio (począwszy od SSMS 17):
    1. Wybierz z menu Narzędzia SSMS **pliku** > **Otwórz** > **scalania plików inspekcji**.

        ![Okienko nawigacji][9]
    2. **Dodaj pliki inspekcji** zostanie otwarte okno dialogowe. Wybierz jedną z **Dodaj** opcji do wyboru, czy chcesz scalić plików inspekcji z dysku lokalnego lub zaimportować je z usługi Azure Storage. Należy podać szczegóły dotyczące usługi Azure Storage i klucz konta.

    3. Po dodaniu wszystkich plików w celu scalenia kliknij **OK** można ukończyć operacji scalania.

    4. Scalono plik zostanie otwarty w programie SSMS, gdzie możesz można wyświetlać i analizować je, a także go wyeksportować w pliku XEL lub CSV lub tabeli.

- Usługa Power BI. Można wyświetlać i analizować dane dzienników inspekcji w usłudze Power BI. Aby uzyskać więcej informacji i uzyskać dostęp do pobrania szablonu, zobacz [Analyzie dane z dziennika inspekcji w usłudze Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Pobierz pliki dziennika z kontenera obiektów blob usługi Azure Storage za pośrednictwem portalu lub przy użyciu narzędzia, takie jak [Eksploratora usługi Azure Storage](http://storageexplorer.com/).
    * Po pobraniu pliku dziennika, który jest lokalnie, kliknij dwukrotnie plik, Otwórz, wyświetlać i analizować dzienniki w programie SSMS.
    * Można również pobrać wielu plików jednocześnie, za pomocą Eksploratora usługi Azure Storage. Aby to zrobić, kliknij prawym przyciskiem myszy określony podfolder, a następnie wybierz **Zapisz jako** można zapisać w folderze lokalnym.

* Dodatkowe metody:
   * Po pobraniu kilka plików lub podfolder, który zawiera pliki dziennika, może scalić je lokalnie zgodnie z opisem w instrukcje plików inspekcji scalania SSMS opisanych powyżej.
   * Inspekcja obiektów blob widoku programowo dzienników:

     * Użyj [czytnika zdarzeń rozszerzonych](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) bibliotekę języka C#.
     * [Rozszerzone zdarzenia pliki zapytań](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) przy użyciu programu PowerShell.

W przypadku wybrania tylko zapisywanie dzienników inspekcji usługi Log Analytics:
- Aby wyświetlić dzienniki inspekcji w usłudze Log Analytics, Otwórz obszar roboczy usługi Log Analytics i w obszarze **wyszukiwanie i analizowanie dzienników**, kliknij przycisk **wyświetlanie dzienników**. W widoku wyszukiwania w dziennikach można uruchomić, klikając **wszystkie zebrane dane**.  

    ![przeszukiwanie dzienników pakietu OMS](./media/sql-database-auditing-get-started/oms_log_search.png)

   W tym miejscu możesz użyć [Operations Management Suite (OMS) Log Analytics](../log-analytics/log-analytics-log-search.md) przeprowadzić zaawansowane wyszukiwanie na dane dziennika inspekcji. Usługa log Analytics udostępnia w czasie rzeczywistym operational insights za pomocą zintegrowanej funkcji wyszukiwania i niestandardowym pulpitom nawigacyjnym umożliwiającym szybkie analizowanie milionów rekordów z wszystkich obciążeń i serwerów. Aby uzyskać dodatkowe przydatne informacje dotyczące języka wyszukiwania usługi Log Analytics pakietu OMS i poleceń, zobacz [usługi Log Analytics Wyszukaj odwołanie](../log-analytics/log-analytics-log-search.md).

W przypadku wybrania tylko zapisywanie dzienników inspekcji w Centrum zdarzeń:
- Korzystanie z danych dzienników inspekcji z Centrum zdarzeń, należy skonfigurować strumienia na korzystanie ze zdarzeń i zapisywać je do obiektu docelowego. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

## <a id="subheading-5"></a>Rozwiązania w środowisku produkcyjnym
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Inspekcja bazy danych replikowanej geograficznie</a>
Bazy danych replikowanej geograficznie po włączeniu inspekcji podstawowej bazy danych pomocniczej bazy danych mają identyczne zasady inspekcji. Istnieje również możliwość skonfigurowania inspekcji w pomocniczej bazie danych przez włączenie inspekcji na **serwer pomocniczy**, niezależnie od podstawowej bazy danych.

* Poziom serwera (**zalecane**): Włącz inspekcję na obu **serwera podstawowego** , jak również **serwer pomocniczy** — podstawowych i pomocniczych baz danych będą każdego inspekcji niezależnie od siebie na podstawie ich odpowiednich zasad na poziomie serwera.

* Poziom bazy danych: Inspekcji dla pomocniczych baz danych na poziomie bazy danych można skonfigurować tylko z podstawowej bazy danych, ustawienia inspekcji.
   * Inspekcja musi być włączona na *podstawowa baza danych sam*, nie na serwerze.
   * Po włączeniu inspekcji podstawowej bazy danych, będzie również stać się włączone w pomocniczej bazie danych.

    >[!IMPORTANT]
    >Za pomocą inspekcji poziomu bazy danych, ustawienia magazynu dla pomocniczej bazy danych będzie identyczne z podstawowej bazy danych, co spowoduje niepowodzenie ruch między regionami. Zaleca się włączania inspekcji tylko poziomu serwera i pozostaw inspekcji poziomu bazy danych wyłączone dla wszystkich baz danych.
<br>

### <a id="subheading-6">Ponowne generowanie klucza magazynu</a>
W środowisku produkcyjnym prawdopodobnie okresowo odświeżyć klucze magazynu. Podczas zapisywania dzienników inspekcji usługi Azure storage, musisz ponownie zapisać zasady inspekcji podczas odświeżania klucze. Proces przebiega w następujący sposób:

1. Otwórz **szczegóły magazynu**. W **klucz dostępu do magazynu** wybierz opcję **dodatkowej**i kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** w górnej części strony Konfiguracja inspekcji.

    ![Okienko nawigacji][5]
2. Przejdź do strony konfiguracji magazynu, a następnie ponownie wygenerować podstawowy klucz dostępu.

    ![Okienko nawigacji][6]
3. Przejdź z powrotem na stronie Konfiguracja inspekcji przełączyć klucz dostępu do magazynu z dodatkowej do głównej, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **Zapisz** w górnej części strony Konfiguracja inspekcji.
4. Wróć do strony konfiguracji magazynu i ponownie wygenerować pomocniczy klucz dostępu (w celu przygotowania następny klawisz cyklu odświeżania).

## <a name="additional-information"></a>Dodatkowe informacje

* Aby uzyskać szczegółowe informacje o dzienniku formatowania hierarchii folderu przechowywania i konwencje nazewnictwa, zobacz [odwołanie Format dziennika inspekcji obiektów Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Inspekcji platformy Azure SQL Database przechowuje 4000 znaków dane dla znaków pola rekordu inspekcji. Gdy **instrukcji** lub **data_sensitivity_information** wartości zwrócone z akcji podlegających inspekcji zawiera więcej niż 4000 znaków, będzie można odświeżyć żadnych danych poza najpierw 4000 znaków  **obcięty i nie inspekcji**.

* Dzienniki inspekcji są zapisywane w **obiekty BLOB dołączania** w usłudze Azure Blob storage w ramach subskrypcji platformy Azure:
    * **Usługa Premium Storage** jest obecnie **nieobsługiwane** przez obiekty BLOB dołączania.
    * **Magazyn w sieci wirtualnej** jest obecnie **nieobsługiwane**.

* Domyślne zasady inspekcji zawiera wszystkie akcje i następujący zestaw grup akcji, które zostanie przeprowadzona inspekcja wszystkie zapytania i procedur składowanych wykonywanych względem bazy danych, a także udane i nieudane logowania:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Można skonfigurować inspekcję różnego rodzaju akcje i grup akcji przy użyciu programu PowerShell, zgodnie z opisem w [Zarządzanie inspekcji usługi SQL database przy użyciu programu Azure PowerShell](#subheading-7) sekcji.

## <a id="subheading-7"></a>Zarządzanie inspekcji usługi SQL database przy użyciu programu Azure PowerShell

**Polecenia cmdlet programu PowerShell**:

* [Utwórz lub zaktualizuj Blob bazy danych inspekcji zasad (Set-AzureRMSqlDatabaseAuditing)][105]
* [Utwórz lub zaktualizuj serwer Blob inspekcji zasad (Set-AzureRMSqlServerAuditing)][106]
* [Pobierz zasady inspekcji bazy danych (Get-AzureRMSqlDatabaseAuditing)][101]
* [Pobieranie obiektów Blob serwera zasady inspekcji (Get-AzureRMSqlServerAuditing)][102]

Aby uzyskać przykładowy skrypt, zobacz [skonfigurować inspekcję i wykrywanie zagrożeń za pomocą programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Zarządzanie inspekcji usługi SQL database przy użyciu interfejsu API REST

**Interfejs API REST — Inspekcja obiektów Blob**:

* [Utwórz lub zaktualizuj bazę danych obiektów Blob, zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/createorupdate)
* [Utwórz lub zaktualizuj obiektów Blob serwera zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/createorupdate)
* [Pobieranie obiektu Blob bazy danych, zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/get)
* [Pobieranie obiektów Blob serwera zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/get)

Rozszerzone zasady, z którym klauzuli pomocy technicznej w celu filtrowania dodatkowych:
* [Utwórz lub zaktualizuj bazę danych *rozszerzone* Blob zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
* [Utwórz lub zaktualizuj serwer *rozszerzone* Blob zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/createorupdate)
* [Pobierz bazę danych *rozszerzone* Blob zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/get)
* [Pobierz serwer *rozszerzone* Blob zasady inspekcji](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/get)

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
