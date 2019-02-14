---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: aa93fb9779e6cda41df52f7979e42348948544ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247059"
---
`Logging` Ustawienia zarządzania obsługę rejestrowania platformy ASP.NET Core kontenera. Można użyć tych samych ustawień konfiguracji oraz wartości kontenera używaną dla aplikacji ASP.NET Core. 

Następujących dostawców rejestrowania są obsługiwane przez kontener:

|Dostawca|Przeznaczenie|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Debugowanie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Dysk](#disk-logging)|Dostawca logowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika instalacji danych wyjściowych.|

### <a name="disk-logging"></a>Rejestrowanie dysku

`Disk` Rejestrowania dostawca obsługuje następujące ustawienia konfiguracji:  

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Format` | Ciąg | Format danych wyjściowych dla plików dziennika.<br/> **Uwaga:** Ta wartość musi być równa `json` do włączenia rejestrowania dostawcy usług. Jeśli ta wartość jest określona bez jednoczesnego określenia dane wyjściowe instalacji podczas tworzenia wystąpienia kontenera, wystąpi błąd. |
| `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar w megabajtach (MB), plik dziennika. Gdy rozmiar bieżącego pliku dziennika, spełnia lub przekracza tę wartość, nowy plik dziennika został uruchomiony przez dostawcę rejestrowania. Jeśli określono wartość -1, rozmiar pliku dziennika jest ograniczona tylko przez maksymalny rozmiar pliku, ewentualne instalacji danych wyjściowych. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania platformy ASP.NET Core, zobacz [ustawień pliku konfiguracji](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
