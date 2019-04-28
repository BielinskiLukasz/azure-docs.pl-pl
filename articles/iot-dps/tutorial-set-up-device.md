---
title: Konfigurowanie urządzenia na potrzeby usługi Azure IoT Hub Device Provisioning
description: Konfigurowanie urządzenia na potrzeby aprowizacji za pośrednictwem usługi IoT Hub Device Provisioning w procesie jego produkcji
author: wesmc7777
ms.author: v-yiso
origin.date: 04/10/2019
ms.date: 05/06/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 344cc3b8ba3f7698f5124d464f3c277b6cb5cdde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61249092"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Konfigurowanie urządzenia na potrzeby aprowizacji przy użyciu usługi Azure IoT Hub Device Provisioning

W poprzednim samouczku przedstawiono sposób konfigurowania usługi Azure IoT Hub Device Provisioning w celu automatycznej aprowizacji urządzeń w centrum IoT. W tym samouczku pokazano, jak skonfigurować urządzenie w procesie jego produkcji, aby mogło być automatycznie aprowizowane w usłudze IoT Hub. Urządzenie jest aprowizowane w oparciu o jego [mechanizm zaświadczania](concepts-device.md#attestation-mechanism) po pierwszym uruchomieniu i nawiązaniu połączenia z usługą aprowizowania. Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Kompilowanie specyficznego dla platformy zestawu SDK klienta usługi Device Provisioning
> * Wyodrębnianie artefaktów zabezpieczeń
> * Tworzenie oprogramowania do rejestracji urządzenia

W tym samouczku przyjęto założenie, że utworzono już wystąpienie usługi Device Provisioning i centrum IoT, korzystając z instrukcji znajdujących się w poprzednim samouczku [Konfigurowanie zasobów w chmurze](tutorial-set-up-cloud.md).

W tym samouczku używane jest [repozytorium zestawów Azure IoT SDK i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c), które zawiera zestaw SDK klienta usługi Device Provisioning dla języka C. Ten zestaw SDK aktualnie obsługuje implementacje systemów Windows i Ubuntu na urządzeniach z modułami TPM i X.509. Ten samouczek bazuje na kliencie deweloperskim systemu Windows. Przyjęto w nim również założenie, że masz podstawowe umiejętności obsługi programu Visual Studio 2017. 

Jeśli nie znasz procesu automatycznego aprowizowania, przed kontynuowaniem zapoznaj się z tematem [Auto-provisioning concepts](concepts-auto-provisioning.md) (Pojęcia związane z automatycznym aprowizowaniem). 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2015 lub [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).



## <a name="build-a-platform-specific-version-of-the-sdk"></a>Kompilowanie specyficznej dla platformy wersji zestawu SDK

Zestaw SDK klienta usługi Device Provisioning ułatwia zaimplementowanie oprogramowania do rejestracji urządzenia. Jednak zanim będzie można go użyć, należy skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego i mechanizmu zaświadczania. W tym samouczku skompilujesz zestaw SDK korzystający z programu Visual Studio 2017 na platformie deweloperskiej systemu Windows dla obsługiwanego typu zaświadczania:

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.


1. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Skompiluj zestaw SDK dla platformy deweloperskiej w oparciu o mechanizmy zaświadczania, które będą używane. Użyj jednego z następujących poleceń (zwróć uwagę na dwa końcowe znaki kropki po każdym poleceniu). Po zakończeniu program CMake utworzy podkatalog `/cmake` z zawartością specyficzną dla urządzenia:
 
    - Urządzenia korzystające z symulatora modułu TPM na potrzeby zaświadczania:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Wszystkie inne urządzenia (korzystające z fizycznego modułu TPM/HSM/X.509 lub symulowanego certyfikatu X.509):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Teraz możesz użyć zestawu SDK do utworzenia kodu rejestracji urządzenia. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Wyodrębnianie artefaktów zabezpieczeń 

Następnym krokiem jest wyodrębnienie artefaktów zabezpieczeń na potrzeby mechanizmu zaświadczania używanego przez urządzenie. 

### <a name="physical-devices"></a>Urządzenia fizyczne 

W zależności od tego, czy kompilowany zestaw SDK ma być używany na potrzeby zaświadczania za pomocą fizycznego modułu TPM/HSM, czy certyfikatów X.509, zbieranie artefaktów zabezpieczeń przebiega następująco:

- Dla urządzenia z modułem TPM należy określić **klucz poręczenia** skojarzony z tym urządzeniem. Można go uzyskać od producenta modułu TPM. Unikatowy **identyfikator rejestracji** urządzenia z modułem TPM można uzyskać przez utworzenie skrótu klucza poręczenia.  

- W przypadku urządzenia X.509 należy uzyskać certyfikaty wystawione dla tego urządzenia. Usługa aprowizowania udostępnia dwa rodzaje wpisów rejestracji, które sterują dostępem do urządzeń przy użyciu mechanizmu zaświadczania X.509. Wymagane certyfikaty zależą od używanych typów rejestracji.

    1. Rejestracje indywidualne: Rejestrowanie dla pojedynczego określonego urządzenia. Ten typ wpisu rejestracji wymaga [certyfikatów jednostki końcowej „liścia”](concepts-security.md#end-entity-leaf-certificate).
    1. Grupy rejestracji: Ten typ wpisu rejestracji wymaga pośrednie lub certyfikaty główne. Aby dowiedzieć się więcej, zobacz [Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Symulowane urządzenia

W zależności od tego, czy kompilowany zestaw SDK ma być używany na potrzeby zaświadczania symulowanego urządzenia za pomocą certyfikatów TPM, czy certyfikatów X.509, zbieranie artefaktów zabezpieczeń przebiega następująco:

- W przypadku symulowanego urządzenia z modułem TPM:

   1. Otwórz wiersz polecenia systemu Windows, przejdź do podkatalogu `azure-iot-sdk-c` i uruchom symulator modułu TPM. Nasłuchuje on przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; będzie ono potrzebne, aby ten symulator działał do czasu zakończenia następnego przewodnika Szybki start. 

      Z poziomu podkatalogu `azure-iot-sdk-c` wpisz następujące polecenie, aby uruchomić symulator:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Jeśli na potrzeby tego kroku używasz wiersza polecenia powłoki Git Bash, konieczne będzie zamienienie ukośników odwrotnych na ukośniki, na przykład: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Za pomocą programu Visual Studio otwórz wygenerowane w folderze *cmake* rozwiązanie o nazwie `azure_iot_sdks.sln` i skompiluj go za pomocą polecenia „Kompiluj rozwiązanie” w menu „Kompilacja”.

   1. W okienku *Eksplorator rozwiązań* w programie Visual Studio przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**. 

   1. Uruchom rozwiązanie za pomocą dowolnego polecenia „Uruchom” w menu „Debugowanie”. W oknie danych wyjściowych zostaną wyświetlone wartości **_Identyfikator rejestracji_** i **_Klucz poręczenia_** symulatora modułu TPM potrzebne do zarejestrowania urządzenia. Skopiuj te wartości w celu późniejszego użycia. Możesz zamknąć to okno (z identyfikatorem rejestracji i kluczem poręczenia), ale pozostaw otwarte okno symulatora modułu TPM uruchomione w kroku 1.

- W przypadku symulowanego urządzenia z certyfikatem X.509:

  1. Za pomocą programu Visual Studio otwórz wygenerowane w folderze *cmake* rozwiązanie o nazwie `azure_iot_sdks.sln` i skompiluj go za pomocą polecenia „Kompiluj rozwiązanie” w menu „Kompilacja”.

  1. W okienku *Eksplorator rozwiązań* w programie Visual Studio przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **dice\_device\_enrollment** i wybierz pozycję **Ustaw jako projekt startowy**. 
  
  1. Uruchom rozwiązanie za pomocą dowolnego polecenia „Uruchom” w menu „Debugowanie”. W oknie danych wyjściowych po wyświetleniu monitu wprowadź wartość **i**, aby przeprowadzić rejestrację indywidualną. W oknie danych wyjściowych zostanie wyświetlony lokalnie wygenerowany certyfikat X.509 dla symulowanego urządzenia. Skopiuj do schowka dane wyjściowe rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się pierwszym wierszem *-----END CERTIFICATE-----*, a następnie upewnij się, że oba te wiersze również zostały skopiowane. Wymagany jest tylko pierwszy certyfikat z okna danych wyjściowych.
 
  1. Utwórz plik o nazwie **_X509testcert.pem_**, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik, ponieważ będzie on używany później do rejestrowania urządzenia. Po uruchomieniu oprogramowanie do rejestracji używa tego samego certyfikatu podczas automatycznego aprowizowania.    

Te artefakty zabezpieczeń są wymagane podczas rejestracji urządzenia w usłudze Device Provisioning. Usługa aprowizacji czeka, aż urządzenie się uruchomi, i łączy się z nim w późniejszym czasie. Podczas pierwszego uruchomienia urządzenia logika zestawu SDK klienta wchodzi w interakcje z modułem (lub symulatorem) w celu wyodrębnienia z urządzenia artefaktów zabezpieczeń i weryfikuje rejestrację przy użyciu usługi Device Provisioning. 

## <a name="create-the-device-registration-software"></a>Tworzenie oprogramowania do rejestracji urządzenia

Ostatnim krokiem jest napisanie aplikacji do rejestracji, która będzie korzystała z zestawu SDK klienta usługi Device Provisioning w celu zarejestrowania urządzenia w usłudze IoT Hub. 

> [!NOTE]
> W tym kroku przyjęto założenie, że używane jest urządzenie symulowane, a do jego obsługi na stacji roboczej uruchomiono przykładową aplikację do rejestracji z zestawu SDK. Podobne procedury mają jednak także zastosowanie podczas kompilowania aplikacji do rejestracji na potrzeby wdrażania na urządzeniu fizycznym. 

1. W witrynie Azure Portal wybierz blok **Przegląd** dla swojej usługi Device Provisioning, a następnie skopiuj wartość **_Identyfikator zakresu_**. *Identyfikator zakresu* jest generowany przez usługę i gwarantuje unikatowość. Jest on niezmienny i używany do jednoznacznego identyfikowania identyfikatorów rejestracji.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. W *Eksploratorze rozwiązań* programu Visual Studio na Twojej maszynie przejdź do folderu **Provision\_Samples**. Wybierz przykładowy projekt o nazwie **prov\_dev\_client\_sample** i otwórz plik źródłowy **prov\_dev\_client\_sample.c**.

1. Przypisz wartość _identyfikatora zakresu_ uzyskaną w kroku 1 do zmiennej `id_scope` (po usunięciu lewego /`[` i prawego /`]` nawiasu): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Zmienna `global_prov_uri` umożliwia interfejsowi API klienta usługi IoT Hub `IoTHubClient_LL_CreateFromDeviceAuth` nawiązywanie połączenia z wyznaczonym wystąpieniem usługi Device Provisioning.

1. W tym samym pliku, w funkcji **main()** dodaj lub usuń znaczniki komentarza przy zmiennej `hsm_type`, aby określić odpowiedni mechanizm zaświadczania używany przez oprogramowanie do rejestracji urządzenia (TPM lub X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Zapisz zmiany i skompiluj ponownie przykład **prov\_dev\_client\_sample**, wybierając polecenie „Kompiluj rozwiązanie” w menu „Kompilacja”. 

1. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample** w folderze **Provision\_Samples**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. NIE uruchamiaj jeszcze przykładowej aplikacji.

> [!IMPORTANT]
> Nie należy jeszcze uruchamiać urządzenia! Przed jego uruchomieniem należy zakończyć proces, rejestrując urządzenie przy użyciu usługi Device Provisioning. W znajdującej się poniżej sekcji Następne kroki zamieszczono wskazówki dotyczące przejścia do następnego artykułu.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>Interfejsy API zestawu SDK używane podczas rejestracji (tylko do celów referencyjnych)

Do celów referencyjnych zestaw SDK udostępnia następujące interfejsy API dla aplikacji do użycia podczas rejestracji. Podczas uruchamiania urządzenia te interfejsy API ułatwiają urządzeniu nawiązywanie połączenia z usługą Device Provisioning i rejestrowanie się w niej. W zamian urządzenie otrzymuje informacje wymagane do nawiązania połączenia z wystąpieniem usługi IoT Hub:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Jeśli zajdzie potrzeba dostrojenia aplikacji do rejestracji klienta usługi Device Provisioning, można najpierw użyć urządzenia symulowanego i testowej konfiguracji usługi. Jeśli aplikacja będzie działać w środowisku testowym, można ją skompilować na potrzeby konkretnego urządzenia i skopiować plik wykonywalny do obrazu urządzenia. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Na tym etapie w portalu prawdopodobnie działają usługi Device Provisioning i IoT Hub. Jeśli nie potrzebujesz konfiguracji aprowizacji urządzeń i/lub chcesz później ukończyć tę serię samouczków, zalecamy ich zamknięcie, aby uniknąć ponoszenia niepotrzebnych kosztów.

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  

## <a name="next-steps"></a>Kolejne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kompilowanie specyficznego dla platformy zestawu SDK klienta usługi Device Provisioning
> * Wyodrębnianie artefaktów zabezpieczeń
> * Tworzenie oprogramowania do rejestracji urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować urządzenie w centrum IoT, rejestrując je w usłudze Azure IoT Hub Device Provisioning na potrzeby automatycznego aprowizowania.

> [!div class="nextstepaction"]
> [Aprowizacja urządzenia w centrum IoT](tutorial-provision-device-to-hub.md)

