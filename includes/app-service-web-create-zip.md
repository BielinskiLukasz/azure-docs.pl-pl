---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133015"
---
## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

Upewnij się, że nadal znajdujesz się w katalogu głównym przykładowego projektu. Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Później przekaż ten plik ZIP do platformy Azure i wdróż go w usłudze App Service.