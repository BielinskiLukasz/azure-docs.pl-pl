---
title: Automatyczne Inicjowanie obsługi urządzeń z systemem Windows przy użyciu programu DPS — Azure IoT Edge | Microsoft Docs
description: Używanie symulowanego urządzenia na komputerze z systemem Windows w celu przetestowania automatycznej aprowizacji urządzeń dla Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5842d6fcb5f03754fc8f5922e299d0d9c30d21db
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900830"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Tworzenie i Inicjowanie obsługi symulowanego urządzenia IoT Edge przy użyciu wirtualnego modułu TPM w systemie Windows

Urządzenia Azure IoT Edge mogą być obsługiwane przy użyciu [usługi Device Provisioning](../iot-dps/index.yml) , podobnie jak dla urządzeń, które nie są włączone. Jeśli nie znasz procesu samoobsługowego inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z [pojęciami autouzupełniania](../iot-dps/concepts-auto-provisioning.md) .

Usługa DPS obsługuje zaświadczenie klucza symetrycznego dla IoT Edge urządzeń zarówno podczas rejestracji indywidualnej, jak i rejestracji grupowej. Jeśli w przypadku rejestracji grupowej opcja "is IoT Edge Device" ma wartość true w zaświadczeniu klucza symetrycznego, wszystkie urządzenia zarejestrowane w ramach tej grupy rejestracji zostaną oznaczone jako urządzenia IoT Edge. 

W tym artykule pokazano, jak przetestować funkcję autoaprowizacji na symulowanym urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz wystąpienie IoT Hub Device Provisioning Service (DPS).
* Utwórz symulowane urządzenie na maszynie z systemem Windows z symulowanym moduł TPM (TPM) na potrzeby zabezpieczeń sprzętowych.
* Utwórz rejestrację indywidualną dla urządzenia.
* Zainstaluj środowisko uruchomieniowe IoT Edge i Połącz urządzenie z IoT Hub.

> [!NOTE]
> Moduł TPM 2,0 jest wymagany w przypadku korzystania z zaświadczania TPM z usługą DPS i może być używany tylko do tworzenia pojedynczych, nie grup, rejestracji.

> [!TIP]
> W tym artykule opisano testowanie samoobsługowego testowania przy użyciu zaświadczania modułu TPM na urządzeniach wirtualnych, ale większość jest stosowana w przypadku używania fizycznego sprzętu modułu TPM.

## <a name="prerequisites"></a>Wymagania wstępne

* Komputer deweloperski systemu Windows. W tym artykule jest wykorzystywany system Windows 10.
* Aktywna IoT Hub.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Skonfiguruj IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie IoT Hub Device Provisioning Service na platformie Azure i połącz je z Centrum IoT. Można postępować zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Ta wartość jest używana podczas konfigurowania środowiska uruchomieniowego IoT Edge.

> [!TIP]
> W przypadku korzystania z fizycznego urządzenia TPM należy określić **Klucz poręczenia**, który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany z przypisanego do niego producenta mikroukładu modułu TPM. Można utworzyć unikatowy **Identyfikator rejestracji** dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.
>
> Postępuj zgodnie z instrukcjami w artykule [jak zarządzać rejestracjami urządzeń za pomocą witryny Azure Portal](../iot-dps/how-to-manage-enrollments.md) , aby utworzyć rejestrację w usłudze DPS, a następnie przejdź do sekcji [Zainstaluj środowisko uruchomieniowe IoT Edge](#install-the-iot-edge-runtime) w tym artykule, aby kontynuować.

## <a name="simulate-a-tpm-device"></a>Symulowanie urządzenia TPM

Utwórz symulowane urządzenie TPM na komputerze deweloperskim systemu Windows. Pobierz **Identyfikator rejestracji** i **Klucz poręczenia** dla urządzenia i użyj go do utworzenia indywidualnego wpisu rejestracji w usłudze DPS.

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W obszarze urządzenia można ustawić Tagi do grupowania urządzeń według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

Wybierz język zestawu SDK, którego chcesz użyć do utworzenia symulowanego urządzenia, i postępuj zgodnie z instrukcjami, dopóki nie utworzysz rejestracji indywidualnej.

Podczas tworzenia rejestracji indywidualnej wybierz **wartość true** , aby zadeklarować, że symulowane urządzenie TPM na komputerze deweloperskim systemu Windows jest **urządzeniem IoT Edge**.

Symulowane urządzenia i indywidualne przewodniki rejestracji:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Po utworzeniu rejestracji indywidualnej Zapisz wartość **identyfikatora rejestracji**. Ta wartość jest używana podczas konfigurowania środowiska uruchomieniowego IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Zainstaluj środowisko uruchomieniowe IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrożenie dodatkowych kontenerów na urządzeniu, aby można było uruchomić kod na krawędzi.

Podczas aprowizacji urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Utworzony **Identyfikator rejestracji** urządzenia

Zainstaluj środowisko uruchomieniowe IoT Edge na urządzeniu, na którym działa symulowany moduł TPM. Skonfigurujesz środowisko uruchomieniowe IoT Edge automatyczne, nie ręczne, Inicjowanie obsługi administracyjnej.

> [!TIP]
> Zachowaj okno, w którym jest uruchomiony symulator modułu TPM, podczas instalacji i testowania.

Aby uzyskać bardziej szczegółowe informacje na temat instalowania IoT Edge w systemie Windows, w tym wymagania wstępne i instrukcje dotyczące zadań, takich jak Zarządzanie kontenerami i aktualizowanie IoT Edge, zobacz [Instalowanie środowiska uruchomieniowego Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

1. Otwórz okno programu PowerShell w trybie administratora. Należy pamiętać, aby podczas instalowania IoT Edge używać sesji AMD64 programu PowerShell, a nie programu PowerShell (x86).

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję Containers, a następnie pobiera środowisko uruchomieniowe Moby i środowisko uruchomieniowe IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie uruchomione ponownie. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, ponownie uruchom urządzenie teraz. Gdy urządzenie jest gotowe, uruchom ponownie program PowerShell jako administrator.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows. Użyj flagi `-Dps`, aby użyć usługi Device Provisioning zamiast ręcznego inicjowania obsługi.

   Zamień wartości zastępcze dla `{scope_id}` i `{registration_id}` na zebrane wcześniej dane.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Jeśli środowisko uruchomieniowe zostało pomyślnie uruchomione, możesz przejść do IoT Hub i rozpocząć wdrażanie modułów IoT Edge na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało prawidłowo zainstalowane i uruchomione.  

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Przeanalizuj dzienniki usług z ostatnich 5 minut.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wyświetl listę uruchomionych modułów.

```powershell
iotedge list
```

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie tagów urządzeń i urządzeń w tym samym czasie, w których jest inicjowane nowe urządzenie. Tych wartości można użyć do kierowania poszczególnych urządzeń lub grup urządzeń przy użyciu funkcji automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-monitor-cli.md)
