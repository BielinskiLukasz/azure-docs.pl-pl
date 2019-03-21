---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e8888a0505a3a38d2844f82c0f7fff255d05353d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261490"
---
W usłudze Azure Cloud Shell skonfiguruj poświadczenia wdrożenia za pomocą [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) polecenia. Ten użytkownik wdrożenia jest wymagany do wdrożenia protokołu FTP i lokalnego narzędzia Git w aplikacji internetowej. Nazwa użytkownika i hasło są na poziomie konta. _Są one różne od poświadczeń subskrypcji platformy Azure._

W poniższym przykładzie Zastąp  *\<username >* i  *\<hasło >*, razem z nawiasami, Nowa nazwa użytkownika i hasło. Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. Hasło musi mieć co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Otrzymasz dane wyjściowe JSON z hasłem wyświetlanym jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd ` 'Bad Request'. Details: 400`, użyj silniejszego hasła.

Ten użytkownik wdrożenia można skonfigurować tylko jeden raz. Można go używać we wszystkich wdrożeniach platformy Azure.

> [!NOTE]
> Zapisz nazwę użytkownika i hasło. Będą one potrzebne później do wdrożenia aplikacji internetowej.
>
>
