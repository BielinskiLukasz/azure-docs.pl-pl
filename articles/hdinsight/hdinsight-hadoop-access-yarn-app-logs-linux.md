---
title: Dostęp do usługi Apache Hadoop YARN application loguje HDInsight opartych na systemie Linux — Azure
description: Dowiedz się, jak dostęp do dzienników aplikacji usługi YARN w klastrze HDInsight opartych na systemie Linux (Apache Hadoop) przy użyciu wiersza polecenia i przeglądarki sieci web.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 302f2f96a7f17699411ab9fdbdb6ab1f9de149c8
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277603"
---
# <a name="access-apache-yarn-application-logs-on-linux-based-hdinsight"></a>Dzienniki aplikacji Apache YARN dostępu na HDInsight opartych na systemie Linux

Dowiedz się, jak dostęp do dzienników aplikacji usługi Apache YARN (jeszcze inny zasób moduł negocjowania) w klastrze Apache Hadoop w usłudze Azure HDInsight.

> [!IMPORTANT]
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym na HDInsight w wersji 3.6 lub nowszej. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składnika HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) zawiera ogólne informacje dotyczące ukończonych aplikacji

YARN Timeline Server zawiera następujący typ danych:

* Identyfikator aplikacji, unikatowy identyfikator aplikacji
* Użytkownik, który uruchomił aplikację
* Informacje o podjętych w celu wykonania aplikacji
* Kontenery posługują się wszelkie próby danej aplikacji

## <a name="YARNAppsAndLogs"></a>Dzienniki aplikacji usługi YARN i

YARN obsługuje wiele modeli programowania (MapReduce jest jeden z nich) dzięki rozdzieleniu zarządzania zasobami z planowania/monitorowania aplikacji. YARN używa globalną *ResourceManager* (MB), każdy proces roboczy — węzeł *NodeManagers* (NMs) i poszczególnych aplikacji *ApplicationMasters* (AMs). AM poszczególnych aplikacji negocjuje zasobów (procesor CPU, pamięci, dysku, użycia sieci) do uruchamiania Twojej aplikacji za pomocą Menedżera zasobów. Menedżer zasobów działa z NMs, aby udzielić tych zasobów, które są przyznawane jako *kontenery*. AM jest odpowiedzialny za monitorowanie postępu kontenerów do niej przypisany, Menedżera zasobów. Aplikacja może wymagać wiele kontenerów, w zależności od rodzaju aplikacji.

Każda aplikacja może składać się z wielu *prób aplikacji*. Aplikacja zakończy się niepowodzeniem, może ponowiony po wybraniu nowej próby. Każda próba jest uruchamiany w kontenerze. W tym sensie kontener udostępnia kontekst dla podstawowa jednostka pracy wykonanej przez aplikacji usługi YARN. Cała praca, którą można wykonać tylko w kontekście kontenera odbywa się w węźle pojedynczego procesu roboczego, na którym została przydzielona kontenera. Zobacz [pojęcia usługi YARN] [ YARN-concepts] dalsze odwołania.

Dzienniki aplikacji (i dzienniki skojarzony kontener) są krytyczne w debugowaniu aplikacji platformy Hadoop innych problematyczne. YARN umożliwia nieuprzywilejowany umożliwiająca zbieranie, agregując i przechowywania dzienników aplikacji za pomocą [agregacji dziennika] [ log-aggregation] funkcji. Funkcja agregacji dziennika umożliwia uzyskiwanie dostępu do dzienników aplikacji bardziej. Ona agreguje dzienników dla wszystkich kontenerów na węzeł procesu roboczego i przechowuje je w postaci jednego pliku dziennika zagregowane na węzeł procesu roboczego. Dziennik jest przechowywany na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienników dla wszystkich kontenerów, uruchom w węźle pojedynczego procesu roboczego zawsze są agregowane do pojedynczego pliku. Dlatego jest tylko 1 dziennika na węzeł procesu roboczego używanych przez aplikację. Agregacja dziennik jest domyślnie włączona, klastrów HDInsight w wersji 3.0 lub nowszej. Zagregowane Dzienniki znajdują się w domyślny magazyn dla klastra. Następująca ścieżka jest ścieżka systemu plików HDFS w dziennikach:

    /app-logs/<user>/logs/<applicationId>

W ścieżce `user` to nazwa użytkownika, który uruchomił aplikację. `applicationId` To unikatowy identyfikator przypisany do aplikacji przez Menedżera zasobów usługi YARN.

Zagregowane dzienniki nie są bezpośrednio do odczytu, ponieważ są one zapisywane [TFile][T-file], [format binarny] [ binary-format] indeksowane przez kontener. Użyj dzienników YARN ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

## <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia usługi YARN

Do korzystania z narzędzi interfejsu wiersza polecenia usługi YARN, musi najpierw połączyć się z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Te dzienniki można wyświetlić jako zwykły tekst, uruchamiając jedno z następujących poleceń:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Określ &lt;applicationId >, &lt;— który pracy — aplikacji użytkownika >, &lt;containerId >, a &lt;adres w przypadku węzła procesu roboczego > informacje po uruchomieniu tych poleceń.

## <a name="yarn-resourcemanager-ui"></a>Interfejsie użytkownika YARN ResourceManager

Interfejsie użytkownika YARN ResourceManager działa na głównym węzłem klastra. Jest on dostępny za pośrednictwem interfejsu użytkownika sieci web Ambari. Aby wyświetlić dzienniki usługi YARN, wykonaj następujące kroki:

1. W przeglądarce internetowej przejdź do https://CLUSTERNAME.azurehdinsight.net. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight.
2. Z listy usług po lewej stronie wybierz **YARN**.

    ![Wybranej usługi yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Z **szybkich łączy** listy rozwijanej wybierz jedną z głównymi węzłami klastra, a następnie wybierz pozycję **dziennika ResourceManager**.

    ![Szybkie linki yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Zostanie wyświetlona lista linków do dzienniki usługi YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
