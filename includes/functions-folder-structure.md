---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205730"
---
Kod wszystkich funkcji w określonej aplikacji funkcji znajduje się w głównym folderze projektu, który zawiera plik konfiguracji hosta i jeden lub więcej podfolderów. Każdy podfolder zawiera kod dla oddzielnej funkcji. Struktura folderów jest wyświetlana w następującej reprezentacji:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

W wersji 2.x i wyższej środowiska wykonawczego Functions wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam stos języka.  

Plik [host.json](../articles/azure-functions/functions-host-json.md) zawiera konfiguracje specyficzne dla środowiska uruchomieniowego i znajduje się w folderze głównym aplikacji funkcji. Folder *bin* zawiera pakiety i inne pliki biblioteki, które wymaga aplikacji funkcji. Zobacz wymagania specyficzne dla języka dla projektu aplikacji funkcji:

* [Biblioteka klas języka C# (csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skrypt języka C# (csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Skrypt języka F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [Javascript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
