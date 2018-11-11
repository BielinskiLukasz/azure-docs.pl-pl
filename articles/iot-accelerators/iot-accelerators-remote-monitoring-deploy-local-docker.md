---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie — Docker — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrożyć zdalnego monitorowania akcelerator rozwiązań do maszyny lokalnej na potrzeby projektowania i testowania.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 91f43473a5648be5264038993f43b61887e0c363
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284979"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie - platformy Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. Artykuł pokazuje, jak wdrażanie mikrousług na lokalnym kontenerów platformy Docker. Wdrożenie lokalne mikrousług używa następujących usług w chmurze: Usługa IoT Hub, Cosmos DB, usługi Azure Stream Analytics i Azure Time Series Insights usług w chmurze.

Jeśli chcesz uruchomić akcelerator rozwiązania monitorowania zdalnego w środowisku IDE na komputerze lokalnym, zobacz [wdrażanie programu Visual Studio zdalne monitorowanie akceleratora rozwiązań lokalnie -](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Ustawienia komputera

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Program Visual Studio](https://visualstudio.microsoft.com/)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia, które skrypty umożliwia tworzenie zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> Te narzędzia są dostępne na wielu platformach, w tym Windows i Linux oraz dla systemu iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Uruchom mikrousług na platformie Docker

Otwórz nowy wiersz polecenia mieć pewność, że dostęp do zmiennych środowiskowych, ustawiane przez **start.cmd** skryptu. W Windows możesz sprawdzić, czy zmienne środowiskowe są ustawiane za pomocą następującego polecenia:

```cmd
set PCS
```

Polecenie wyświetla wszystkie zmienne środowiskowe ustawione **start.cmd** skryptu.

Upewnij się, że platformy Docker jest uruchomiona na komputerze lokalnym.

Mikrousługi uruchomiona w lokalnym kontenerów platformy Docker konieczne dostęp do usług Azure cloud services. Można przetestować łączność z Internetem środowiska platformy Docker na polecenie ping adresu internetowego z wewnątrz kontenera przy użyciu następującego polecenia:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Aby uruchomić akcelerator rozwiązań, przejdź do **usług\\skrypty\\lokalnego** folder w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd/sh
docker-compose up
```

Podczas pierwszego uruchamiania tego polecenia Docker pobiera obrazy mikrousług z usługi Docker hub do tworzenia kontenerów lokalnie. Na następujące przebiegi Docker uruchamia kontenery natychmiast.

> [!TIP]
> Często publikujemy nowych obrazów platformy Docker za pomocą nowych funkcji. Następujący zestaw poleceń, aby oczyścić można użyć lokalnego kontenerów platformy Docker i odpowiednie obrazy przed pobierania najnowszych te. 

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Aby wyświetlić dzienniki z kontenera, można użyć oddzielnych powłoki. Najpierw Znajdź przy użyciu Identyfikatora kontenera `docker ps` polecenia. Następnie użyj `docker logs {container-id} --tail 1000` ostatnich 1000 zapisy określonego kontenera.

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie usługi Stream Analytics:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** tworzony dla rozwiązania. Nazwa grupy zasobów jest nazwa została wybrana opcja rozwiązania jest wyświetlany po uruchomieniu **start.cmd** skryptu.
1. Kliknij pozycję **zadania usługi Stream Analytics** na liście zasobów.
1. W zadaniu Stream Analytics **Przegląd** kliknij **Start** przycisku. Następnie kliknij przycisk **Start** można teraz uruchomić zadania.

### <a name="connect-to-the-dashboard"></a>Łączenie do pulpitu nawigacyjnego

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego, przejdź do [ http://localhost:8080 ](http://localhost:8080) w przeglądarce. Można teraz używać lokalnej mikrousług i internetowego interfejsu użytkownika.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usług w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i Usuń zasób grupy, która **start.cmd** skrypt utworzony.

Użyj `docker-compose down --rmi all` polecenie, aby usunąć obrazy platformy Docker i wolnego miejsca na komputerze lokalnym. Możesz także usunąć lokalną kopię repozytorium zdalne monitorowanie utworzone podczas klonowania kodu źródłowego z repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj lokalne Środowisko deweloperskie
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, że udało Ci się wdrożyć rozwiązania do zdalnego monitorowania, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->