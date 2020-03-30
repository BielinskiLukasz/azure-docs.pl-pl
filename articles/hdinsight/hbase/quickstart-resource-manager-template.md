---
title: 'Szybki start: tworzenie klastra Apache HBase przy użyciu szablonu — Usługa Azure HDInsight'
description: Ten przewodnik Szybki start pokazuje, jak utworzyć klaster Apache HBase w usłudze Azure HDInsight za pomocą szablonu Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 609580fb57e5c11741332c437172532853e73917
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79485776"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Szybki start: tworzenie klastra Apache HBase w usłudze Azure HDInsight przy użyciu szablonu Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu usługi Azure Resource Manager do utworzenia klastra [Apache HBase](./apache-hbase-overview.md) w usłudze Azure HDInsight. HBase jest open-source, NoSQL bazy danych, która jest zbudowana na Apache Hadoop i wzorowany na [Google BigTable](https://cloud.google.com/bigtable/).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-apache-hbase-cluster"></a>Tworzenie klastra Apache HBase

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-hbase-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json" range="1-145":::


Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft.Storage/storageKonta:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)tworzenie konta usługi Azure Storage.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): tworzenie klastra HDInsight.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdrażanie na platformie Azure** poniżej, aby zalogować się na platformę Azure i otworzyć szablon Menedżera zasobów.

    [![Wdrażanie na platformie Azure](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona lokalizacją używaną dla grupy zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. W tym szablonie należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika, domyślnie jest **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką literę i jedną wielką literę, jedną znak niealtalumeryczny (z wyjątkiem znaków " " ). |
    |Nazwa użytkownika Ssh|Podaj nazwę użytkownika, domyślnie jest sshuser|
    |Hasło Ssh|Podaj hasło.|

    ![Szablon Menedżera zasobów HBase](./media/quickstart-resource-manager-template/resource-manager-template-hbase.png)

1. Zapoznaj się z **regulaminem**. Następnie wybierz **zgadzam się z warunkami określonymi powyżej,** a następnie **zakup.** Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po utworzeniu klastra otrzymasz powiadomienie **o pomyślnym wdrożeniu** z łączem **Przejdź do zasobu.** Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma konto [usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub zależność konta usługi Azure Data Lake [Storage.](../hdinsight-hadoop-use-data-lake-store.md) Jest on określany jako domyślne konto magazynu. Klaster USŁUGI HDInsight i jego domyślne konto magazynu muszą być współlokowane w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

W witrynie Azure Portal przejdź do klastra i wybierz pozycję **Usuń**.

![Szablon Menedżera zasobów HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Apache HBase w usłudze HDInsight przy użyciu szablonu Menedżera zasobów. W następnym artykule dowiesz się, jak zbadać HBase w programie HDInsight za pomocą powłoki HBase.

> [!div class="nextstepaction"]
> [Zapytanie Apache HBase w usłudze Azure HDInsight z powłoką HBase](./query-hbase-with-hbase-shell.md)
