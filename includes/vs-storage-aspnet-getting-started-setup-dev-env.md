---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 634143f56bc9134f240ff42b7b5989605c8bffde
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979303"
---
## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska deweloperskiego

W tej sekcji przedstawiono konfigurowanie środowiska deweloperskiego. Obejmuje to tworzenie aplikacji ASP.NET MVC, dodawanie połączenia usług połączonych, Dodawanie kontrolera i określając dyrektywy wymaganej przestrzeni nazw.

### <a name="create-an-aspnet-mvc-app-project"></a>Tworzenie projektu aplikacji platformy ASP.NET MVC

1. Otwórz program Visual Studio.

1. W menu głównym wybierz **pliku** > **New** > **projektu**.

1. W **nowy projekt** okno dialogowe, wybierz opcję **Web** > **aplikacji sieci Web platformy ASP.NET (.NET Framework)**. W **nazwa** określ **StorageAspNet**. Kliknij przycisk **OK**.

    ![Zrzut ekranu nowego projektu, okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. W **Nowa aplikacja internetowa ASP.NET** okno dialogowe, wybierz opcję **MVC**, a następnie wybierz pozycję **OK**.

    ![Zrzut ekranu z nowej aplikacji sieci Web ASP.NET, okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Nawiązywanie połączenia z konta usługi Azure storage za pomocą usług połączonych

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt.

2. Z menu kontekstowego wybierz **Dodaj** > **podłączoną usługę**.

1. W **podłączone usługi** okno dialogowe, wybierz opcję **magazynu w chmurze z usługą Azure Storage**.

    ![Zrzut ekranu z połączonych usług, okno dialogowe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. W **usługi Azure Storage** okno dialogowe, wybierz opcję konta magazynu platformy Azure ma być używany na potrzeby tego samouczka. Aby utworzyć nowe konto magazynu platformy Azure, wybierz **Utwórz nowe konto magazynu**i wypełnij formularz. Po wybraniu istniejące konto magazynu lub tworząc nową, wybierz **Dodaj**. Program Visual Studio instaluje pakiet NuGet dla usługi Azure Storage i parametry połączenia magazynu w celu **Web.config**.

> [!TIP]
> Aby dowiedzieć się, jak utworzyć konto magazynu przy użyciu [witryny Azure portal](https://portal.azure.com), zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Można również utworzyć konto magazynu przy użyciu [programu Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [wiersza polecenia platformy Azure](../articles/storage/common/storage-azure-cli.md), lub [usługi Azure Cloud Shell](../articles/cloud-shell/overview.md).

