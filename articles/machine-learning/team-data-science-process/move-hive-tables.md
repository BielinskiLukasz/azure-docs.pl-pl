---
title: Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage | Dokumentacja firmy Microsoft
description: Tworzenie tabel programu Hive i ładowanie danych w obiekcie blob do tabel programu hive
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c1cbd523b8c74bf1221dc47b832c35e9119493a9
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346290"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage

W tym artykule przedstawiono ogólny zapytań programu Hive, które umożliwiają tworzenie tabel programu Hive i ładowanie danych z usługi Azure blob storage. Wskazówki udostępniane są również na partycjonowania tabel programu Hive i przy użyciu zoptymalizowane pod kątem wiersz kolumnowych (ORC) odpowiadający ustawieniom lokalnym poprawić wydajność zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).
* Zainicjowano obsługę administracyjną dostosowane klaster Hadoop w usłudze HDInsight.  Aby uzyskać instrukcje, zobacz [dostosować Azure HDInsight Hadoop clusters zaawansowanych funkcji analizy](customize-hadoop-cluster.md).
* Włączony zdalny dostęp do klastra, zalogowany, a następnie otworzyć konsolę wiersza polecenia usługi Hadoop. Aby uzyskać instrukcje, zobacz [dostęp węzeł główny klastra Hadoop](customize-hadoop-cluster.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure blob storage
Jeśli utworzono maszynę wirtualną platformy Azure, postępując zgodnie z instrukcjami podanymi w [skonfigurować maszynę wirtualną platformy Azure, aby uzyskać zaawansowane funkcje analityczne](../data-science-virtual-machine/setup-virtual-machine.md), tego pliku skryptu powinny pobierane do *C:\\użytkowników \\ \<nazwa_użytkownika\>\\dokumenty\\skryptów do nauki o danych* katalog na maszynie wirtualnej. Te zapytania programu Hive wymagają tylko należy podłączyć własny schemat danych i konfiguracji magazynu obiektów blob platformy Azure w odpowiednich polach jest gotowy do przesłania.

Przyjęto założenie, że dane dla tabel programu Hive jest w **nieskompresowanych** formacie tabelarycznym, a także czy danych został przekazany do domyślnego (lub dodatkowego) kontenera konta magazynu używanego przez klaster usługi Hadoop.

Jeśli chcesz rozwiązanie polegające na **danych podróży taksówek NYC**, musisz:

* **Pobierz** 24 [danych podróży taksówek NYC](http://www.andresmh.com/nyctaxitrips) plików (12 pliki podróży i 12 taryfy plików)
* **Rozpakuj** wszystkich plików w plikach CSV, a następnie
* **Przekaż** je do domyślnych (lub odpowiedniego kontenera) konto usługi Azure storage, która została utworzona przez procedury opisanej w [dostosować Azure HDInsight Hadoop clusters for Advanced Analytics Process and Technology](customize-hadoop-cluster.md)tematu. Proces, aby przekazać pliki CSV do domyślnego kontenera na koncie magazynu można znaleźć w tym [strony](hive-walkthrough.md#upload).

## <a name="submit"></a>Jak przesłać zapytania programu Hive
Zapytania programu hive można przesyłać za pomocą:

1. [Przesyłanie zapytań programu Hive za pośrednictwem wiersza polecenia usługi Hadoop w głównym węzłem klastra usługi Hadoop](#headnode)
2. [Przesyłanie zapytań programu Hive za pomocą edytora programu Hive](#hive-editor)
3. [Przesyłanie zapytań programu Hive za pomocą poleceń programu PowerShell platformy Azure](#ps)

Zapytania hive działają podobnego do SQL. Jeśli jesteś zaznajomiony z językiem SQL, może się okazać [Hive arkusza da się oszukać użytkowników SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) przydatne.

Podczas przesyłania zapytań programu Hive, można też sterować miejsce docelowe danych wyjściowych z zapytań programu Hive, czy jest to na ekranie lub do pliku lokalnego, w węźle głównym lub do obiektu blob platformy Azure.

### <a name="headnode"></a> 1. Przesyłanie zapytań programu Hive za pośrednictwem wiersza polecenia usługi Hadoop w głównym węzłem klastra usługi Hadoop
Jeśli zapytanie Hive jest złożone, przesłania go bezpośrednio w Hadoop węzłem głównym klastra zwykle prowadzi do niewyłącznej szybciej niż przesłaniem go za pomocą skryptów Edytor Hive lub programu Azure PowerShell.

Zaloguj się do węzła głównego klastra usługi Hadoop, Otwórz wiersza polecenia usługi Hadoop na pulpicie węzeł główny i wpisz polecenie `cd %hive_home%\bin`.

Istnieją trzy sposoby, aby przesłać zapytania Hive w wierszu polecenia usługi Hadoop:

* bezpośrednio
* Korzystanie z plików .hql
* za pomocą konsoli poleceń programu Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Przesłać zapytania Hive bezpośrednio w wiersza polecenia usługi Hadoop.
Możesz uruchomić polecenie, takie jak `hive -e "<your hive query>;` do przesyłania prostych zapytań programu Hive bezpośrednio w wiersza polecenia usługi Hadoop. Oto przykład, gdzie czerwoną otoczkę przedstawiono polecenia, który przesyła zapytania programu Hive i zielone pole przedstawia wyniki zapytania programu Hive.

![Tworzenie obszaru roboczego](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Przesłać zapytania Hive w plikach .hql
Jeśli zapytanie Hive jest bardziej skomplikowane i ma wiele wierszy, edytowanie zapytań, w wierszu polecenia lub konsoli poleceń programu Hive nie jest praktyczne. Alternatywą jest Użyj edytora tekstów węzłem głównym klastra usługi Hadoop, aby zapisać zapytania programu Hive w pliku .hql w katalogu lokalnym węzła głównego. Następnie można przesłać zapytania Hive w pliku .hql przy użyciu `-f` argument w następujący sposób:

    hive -f "<path to the .hql file>"

![Tworzenie obszaru roboczego](./media/move-hive-tables/run-hive-queries-3.png)

**Pomiń wydruku ekranu stanu postępu zapytań Hive**

Domyślnie po wysłaniu zapytania programu Hive w wierszu polecenia usługi Hadoop postęp zadań Map/Reduce są drukowane na ekranie. Aby wstrzymać drukowanie ekranu postępu zadań Map/Reduce, można użyć argumentu `-S` ("S" napisane wielkimi literami) w poleceniu wiersza w następujący sposób:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Przesłać zapytania Hive w konsoli poleceń programu Hive.
Możesz również najpierw wprowadzić konsoli poleceń gałęzi, uruchamiając polecenie `hive` w wierszu polecenia usługi Hadoop i następnie przesłać zapytania Hive w konsoli poleceń programu Hive. Oto przykład. W tym przykładzie dwie czerwone pola Zaznacz polecenia służące do wprowadzania konsoli poleceń programu Hive i zapytania programu Hive przesłanego w konsoli poleceń programu Hive, odpowiednio. Zielone pole prezentuje dane wyjściowe z zapytania programu Hive.

![Tworzenie obszaru roboczego](./media/move-hive-tables/run-hive-queries-2.png)

Poprzednie przykłady danych wyjściowych bezpośrednio wyniki zapytania programu Hive na ekranie. W węźle głównym lub obiektu blob platformy Azure, można zapisać danych wyjściowych do pliku lokalnego. Następnie można użyć innych narzędzi, aby dalej analizować dane wyjściowe zapytań programu Hive.

**Dane wyjściowe wyniki zapytania programu Hive do pliku lokalnego.**
Aby dane wyjściowe wyniki zapytania programu Hive w lokalnym katalogu w węźle głównym, musisz przesłać zapytania Hive w wierszu polecenia usługi Hadoop w następujący sposób:

    hive -e "<hive query>" > <local path in the head node>

W poniższym przykładzie danych wyjściowych zapytania programu Hive są zapisywane w pliku `hivequeryoutput.txt` w katalogu `C:\apps\temp`.

![Tworzenie obszaru roboczego](./media/move-hive-tables/output-hive-results-1.png)

**Dane wyjściowe wyniki zapytania programu Hive do obiektu blob platformy Azure**

Mogą również przesyłać dane wyjściowe wyniki zapytania programu Hive do obiektu blob platformy Azure w ramach domyślnego kontenera klastra Hadoop. Zapytania programu Hive w taki sposób, w tym jest następująca:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

W poniższym przykładzie danych wyjściowych zapytania programu Hive są zapisywane w katalogu obiektów blob `queryoutputdir` w kontenerze domyślnego klastra usługi Hadoop. W tym miejscu wystarczy podać nazwę katalogu bez nazwy obiektu blob. Błąd jest generowany, jeśli podasz nazwy katalogu i obiektów blob, takich jak `wasb:///queryoutputdir/queryoutput.txt`.

![Tworzenie obszaru roboczego](./media/move-hive-tables/output-hive-results-2.png)

Jeśli otworzysz domyślnego kontenera w klastrze usługi Hadoop, za pomocą Eksploratora usługi Azure Storage, możesz wyświetlić dane wyjściowe zapytania programu Hive, jak pokazano na poniższej ilustracji. Filtr (wyróżniony za czerwoną otoczkę) można zastosować tylko pobrać obiekt blob z określonej litery w nazwach.

![Tworzenie obszaru roboczego](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Przesyłanie zapytań programu Hive za pomocą edytora programu Hive
Umożliwia także z konsoli zapytań (Edytor Hive), wprowadzając adres URL w formacie *https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* w przeglądarce sieci web. Musi być zalogowany widzą tę konsolę i dlatego należy poświadczeń klastra usługi Hadoop.

### <a name="ps"></a> 3. Przesyłanie zapytań programu Hive za pomocą poleceń programu PowerShell platformy Azure
Program PowerShell umożliwia również przesłać zapytania Hive. Aby uzyskać instrukcje, zobacz [Hive przesyłanie zadań za pomocą programu PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Tworzenie bazy danych programu Hive i tabel
Zapytania programu Hive są udostępniane w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) i można je pobrać w tym miejscu.

Oto zapytanie Hive, które tworzy tabelę programu Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Poniżej przedstawiono opisy pól, które należy podłączyć i inne konfiguracje:

* **<database name>**: Nazwa bazy danych, który chcesz utworzyć. Jeśli chcesz użyć domyślnej bazy danych, zapytanie *Utwórz bazę danych...*  można pominąć.
* **<table name>**: Nazwa tabeli, która ma zostać utworzona w ramach określonej bazy danych. Jeśli chcesz użyć domyślnej bazy danych, tabeli może być bezpośrednio odwołuje się *<table name>* bez <database name>.
* **<field separator>**: separator ograniczającego pola w pliku danych do przesłania do tabeli programu Hive.
* **<line separator>**: separator ograniczającego wierszy w pliku danych.
* **<storage location>**: lokalizacji magazynu platformy Azure, aby zapisać dane z tabel programu Hive. Jeśli nie określisz *lokalizacji <storage location>* , bazy danych i tabel są przechowywane w *hive/warehouse/* katalogu w domyślnym kontenerze klastra Hive domyślnie. Jeśli chcesz określić lokalizację magazynu, lokalizację magazynu musi mieścić się w domyślny kontener dla bazy danych i tabel. Ta lokalizacja musi być określony jako lokalizacji względnej wobec domyślny kontener klastra w formacie *"wasb: / / / < katalog 1 > /"* lub *"wasb: / / / < katalog 1 > / < katalog 2 > /"* itp. Po wykonaniu zapytania względne katalogi są tworzone w domyślnym kontenerze.
* **TBLPROPERTIES("SKIP.Header.line.Count"="1")**: Jeśli plik danych ma wiersz nagłówka, należy dodać tę właściwość **na końcu** z *Utwórz tabelę* zapytania. W przeciwnym razie wiersz nagłówka jest ładowany jako rekord w tabeli. Jeśli plik danych nie ma wiersz nagłówka, można pominąć tę konfigurację w zapytaniu.

## <a name="load-data"></a>Ładowanie danych do tabel programu Hive
Oto zapytanie Hive, który ładuje dane do tabeli programu Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Jeśli plik obiektu blob do przekazania do tabeli programu Hive znajduje się w domyślnym kontenerze klastra usługi HDInsight Hadoop *<path to blob data>* powinien być w formacie *"wasb: / / /<directory in this container> / <blob file name>'*. Można także pliku obiektu blob w kontenerze dodatkowe klastra usługi HDInsight Hadoop. W tym przypadku *<path to blob data>* powinien być w formacie *"wasb: / /<container name><storage account name>.blob.core.windows.net/<blob file name>"*.

  > [!NOTE]
  > Dane obiektu blob do przekazania do tabeli programu Hive, musi mieć domyślne lub dodatkowego kontenera konta magazynu dla klastra usługi Hadoop. W przeciwnym razie *ładowanie danych* zapytanie nie powiedzie się skarżących się, że nie można uzyskać dostęp do danych.
  >
  >

## <a name="partition-orc"></a>Tematy zaawansowane: podzielona na partycje tabeli i magazynu danych programu Hive w formacie ORC
Jeśli dane są obszerne, partycjonowania tabeli jest korzystne dla zapytań, które należy tylko skanować kilka partycji tabeli. Na przykład jest uzasadnione w celu podzielenia danych dzienników witryny sieci web według daty.

Oprócz partycjonowania tabel programu Hive, jest również przydatne do przechowywania danych programu Hive w formacie zoptymalizowane pod kątem wiersz kolumnowych (ORC). Aby uzyskać więcej informacji na temat formatowania ORC, zobacz <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">plików ORC przy użyciu zwiększa wydajność podczas odczytywania, zapisywania i przetwarzanie danych Hive</a>.

### <a name="partitioned-table"></a>Partycjonowanej tabeli
Oto zapytanie Hive, które tworzy tabeli partycjonowanej i ładowania danych do niego.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Podczas wykonywania zapytań względem tabel podzielonych na partycje, zalecane jest Dodawanie warunku partycji w **początku** z `where` klauzuli, ponieważ zwiększa skuteczność znacznie wyszukiwania.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Store Hive data w formacie ORC
Nie można bezpośrednio ładowanie danych z magazynu obiektów blob do tabel programu Hive, które są przechowywane w formacie ORC. Poniżej przedstawiono kroki, które należy wykonać, aby załadować dane z platformy Azure, obiekty BLOB do tabel programu Hive są przechowywane w formacie ORC.

Tworzenie zewnętrznej tabeli **PRZECHOWYWANE TEXTFILE AS** i ładowanie danych z magazynu obiektów blob do tabeli.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Tworzenie tabeli wewnętrznej przy użyciu tego samego schematu jako tabeli zewnętrznej w kroku 1, przy użyciu tego samego ogranicznik pola i przechowywania danych programu Hive w formacie ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Wybierz dane z tabeli zewnętrznej w kroku 1, a następnie wstawianie do tabeli ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Jeśli tabela TEXTFILE  *<database name>.<external textfile table name>* ma partycje, w KROKU 3 `SELECT * FROM <database name>.<external textfile table name>` polecenie wybiera zmiennej partycji jako pole zwróconego zestawu danych. Wstawianie do  *<database name>.<ORC table name>* kończy się niepowodzeniem, ponieważ  *<database name>.<ORC table name>* nie ma zmienną partycji jako pole w schematu tabeli. W takim przypadku musisz wybrać pola, które ma zostać wstawiony do  *<database name>.<ORC table name>* w następujący sposób:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Można bezpiecznie usunąć *<external textfile table name>* po użyciu następującego zapytania po wszystkich danych został wstawiony do *<database name>.<ORC table name>*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po wykonaniu tej procedury, należy mieć tabelę z danymi w formacie ORC, gotowe do użycia.  
