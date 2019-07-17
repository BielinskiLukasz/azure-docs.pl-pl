---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229299"
---
Aby utworzyć usługę IoT Hub przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.

    ![Wybierz, aby zainstalować usługę IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Aby utworzyć usługę IoT Hub z warstwą bezpłatną, użyj wartości z poniższych tabel:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subscription | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Resource group | Utwórz nową. W tym samouczku użyto nazwy **tutorials-iot-hub-rg**. |
    | Region | W tym samouczku jest używany region **Zachodnie stany USA**. Można wybrać region znajdujący się najbliżej. |
    | Name (Nazwa) | W poniższym zrzucie ekranu użyto nazwy **tutorials-iot-hub**. Podczas tworzenia centrum należy wybrać własną unikatową nazwę. |

    ![Ustawienia centrum 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Warstwa cenowa i warstwa skali | Bezpłatna F1. W ramach subskrypcji można mieć tylko jedno centrum z warstwą bezpłatną. |
    | Jednostki usługi IoT Hub | 1 |

    ![Ustawienia centrum 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Kliknij przycisk **Utwórz**. Tworzenie centrum może zająć kilka minut.

    ![Ustawienia centrum 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Zanotuj nazwę wybranego centrum IoT. Będzie potrzebna w dalszej części samouczka.
