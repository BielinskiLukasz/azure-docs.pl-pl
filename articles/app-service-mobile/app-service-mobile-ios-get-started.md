---
title: Tworzenie aplikacji systemu iOS przy użyciu funkcji Azure App Service Mobile Apps| Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby rozpocząć korzystanie z zapleczy usług mobilnych Azure na potrzeby opracowywania aplikacji systemu iOS w środowisku Objective-C lub Swift.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: d0d6a3d9da2768c2d7b04bd9c4a7c24fba9eb65e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781684"
---
# <a name="create-an-ios-app"></a>Tworzenie aplikacji systemu iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Przegląd

W tym samouczku przedstawiono sposób dodawania [aplikacji mobilnych usługi Azure Mobile Apps](app-service-mobile-value-prop.md) (usługi zaplecza w chmurze) do aplikacji systemu iOS. Pierwszym krokiem jest utworzenie nowego zaplecza aplikacji mobilnej na platformie Azure. Następnie należy pobrać prostą przykładową aplikację systemu iOS *Lista zadań do wykonania*, która służy do przechowywania danych na platformie Azure.

Aby można było ukończyć ten samouczek, potrzebny jest komputer Mac i [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Krok I: Tworzenie zaplecza nowej aplikacji mobilnej Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Krok II Konfigurowanie projektu zaplecza

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Krok III Pobieranie i uruchamianie aplikacji systemu iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
