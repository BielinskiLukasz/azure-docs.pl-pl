---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836912"
---
Usługa FTP i lokalne narzędzia Git można wdrożyć w aplikacji sieci Web platformy Azure przy użyciu *użytkownika wdrożenia*. Po skonfigurowaniu użytkownika wdrożenia można go użyć do wszystkich wdrożeń platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) w Azure Cloud Shell. Zastąp \<> nazwy \<użytkownika i hasła> nazwą użytkownika i hasłom wdrożenia. 

- Nazwa użytkownika musi być unikatowa w ramach platformy Azure, a w przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu "@". 
- Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON przedstawiają hasło jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zapisz nazwę użytkownika i hasło, aby użyć do wdrożenia aplikacji sieci Web.
