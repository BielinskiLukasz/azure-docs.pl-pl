---
title: Rejestrowanie urządzenia TPM w usłudze Azure Device Provisioning przy użyciu języka Python | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning przy użyciu zestawu SDK usługi aprowizacji języka Python
author: bryanla
ms.author: bryanla
ms.date: 01/26/2018
ms.topic: quickstarts
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: bd2a50b4f69fda88ac4b3f90941cb9da33298c74
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629953"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Rejestrowanie urządzenia TPM w usłudze IoT Hub Device Provisioning przy użyciu zestawu SDK usługi aprowizacji języka Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tych instrukcjach pokazano, jak programowo utworzyć rejestrację indywidualną dla urządzenia TPM w usłudze Azure IoT Hub Device Provisioning przy użyciu [zestawu SDK usługi aprowizacji języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) i przykładowej aplikacji w języku Python. Mimo że zestaw SDK usługi języka Python działa zarówno na komputerach z systemami Windows, jak i Linux, w tym artykule w celu zaprezentowania procesu rejestracji użyto maszyny deweloperskiej z systemem Windows.

Pamiętaj, aby wcześniej [skonfigurować usługę IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md).


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Pobierz i zainstaluj środowisko [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennych środowiskowych specyficznych dla platformy. 

1. Wybierz jedną z następujących opcji:

    - Utwórz i skompiluj **zestaw SDK języka Python usługi Azure IoT**. Postępuj zgodnie z [tymi instrukcjami](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md), aby skompilować pakiety języka Python. Jeśli używasz systemu operacyjnego Windows, zainstaluj także [Pakiet redystrybucyjny języka Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

    - [Zainstaluj lub uaktualnij program *pip*, czyli system zarządzania pakietami języka Python](https://pip.pypa.io/en/stable/installing/), i zainstaluj pakiet za pomocą następującego polecenia:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Wymagany jest klucz poręczenia dla danego urządzenia. Jeśli w celu utworzenia symulowanego urządzenia TPM wykonano kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md) przewodnika Szybki start, użyj klucza utworzonego dla tego urządzenia. W przeciwnym razie możesz użyć następującego klucza poręczenia dostarczonego razem z zestawem SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Modyfikowanie przykładowego kodu w języku Python

W tej sekcji przedstawiono sposób dodawania szczegółów aprowizacji urządzenia TPM do przykładowego kodu. 

1. Przy użyciu edytora tekstów utwórz nowy plik **TpmEnrollment.py**.

1. Dodaj następujące instrukcje `import` i zmienne na początku pliku **TpmEnrollment.py**. Następnie zastąp element `dpsConnectionString` parametrami połączenia zawartymi w **zasadach dostępu współużytkowanego** usługi **Device Provisioning Service** w witrynie **Azure Portal**. Zastąp element `endorsementKey` wartością zanotowaną wcześniej podczas [przygotowywania środowiska](quick-enroll-device-tpm-python.md#prepareenvironment). Na koniec utwórz unikatowy identyfikator `registrationid` i upewnij się, że zawiera on wyłącznie małe litery, cyfry i łączniki.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Dodaj następującą funkcję i wywołanie funkcji w celu zaimplementowania tworzenia rejestracji grupowej:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Zapisz i zamknij plik **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Uruchamianie przykładowej rejestracji urządzenia TPM

1. Otwórz wiersz polecenia i uruchom skrypt.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Sprawdź poprawność rejestracji w danych wyjściowych.

1. Przejdź do usługi aprowizacji w witrynie Azure Portal. Kliknij pozycję **Zarządzanie rejestracjami**. Zauważ, że urządzenie TPM jest wyświetlane na karcie **Indywidualne rejestracje** z nazwą `registrationid` utworzoną wcześniej. 

    ![Potwierdzanie poprawności rejestracji urządzenia TPM w portalu](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykładową usługę Java, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu w języku Python na swojej maszynie.
1. Jeśli utworzono symulowane urządzenie TPM, zamknij okno symulatora modułu TPM.
1. Przejdź do usługi Device Provisioning w witrynie Azure Portal, kliknij pozycję **Zarządzanie rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne**. Wybierz *Identyfikator rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** w górnej części bloku.  


## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki start utworzono programowo wpis rejestracji indywidualnej dla urządzenia TPM oraz opcjonalnie na komputerze utworzono symulowane urządzenie TPM, które zaaprowizowano do Twojego centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
