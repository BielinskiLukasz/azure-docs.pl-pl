---
title: Zarządzanie klastrami HDInsight przyłączone do domeny — Azure
description: Dowiedz się, jak zarządzać klastrami HDInsight przyłączone do domeny
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.openlocfilehash: 51feb7099328e366e59519ce108ccd9afc1e277e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597679"
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Zarządzanie klastrami HDInsight przyłączone do domeny
Dowiedz się, użytkownicy i role w HDInsight przyłączone do domeny i zarządzanie klastrami HDInsight przyłączone do domeny.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu VSCode

Można połączyć normalny klaster przy użyciu nazwy użytkownika systemu Ambari zarządzane, również połączyć klaster hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com).
1. Otwórz paletę poleceń, wybierając pozycję **CTRL + SHIFT + P**, a następnie wprowadź **HDInsight: Połącz klaster**.

   ![polecenie klastra łącze](./media/apache-domain-joined-manage/link-cluster-command.png)

2. HDInsight wprowadź adres URL klastra -> wejście-Username > Wprowadź hasło -> Wybierz typ klastra -> on informacje o powodzeniu pokazuje jeśli przekazany weryfikacji.
   
   ![okno dialogowe klastra łącza](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]
   > Połączone nazwę użytkownika i hasło są używane, jeśli klaster w subskrypcji platformy Azure i połączone w klaster. 
   
3. Zobaczysz klastra połączonej za pomocą polecenia **klastra listy**. Możesz teraz przesłać skrypt do tego klastra połączonych.

   ![połączone klastra](./media/apache-domain-joined-manage/linked-cluster.png)

4. Wstawiając może zostać odłączyć klaster **HDInsight: odłączyć klaster** z palety poleceń.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą środowiska IntelliJ

