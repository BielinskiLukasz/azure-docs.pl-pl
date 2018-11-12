---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacja i korzyści
description: Dowiedz się motywacją i korzyści w przypadku migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 82792414da9df32524e48031089dc18d2dc521cc
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288122"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacja i korzyści

Ten artykuł jest pierwszy w serii na najlepsze rozwiązania dotyczące migracji w środowisku lokalnym wdrożeń ekosystemu Apache Hadoop do usługi Azure HDInsight. Ta seria artykułów jest dla osób, które odpowiadają za projektowanie, wdrażanie i migracja rozwiązania Apache Hadoop w usłudze Azure HDInsight. Role, które mogą korzystać z tych artykułów obejmują architektów chmury, Administratorzy usługi Hadoop i inżynierom DevOps. Deweloperzy oprogramowania, inżynierami danych i analitykom danych powinien również korzystać z wyjaśnienie sposobu różnych typów klastrów pracy w chmurze.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Dlaczego do migracji do usługi Azure HDInsight

Usługa Azure HDInsight jest dystrybucją w chmurze składniki platformy Hadoop z [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Usługa Azure HDInsight ułatwia i przyspiesza przetwarzanie ogromnych ilości danych przy jednoczesnej minimalizacji kosztów. HDInsight obejmuje najbardziej popularnych struktur open source, takie jak:

- Apache Hadoop
- Apache Spark
- Apache Hive z opcją LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Korzyści oferowanych przez usługi Azure HDInsight za pośrednictwem lokalnej usługi Hadoop

- **Niski koszt** — można zmniejszyć koszty przez [tworząc klastry na żądanie](../hdinsight-hadoop-create-linux-clusters-adf.md) i płacąc wyłącznie za rzeczywiste użycie. Oddzielenie obliczeń i magazynu zapewnia elastyczność, przechowując ilość danych niezależnie od rozmiaru klastra.

- **Zautomatyzowane tworzenie klastra** — zautomatyzowane tworzenie klastra wymaga minimalnej instalacji i konfiguracji. Automatyzacja może służyć do klastrów na żądanie.

- **Zarządzane sprzętu i konfiguracji** — nie trzeba już martwić się o sprzętu fizycznego lub infrastruktury z klastrem usługi HDInsight. Wystarczy określić konfigurację klastra, a Azure ustawia go.

- **Łatwe skalowanie** — HDInsight umożliwia [skalowania](../hdinsight-administer-use-portal-linux.md) obciążeń w górę lub w dół. Platforma Azure zajmie się rozpowszechnianie danych i obciążenia ponowne równoważenie bez przerywania zadań przetwarzania danych.

- **Globalna dostępność** — HDInsight jest dostępna w większej liczbie [regionów](https://azure.microsoft.com/regions/services/) niż inne oferty do analizy danych big data. Usługa Azure HDInsight jest również dostępna w ramach chmur Azure Government, Azure (Chiny) i Azure (Niemcy), dzięki czemu odpowiada na potrzeby Twojego przedsiębiorstwa w najważniejszych obszarach suwerenności.

- **Bezpieczeństwo i zgodność** — HDInsight umożliwia ochronę zasobów danych przedsiębiorstwa przy użyciu [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [szyfrowania](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)oraz integracji z usługą [usługi Azure Active Katalog](../domain-joined/apache-domain-joined-introduction.md). HDInsight spełnia również najpopularniejsze branżowe i rządowe [standardami](https://azure.microsoft.com/overview/trusted-cloud).

- **Uproszczone zarządzanie wersjami** — Azure HDInsight zarządza wersja składników ekosystemu Hadoop, a jednocześnie dbając o ich aktualności. Aktualizacje oprogramowania są zwykle złożony proces w przypadku wdrożeń lokalnych.

- **Mniejszych klastrów zoptymalizowanych pod kątem konkretnych obciążeń na mniejszej liczbie zależności między składnikami** -konfigurację usługi Hadoop typowe lokalnych korzysta z jednego klastra, która służy do wielu celów. Za pomocą usługi Azure HDInsight można utworzyć klastry specyficznego dla obciążenia. Tworzenie klastrów dla konkretnych obciążeń eliminuje złożoność obsługi pojedynczy klaster z wzrostem złożoności.

- **Produktywność** — można użyć różnych narzędzi dla usług Hadoop i Spark w preferowanym środowisku programistycznym.

- **Rozszerzalność przy użyciu niestandardowego narzędzia lub aplikacje innych producentów** -klastrów HDInsight można rozszerzyć za pomocą zainstalowanych składników i mogą również być zintegrowane z innymi rozwiązaniami danych big data przy użyciu [jednym kliknięciem](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  wdrożenia z poziomu portalu Azure Marketplace.

- **Łatwe zarządzanie, administrowanie oraz monitorowanie** — Azure HDInsight integruje się z [usługi Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , zapewniając pojedynczy interfejs, za pomocą którego można monitorować wszystkie swoje klastry.

- **Integracja z innymi usługami Azure** — HDInsight można łatwo zintegrować z innymi popularnymi usługami platformy Azure, takich jak następujące:

    - Usługi Azure Data Factory (ADF)
    - Azure Blob Storage
    - Usługa Azure Data Lake Storage 2. generacji
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Samonaprawiania procesów i składniki** — HDInsight nieustannie analizuje składników infrastruktury i typu open source, za pomocą własnej infrastruktury monitorowania. Również automatycznie odzyskuje błędy krytyczne, takie jak niedostępność składników typu open source i węzłów. Alerty są wyzwalane w Ambari, jeśli dowolny składnik OSS nie powiodło się.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest Azure HDInsight oraz stosu technologii Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces planowania migracji

Poniższe kroki są zalecane w przypadku planowania migracji lokalnych klastrów Hadoop do usługi Azure HDInsight:

1. Dowiedz się, bieżącego wdrożenia w środowisku lokalnym i topologii.
2. Dowiedz się, bieżącego zakresu projektu, osie czasu i doświadczenia zespołu.
3. Zapoznaj się z wymaganiami platformy Azure.
4. Do skompilowania szczegółowego planu, w oparciu o najlepsze rozwiązania.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Zbieranie szczegółów, aby przygotować się do migracji

Ta sekcja zawiera kwestionariuszy szablonów ułatwiających zbieranie ważne informacje dotyczące:

- Wdrażanie w środowisku lokalnym
- Szczegóły projektu
- Wymagania systemu Azure

### <a name="on-premises-deployment-questionnaire"></a>Kwestionariusz wdrożenia w środowisku lokalnym

| **Pytanie** | **Przykład** | **Odpowiedź** |
|---|---|---|
|**Temat**: **środowiska**|||
|Typ dystrybucji klastra|Hortonworks, Cloudera, MapR| |
|Wersja dystrybucji klastra|HDP 2.6.5, 5.7 PROGRAMU CDH|
|Dużych składników ekosystemu danych|Systemu plików HDFS, Yarn, Hive, LLAP, Impala, Kudu, bazy danych HBase, Spark, MapReduce, Kafka, dozorcy, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Typy klastrów|Solr jednowarstwową platformy Kafka, Storm, Hadoop, Spark,|
|Liczba klastrów|4|
|Liczba węzłów wzorca|2|
|Liczba węzłów procesu roboczego|100|
|Liczba węzłów brzegowych| 5|
|Całkowita ilość miejsca na dysku|100 TB|
|Konfiguracja węzła głównego|m/y, procesora cpu, dysku itp.|
|Konfiguracja węzły danych|m/y, procesora cpu, dysku itp.|
|Konfiguracja węzłów brzegowych|m/y, procesora cpu, dysku itp.|
|Szyfrowanie systemu plików HDFS?|Yes|
|Wysoka dostępność|Wysokiej dostępności z systemu plików HDFS i magazynu metadanych wysokiej dostępności|
|Odzyskiwanie po awarii / tworzenie kopii zapasowej|Klaster kopii zapasowej?|  
|Systemy, które są zależne od klastra|SQL Server, Teradata, usługa Power BI, bazy danych MongoDB|
|Integracje innych firm|Firmy TABLEAU GridGain Qubole, Informatica, Splunk|
|**Temat**: **zabezpieczeń**|||
|Zabezpieczenia brzegowe|Zapory|
|Klaster uwierzytelnianie i autoryzacja|Active Directory, Ambari, Cloudera Manager bez uwierzytelniania|
|Kontrola dostępu do systemu plików HDFS|  Ręczne, ssh użytkowników|
|Hive, uwierzytelnianie i autoryzacja|Sentry, LDAP, AD przy użyciu protokołu Kerberos, platformy Ranger|
|Inspekcja|Ambari, Nawigator Cloudera platformy Ranger|
|Monitorowanie|Grafitu, zebrane, statsd, Telegraf, InfluxDB|
|Generowanie alertów|Kapacitor, Prometheus, pomocą usługi Datadog|
|Czas przechowywania danych| 3 lata, 5 lat|
|Administratorzy klastra|Wielu administratorów jednego administratora|

### <a name="project-details-questionnaire"></a>Kwestionariusz szczegóły projektu

|**Pytanie**|**Przykład**|**Odpowiedź**|
|---|---|---|
|**Temat**: **obciążeń i częstotliwości**|||
|Zadania MapReduce|10 zadań — dwa razy dziennie||
|Zadania hive|100 zadań — co godzinę||
|Zadania wsadowe platformy Spark|50 zadań — co 15 minut||
|Zadania przesyłania strumieniowego platformy Spark|5 zadań — co 3 minuty||
|Strukturalne zadania przesyłania strumieniowego|5 zadań — co minutę||
|Zadania szkolenia modelu usługi uczenie Maszynowe|2 zadania — raz w tygodniu||
|Języki programowania|Python i Scala, Java||
|Skrypty|Powłoka, języka Python||
|**Temat**: **danych**|||
|Źródła danych|Pliki proste, Json, Kafka, RDBMS||
|Organizowanie danych|Przepływy pracy programu Oozie, powietrza||
|W wyszukiwaniach pamięci|Apache Ignite, redis Cache||
|Miejsca docelowe danych|System plików HDFS, RDBMS, Kafka, MPP ||
|**Temat**: **metadanych**|||
|Typ bazy danych programu hive|MySQL, Postgres||
|Nie. z magazyny metadanych Hive|2||
|Nie. z tabel programu Hive|100||
|Nie. zasady platformy Ranger|20||
|Nie. przepływów pracy programu Oozie|100||
|**Temat**: **skalowania**|||
|Ilość danych, w tym replikacji|100 TB||
|Dzienna ilość pozyskiwania|50 GB||
|Szybkość wzrostu ilości danych|10% rocznie||
|Tempo wzrostu węzłów klastra|5% rocznie
|**Temat**: **klastra wykorzystania**|||
|Średni procent użycia procesora CPU używane|60%||
|Średni procent pamięci używane|75%||
|Używane miejsce na dysku|75%||
|Średni procent sieci używane|25%
|**Temat**: **pracowników**|||
|Nie. administratorów|2||
|Nie. deweloperów|10||
|Nie. użytkownikom końcowym|100||
|Umiejętności|Hadoop, Spark||
|Nie. zasoby dostępne dla wysiłków migracji|2||
|**Temat**: **ograniczenia**|||
|Bieżące ograniczenia|Opóźnienie jest duże||
|Bieżące problemy.|Problem współbieżności||

### <a name="azure-requirements-questionnaire"></a>Wymagania dotyczące usługi Azure kwestionariusz

|**Temat**: **infrastruktury** |||
|---|---|---|
|**Pytanie**|**Przykład**|**Odpowiedź**|
| Preferowany Region|Wschodnie stany USA||
|Preferowanych sieci wirtualnej?|Yes||
|Wysoka dostępność / odzyskiwanie po awarii potrzebne?|Yes||
|Integracja z innymi usługami w chmurze?|ADF, bazy danych cosmos DB||
|**Temat**: **przenoszenia danych**  |||
|Preferencji ładowania początkowego|Narzędzia DistCp, Data box, ADF, platforma WANDisco||
|Przyrost transferu danych|Narzędzia DistCp, narzędzia AzCopy||
|Trwającą dane przyrostowe transferu|Narzędzia DistCp, Sqoop||
|**Temat**: **monitorowanie i alerty** |||
|Za pomocą monitorowania platformy Azure i integracja programu Vs alertów monitorowania innych firm|Korzystanie z systemu Azure, monitorowanie i alerty||
|**Temat**: **preferencji zabezpieczeń** |||
|Potok danych prywatnych i chronionych?|Yes||
|Klastra dołączono do domeny (ESP)?|     Yes||
|W środowisku lokalnym AD Sync do chmury?|     Yes||
|Nie. użytkowników usługi AD do synchronizacji?|          100||
|OK, aby zsynchronizować hasła do chmury?|    Yes||
|Użytkownicy tylko w chmurze?|                 Yes||
|Wymagane uwierzytelnianie wieloskładnikowe?|                       Nie|| 
|Wymagania dotyczące autoryzacji danych?|  Yes||
|Kontrola dostępu oparta na rolach?|        Yes||
|Inspekcja potrzebne?|                  Yes||
|Szyfrowanie danych w spoczynku?|          Yes||
|Szyfrowanie danych podczas przesyłania?|       Yes||
|**Temat**: **preferencje Re architektury** |||
|Pojedynczy klaster, a określone typy klastrów|Określone typy klastrów||
|Wspólnie przechowywane Vs zdalnego magazynu?|Magazyn zdalny||
|Mniejszy rozmiar klastra jako dane są przechowywane zdalnie?|Mniejszy rozmiar klastra||
|Korzystać z wielu mniejszych klastrów, a nie pojedynczego dużego klastra?|Użyj wielu mniejszych klastrów||
|Użyj zdalnego magazynu metadanych?|Yes||
|Udostępnianie magazyny metadanych między różnymi klastrami?|Yes||
|Dekonstruować obciążenia?|Zastąp zadań Hive za pomocą zadań platformy Spark||
|Na użytek ADF organizowanie danych?|Nie||
|HDInsight vs Hortonworks Data Platform w usłudze IaaS?|HDInsight||

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Najlepsze rozwiązania architektury dla lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
