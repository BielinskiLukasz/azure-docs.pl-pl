---
title: Testowanie i ponowne uczenie modelu — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przetestować obraz, a następnie użyć go do ponownego nauczenia modelu.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 3f78f0b992581a44b030387f1bd0e37664df4cfd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560910"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testowanie i ponowne uczenie modelu z Custom Vision Service

Po przeprowadzeniu szkolenia modelu można szybko przetestować go przy użyciu lokalnie przechowywanego obrazu lub obrazu w trybie online. Test korzysta z ostatnio przeszkolonej iteracji.

## <a name="test-your-model"></a>Testowanie modelu

1. Na [stronie sieci web Custom Vision](https://customvision.ai)wybierz projekt. Wybierz pozycję **szybka próba** po prawej stronie górnego paska menu. Ta akcja powoduje otwarcie okna z etykietą **szybki test**.

    ![Przycisk Szybki test jest wyświetlany w prawym górnym rogu okna.](./media/test-your-model/quick-test-button.png)

2. W oknie **szybkie testy** kliknij pole **Prześlij obraz** , a następnie wprowadź adres URL obrazu, który ma być używany dla testu. Jeśli zamiast tego chcesz użyć lokalnie przechowywanego obrazu, kliknij przycisk **Przeglądaj pliki lokalne** i wybierz plik obrazu lokalnego.

    ![Obraz strony przesyłania obrazu](./media/test-your-model/submit-image.png)

Wybrany obraz pojawia się w środku strony. Następnie wyniki są wyświetlane poniżej obrazu w postaci tabeli zawierającej dwie kolumny z etykietami i **pewnością**. Po wyświetleniu wyników możesz zamknąć okno **szybkie testy** .

Teraz możesz dodać ten obraz testowy do modelu, a następnie ponownie przeprowadzić uczenie modelu.

## <a name="use-the-predicted-image-for-training"></a>Używanie przewidywanego obrazu do szkolenia

Aby użyć obrazu przesłanego wcześniej do szkolenia, wykonaj następujące czynności:

1. Aby wyświetlić obrazy przesłane do klasyfikatora, Otwórz [stronę sieci web Custom Vision](https://customvision.ai) i wybierz kartę __przewidywania__ .

    ![Obraz karty przewidywania](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazy z bieżącej iteracji. Pole listy rozwijanej __iteracji__ służy do wyświetlania obrazów przesłanych podczas poprzednich iteracji.

2. Umieść kursor na obrazie, aby zobaczyć znaczniki, które zostały przewidziane przez klasyfikator.

    > [!TIP]
    > Obrazy są klasyfikowane, dzięki czemu obrazy, które mogą przynieść największe zyski do klasyfikatora, znajdują się u góry. Aby wybrać inne sortowanie, użyj sekcji __Sortuj__ .

    Aby dodać obraz do danych szkoleniowych, wybierz obraz, wybierz tag, a następnie wybierz pozycję __Zapisz i Zamknij__. Obraz jest usuwany z __prognoz__ i dodawany do obrazów szkoleniowych. Możesz ją wyświetlić, wybierając kartę __obrazy szkoleniowe__ .

    ![Obraz strony tagowania](./media/test-your-model/tag-image.png)

3. Użyj przycisku __uczenie__ , aby ponownie przeprowadzić szkolenie klasyfikatora.

## <a name="next-steps"></a>Kolejne kroki

[Ulepszanie klasyfikatora](getting-started-improving-your-classifier.md)
