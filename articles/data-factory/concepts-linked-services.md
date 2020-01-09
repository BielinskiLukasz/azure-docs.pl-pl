---
title: Połączone usługi w Azure Data Factory
description: Dowiedz się więcej o połączonych usługach w Data Factory. Połączone usługi łączą magazyny obliczeniowe z danymi do fabryki danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 90e51e8b56bd3fb63d56c630d47770e97f439796
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563546"
---
# <a name="linked-services-in-azure-data-factory"></a>Połączone usługi w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-linked-services.md)

W tym artykule opisano, jakie są połączone usługi, jak są zdefiniowane w formacie JSON i jak są używane w potokach Azure Data Factory.

Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](introduction.md) przegląd.

## <a name="overview"></a>Przegląd
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Można na przykład użyć działania kopiowania w celu skopiowania danych z SQL Server lokalnego do usługi Azure Blob Storage. Następnie możesz użyć działania programu Hive, które uruchamia skrypt programu Hive w klastrze usługi Azure HDInsight, aby przetwarzać dane z magazynu obiektów BLOB w celu utworzenia danych wyjściowych. Na koniec możesz użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do Azure SQL Data Warehouse, na podstawie których są tworzone rozwiązania do raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania](concepts-pipelines-activities.md) w Azure Data Factory.

Teraz **zestaw** danych to nazwany widok zawierający dane, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe.

Przed utworzeniem zestawu danych należy utworzyć **połączoną usługę** , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zastanów się to w ten sposób; DataSet reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, które zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do bazy danych SQL, należy utworzyć dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: DataSet usługi Azure BLOB (który odwołuje się do połączonej usługi Azure Storage) i zestawu danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Azure Storage i Azure SQL Database zawierają parametry połączenia, które Data Factory używane w środowisku uruchomieniowym do nawiązywania połączenia z usługą Azure Storage i Azure SQL Database. Zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB. Zestaw danych tabeli SQL Azure określa tabelę SQL w bazie danych SQL, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>KOD JSON połączonej usługi
Połączona usługa w Data Factory jest definiowana w formacie JSON w następujący sposób:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa połączonej usługi. Zobacz [reguły nazewnictwa Azure Data Factory](naming-rules.md). |  Tak |
type | Typ połączonej usługi. Na przykład: AzureStorage (magazyn danych) lub AzureBatch (COMPUTE). Zobacz opis typeProperties. | Tak |
typeProperties | Właściwości typu są różne dla poszczególnych magazynów danych lub obliczeń. <br/><br/> W przypadku obsługiwanych typów magazynów danych i ich właściwości typów zobacz tabelę [typów zestawu danych](concepts-datasets-linked-services.md#dataset-type) w tym artykule. Przejdź do artykułu łącznika magazynu danych, aby dowiedzieć się więcej o właściwościach typu specyficznych dla magazynu danych. <br/><br/> W przypadku obsługiwanych typów obliczeniowych i ich właściwości typu zobacz [połączone usługi obliczeniowe](compute-linked-services.md). | Tak |
connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie

## <a name="linked-service-example"></a>Przykład połączonej usługi
Następująca połączona usługa to połączona usługa Azure Storage. Należy zauważyć, że typ jest ustawiony na AzureStorage. Właściwości typu połączonej usługi Azure Storage obejmują parametry połączenia. Usługa Data Factory używa tych parametrów połączenia do nawiązywania połączenia z magazynem danych w czasie wykonywania.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Tworzenie połączonych usług

Połączone usługi można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [interfejsów API platformy .NET](quickstart-create-data-factory-dot-net.md), [programu PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager szablonu i Azure Portal

## <a name="data-store-linked-services"></a>Połączone usługi magazynu danych
Listę magazynów danych obsługiwanych przez Data Factory można znaleźć w artykule [Omówienie łączników](copy-activity-overview.md#supported-data-stores-and-formats) . Kliknij magazyn danych, aby poznać obsługiwane właściwości połączenia.

## <a name="compute-linked-services"></a>Usługi połączone usługi Compute
[Środowiska obliczeniowe](compute-linked-services.md) referencyjne obsługują szczegółowe informacje o różnych środowiskach obliczeniowych, z którymi można nawiązać połączenie z fabryki danych oraz w różnych konfiguracjach.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawu SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu Azure Portal](quickstart-create-data-factory-portal.md)
