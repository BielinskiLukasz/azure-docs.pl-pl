---
title: Interfejsy API i narzędzia dla deweloperów — Azure Batch | Microsoft Docs
description: Informacje na temat narzędzi i interfejsów API przeznaczonych do tworzenia rozwiązań korzystających z usługi Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 00d2a74946957f690979eec1d3a03a9b766299d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252326"
---
# <a name="overview-of-batch-apis-and-tools"></a>Omówienie narzędzi i interfejsów API usługi Batch

Przetwarzanie obciążeń równoległych przy użyciu usługi Azure Batch jest zazwyczaj wykonywane programowo przy użyciu jednego z interfejsów API usługi Batch. Aplikacja lub usługa kliencka, której autorem jest użytkownik, może używać interfejsów API usługi Batch do komunikacji z usługą Batch. Za pomocą interfejsów API usługi Batch można tworzyć pule węzłów obliczeniowych (maszyny wirtualne lub usługi w chmurze) i zarządzać nimi. Następnie można zaplanować wykonywanie zadań i podzadań w ramach tych węzłów. 

Można skutecznie przetwarzać duże obciążenia na potrzeby swojej organizacji lub zapewniać swoim klientom fronton usług, aby umożliwić im uruchamianie zadań i podzadań — na żądanie lub zgodnie z harmonogramem — w jednym węźle albo setkach lub nawet tysiącach węzłów. Usługę Azure Batch można także używać w ramach większego przepływu pracy zarządzanego za pomocą takich narzędzi, jak usługa [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Jeśli zechcesz dokładniej zapoznać się z interfejsem API usługi Batch i uzyskać bardziej szczegółowe informacje dotyczące funkcji, które on zapewnia, przeczytaj artykuł [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Konta platformy Azure na potrzeby programowania w usłudze Batch
Podczas opracowywania rozwiązań usługi Batch potrzebne są następujące konta w ramach subskrypcji platformy Azure:

* **Konto usługi Batch** — zasoby usługi Batch, w tym pule, węzły obliczeniowe, zadania i podzadania są skojarzone z [kontem usługi Azure Batch](batch-api-basics.md#account). Gdy aplikacja wykonuje żądanie względem usługi Batch, usługa ta uwierzytelnia żądanie przy użyciu nazwy konta usługi Batch, adresu URL konta oraz klucza dostępu albo tokenu usługi Azure Active Directory. [Konto usługi Batch możesz utworzyć](batch-account-create-portal.md) w witrynie Azure Portal lub programowo.
* **Konto magazynu** — usługa Batch obejmuje wbudowaną obsługę pracy z plikami w [usłudze Azure Storage][azure_storage]. Usługa Azure Blob Storage jest używana prawie w każdym scenariuszu usługi Batch — do przemieszczania programów uruchamianych przez podzadania oraz danych, które one przetwarzają, oraz do przechowywania danych wyjściowych, które generują. Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>Interfejsy API usługi Batch

Aplikacje i usługi mogą wykonywać bezpośrednie wywołania interfejsu API REST lub użyć co najmniej jednej z następujących bibliotek klienckich do uruchamiania obciążeń usługi Azure Batch i zarządzania nimi.

| Interfejs API | Dokumentacja interfejsu API | Pobieranie | Samouczek | Przykłady kodu | Więcej informacji |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N/D |- |- | [Obsługiwane wersje](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[Samouczek](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Informacje o wersji](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Samouczek](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Plik Readme](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Samouczek](batch-nodejs-get-started.md) |- | [Plik Readme](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Plik Readme][api_sample_java] | [Plik Readme](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Interfejsy API usługi Batch Management

Interfejsy API usługi Azure Resource Manager dla usługi Batch zapewniają dostęp programowy do kont usługi Batch. Za pomocą tych interfejsów API możesz programowo zarządzać kontami usługi Batch, limitami przydziału, pakietami aplikacji i innymi zasobami za pośrednictwem dostawcy Microsoft.Batch.  

| Interfejs API | Dokumentacja interfejsu API | Pobieranie | Samouczek | Przykłady kodu |
| --- | --- | --- | --- | --- |
| **Batch Management REST (Interfejs REST zarządzania usługą Batch)** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [Samouczek](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Zarządzanie usługą Batch za pomocą języka Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Zarządzanie usługą Batch za pomocą oprogramowania Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Zarządzanie usługą Batch za pomocą języka Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Narzędzia wiersza polecenia usługi Batch

Te narzędzia wiersza polecenia zawierają te same funkcje co interfejsy API usług Batch i Batch Management: 

* [Polecenia cmdlet programu PowerShell][batch_ps]w usłudze Batch: polecenia cmdlet Azure Batch w module [Azure PowerShell](/powershell/azure/overview) umożliwiają zarządzanie zasobami wsadowymi przy użyciu programu PowerShell.
* [Interfejs wiersza polecenia platformy Azure](/cli/azure): interfejs wiersza polecenia platformy Azure jest działającym na wielu platformach zestawem narzędzi, który udostępnia polecenia powłoki do interakcji z wieloma usługami platformy Azure, w tym usługi Batch i usługi Batch Management. Zobacz [Zarządzanie zasobami usługi Batch przy użyciu interfejsu wiersza polecenia platformy Azure](batch-cli-get-started.md), aby uzyskać więcej informacji o korzystaniu z interfejsu wiersza polecenia platformy Azure z usługą Batch.

## <a name="other-tools-for-application-development"></a>Inne narzędzia do opracowywania aplikacji

Oto niektóre dodatkowe narzędzia, które mogą być przydatne do budowania i debugowania aplikacji i usług tworzonych za pomocą usługi Batch:

* [Azure Portal][portal]: w Azure Portal można tworzyć, monitorować i usuwać pule, zadania i zadania w usłudze Batch. Można wyświetlić informacje o stanie dla tych i innych zasobów podczas uruchamiania zadań, a nawet pobierać pliki z węzłów obliczeniowych w pulach. Na przykład podczas rozwiązywania problemów można pobrać plik `stderr.txt` zadania zakończonego niepowodzeniem. Można również pobrać pliki Remote Desktop (RDP) umożliwiające logowanie się do węzłów obliczeniowych.
* [Azure Batch Explorer][batch_labs]: Batch Explorer (dawniej BatchLabs) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające w tworzeniu, debugowaniu i monitorowaniu aplikacji Azure Batch. Pobierz [pakiet instalacyjny](https://azure.github.io/BatchExplorer/) dla komputerów Mac lub systemu Linux albo Windows.
* [Azure Batch stocznie](https://github.com/Azure/batch-shipyard): stoczniowe narzędzie do obsługi inicjowania, wykonywania i monitorowania przetwarzania wsadowego opartego na kontenerach i obciążeń HPC na Azure Batch.
* [Eksplorator usługi Azure Storage][storage_explorer]: Chociaż nie jest to dokładnie narzędzie Azure Batch, Eksplorator usługi Storage jest innym przydatnym narzędziem, które trzeba uzyskać podczas tworzenia i debugowania rozwiązań wsadowych.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby dowiedzieć się więcej o rejestrowaniu zdarzeń z aplikacji usługi Batch, zobacz [Log events for diagnostic evaluation and monitoring of Batch solutions](batch-diagnostics.md) (Rejestrowanie zdarzeń na potrzeby ewaluacji diagnostycznej i monitorowania rozwiązań usługi Batch). Aby zapoznać się z dokumentacją zdarzeń zgłaszanych przez usługę Batch, zobacz [Analiza danych usługi Batch](batch-analytics.md).
- Aby uzyskać informacje o zmiennych środowiskowych dla węzłów obliczeniowych, zobacz [Zmienne środowiskowe węzła obliczeniowego usługi Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Następne kroki

* Przeczytaj artykuł [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów) zawierający informacje kluczowe dla wszystkich osób przygotowujących się do korzystania z usługi Batch. Ten artykuł zawiera bardziej szczegółowe informacje o zasobach usługi Batch, takich jak pule, węzły i zadania oraz wielu funkcjach API, których można używać podczas kompilowania aplikacji usługi Batch.
* Przeczytaj artykuł [Get started with the Azure Batch library for .NET](tutorial-parallel-dotnet.md) (Wprowadzenie do biblioteki usługi Azure Batch dla platformy .NET), aby dowiedzieć się, jak używać języka C# i biblioteki usługi Batch dla środowiska .NET w celu wykonania prostego obciążenia przy użyciu popularnego przepływu pracy usługi Batch. Dostępna jest także wersja samouczka dla [języka Python](tutorial-parallel-python.md) i [oprogramowania Node.js](batch-nodejs-get-started.md).
* Pobierz [przykłady kodu w usłudze GitHub][github_samples] , aby zobaczyć, C# jak interfejs i język Python mogą być w trybie wsadowym z usługą Batch w celu planowania i przetwarzania przykładowych obciążeń.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
