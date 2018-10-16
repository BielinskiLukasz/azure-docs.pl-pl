---
title: Aprowizowanie symulowanego urządzenia TPM dla usługi Azure IoT Hub przy użyciu języka C | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz i aprowizujesz symulowane urządzenie TPM za pomocą zestawu SDK języka C dla usługi Azure IoT Hub Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 07/13/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3f88da7e70ca62e14444fa742013c982daec3cc6
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633277"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Szybki start: aprowizowanie symulowanego urządzenia TPM za pomocą zestawu SDK języka C usługi Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Dzięki temu przewodnikowi Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia modułu TPM na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie połączysz z usługą IoT Hub przy użyciu wystąpienia usługi Device Provisioning. Przykładowy kod z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) będzie używany w celu sprawniejszego rejestrowania urządzenia z wystąpieniem usługi Device Provisioning i symulowania sekwencji rozruchu dla tego urządzenia.

Jeśli nie znasz procesu automatycznego aprowizowania, zapoznaj się z tematem [Auto-provisioning concepts](concepts-auto-provisioning.md) (Pojęcia związane z automatycznym aprowizowaniem). Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2015 lub [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) i przykładowy symulator urządzenia [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview).

1. Pobierz wersję 3.11.4 [systemu kompilacji CMake](https://cmake.org/download/). Sprawdź pobrane dane binarne przy użyciu odpowiedniej wartości skrótu kryptograficznego. W poniższym przykładzie użyto programu Windows PowerShell do sprawdzenia wartości skrótu kryptograficznego dla wersji dystrybucji MSI 3.11.4 x64:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Następujące wartości skrótu dla wersji 3.11.4 były wymienione w witrynie narzędzia CMake w momencie pisania tego dokumentu:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Rozmiar tego repozytorium wynosi obecnie około 220 MB. Należy się spodziewać, że ukończenie operacji potrwa kilka minut.


3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Skompiluj zestaw SDK i uruchom symulator urządzenia modułu TPM

W tej sekcji skompilujesz zestaw SDK języka C usługi Azure IoT, który zawiera przykładowy kod symulatora urządzenia TPM. W tym przykładzie przedstawiono [mechanizm zaświadczania](concepts-security.md#attestation-mechanism) modułu TPM za pośrednictwem uwierzytelniania tokenu sygnatury dostępu współdzielonego (SAS).

1. Z podkatalogu `cmake` utworzonego w repozytorium Git azure-iot-sdk-c uruchom następujące polecenie, aby skompilować przykładowy kod. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie również wygenerowane przez to polecenie kompilacji.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Przejdź do folderu głównego sklonowanego repozytorium git i uruchom symulator [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) przy użyciu ścieżki pokazanej poniżej. Ten symulator nasłuchuje przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; będzie ono potrzebne, aby ten symulator działał do czasu zakończenia tego przewodnika Szybki start. 

   Jeśli znajdujesz się w folderze *cmake*, uruchom następujące polecenia:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Nie będą widoczne żadne dane wyjściowe z symulatora. Pozwól mu kontynuować pracę i symulowanie urządzenia TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Odczytywanie kluczy kryptograficznych z urządzenia TPM

W tej sekcji skompilujesz i wykonasz przykładowy kod, który odczyta klucz poręczenia i identyfikator rejestracji z symulatora modułu TPM, który pozostał uruchomiony i nasłuchuje na portach 2321 i 2322. Te wartości będą używane do rejestracji urządzenia z wystąpieniem usługi Device Provisioning Service.

1. Uruchom program Visual Studio i otwórz nowy plik rozwiązania o nazwie `azure_iot_sdks.sln`. Ten plik rozwiązania znajduje się w folderze `cmake` utworzonym wcześniej w katalogu głównym repozytorium Git azure-iot-sdk-c.

2. Z menu programu Visual Studio wybierz kolejno polecenia **Kompiluj** > **Kompiluj rozwiązanie**, aby skompilować wszystkie projekty w rozwiązaniu.

3. W oknie programu Visual Studio *Eksplorator rozwiązań* przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**. 

4. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. Aplikacja odczytuje i wyświetla **_identyfikator rejestracji_** oraz **_klucz poręczenia_**. Skopiuj te wartości. Będą one używane w następnej sekcji do rejestracji urządzenia. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Tworzenie wpisu rejestracji urządzenia w portalu

1. Zaloguj się w witrynie Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoją usługę Device Provisioning.

2. Wybierz kartę **Zarządzaj rejestracjami**, następnie kliknij przycisk **Dodaj rejestrację indywidualną** u góry strony. 

3. W obszarze **Dodaj rejestrację** wprowadź następujące informacje i kliknij przycisk **Zapisz**.

    - **Mechanizm:** wybierz **TPM** jako *Mechanizm* poświadczania tożsamości.
    - **Klucz poręczenia:** wprowadź *klucz poręczenia* wygenerowany dla urządzenia TPM, uruchamiając projekt *tpm_device_provision*.
    - **Identyfikator rejestracji:** wprowadź *identyfikator rejestracji* wygenerowany dla urządzenia TPM, uruchamiając projekt *tpm_device_provision*.
    - **Urządzenie usługi IoT Edge:** wybierz opcję **Wyłącz**.
    - **Identyfikator urządzenia usługi IoT Hub:** wprowadź ciąg **test-docs-device**, aby nadać urządzeniu identyfikator.

    ![Wprowadzanie informacji o rejestracji urządzenia w portalu](./media/quick-create-simulated-device/enter-device-enrollment.png)  

    Po pomyślnej rejestracji *Identyfikator rejestracji* Twojego urządzenia pojawi się na liście na karcie *Indywidualne rejestracje*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

W tej sekcji skonfigurujesz przykładowy kod w celu używania protokołu [AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) do wysyłania sekwencji rozruchu urządzenia do wystąpienia usługi Device Provisioning Service. Ta sekwencja uruchamiania spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub połączonego z wystąpieniem usługi Device Provisioning Service.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning, a następnie skopiuj wartość **_Identyfikator zakresu_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z portalu](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

3. W górnej części pliku znajdź instrukcje `#define` dla każdego protokołu urządzenia, jak pokazano poniżej. Upewnij się, że tylko `SAMPLE_AMQP` jest pozbawiony komentarza.

    Obecnie [protokół MQTT nie jest obsługiwana w przypadku rejestracji indywidualnej urządzenia TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_TPM`, a nie `SECURE_DEVICE_TYPE_X509`, jak pokazano poniżej.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

7. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia aprowizowanego urządzenia klienta oraz podłączenia do aprowizowanego wystąpienia usługi Device Provisioning Service w celu uzyskania informacji na temat usługi IoT Hub i jej rejestracji:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Po aprowizacji symulowanego urządzenia w centrum IoT Hub przez Twoją usługę aprowizacji identyfikator urządzenia będzie widoczny w bloku **Urządzenia IoT** centrum. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszą pracę z przykładem klienta urządzenia i eksplorowanie go, nie czyść zasobów utworzonych w ramach tego przewodnika Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. Zamknij okno symulatora modułu TPM na swojej maszynie.
3. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. Otwórz obszar **Zarządzanie rejestracjami** dla usługi, a następnie kliknij kartę **Rejestracje indywidualne**. Wybierz *IDENTYFIKATOR REJESTRACJI* urządzenia zarejestrowanego w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** u góry. 
4. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. Otwórz blok **Urządzenia IoT** w centrum, wybierz *IDENTYFIKATOR URZĄDZENIA* zarejestrowanego w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** u góry.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start na Twojej maszynie zostało utworzone symulowane urządzenie TPM, które zostało zaaprowizowane do Twojego centrum IoT przy użyciu usługi IoT Hub Device Provisioning. Aby dowiedzieć się, jak zarejestrować urządzenie TPM programowo, przejdź do przewodnika Szybki start dotyczącego programowej rejestracji urządzenia TPM. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — rejestrowanie urządzenia TPM w usłudze Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-java.md)

