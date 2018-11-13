---
title: Automatyczna obsługa usługi Azure IoT Edge urządzenia przy użyciu usługi DPS - Windows | Dokumentacja firmy Microsoft
description: Korzystanie z symulowanego urządzenia na urządzeniu Windows do testowania, automatyczne aprowizowanie urządzeń Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9be790d9b512dc9338cf183240430ad0ada3bef4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565109"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Tworzenie i aprowizowanie symulowanego urządzenia TPM Edge na Windows

Urządzenia w usłudze Azure IoT Edge mogą być automatycznie aprowizowane za pomocą [usługi Device Provisioning](../iot-dps/index.yml) podobnie jak w przypadku urządzeń, których nie włączono usługi edge. Jeśli znasz procesu automatycznego aprowizowania, zapoznaj się z [automatycznej aprowizacji pojęcia](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem. 

W tym artykule pokazano, jak przetestować automatycznej aprowizacji na symulowanym urządzeniu usługi Edge wykonując następujące kroki: 

* Utwórz wystąpienie z IoT Hub urządzenia inicjowania obsługi usługi (DPS).
* Utworzenie symulowanego urządzenia na urządzeniu Windows przy użyciu symulowanego Trusted Platform Module (TPM) dotyczące zabezpieczeń sprzętowych.
* Utworzyć rejestrację indywidualną dla urządzenia.
* Zainstalować środowisko uruchomieniowe usługi IoT Edge i połączyć to urządzenie do Centrum IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputera deweloperskiego z systemem Windows. W tym artykule używany jest system Windows 10. 
* Aktywnym Centrum IoT. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie klasy IoT Hub Device Provisioning Service na platformie Azure i połączyć go z Centrum IoT hub. Możesz wykonać instrukcje w [Konfigurowanie usługi IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po użytkownik ma uruchomione usługi Device Provisioning, skopiuj wartość **zakres identyfikatorów** na stronie Przegląd. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge. 

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Tworzenie symulowanego urządzenia TPM na komputerze deweloperskim Windows. Pobieranie **identyfikator rejestracji** i **klucz poręczenia** dla Twojego urządzenia i ich używać, aby utworzyć wpis rejestracji indywidualnej w usłudze DPS. 

Po utworzeniu rejestracji w usłudze DPS mają okazję do deklarowania **początkowy stan bliźniaczej reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi grupowanie urządzeń według dowolnej metryki potrzebnych w rozwiązaniu, takich jak typ region, środowisko, lokalizacji lub urządzenia. Tagi te są używane do tworzenia [automatycznego wdrożenia](how-to-deploy-monitor.md). 

Wybierz język zestawu SDK, który ma używać do tworzenia symulowanego urządzenia, a następnie postępuj zgodnie z instrukcjami, do czasu utworzenia rejestracji indywidualnej. 

Podczas tworzenia rejestracji indywidualnej wybrać **Włącz** Aby zadeklarować, że ta maszyna wirtualna jest **urządzenie usługi IoT Edge**.

Symulowane urządzenie i przewodniki dotyczące rejestracji indywidualnej: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu rejestracji indywidualnej należy zapisać wartości **identyfikator rejestracji**. Będzie ona używana podczas konfigurowania środowiska uruchomieniowego usługi IoT Edge. 

## <a name="install-the-iot-edge-runtime"></a>Zainstalować środowisko uruchomieniowe usługi IoT Edge

Po zakończeniu pracy w poprzedniej sekcji, powinien zostać wyświetlony nowe urządzenie na liście jako urządzenia usługi IoT Edge w usłudze IoT Hub. Teraz musisz zainstalować środowisko uruchomieniowe usługi IoT Edge na urządzeniu. 

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i pozwalają wdrożyć dodatkowe kontenery na urządzeniu, aby uruchomić kod, na urządzeniach brzegowych. Na urządzeniach z systemem Windows można użyć kontenery Windows lub kontenerów systemu Linux. Wybierz typ kontenerów, które chcesz użyć, a następnie postępuj zgodnie z instrukcjami. Upewnij się, że Konfigurowanie środowiska uruchomieniowego usługi IoT Edge do automatycznego, nie ręcznego inicjowania obsługi. 

Postępuj zgodnie z instrukcjami, aby zainstalować środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem symulowanego modułu TPM z poprzedniej sekcji. 

Znasz usłudze DPS **zakres identyfikatorów** i urządzenia **identyfikator rejestracji** przed rozpoczęciem tych artykułów. 

* [Kontenery Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Kontenery systemu Linux](how-to-install-iot-edge-windows-with-linux.md)

## <a name="verify-successful-installation"></a>Sprawdź pomyślnej instalacji

Jeśli środowisko wykonawcze został uruchomiony pomyślnie, możesz przejść do Centrum IoT Hub i zacząć wdrażać moduły usługi IoT Edge na urządzeniu. Użyj następujących poleceń na twoim urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zainstalowana i uruchomiona pomyślnie.  

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usługi z ostatnich 5 minut.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Utwórz listę uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Kolejne kroki

Proces rejestracji usługi Device Provisioning pozwala ustawić identyfikator urządzenia i tagów bliźniaczych reprezentacji urządzeń w tym samym czasie, jak aprowizować nowe urządzenie. Pod kątem poszczególnych urządzeń lub grup urządzeń za pomocą urządzenia automatycznego zarządzania, można użyć tych wartości. Dowiedz się, jak [monitora usługi IoT Edge modułów na skalowanie przy użyciu witryny Azure portal i wdróż](how-to-deploy-monitor.md) lub [przy użyciu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)
