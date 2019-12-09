---
title: Szyfruj poświadczenia w Azure Data Factory
description: Dowiedz się, jak szyfrować i przechowywać poświadczenia dla lokalnych magazynów danych na komputerze przy użyciu własnego środowiska Integration Runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: bcca2e6168baafe9f98f663298790841c0f1f450
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927427"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Szyfruj poświadczenia dla lokalnych magazynów danych w Azure Data Factory
Poświadczenia dla lokalnych magazynów danych (połączonych usług z informacjami poufnymi) można zaszyfrować i zapisać na komputerze przy użyciu własnego środowiska Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przekaż plik definicji JSON z poświadczeniami do <br/>Polecenie cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) w celu utworzenia pliku wyjściowego JSON z zaszyfrowanymi poświadczeniami. Następnie użyj zaktualizowanej definicji JSON, aby utworzyć połączone usługi.

## <a name="author-sql-server-linked-service"></a>Tworzenie połączonej usługi SQL Server
Utwórz plik JSON o nazwie **SqlServerLinkedService. JSON** w dowolnym folderze o następującej zawartości:  

Przed zapisaniem pliku Zastąp `<servername>`, `<databasename>`, `<username>`i `<password>` wartościami dla SQL Server. I Zastąp `<integration runtime name>` nazwą Twojego środowiska Integration Runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Szyfrowanie poświadczeń
Aby zaszyfrować poufne dane z ładunku JSON w lokalnym środowisku Integration Runtime, uruchom polecenie **New-AzDataFactoryV2LinkedServiceEncryptedCredential**i przekaż ładunek JSON. To polecenie cmdlet zapewnia szyfrowanie poświadczeń przy użyciu funkcji DPAPI i przechowywanie ich lokalnie na własnym węźle środowiska Integration Runtime. Ładunek wyjściowy zawierający zaszyfrowane odwołanie do poświadczenia może zostać przekierowany do innego pliku JSON (w tym przypadku "encryptedLinkedService. JSON").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Używanie pliku JSON z zaszyfrowanymi poświadczeniami
Teraz Użyj wyjściowego pliku JSON z poprzedniego polecenia zawierającego zaszyfrowane poświadczenia, aby skonfigurować **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Następne kroki
Informacje o kwestiach dotyczących zabezpieczeń dotyczących przenoszenia danych znajdują się w temacie [zagadnienia dotyczące zabezpieczeń przenoszenia danych](data-movement-security-considerations.md).

