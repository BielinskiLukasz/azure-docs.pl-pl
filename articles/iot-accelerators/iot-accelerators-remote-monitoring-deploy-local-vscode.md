---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie (Visual Studio Code) — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrożyć zdalnego akcelerator rozwiązań do monitorowania na komputerze lokalnym przy użyciu programu Visual Studio Code na potrzeby projektowania i testowania.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6e2fafa398b09d0822c4582e196345b812e6fc52
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392245"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie — Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. Dowiesz się, jak uruchomić mikrousługi w programie Visual Studio Code. Wdrożenie lokalne mikrousług używa następujących usług w chmurze: Usługa IoT Hub, usługa Cosmos DB, analizy przesyłania strumieniowego usługi Azure i usługi Azure Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Ustawienia komputera

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [.Net Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](http://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Program VS Code C# rozszerzenia](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia, które skrypty umożliwia tworzenie zasobów platformy Azure. Nie używaj v10 środowiska Node.js

> [!NOTE]
> Visual Studio Code jest dostępny dla Windows, Mac i Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchamiasz mikrousług monitorowania zdalnego. Działania interfejsu użytkownika sieci web, usługi symulacji urządzenia na platformie Docker i mikrousług w programie Visual Studio Code.

### <a name="build-the-code"></a>Kompilowanie kod

Przejdź do azure-iot-pcs-remote-monitoring-dotnet\services w wierszu polecenia, a następnie uruchom następujące polecenia, aby skompilować kod.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdrażaj wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousług zdalnego monitorowania w programie Visual Studio 2017:

1. Uruchom program Visual Studio Code.
1. Otwórz **azure-iot-pcs-remote-monitoring-dotnet** modułu na podstawie kopii lokalnej w programie VS Code.
1. Skopiuj pliki **launch.json** i **tasks.json** z scripts\local\launch\idesettings\vscode\. Utwórz nowy folder **azure-iot-pcs-remote-monitoring-dotnet\.vscode** i Wklej w nim plików.
1. Otwórz panel debugowania w programie VS Code i uruchom **Uruchom wszystkie mikrousługi** konfiguracji. Ta konfiguracja działa mikrousług symulacji urządzenia na platformie Docker i uruchamia innych mikrousług w debugerze.

Na przykład danych wyjściowych dla **uwierzytelniania** w **konsoli debugowania** wygląda podobnie do następującego:

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Uruchom interfejs użytkownika sieci web

W tym kroku należy uruchomić interfejs użytkownika sieci web. Przejdź do **azure-iot-pcs-remote-monitoring-dotnet\webui** folderu w lokalnym skopiować, a następnie uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu początkowego przeglądarki zostanie wyświetlona strona **http://localhost:3000/dashboard**. Oczekiwane są błędy na tej stronie. Aby wyświetlić aplikację bez błędów, wykonaj poniższe czynności.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie serwera NGINX

Konfigurowanie zwrotnego serwera proxy do łączenia aplikacji sieci web i mikrousług uruchomiona na komputerze lokalnym:

* Kopiuj **nginx.conf** plik wchodzącej w skład **webui\scripts\localhost** folder **nginx\conf** katalog_instalacji.
* Uruchom **nginx**.

Aby uzyskać więcej informacji o uruchamianiu **nginx**, zobacz [nginx dla Windows](http://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Łączenie do pulpitu nawigacyjnego

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego, przejdź do [ http://localhost:9000 ](http://localhost:9000) w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usług w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i Usuń zasób grupy, która **start.cmd** skrypt utworzony.

Możesz także usunąć lokalną kopię repozytorium zdalne monitorowanie utworzone podczas klonowania kodu źródłowego z repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się wdrożyć rozwiązania do zdalnego monitorowania, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).