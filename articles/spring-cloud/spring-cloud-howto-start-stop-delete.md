---
title: Uruchamianie, zatrzymywanie i usuwanie aplikacji w chmurze Azure wiosny | Microsoft Docs
description: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: ed3db5cfbdeb501e564a8da0020821c550a54787
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461631"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uruchamianie, zatrzymywanie i usuwanie aplikacji w chmurze platformy Azure

W tym przewodniku wyjaśniono, jak zmienić stan aplikacji w chmurze Azure wiosennej za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu aplikacji można uruchomić, zatrzymać i usunąć ją za pomocą Azure Portal.

1. Przejdź do wystąpienia usługi w chmurze ze sprężyną platformy Azure w Azure Portal.
1. Wybierz kartę **pulpit nawigacyjny aplikacji** .
1. Wybierz aplikację, której stan chcesz zmienić.
1. Na stronie **Przegląd** dla tej aplikacji wybierz pozycję **Uruchom/Zatrzymaj**, **Uruchom ponownie**lub **Usuń**.

## <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Możesz użyć parametrów opcjonalnych i skonfigurować wartości domyślne przy użyciu interfejsu wiersza polecenia platformy Azure. Dowiedz się więcej o interfejsie wiersza polecenia platformy Azure, odczytując [dokumentację referencyjną](spring-cloud-cli-reference.md).  

Najpierw zainstaluj rozszerzenie Cloud wiosny Azure dla interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli
az extension add --name spring-cloud
```

Następnie wybierz dowolną z następujących operacji interfejsu wiersza polecenia platformy Azure:

* Aby uruchomić aplikację:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby zatrzymać aplikację:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby ponownie uruchomić aplikację:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby usunąć aplikację:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
