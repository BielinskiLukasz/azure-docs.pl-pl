---
title: 'Zestaw narzędzi platformy Azure dla środowiska IntelliJ: aplikacje Spark debugowanie zdalne za pośrednictwem protokołu SSH '
description: Wskazówki krok po kroku dotyczące sposobu używania narzędzi HDInsight w usłudze Azure Toolkit for IntelliJ do debugowania aplikacji zdalnie w HDInsight clusters za pośrednictwem protokołu SSH
keywords: zdalne debugowanie środowiska intellij, zdalne debugowanie środowiska intellij, ssh, intellij, hdinsight, debugowanie środowiska intellij, debugowanie
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: f26355f32eeeb6b6e195b838bed502271533f4f4
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047744"
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debugowanie aplikacji Spark lokalnie lub zdalnie w klastrze usługi HDInsight przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ za pośrednictwem protokołu SSH

Ten artykuł zawiera wskazówki krok po kroku dotyczące sposobu używania narzędzi HDInsight w Azure Toolkit for IntelliJ, aby debugować aplikacje zdalne w klastrze usługi HDInsight. Aby debugować projekt, możesz również wyświetlić [aplikacje debugowania platformy HDInsight Spark przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) wideo.

**Wymagania wstępne**
* **Narzędzia HDInsight w usłudze Azure Toolkit for IntelliJ**. To narzędzie jest częścią zestawu narzędzi platformy Azure dla środowiska IntelliJ. Aby uzyskać więcej informacji, zobacz [zainstalować Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). I **Azure Toolkit for IntelliJ**. Ten zestaw narzędzi umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight. Aby uzyskać więcej informacji, postępuj zgodnie z instrukcjami w [użycia Azure Toolkit for IntelliJ, tworzenie aplikacji dla klastra usługi HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Usługa HDInsight protokołu SSH przy użyciu nazwy użytkownika i hasła management**. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z HDInsight (Hadoop) przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [używanie protokołu SSH tunelowania do dostępu do systemu Ambari web UI, JobHistory, NameNode, Oozie i innych interfejsów użytkownika sieci web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Dowiedz się, jak wykonać lokalne uruchamianie i debugowanie
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenariusz 1: Tworzenie aplikacji Spark Scala 

1. Uruchom środowisko IntelliJ IDEA, a następnie utwórz projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

   a. Wybierz **HDInsight**. 

   b. Wybierz język Java lub Scala szablon na podstawie preferencji użytkownika. Wybierz jedną z następujących opcji:

      - **Spark on HDInsight (Scala)**

      - **Spark on HDInsight (Java)**

      - **Spark on HDInsight przykładowy (Scala)**

      W tym przykładzie użyto **Spark on HDInsight przykładowy (Scala)** szablonu.

   c. Z listy **Narzędzie kompilacji** wybierz jedną z następujących pozycji odpowiednio do potrzeb:

      - **Maven** — w celu obsługi kreatora tworzenia projektu Scala

      -  **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala 

      ![Tworzenie projektu debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Wybierz opcję **Dalej**.     
 
1. W ciągu następnych **nowy projekt** okna, wykonaj następujące czynności:

   ![Wybierz zestaw SDK platformy Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Wprowadź nazwę projektu i lokalizację projektu.

   b. W **SDK projektu** listy rozwijanej wybierz **Java 1.8** dla **Spark 2.x** klastra lub wybierz **środowiska Java 1.7** dla **Spark 1.x**  klastra.

   c. W **wersji platformy Spark** listy rozwijanej, Kreator tworzenia projektu w języku Scala integruje się poprawna wersja zestawu SDK platformy Spark i Scala zestawu SDK. Jeśli wersja klastra spark jest starsza niż w wersji 2.0, wybierz opcję **Spark 1.x**. W przeciwnym razie wybierz **Spark 2.x.** W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.

   d. Wybierz pozycję **Finish** (Zakończ).

1. Wybierz **src** > **głównego** > **scala** otworzyć swój kod w projekcie. W tym przykładzie użyto **SparkCore_wasbloTest** skryptu.

### <a name="prerequisite-for-windows"></a>Wymagania wstępne dla systemu windows
Gdy korzystasz z aplikacji Spark Scala lokalnej na komputerze Windows, możesz otrzymać wyjątek, jak wyjaśniono w [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje, ponieważ brakuje WinUtils.exe na Windows. 

Aby rozwiązać ten problem, [Pobierz plik wykonywalny](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takich jak **C:\WinUtils\bin**. Następnie należy dodać zmienną środowiskową **HADOOP_HOME**, a następnie ustaw wartość zmiennej **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenariusz 2: Wykonaj uruchamiania lokalnego
1. Otwórz **SparkCore_wasbloTest** skrypt, kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz opcję **Uruchom "[zadanie platformy Apache Spark] XXX"** do wykonania przebiegu lokalnego.
1. Raz lokalne uruchomienie zakończone, widać plik wyjściowy, zapisać swoje bieżące Eksplorator projektów **danych** > **__domyślne__**.

    ![Wynik uruchomienia lokalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Nasze narzędzia ustawiono domyślną debugowanie konfiguracji uruchamiania lokalnego automatycznie, gdy wykonujesz lokalnego uruchomienia i lokalne. Otwórz konfigurację **XXX [zadanie platformy Apache Spark]** w prawym górnym rogu widać **XXX [zadanie platformy Apache Spark]** już utworzonych w ramach **zadanie platformy Apache Spark HDInsight Azure**. Przełącz się do **lokalnie uruchomić** kartę.

    ![Konfiguracji uruchamiania lokalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Zmienne środowiskowe](#prerequisite-for-windows): Jeśli ustawisz zmiennej środowiskowej systemu **HADOOP_HOME** do **C:\WinUtils**, można automatycznie wykryć, że nie trzeba ręcznie dodać.
    - [Lokalizacja WinUtils.exe](#prerequisite-for-windows): Jeśli nie ustawiono zmiennej środowiskowej systemu, można znaleźć lokalizacji, klikając przycisk.
    - Wystarczy wybrać jedną z dwóch opcji, a nie są one potrzebne w systemach MacOS i Linux.
1. Można również ustawić konfigurację ręcznie przed przystąpieniem do wykonywania debugowania lokalnego uruchomienia i lokalnych. Na poprzednim zrzucie ekranu, wybierz znak plus (**+**). Następnie wybierz pozycję **zadanie platformy Apache Spark HDInsight Azure** opcji. Wprowadź informacje dotyczące **nazwa**, **Nazwa klasy Main** można zapisać, a następnie kliknij przycisk uruchamiania lokalnego.

### <a name="scenario-3-perform-local-debugging"></a>Scenariusz 3: Wykonaj debugowanie lokalne
1. Otwórz **SparkCore_wasbloTest** skryptu, ustawianie punktów przerwania.
1. Kliknij prawym przyciskiem myszy w Edytorze skryptów, a następnie wybierz opcję **Debug "[zadanie platformy Apache Spark] XXX"** przeprowadzić debugowania lokalnego.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Dowiedz się, jak przeprowadzić zdalne uruchamianie i debugowanie
### <a name="scenario-1-perform-remote-run"></a>Scenariusz 1: Wykonania zdalnego wykonywania

1. Aby uzyskać dostęp do **Edytuj konfiguracje** menu, wybierz ikonę w prawym górnym rogu. Z tego menu możesz Tworzenie lub Edycja konfiguracji do zdalnego debugowania.

   ![Edytuj konfiguracje](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. W **konfiguracji uruchomienia/debugowania** okna dialogowego wybierz znak plus (**+**). Następnie wybierz pozycję **zadanie platformy Apache Spark HDInsight Azure** opcji.

   ![Dodawanie nowej konfiguracji](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Przełącz się do **zdalnie uruchomić w klastrze** kartę. Wprowadź informacje dotyczące **nazwa**, **klastra Spark**, i **Nazwa klasy Main**. Następnie wybierz pozycję **zaawansowanej konfiguracji**. Nasze narzędzia obsługi debugowania za pomocą **Executors**. **NumExectors**, wartość domyślna to 5. Czy lepiej nie ustawisz wyższej niż 3.

   ![Uruchom konfiguracje debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. W **Spark przesyłania Advanced Configuration** okno dialogowe, wybierz opcję **Spark włączyć zdalne debugowanie**. Wprowadź nazwę użytkownika SSH, a następnie wprowadź hasło lub użyj pliku klucza prywatnego. Aby zapisać konfigurację, zaznacz **OK**. Jeśli chcesz przeprowadzić debugowania zdalnego, należy ustawić go. Nie ma potrzeby można ustawić go, jeśli chcesz użyć zdalnego uruchamiania.

   ![Włącz zdalne debugowanie platformy Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfiguracja jest teraz zapisywane przy użyciu podanej nazwy. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz **Edytuj konfiguracje**. 

1. Po zakończeniu konfiguracji ustawień konfiguracji, możesz uruchomić aplikację w klastrze zdalnym lub przeprowadzić zdalne debugowanie.
   
   ![Przycisk uruchamiania zdalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Kliknij przycisk **rozłączenia** przycisk, który pojawia się w lewym panelu w dziennikach przesyłania. Jednak jest wciąż uruchomiona na wewnętrznej bazie danych.

   ![Przycisk uruchamiania zdalnego](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scenariusz 2: Przeprowadzić zdalne debugowanie
1. Ustawianie punktów przerwania, a następnie kliknij przycisk **zdalne debugowanie** ikony. Różnica między przesyłania zdalnego jest wymagające nazwę użytkownika i hasło protokołu SSH, można skonfigurować.

   ![Wybierz ikonę debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Podczas wykonywania programu osiąga punkt krytyczny, zostanie wyświetlony **sterownika** kartę i dwa **wykonawca** karty w **debugera** okienka. Wybierz **Program Wznów** ikonę, aby kontynuować wykonywanie kodu, który następnie osiągnie następnego punktu przerwania. Musisz przełączyć się do prawidłowego **wykonawca** kartę, aby znaleźć wykonawca docelowego debugowania. Możesz wyświetlić dzienniki wykonywania w odpowiedniej **konsoli** kartę.

   ![Karta debugowanie](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenariusz 3: Wykonywania zdalnego debugowania i poprawianie błędów
1. Konfigurowanie dwóch punktów przerwania, a następnie wybierz **debugowania** ikonę, aby rozpocząć proces debugowania zdalnego.

1. Kod zatrzymuje się zgodnie z pierwszym punktem podziału, a parametr i zmienne informacje są wyświetlane w **zmienne** okienka. 

1. Wybierz **Program Wznów** ikonę, aby kontynuować. Kod zatrzymuje się na drugiego punktu. Wyjątek jest zgodnie z oczekiwaniami.

   ![Zgłoś błąd](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Wybierz **Program Wznów** ponownie ikonę. **HDInsight Spark przesyłania** okno wyświetla błąd "Uruchamianie nie powiodło się zadanie".

   ![Przesyłanie błędów](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Aby dynamicznie aktualizować wartość zmiennej za pomocą środowiska IntelliJ funkcji debugowania, wybierz **debugowania** ponownie. **Zmienne** okienku pojawi się ponownie. 

1. Kliknij prawym przyciskiem myszy element docelowy **debugowania** , a następnie wybierz pozycję **ustaw wartość**. Następnie wprowadź nową wartość dla zmiennej. Następnie wybierz pozycję **Enter** można zapisać wartości. 

   ![Ustawiono wartość](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Wybierz **Program Wznów** ikonę, aby kontynuować uruchomić program. Tym razem zgłoszony żaden wyjątek. Możesz zobaczyć, czy projekt jest wykonywana prawidłowo bez żadnych wyjątków.

   ![Debugowanie bez wyjątku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Następne kroki
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja
* Tworzenie projektu Scala (wideo): [tworzenia aplikacji Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Zdalne debugowanie (wideo): [użycia Azure Toolkit for IntelliJ debugowanie aplikacji Spark zdalnie w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i usługa Power BI: wykonaj interakcyjna analiza danych przy użyciu platformy Spark w HDInsight przy użyciu narzędzi do analizy Biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Azure Toolkit for IntelliJ umożliwia tworzenie aplikacji Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md)
* [Debugowanie aplikacji Spark zdalnie za pośrednictwem sieci VPN przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystaj z narzędzi HDInsight dla programu IntelliJ z Piaskownicą Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Narzędzia HDInsight znajdujące się w zestaw narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark for HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
