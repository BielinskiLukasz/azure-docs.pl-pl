---
title: Sparametryzuj połączone usługi w Azure Data Factory
description: Dowiedz się, jak Sparametryzuj połączone usługi w Azure Data Factory i przekazywać wartości dynamiczne w czasie wykonywania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440094"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Sparametryzuj połączone usługi w Azure Data Factory

Teraz można Sparametryzuj połączonej usługi i przekazywać wartości dynamiczne w czasie wykonywania. Na przykład jeśli chcesz nawiązać połączenie z różnymi bazami danych na tym samym serwerze Azure SQL Database, możesz teraz Sparametryzuj nazwę bazy danych w definicji połączonej usługi. Zapobiega to konieczności tworzenia połączonej usługi dla każdej bazy danych na serwerze usługi Azure SQL Database. Można Sparametryzuj inne właściwości w definicji połączonej usługi, jak również na przykład *Nazwa użytkownika.*

Aby Sparametryzuj połączone usługi, można użyć interfejsu użytkownika Data Factory w Azure Portal lub interfejsie programowania.

> [!TIP]
> Nie zaleca się Sparametryzuj haseł ani wpisów tajnych. W zamian Przechowuj wszystkie parametry połączenia w Azure Key Vault i Sparametryzuj *nazwę klucza tajnego*.

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

W tej chwili parametryzacja połączonej usługi jest obsługiwany przez interfejs użytkownika Data Factory w Azure Portal dla następujących magazynów danych. W przypadku wszystkich innych magazynów danych można Sparametryzuj połączoną usługę, wybierając ikonę **kodu** na karcie **połączenia** i używając edytora JSON.
- Baza danych SQL Azure
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

![Dodawanie zawartości dynamicznej do definicji połączonej usługi](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Tworzenie nowego parametru](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