Można połączyć normalny klaster przy użyciu nazwy użytkownika systemu Ambari zarządzane, również połączyć klaster hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com). 
1. Kliknij przycisk **połączenia klastra** z **Eksploratora usługi Azure**.

   ![menu kontekstowe klaster w linku](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Wprowadź **nazwa klastra**, **nazwa_użytkownika** i **hasło**. Należy sprawdzić nazwę użytkownika i hasło, jeśli masz niepowodzenie uwierzytelniania. Opcjonalnie dodaj konto magazynu, klucz magazynu, a następnie wybierz kontener z kontenera magazynu. Informacje dotyczące magazynu jest Eksplorator usługi storage w drzewie po lewej stronie
   
   ![okno dialogowe klastra łącza](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]
   > Używamy klucz połączonego magazynu, nazwę użytkownika i hasło, jeśli klaster w subskrypcji platformy Azure i połączone w klaster.
   > ![Eksplorator usługi Storage w programie IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Możesz zobaczyć połączone w klaster **HDInsight** węzła, jeśli dane wejściowe są odpowiednie. Możesz teraz przesłać aplikację do tego klastra połączonych.

   ![połączone klastra](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Możesz również można odłączyć elementy od klastra z **Eksploratora usługi Azure**.
   
   ![Odłączono klastra](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Łączenie z klastrem przyłączonym do domeny za pomocą programu Eclipse

Można połączyć normalny klaster przy użyciu nazwy użytkownika systemu Ambari zarządzane, również połączyć klaster hadoop zabezpieczeń przy użyciu nazwy użytkownika domeny (takich jak: user1@contoso.com).
1. Kliknij przycisk **połączenia klastra** z **Eksploratora usługi Azure**.

   ![menu kontekstowe klaster w linku](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Wprowadź **nazwy klastra**, **nazwa_użytkownika** i **hasło**, następnie kliknij przycisk OK w celu połączenia klastra. Opcjonalnie wprowadź klucz magazynu konta magazynu, a następnie wybierz kontener magazynu Eksplorator usługi storage do pracy w widoku drzewa po lewej stronie
   
   ![okno dialogowe klastra łącza](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]
   > Używamy klucz połączonego magazynu, nazwę użytkownika i hasło, jeśli klaster w subskrypcji platformy Azure i połączone w klaster.
   > ![Eksplorator usługi Storage w środowisku Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Możesz zobaczyć połączone w klaster **HDInsight** węzła po kliknięciu przycisku OK, jeśli dane wejściowe są odpowiednie. Możesz teraz przesłać aplikację do tego klastra połączonych.

   ![połączone klastra](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Możesz również można odłączyć elementy od klastra z **Eksploratora usługi Azure**.
   
   ![Odłączono klastra](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Dostęp do klastrów z pakietem Enterprise Security.

Pakiet Enterprise Security (dawniej HDInsight Premium) umożliwia wielu użytkownikom dostęp do klastra, w których uwierzytelnianie odbywa się przez usługi Active Directory i autoryzacji, struktury Apache Ranger i magazynu list kontroli dostępu (ACL usługi ADLS). Autoryzacja zapewnia bezpieczne granice przez wielu użytkowników i umożliwia tylko uprzywilejowanych użytkowników mają dostęp do danych na podstawie zasad autoryzacji.

Izolacji zabezpieczeń i użytkowników są ważne w przypadku klastra HDInsight z pakietem Enterprise Security. Aby spełnić te wymagania, dostęp SSH w klastrze z pakietem Enterprise Security jest zablokowany. W poniższej tabeli przedstawiono metody dostępu zalecane dla każdego typu klastra:

|Obciążenie|Scenariusz|Metoda dostępu|
|--------|--------|-------------|
|Hadoop|Gałąź — zadania/zapytań interakcyjnych |<ul><li>[Z usługi beeline](#beeline)</li><li>[Widok programu hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — usługa Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|platforma Spark|Zadania/zapytania interakcyjne, interakcyjnego PySpark|<ul><li>[Z usługi beeline](#beeline)</li><li>[Zeppelin przy użyciu usługi Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Widok programu hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — usługa Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|platforma Spark|Scenariusze aplikacji usługi Batch — Prześlij platformy Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Zapytania interakcyjnego (LLAP)|Interaktywne|<ul><li>[Z usługi beeline](#beeline)</li><li>[Widok programu hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC — usługa Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Dowolne|Instalowanie aplikacji niestandardowych|<ul><li>[Akcje skryptu](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]
   > Jupyter nie jest zainstalowana/obsługiwanych w pakiet Enterprise Security.

Przy użyciu standardowych interfejsów API ułatwia z punktu widzenia zabezpieczeń. Ponadto możesz uzyskać następujące korzyści:

1.  **Zarządzanie** — możesz zarządzać kodem i Automatyzuj zadania za pomocą standardowych interfejsów API — usługi Livy, HS2 itd.
2.  **Inspekcja** — przy użyciu protokołu SSH, nie ma możliwości inspekcji, które użytkownicy SSH miał do klastra. W takich sytuacjach przydałaby to mieć miejsce, gdy zadania są konstruowane przy użyciu standardowych punktów końcowych, zgodnie z może być wykonywane w kontekście użytkownika. 



### <a name="beeline"></a>Korzystanie z usługi Beeline 
Zainstaluj z usługi Beeline na swojej maszynie i łączenie za pośrednictwem publicznej sieci internet, użyj następujących parametrów: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Jeśli masz zainstalowane lokalnie z usługi Beeline i łączenie za pośrednictwem usługi Azure Virtual Network, należy użyć następujących parametrów: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Aby znaleźć nazwę FQDN węzła głównego, skorzystaj z informacji w zarządzanie HDInsight, za pomocą interfejsu API REST Ambari dokumentu.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Użytkownicy z klastrami HDInsight przyłączone do domeny
Klaster usługi HDInsight, który nie jest przyłączony do domeny ma dwa konta użytkownika, które są tworzone podczas tworzenia klastra:

* **Administrator systemu Ambari**: to konto jest także znana jako *użytkownika usługi Hadoop* lub *użytkownika HTTP*. To konto może służyć do logowania do systemu Ambari pod https://&lt;nazwa_klastra >. azurehdinsight.net. Jego można również uruchamiać zapytania dotyczące widoków Ambari, wykonywanie zadania za pomocą narzędzia zewnętrzne (na przykład programu PowerShell, Templeton, Visual Studio) i uwierzytelnianie za pomocą sterownika ODBC programu Hive i narzędzi do analizy Biznesowej (na przykład programu Excel usługi Power Bi i Tableau).

Klaster HDInsight przyłączone do domeny ma trzy nowi użytkownicy, oprócz administratora Ambari.

* **Administratora platformy ranger**: to konto jest lokalne konto administratora platformy Apache Ranger. Nie jest użytkownikiem domeny usługi active directory. To konto, można skonfigurować zasady i innych użytkowników, administratorów lub administratora delegowanego (tak, aby zasadami można zarządzać tymi użytkownikami). Domyślnie, nazwa użytkownika jest *administratora* , a hasło to takie same jak hasło administratora Ambari. Hasło może zostać zaktualizowana na stronie Ustawienia w platformy Ranger.
* **Użytkownik domeny administratora klastra**: to konto jest wyznaczony jako administratora klastra usługi Hadoop, w tym Ambari i Ranger użytkownika domeny usługi active directory. Podczas tworzenia klastra, musisz podać poświadczenia tego użytkownika. Ten użytkownik nie ma następujące uprawnienia:

  * Przyłączanie maszyn do domeny i umieścić je w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
  * Tworzenie jednostek usługi w jednostce Organizacyjnej, który jest określany podczas tworzenia klastra.
  * Utwórz wpisy odwrotnego systemu DNS.

    Należy zauważyć, że inni użytkownicy AD również mają te uprawnienia.

    Istnieje kilka punktów końcowych w ramach klastra (na przykład Templeton), które nie są zarządzane przez Ranger i dlatego nie są bezpieczne. Te punkty końcowe są zablokowane dla wszystkich użytkowników z wyjątkiem użytkownika domeny administratora klastra.
* **Regularne**: podczas tworzenia klastra, możesz podać wiele grup usługi active directory. Użytkownicy w tych grupach są synchronizowane z usługą platformy Ranger i Ambari. Ci użytkownicy są użytkownikami domeny i mieć dostęp do tylko zarządzane platformy Ranger punktów końcowych (na przykład serwera Hiveserver2). Wszystkie zasady RBAC i inspekcji będą mieć zastosowanie do tych użytkowników.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Role z klastrów HDInsight przyłączone do domeny
HDInsight przyłączone do domeny mają następujące role:

* Administrator klastra
* Operator klastra
* Administrator usługi
* Operator usługi
* Użytkownika klastra

**Aby wyświetlić uprawnienia te role**

1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij **role**.
3. Kliknij niebieski znak zapytania, aby wyświetlić uprawnienia:

    ![Przyłączone do domeny uprawnienia ról HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klastra usługi HDInsight. Zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Kliknij przycisk **pulpit nawigacyjny** z górnego menu, aby otworzyć narzędzie Ambari.
4. Zaloguj się przy użyciu nazwy użytkownika domeny administratora klastra i hasło Ambari.
5. Kliknij przycisk **administratora** menu rozwijane w górnym prawym rogu, a następnie kliknij **Zarządzanie Ambari**.

    ![HDInsight przyłączone do domeny zarządzania Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Interfejs użytkownika wygląda następująco:

    ![Zarządzanie HDInsight Ambari przyłączone do domeny interfejs użytkownika](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Utwórz listę użytkowników domeny, synchronizowane z usługi Active Directory
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij **użytkowników**. Zostanie wyświetlona wszystkich użytkowników, które są synchronizowane z usługi Active Directory do klastra HDInsight.

    ![Przyłączone do domeny systemu HDInsight Ambari zarządzania interfejsu użytkownika lista użytkowników](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Wyświetlanie listy grup domeny synchronizowane z usługi Active Directory
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij **grup**. Zobaczysz wszystkie grupy, które są synchronizowane z usługi Active Directory do klastra HDInsight.

    ![Przyłączone do domeny grup listy interfejsu użytkownika zarządzania HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Skonfiguruj uprawnienia widoki Hive
1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij **widoków**.
3. Kliknij przycisk **HIVE** Aby wyświetlić jego szczegóły.

    ![Przyłączone do domeny zarządzania HDInsight Ambari widoki Hive interfejsu użytkownika](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Kliknij przycisk **Hive View** link, aby skonfigurować widoki Hive.
5. Przewiń w dół do **uprawnienia** sekcji.

    ![Przyłączone do domeny zarządzania HDInsight Ambari widoki Hive interfejsu użytkownika konfigurowania uprawnień](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Kliknij przycisk **Dodaj użytkownika** lub **Dodaj grupę**, a następnie określ użytkowników lub grupy, które mogą używać widoki Hive.

## <a name="configure-users-for-the-roles"></a>Konfigurowanie użytkowników dla ról
 Aby wyświetlić listę ról i uprawnień, zobacz [klastry HDInsight przyłączone do ról domeny](#roles-of-domain---joined-hdinsight-clusters).

1. Otwórz przystawkę Zarządzanie Ambari interfejsu użytkownika.  Zobacz [Otwórz interfejs użytkownika zarządzania Ambari](#open-the-ambari-management-ui).
2. W menu po lewej stronie kliknij **role**.
3. Kliknij przycisk **Dodaj użytkownika** lub **Dodaj grupę** można przypisać użytkowników i grup dla różnych ról.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować przyłączony do domeny klaster usługi HDInsight, zobacz [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight).
* Aby znaleźć informacje na temat konfigurowania zasad Hive i uruchamiania kwerend Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów usługi HDInsight](apache-domain-joined-run-hive.md).
