---
title: Zarządzanie rejestracjami urządzeń za pomocą zestawów SDK usługi Azure DPS
description: Jak zarządzać rejestracjami urządzeń w IoT Hub Device Provisioning Service (DPS) przy użyciu zestawów SDK usługi
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975078"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Jak zarządzać rejestracjami urządzeń za pomocą zestawów SDK usługi Azure Device Provisioning
*Rejestracja urządzenia* powoduje utworzenie rekordu jednego urządzenia lub grupy urządzeń, które mogą zostać zarejestrowane w ramach usługi Device Provisioning. Rekord rejestracji zawiera początkową wymaganą konfigurację dla urządzeń w ramach rejestracji, łącznie z żądanym Centrum IoT Hub. W tym artykule opisano sposób programowego zarządzania rejestracjami urządzeń dla usługi aprowizacji przy użyciu zestawów SDK usługi aprowizacji Azure IoT.  Zestawy SDK są dostępne w witrynie GitHub w tym samym repozytorium co zestawy SDK usługi Azure IoT.

## <a name="prerequisites"></a>Wymagania wstępne
* Uzyskaj parametry połączenia z wystąpienia usługi Device Provisioning.
* Uzyskaj artefakty zabezpieczeń urządzenia dla używanego [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) :
    * [**Moduł TPM (TPM)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Rejestracja indywidualna: Identyfikator rejestracji i klucz poręczenia modułu TPM z urządzenia fizycznego lub symulatora modułu TPM.
        * Grupa rejestracji nie ma zastosowania do zaświadczania modułu TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Rejestracja indywidualna: [certyfikat liścia](/azure/iot-dps/concepts-security) z urządzenia fizycznego lub z emulatora [indeksu](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) zestawu SDK.
        * Grupa rejestracji: [urząd certyfikacji/certyfikat główny](/azure/iot-dps/concepts-security#root-certificate) lub [certyfikat pośredni](/azure/iot-dps/concepts-security#intermediate-certificate)używany do tworzenia certyfikatu urządzenia na urządzeniu fizycznym.  Można go również generować z emulatora indeksu zestawu SDK.
* Dokładne wywołania interfejsu API mogą być różne z powodu różnic w języku. Zapoznaj się z przykładami w witrynie GitHub, aby uzyskać szczegółowe informacje:
   * [Przykłady klienta usługi aprowizacji Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Przykłady klienta usługi aprowizacji środowiska Node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Przykłady klienta usługi aprowizacji platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń
Istnieją dwa sposoby rejestrowania urządzeń przy użyciu usługi aprowizacji:

* **Grupa rejestracji** to wpis dla grupy urządzeń, który udostępnia wspólny mechanizm zaświadczania certyfikatów X. 509 podpisany przez [certyfikat główny](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikat pośredni](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Zalecamy używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową, lub dla urządzeń, które przechodzą do tej samej dzierżawy. Należy pamiętać, że można rejestrować tylko urządzenia korzystające z mechanizmu zaświadczania X. 509 jako *grupy rejestracji*. 

    Grupę rejestracji można utworzyć przy użyciu zestawów SDK następujących po tym przepływie pracy:

    1. W przypadku grupy rejestracji mechanizm zaświadczania używa certyfikatu głównego X. 509.  Interfejs API zestawu SDK usługi wywołań ```X509Attestation.createFromRootCertificate``` z certyfikatem głównym, aby utworzyć zaświadczanie do rejestracji.  Certyfikat główny X. 509 znajduje się w pliku PEM lub w postaci ciągu.
    1. Utwórz nową zmienną ```EnrollmentGroup``` przy użyciu ```attestation``` utworzonego i unikatowego ```enrollmentGroupId```.  Opcjonalnie można ustawić parametry takie jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Interfejs API zestawu SDK usługi wywołań ```createOrUpdateEnrollmentGroup``` w aplikacji zaplecza z ```EnrollmentGroup```, aby utworzyć grupę rejestracji.

* **Rejestracja indywidualna** to wpis dla jednego urządzenia, które może się zarejestrować. Rejestracje indywidualne mogą używać certyfikatów X. 509 lub tokenów SAS (z poziomu fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Zalecamy używanie indywidualnych rejestracji dla urządzeń, które wymagają unikatowej konfiguracji początkowej, lub dla urządzeń, które mogą używać tylko tokenów SAS za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Można utworzyć rejestrację indywidualną przy użyciu zestawów SDK następujących po tym przepływie pracy:
    
    1. Wybierz mechanizm ```attestation```, który może być modułem TPM lub X. 509.
        1. **Moduł TPM**: przy użyciu klucza poręczenia z urządzenia fizycznego lub symulatora modułu TPM jako dane wejściowe można wywołać interfejs API usługi Service SDK ```TpmAttestation```, aby utworzyć zaświadczanie do rejestracji. 
        2. **X. 509**: przy użyciu certyfikatu klienta jako dane wejściowe można wywołać interfejs API usługi Service SDK ```X509Attestation.createFromClientCertificate```, aby utworzyć zaświadczanie do rejestracji.
    2. Utwórz nową zmienną ```IndividualEnrollment``` przy użyciu ```attestation``` utworzonych i unikatowych ```registrationId``` jako dane wejściowe, które są na urządzeniu lub wygenerowane z symulatora modułu TPM.  Opcjonalnie można ustawić parametry takie jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Interfejs API zestawu SDK usługi wywołań ```createOrUpdateIndividualEnrollment``` w aplikacji zaplecza z ```IndividualEnrollment```, aby utworzyć rejestrację indywidualną.

Po pomyślnym utworzeniu rejestracji usługa Device Provisioning zwraca wynik rejestracji. Ten przepływ pracy jest przedstawiony w pokazanych [powyżej](#prerequisites)przykładach.

## <a name="update-an-enrollment-entry"></a>Aktualizowanie wpisu rejestracji

Po utworzeniu wpisu rejestracji warto zaktualizować rejestrację.  Potencjalne scenariusze obejmują aktualizowanie żądanej właściwości, aktualizowanie metody zaświadczania lub odwoływanie dostępu do urządzenia.  Istnieją różne interfejsy API dla poszczególnych rejestracji i rejestracji grup, ale bez rozróżniania mechanizmu zaświadczania.

Wpis rejestracji można zaktualizować po tym przepływie pracy:
* **Rejestracja indywidualna**:
    1. Pobierz najnowszą rejestrację z usługi aprowizacji najpierw za pomocą interfejsu API usługi Service SDK ```getIndividualEnrollment```.
    2. W razie potrzeby zmodyfikuj parametr najnowszej rejestracji. 
    3. Przy użyciu najnowszej rejestracji ```createOrUpdateIndividualEnrollment``` interfejs API zestawu SDK wywołania, aby zaktualizować wpis rejestracyjny.
* **Rejestracja grupy**:
    1. Pobierz najnowszą rejestrację z usługi aprowizacji najpierw za pomocą interfejsu API usługi Service SDK ```getEnrollmentGroup```.
    2. W razie potrzeby zmodyfikuj parametr najnowszej rejestracji.
    3. Przy użyciu najnowszej rejestracji ```createOrUpdateEnrollmentGroup``` interfejs API zestawu SDK wywołania, aby zaktualizować wpis rejestracyjny.

Ten przepływ pracy jest przedstawiony w pokazanych [powyżej](#prerequisites)przykładach.

## <a name="remove-an-enrollment-entry"></a>Usuwanie wpisu rejestracji

* Każdą **rejestrację** można usunąć za pomocą interfejsu API zestawu SDK usługi wywołującej ```deleteIndividualEnrollment``` przy użyciu ```registrationId```.
* **Rejestrację grup** można usunąć, korzystając z interfejsu API zestawu SDK usługi wywołującej ```deleteEnrollmentGroup``` przy użyciu ```enrollmentGroupId```.

Ten przepływ pracy jest przedstawiony w pokazanych [powyżej](#prerequisites)przykładach.

## <a name="bulk-operation-on-individual-enrollments"></a>Operacja zbiorcza dla indywidualnych rejestracji

Można wykonać operację zbiorczą w celu utworzenia, zaktualizowania lub usunięcia wielu rejestracji pojedynczych po tym przepływie pracy:

1. Utwórz zmienną, która zawiera wiele ```IndividualEnrollment```.  Implementacja tej zmiennej jest inna dla każdego języka.  Aby uzyskać szczegółowe informacje, zapoznaj się z przykładem operacji zbiorczej w witrynie GitHub.
2. Interfejs API zestawu SDK usługi wywołań ```runBulkOperation``` z ```BulkOperationMode``` dla żądanej operacji i zmiennej dla indywidualnych rejestracji. Obsługiwane są cztery tryby: Create, Update, updateIfMatchEtag i DELETE.

Po pomyślnym wykonaniu operacji usługa Device provisioner zwróci wynik operacji zbiorczej.

Ten przepływ pracy jest przedstawiony w pokazanych [powyżej](#prerequisites)przykładach.
