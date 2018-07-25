---
title: Konfigurowanie aprowizowania urządzeń w witrynie Azure Portal | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — Konfigurowanie usługi Azure IoT Hub Device Provisioning Service w witrynie Azure Portal
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5509027b9c41a021ce8ab5dd468627bd8307d354
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036425"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Konfigurowanie usługi IoT Hub Device Provisioning (wersja zapoznawcza) w witrynie Azure Portal

W tych krokach pokazano, jak w portalu skonfigurować zasoby platformy Azure w chmurze na potrzeby aprowizowania posiadanych urządzeń. Ten artykuł zawiera instrukcje tworzenia centrum IoT Hub, tworzenia nowego wystąpienia usługi IoT Hub Device Provisioning Service oraz łączenia obu usług ze sobą. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Tworzenie nowego wystąpienia dla usługi IoT Hub Device Provisioning Service

1. Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.

2. *Wyszukaj w witrynie Marketplace* **usługę Device Provisioning Service**. Wybierz pozycję **Usługa IoT Hub Device Provisioning** i kliknij przycisk **Utwórz**. 

3. Podaj następujące informacje dotyczące nowego wystąpienia usługi Device Provisioning Service, a następnie kliknij pozycję **Utwórz**.

    * **Nazwa:** podaj unikatową nazwę nowego wystąpienia usługi Device Provisioning Service. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.
    * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego wystąpienia usługi Device Provisioning Service.
    * **Grupa zasobów:** w tym polu możesz utworzyć nową grupę zasobów lub wybrać istniejącą grupę, w której ma znajdować się nowe wystąpienie. Wybierz tę grupę zasobów, w której znajduje się utworzone wcześniej centrum IoT Hub, na przykład **TestResources**. Dzięki umieszczeniu wszystkich powiązanych zasobów w jednej grupie można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w tej grupie. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/resource-group-portal.md).
    * **Lokalizacja**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.
    * **Przypnij do pulpitu nawigacyjnego:** wybierz tę opcję, aby przypiąć to wystąpienie do pulpitu nawigacyjnego, co ułatwi jego znalezienie.

    ![Wprowadzanie podstawowych informacji o wystąpieniu usługi DPS w bloku portalu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Po pomyślnym wdrożeniu usługi automatycznie zostanie otwarty blok podsumowania tej usługi.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Połącz centrum IoT ze swoją usługą Device Provisioning Service

W tej sekcji dodasz konfigurację wystąpienia usługi Device Provisioning Service. Ta konfiguracja określa centrum IoT Hub, dla którego będą aprowizowane urządzenia.

1. Kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie witryny Azure Portal. Wybierz wystąpienie usługi Device Provisioning Service utworzone we wcześniejszej sekcji.  

2. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Połączone centra IoT**. Kliknij przycisk **+ Dodaj** widoczny u góry. 

3. Na stronie **Dodawanie linku do centrum IoT Hub** wprowadź następujące informacje, aby połączyć nowe wystąpienie usługi Device Provisioning Service z centrum IoT Hub. Następnie kliknij przycisk **Zapisz**. 

    * **Subskrypcja:** wybierz subskrypcję zawierającą centrum IoT Hub, które chcesz połączyć z nowym wystąpieniem usługi Device Provisioning Service.
    * **Centrum IoT Hub:** wybierz centrum IoT Hub, które chcesz połączyć z nowym wystąpieniem usługi Device Provisioning Service.
    * **Zasady dostępu:** wybierz **iothubowner** jako poświadczenia do nawiązania połączenia z centrum IoT Hub.  

    ![Powiązywanie nazwy centrum z wystąpieniem usługi DPS w bloku portalu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Teraz wybrane centrum powinno być widoczne w bloku **Powiązane centra IoT**. Aby wyświetlić blok **Połączone centra IoT Hub**, może być konieczne kliknięcie przycisku **Odśwież**.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start lub samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  
2. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. U góry bloku **Wszystkie zasoby** kliknij pozycję **Usuń**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zostały wdrożone centrum IoT i wystąpienie usługi Device Provisioning Service, po czym te dwa zasoby zostały połączone ze sobą. Aby dowiedzieć się, jak za pomocą tej konfiguracji zaaprowizować symulowane urządzenie, przejdź do przewodnika Szybki start omawiającego tworzenie symulowanego urządzenia.

> [!div class="nextstepaction"]
> [Przewodnik Szybki start z tworzeniem symulowanego urządzenia](./quick-create-simulated-device.md)
