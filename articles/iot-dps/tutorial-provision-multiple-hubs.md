---
title: Aprowizowanie urządzeń w wielu centrach IoT Hub z równoważeniem obciążenia przy użyciu usługi Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Automatyczne aprowizowanie urządzeń w wielu centrach IoT Hub przy użyciu usługi DPS w witrynie Azure Portal
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d0a3720fe729d5e260bbe5b0902460c8c7cfc7cb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629630"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Aprowizowanie urządzeń w centrach IoT Hub z równoważeniem obciążenia

W tym samouczku przedstawiono sposób aprowizacji urządzeń w wielu centrach IoT Hub z równoważeniem obciążenia przy użyciu usługi Device Provisioning Service (DPS). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aprowizowanie drugiego urządzenia w drugim centrum IoT Hub za pomocą witryny Azure Portal 
> * Dodawanie wpisu listy rejestracji na drugim urządzeniu
> * Ustawianie zasad alokacji usługi DPS na **dystrybucja z równymi wagami**
> * Łączenie nowego centrum IoT Hub z usługą DPS

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku wykorzystywane są informacje z poprzedniego samouczka, [Aprowizacja urządzenia w centrum](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Aprowizowanie drugiego urządzenia w drugim centrum IoT Hub za pomocą witryny Azure Portal

Wykonaj czynności opisane w samouczku [Aprowizacja urządzenia w centrum](tutorial-provision-device-to-hub.md), aby aprowizować drugie urządzenie w innym centrum IoT Hub.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Dodawanie wpisu listy rejestracji na drugim urządzeniu

Lista rejestracji wskazuje, jaka metoda zaświadczania (potwierdzania tożsamości urządzenia) powinna być używana w usłudze DPS w przypadku tego urządzenia. Następnym krokiem jest dodanie wpisu listy rejestracji dotyczącego drugiego urządzenia. 

1. Na stronie usługi DPS kliknij pozycję **Zarządzanie rejestracjami**. Zostanie wyświetlona strona **Dodawanie wpisu listy rejestracji**. 
2. Kliknij pozycję **Dodaj** w górnej części strony.
2. Wypełnij pola, a następnie kliknij przycisk **Zapisz**.

## <a name="set-the-dps-allocation-policy"></a>Ustawianie zasad alokacji usługi DPS

Zasady alokacji to ustawienie usługi DPS określające, w jaki sposób urządzenia są przypisane do centrum IoT Hub. Są obsługiwane trzy zasady alokacji: 

1. **Najmniejsze opóźnienie**: urządzenia są aprowizowane do centrum IoT z uwzględnieniem centrum z najmniejszym opóźnieniem do urządzenia.
2. **Dystrybucja z równymi wagami** (domyślnie): aprowizacja do każdego z połączonych centrów IoT jest jednakowo prawdopodobna. Jest to ustawienie domyślne. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie. 
3. **Konfiguracja statyczna za pośrednictwem listy rejestracji**: określenie żądanego centrum IoT na liście rejestracji ma wyższy priorytet niż zasady alokacji na poziomie usługi DPS.

Wykonaj następujące czynności, aby skonfigurować zasady alokacji:

1. Aby ustawić zasady alokacji, na stronie usługi DPS kliknij pozycję **Zarządzanie zasadami alokacji**.
2. Wybierz zasady alokacji **Dystrybucja z równymi wagami**.
3. Kliknij pozycję **Zapisz**.

## <a name="link-the-new-iot-hub-to-dps"></a>Łączenie nowego centrum IoT Hub z usługą DPS

Połącz usługę DPS z centrum IoT Hub, aby umożliwić rejestrowanie urządzeń w tym centrum przez usługę DPS.

1. Na stronie **Wszystkie zasoby** kliknij utworzone wcześniej wystąpienie usługi DPS.
2. Na stronie usługi DPS kliknij pozycję **Połączone centra IoT Hub**.
3. Kliknij pozycję **Add** (Dodaj).
4. Na stronie **Dodawanie linku do centrum IoT Hub** użyj przycisków opcji, aby określić, czy połączone centrum IoT znajduje się w bieżącej subskrypcji, czy w innej subskrypcji. Następnie wybierz nazwę centrum IoT w polu **Centrum IoT**.
5. Kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aprowizowanie drugiego urządzenia w drugim centrum IoT Hub za pomocą witryny Azure Portal 
> * Dodawanie wpisu listy rejestracji na drugim urządzeniu
> * Ustawianie zasad alokacji usługi DPS na **dystrybucja z równymi wagami**
> * Łączenie nowego centrum IoT Hub z usługą DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
