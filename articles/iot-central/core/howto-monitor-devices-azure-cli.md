---
title: Monitorowanie łączności urządzenia przy użyciu centralnego eksploratora Usługi Azure IoT
description: Monitoruj komunikaty urządzenia i obserwuj zmiany bliźniaczej reprezentacji urządzenia za pośrednictwem interfejsu wiersza polecenia programu IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 09209c21fe1b2b115c1ba6d6e00fcd0ee59a9393
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365426"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorowanie łączności urządzeń przy użyciu interfejsu wiersza polecenia platformy Azure

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj rozszerzenia IoT interfejsu wiersza polecenia azure, aby wyświetlić komunikaty wysyłane przez urządzenia do centrum IoT i obserwować zmiany w bliźniaczej reprezentacji urządzenia. Za pomocą tego narzędzia można debugować i obserwować łączność urządzenia i diagnozować problemy z komunikatami urządzenia, które nie docierają do chmury lub urządzeń nie reagujących na zmiany bliźniacze.

[Odwiedź odwołanie do rozszerzeń interfejsu wiersza polecenia platformy Azure, aby uzyskać więcej informacji](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Wymagania wstępne

+ Zainstalowano narzędzie cli platformy Azure i jest w wersji 2.0.7 lub nowszej. Sprawdź wersję interfejsu wiersza `az --version`polecenia platformy Azure, uruchamiając program . Dowiedz się, jak zainstalować i zaktualizować dokumenty [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Konto służbowe na platformie Azure, dodane jako użytkownik w aplikacji IoT Central.

## <a name="install-the-iot-central-extension"></a>Instalowanie rozszerzenia IoT Central

Uruchom następujące polecenie z wiersza polecenia, aby zainstalować:

```azurecli
az extension add --name azure-iot
```

Sprawdź wersję rozszerzenia, uruchamiając:

```azurecli
az --version
```

Powinien zostać wyświetlony rozszerzenie azure-iot jest 0.8.1 lub wyższe. Jeśli tak nie jest, uruchom:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Korzystanie z rozszerzenia

W poniższych sekcjach opisano typowe polecenia i opcje, których można używać podczas uruchamiania `az iot central`. Aby wyświetlić pełny zestaw poleceń `--help` i `az iot central` opcji, przekaż do lub do któregokolwiek z jego podpoleceń.

### <a name="login"></a>Logowanie

Zacznij od zalogowania się do interfejsu wiersza polecenia platformy Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Pobierz identyfikator aplikacji IoT Central
W **obszarze Ustawienia administracyjne/aplikacyjne**skopiuj **identyfikator aplikacji**. Ta wartość jest używana w późniejszych krokach.

### <a name="monitor-messages"></a>Monitorowanie komunikatów
Monitoruj wiadomości wysyłane do aplikacji IoT Central z urządzeń. Dane wyjściowe obejmują wszystkie nagłówki i adnotacje.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Wyświetlanie właściwości urządzenia
Wyświetl bieżące właściwości urządzenia odczytu i odczytu/zapisu dla danego urządzenia.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z IoT Central Explorer, sugerowanym następnym krokiem jest [zbadanie zarządzania urządzeniami IoT Central](howto-manage-devices.md).
