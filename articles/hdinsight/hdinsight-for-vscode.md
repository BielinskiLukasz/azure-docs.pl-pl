---
title: Usługa Azure HDInsight dla Visual Studio Code
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty Visual Studio Code za pomocą narzędzi Hive & platformy Spark (Azure HDInsight).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435680"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Użyj narzędzi Hive & platformy Spark dla Visual Studio Code

Dowiedz się, jak używać narzędzi Hive & platformy Spark dla Visual Studio Code do tworzenia i przesyłania Apache Hive zadań wsadowych, interakcyjnych zapytań Hive i skryptów PySpark dla Apache Spark. Najpierw opisano sposób instalowania narzędzi Hive & platformy Spark w programie Visual Studio Code, a następnie przeprowadzimy Cię przez proces przesyłania zadań do narzędzi programu Hive & platformy Spark.  

Narzędzia programu Hive & platformy Spark można instalować na platformach obsługiwanych przez Visual Studio Code, takich jak Windows, Linux i macOS. Należy pamiętać o następujących wymaganiach wstępnych dla różnych platform.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

- Klaster usługi Azure HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Lub użyj klastra Spark i Hive obsługującego punkt końcowy Apache usługi Livy.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest wymagane tylko dla systemów Linux i macOS.
- [Środowisko interaktywne PySpark dla Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Katalog lokalny. W tym artykule jest używanych **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalowanie narzędzi Hive & platformy Spark

Po spełnieniu wymagań wstępnych można zainstalować narzędzia Hive & platformy Spark dla Visual Studio Code, wykonując następujące czynności:

1. Otwórz program Visual Studio Code.

2. Na pasku menu Przejdź do **widoku** > **rozszerzenia**.

3. W polu wyszukiwania wprowadź **gałąź & platformy Spark**.

4. W wynikach wyszukiwania wybierz pozycję **Narzędzia platformy Spark & Hive** , a następnie wybierz pozycję **Zainstaluj**:

   ![Gałąź & platformy Spark dla Visual Studio Code instalacji języka Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. W razie potrzeby wybierz pozycję **Załaduj ponownie** .

## <a name="open-a-work-folder"></a>Otwieranie folderu służbowego

Aby otworzyć folder roboczy i utworzyć plik w Visual Studio Code, wykonaj następujące kroki:

1. Na pasku menu Przejdź do **pliku** > **Otwórz folder...**  > **C:\HD\HDexample**, a następnie wybierz przycisk **Wybierz folder** . Folder zostanie wyświetlony w widoku **Eksploratora** po lewej stronie.

2. W widoku **Eksploratora** wybierz folder **HDexample** , a następnie wybierz ikonę **nowy plik** obok folderu Work:

   ![ikona nowego pliku programu Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nazwij nowy plik przy użyciu `.hql` (zapytania Hive) lub rozszerzenia pliku `.py` (Spark Script). W tym przykładzie zastosowano **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika w chmurze krajowej wykonaj następujące kroki, aby najpierw ustawić środowisko platformy Azure, a następnie zaloguj się do platformy Azure przy użyciu polecenia **Azure: Signing** :

1. Przejdź do **okna preferencji** > **pliku** > **Ustawienia**.
2. Wyszukaj następujący ciąg: **Azure: Cloud**.
3. Wybierz z listy chmurę krajową:

   ![Ustaw domyślną konfigurację wpisu logowania](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Nawiązywanie połączenia z kontem platformy Azure

Aby można było przesłać skrypty do klastrów z Visual Studio Code, należy połączyć się z kontem platformy Azure lub połączyć klaster (przy użyciu poświadczeń użytkownika Apache Ambari i hasła lub konta przyłączonego do domeny). Wykonaj następujące kroki, aby nawiązać połączenie z platformą Azure:

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Azure: Logowanie**:

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code logowania](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z instrukcjami logowania, aby zalogować się do platformy Azure. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu u dołu okna Visual Studio Code.  

## <a name="link-a-cluster"></a>Łączenie klastra

### <a name="link-azure-hdinsight"></a>Link: usługa Azure HDInsight

Można połączyć normalny klaster przy użyciu nazwy użytkownika zarządzanego przez firmę [Apache Ambari](https://ambari.apache.org/)lub połączyć klaster usługi Hadoop z pakietem zabezpieczeń przedsiębiorstwa przy użyciu nazwy użytkownika domeny (na przykład: `user1@contoso.com`).

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Spark/Hive: Połącz klaster**.

   ![Link do palety poleceń — polecenie cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wybierz pozycję połączony klaster wpisz **Azure HDInsight**.

3. Wprowadź adres URL klastra usługi HDInsight.

4. Wprowadź nazwę użytkownika Ambari; wartość domyślna to **admin**.

5. Wprowadź hasło Ambari.

6. Wybierz typ klastra.

7. Ustaw nazwę wyświetlaną klastra (opcjonalnie).

8. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

   > [!NOTE]  
   > Połączona nazwa użytkownika i hasło są używane, Jeśli klaster jest zalogowany do subskrypcji platformy Azure i połączony z klastrem.  

### <a name="link-generic-livy-endpoint"></a>Link: ogólny punkt końcowy usługi Livy

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Spark/Hive: Połącz klaster**.

2. Wybierz pozycję połączony klaster typ **rodzajowy punkt końcowy usługi Livy**.

3. Wprowadź ogólny punkt końcowy usługi Livy. Na przykład: http\://10.172.41.42:18080.

4. Wybierz pozycję typ autoryzacji **podstawowa** lub **Brak**.  W przypadku wybrania opcji **podstawowa**:  
    &emsp;a. Wprowadź nazwę użytkownika Ambari; wartość domyślna to **admin**.  
    &emsp;b. Wprowadź hasło Ambari.

5. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

## <a name="list-clusters"></a>Wyświetl listę klastrów

1. Na pasku menu Przejdź do **widoku** > **paleta poleceń...** , a następnie wprowadź **Spark/Hive: Lista klaster**.

2. Wybierz żądaną subskrypcję.

3. Przejrzyj widok **danych wyjściowych** . Ten widok przedstawia połączony klaster (lub klastry) i wszystkie klastry w ramach subskrypcji platformy Azure:

    ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Ustaw domyślny klaster

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** , który został utworzony [wcześniej](#open-a-work-folder). Zostanie on otwarty w Edytorze skryptów.

3. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark/Hive: Ustaw domyślny klaster**.  

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Wybierz klaster jako domyślny klaster dla bieżącego pliku skryptu. Narzędzia automatycznie aktualizują **.** Plik konfiguracji VSCode\settings.JSON:

   ![Ustaw domyślną konfigurację klastra](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Przesyłanie interakcyjnych zapytań Hive i skryptów wsadowych Hive

Za pomocą narzędzi platformy Spark & Hive dla Visual Studio Code można przesyłać interaktywne zapytania Hive i skrypty wsadowe Hive do klastrów.

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** , który został utworzony [wcześniej](#open-a-work-folder). Zostanie on otwarty w Edytorze skryptów.

3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów i wybierz polecenie **Hive: Interactive** , aby przesłać zapytanie, lub użyj skrótu klawiaturowego Ctrl + Alt + I.  Wybierz **gałąź Hive: Batch** , aby przesłać skrypt, lub użyj skrótu klawiaturowego CTRL + ALT + H.  

6. Jeśli klaster domyślny nie został określony, wybierz klaster. Narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania pojawią się na nowej karcie:

   ![Interaktywny wynik zapytania Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **wyników** : można zapisać cały wynik jako plik CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

    - Panel **komunikatów** : po wybraniu numeru **wiersza** zostanie on przeskoczy do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Prześlij interaktywne zapytania PySpark

Aby przesłać interaktywne zapytania PySpark, wykonaj następujące kroki:

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** , postępując zgodnie z [wcześniejszymi](#open-a-work-folder) krokami.

3. Skopiuj i wklej następujący kod do pliku skryptu:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Zaznacz cały kod, kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark: PySpark Interactive** , aby przesłać zapytanie. Lub użyj skrótu Ctrl + Alt + I.

   ![interakcyjne menu kontekstowe pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Wybierz klaster, jeśli nie został określony domyślny klaster. Po kilku chwilach interaktywne wyniki języka **Python** pojawiają się na nowej karcie. Narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego:

   ![interaktywne okno interaktywnego środowiska Python pyspark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Wprowadź **%% info**, a następnie naciśnij klawisze SHIFT + ENTER, aby wyświetlić informacje o zadaniu (opcjonalnie):

   ![Informacje o zadaniach pyspark Interactive](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Narzędzie obsługuje również zapytanie **Spark SQL** :

   ![Wynik widoku interaktywnego Pyspark](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Stan przesyłania jest wyświetlany po lewej stronie dolnego paska stanu, gdy są wykonywane zapytania. Nie przesyłaj innych zapytań, gdy stanem jest **PySpark jądra (zajęty)** .  

   > [!NOTE]
   >
   > Gdy **włączone rozszerzenie języka Python** zostanie wyczyszczone w ustawieniach (domyślnie zaznaczone), przesłane wyniki interakcji pyspark będą używać starego okna:
   >
   > ![pyspark interaktywne rozszerzenie Python jest wyłączone](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Prześlij zadanie wsadowe PySpark

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **BatchFile.py** , wykonując czynności opisane w [poprzednich](#open-a-work-folder) krokach.

3. Skopiuj i wklej następujący kod do pliku skryptu:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark: PySpark Batch**lub użyj skrótu klawiaturowego CTRL + ALT + H.

6. Wybierz klaster, do którego chcesz przesłać zadanie PySpark:

   ![Przesyłanie danych wyjściowych wyników zadania w języku Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Po przesłaniu zadania w języku Python dzienniki przesyłania są wyświetlane w oknie **dane wyjściowe** w Visual Studio Code. Wyświetlany jest również adres URL interfejsu użytkownika platformy Spark i adres URL interfejsu użytkownika przędzy. Możesz otworzyć adres URL w przeglądarce sieci Web, aby śledzić stan zadania.

## <a name="apache-livy-configuration"></a>Konfiguracja Apache usługi Livy

Konfiguracja [Apache usługi Livy](https://livy.incubator.apache.org/) jest obsługiwana. Można ją skonfigurować w programie **. Plik VSCode\settings.json** w folderze obszaru roboczego. Obecnie konfiguracja usługi Livy obsługuje tylko skrypt języka Python. Aby uzyskać więcej informacji, zobacz [plik Readme usługi Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak wyzwolić konfigurację usługi Livy**

Metoda 1  
1. Na pasku menu Przejdź do **okna preferencji** > **pliku** > **Ustawienia**.
2. W polu **Wyszukaj ustawienia** wprowadź wartość **przesyłanie zadania usługi HDInsight: usługi Livy conf**.  
3. Wybierz pozycję **Edytuj w pliku Settings. JSON** , aby uzyskać odpowiedni wynik wyszukiwania.

Metoda 2 przesyła plik i Zauważ, że folder. programu vscode jest automatycznie dodawany do folderu Work. Konfigurację usługi Livy można wyświetlić, wybierając pozycję **. vscode\settings.JSON**.

+ Ustawienia projektu:

    ![Konfiguracja usługi HDInsight Apache usługi Livy](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Dla ustawień **driverMemory** i **executorMemory** ustaw wartość i jednostkę. Na przykład: 1g lub 1024m.

+ Obsługiwane konfiguracje usługi Livy:

    **Opublikuj/Batches** Treść żądania

    | name | description | type |
    | :- | :- | :- |
    | plik | Plik zawierający aplikację do wykonania | Ścieżka (wymagana) |
    | proxyUser | Użytkownik do personifikacji podczas uruchamiania zadania | Ciąg |
    | className | Aplikacja Java/Spark klasy głównej | Ciąg |
    | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów |
    | jars | Jars do użycia w tej sesji | Lista ciągów | 
    | pyFiles | Pliki języka Python, które mają być używane w tej sesji | Lista ciągów |
    | files | Pliki, które mają być używane w tej sesji | Lista ciągów |
    | driverMemory | Ilość pamięci do użycia w procesie sterownika | Ciąg |
    | driverCores | Liczba rdzeni do użycia w procesie sterownika | Int |
    | executorMemory | Ilość pamięci do użycia na proces wykonawczy | Ciąg |
    | executorCores | Liczba rdzeni do użycia dla każdego wykonawcy | Int |
    | numExecutors | Liczba uruchomień do uruchomienia dla tej sesji | Int |
    | archives | Archiwa, które mają być używane w tej sesji | Lista ciągów |
    | kolejka | Nazwa kolejki PRZĘDZy do przesłania| Ciąg |
    | name | Nazwa tej sesji | Ciąg |
    | conf | Właściwości konfiguracji platformy Spark | Mapa klucza = Val |

    Treść odpowiedzi utworzonego obiektu wsadowego.

    | name | description | type |
    | :- | :- | :- |
    | id | Identyfikator sesji | Int |
    | appId | Identyfikator aplikacji dla tej sesji | Ciąg |
    | appInfo | Szczegółowe informacje o aplikacji | Mapa klucza = Val |
    | log | Wiersze dziennika | Lista ciągów |
    | state |Stan partii | Ciąg |

    > [!NOTE]
    > Przypisana usługi Livy konfiguracja zostanie wyświetlona w okienku danych wyjściowych podczas przesyłania skryptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

Tabelę programu Hive można wyświetlić w klastrach bezpośrednio za pomocą Eksploratora **usługi Azure HDInsight** :

1. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

2. Wybierz ikonę **platformy Azure** z kolumny z lewej strony.

3. W okienku po lewej stronie rozwiń pozycję **Azure: HDINSIGHT**. Dostępne subskrypcje i klastry są wymienione na liście.

4. Rozwiń klaster, aby wyświetlić bazę danych metadanych programu Hive i schemat tabeli.

5. Kliknij prawym przyciskiem myszy tabelę programu Hive. Na przykład: **hivesampletable**. Wybierz pozycję **Podgląd**.

   ![Gałąź & platformy Spark dla Visual Studio Code wersji zapoznawczej tabeli Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Zostanie otwarte okno **Podgląd wyników** :

   ![Gałąź & platformy Spark dla okna wyników podglądu Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Panel wyników

   Można zapisać cały wynik jako plik CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

- Panel komunikatów
   1. Gdy liczba wierszy w tabeli jest większa niż 100, zostanie wyświetlony następujący komunikat: "pierwsze 100 wierszy są wyświetlane dla tabeli programu Hive".
   2. Gdy liczba wierszy w tabeli jest mniejsza lub równa 100, zostanie wyświetlony komunikat podobny do następującego: "60 wierszy są wyświetlane dla tabeli programu Hive".
   3. Gdy w tabeli nie ma zawartości, zostanie wyświetlony następujący komunikat: "0 wierszy jest wyświetlanych dla tabeli programu Hive".

        >[!NOTE]
        >
        >W systemie Linux Zainstaluj XCLIP, aby włączyć dane kopiowania z tabeli.
        >
        >![Platforma Spark & Hive dla programu Visual Studio Code w systemie Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Funkcje dodatkowe

Gałąź & platformy Spark dla Visual Studio Code obsługuje również następujące funkcje:

- **Autouzupełnianie funkcji IntelliSense**. Sugestie są wyskakujące dla słów kluczowych, metod, zmiennych i innych elementów programistycznych. Różne ikony reprezentują różne typy obiektów:

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code obiektów IntelliSense](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Znacznik błędu funkcji IntelliSense**. Usługa językowa podkreśla błędy w skrypcie Hive.     
- **Najważniejsze informacje o składni**. Usługa językowa używa różnych kolorów do odróżniania zmiennych, słów kluczowych, typu danych, funkcji i innych elementów programistycznych:

    ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code składni](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rola tylko do odczytu

Użytkownicy, którym przypisano rolę tylko do odczytu dla klastra, nie mogą przesyłać zadań do klastra usługi HDInsight ani nie mogą wyświetlać bazy danych programu Hive. Skontaktuj się z administratorem klastra, aby uaktualnić rolę do [**operatora klastra usługi HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) w [Azure Portal](https://ms.portal.azure.com/). Jeśli masz prawidłowe poświadczenia Ambari, możesz ręcznie połączyć klaster, korzystając z poniższych wskazówek.

### <a name="browse-the-hdinsight-cluster"></a>Przeglądanie klastra usługi HDInsight  

Po wybraniu programu Azure HDInsight Explorer do rozwinięcia klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli istnieje rola tylko do odczytu dla danego klastra. Użyj następującej metody, aby połączyć się z klastrem przy użyciu poświadczeń Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Prześlij zadanie do klastra usługi HDInsight

Podczas przesyłania zadania do klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli jesteś w roli tylko do odczytu klastra. Wykonaj następujące kroki, aby połączyć się z klastrem przy użyciu poświadczeń Ambari.

### <a name="link-to-the-cluster"></a>Połącz z klastrem

1. Wprowadź prawidłową nazwę użytkownika Ambari.
2. Wprowadź prawidłowe hasło.

   ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code użytkownika](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Narzędzia programu Hive & platformy Spark dla hasła Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Aby sprawdzić połączony klaster, można użyć `Spark / Hive: List Cluster`.
  >
  >![Narzędzia programu Hive & platformy Spark dla połączonego czytnika Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="browse-a-data-lake-storage-gen2-account"></a>Przeglądanie konta Data Lake Storage Gen2

Po wybraniu opcji Eksplorator usługi Azure HDInsight w celu rozszerzenia konta Data Lake Storage Gen2 zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli Twoje konto platformy Azure nie ma dostępu do magazynu Gen2. Po sprawdzeniu poprawności klucza dostępu konto Data Lake Storage Gen2 zostanie rozwinięte.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Przesyłanie zadań do klastra usługi HDInsight przy użyciu Data Lake Storage Gen2

Po przesłaniu zadania do klastra usługi HDInsight przy użyciu Data Lake Storage Gen2 zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli konto platformy Azure nie ma dostępu do zapisu do magazynu Gen2. Po sprawdzeniu poprawności klucza dostępu zadanie zostanie pomyślnie przesłane.

![Narzędzia programu Hive & platformy Spark dla Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Możesz uzyskać klucz dostępu dla konta magazynu z Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Odłącz klaster

1. Na pasku menu Przejdź do pozycji **wyświetl** > **paleta poleceń**, a następnie wprowadź **Spark/Hive: Odłącz klaster**.  

2. Wybierz klaster do odłączenia.  

3. Zobacz widok **danych wyjściowych** w celu weryfikacji.  

## <a name="sign-out"></a>Wyloguj  

Na pasku menu Przejdź do pozycji **wyświetl** > **paleta poleceń**, a następnie wprowadź **Azure: Wyloguj się**.

## <a name="next-steps"></a>Następne kroki

Film przedstawiający korzystanie z usługi Spark & Hive dla Visual Studio Code można znaleźć w temacie [gałąź & platformy Spark dla Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
