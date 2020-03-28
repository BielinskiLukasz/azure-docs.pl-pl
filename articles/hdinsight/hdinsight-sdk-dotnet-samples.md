---
title: 'Usługa Azure HDInsight: przykłady platformy .NET'
description: Znajdź przykłady języka C# .NET w usłudze GitHub dla typowych zadań przy użyciu sdk HDInsight dla platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 097323b4566daeda737428121bee4d64e9f7248b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74951568"
---
# <a name="azure-hdinsight-net-samples"></a>Usługa Azure HDInsight: przykłady platformy .NET

> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](hdinsight-sdk-dotnet-samples.md)
> * [Przykłady Pythona](hdinsight-sdk-python-samples.md)
> * [Przykłady kodu Java](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ten artykuł zawiera:

* Łącza do przykładów zadań tworzenia klastra.
* Łącza do zawartości referencyjnej dla innych zadań zarządzania.

Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>Wymagania wstępne

[Zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Zarządzanie klastrem — tworzenie

* [Tworzenie klastra platformy Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Tworzenie klastra Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Tworzenie klastra platformy Spark za pomocą usługi Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Tworzenie klastra platformy Spark za pomocą pakietu zabezpieczeń przedsiębiorstwa (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Te przykłady można uzyskać dla platformy .NET, klonując repozytorium GitHub [hdinsight-dotnet-sdk-samples.](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples)

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Fragmenty kodu dla tej dodatkowej funkcji SDK można znaleźć w [sdk HDInsight dla dokumentacji referencyjnej .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
