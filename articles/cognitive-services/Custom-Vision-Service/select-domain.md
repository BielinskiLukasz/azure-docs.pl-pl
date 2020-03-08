---
title: Wybierz domenę dla Custom Vision projektu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób wybierania domeny dla projektu w Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899452"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Wybierz domenę dla projektu Custom Vision

W bloku ustawienia dla projektu Custom Vision możesz wybrać domenę dla projektu. Wybierz domenę znajdującą się najbliżej Twojego scenariusza.

## <a name="image-classification"></a>Klasyfikacja obrazów

|Domain|Przeznaczenie|
|---|---|
|__Ogólnego__| Optymalizacja pod kątem szerokiego zakresu zadań klasyfikacji obrazów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną.|
|__Żywności__|Optymalizacja pod kątem zdjęć naczyń w postaci widocznej w menu restauracji. Jeśli chcesz sklasyfikować fotografie poszczególnych owoców lub warzyw, użyj domeny żywności.|
|__Charakterystycznych elementów krajobrazu__|Optymalizacja pod kątem rozpoznawalnych terenów, zarówno naturalnych, jak i sztucznej. Ta domena działa najlepiej, gdy punkt orientacyjny jest jasno widoczny na zdjęciu. Ta domena działa nawet wtedy, gdy punkt orientacyjny jest nieco przesunięty przez osoby przed nim.|
|__Stępują__|Optymalizacja pod kątem obrazów znajdujących się w katalogu zakupów lub witrynie internetowej do kupowania. Jeśli potrzebujesz wysokiej dokładności klasyfikowania między Dresses, Pants i koszulami, Użyj tej domeny.|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń klasyfikacji w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="object-detection"></a>Wykrywanie obiektów

|Domain|Przeznaczenie|
|---|---|
|__Ogólne__| Optymalizacja pod kątem szerokiego zakresu zadań wykrywania obiektów. Jeśli żadna z pozostałych domen nie jest odpowiednia lub nie masz pewności, którą domenę wybrać, wybierz domenę generyczną.|
|__Znaku__|Optymalizacja pod kątem znajdowania logo marki w obrazach.|
|__Produkty na półkach__|Optymalizacja pod kątem wykrywania i klasyfikowania produktów na półkach.|
|__Domeny kompaktowe__| Optymalizacja pod kątem ograniczeń wykrywania obiektów w czasie rzeczywistym na urządzeniach brzegowych.|

## <a name="compact-domains"></a>Domeny kompaktowe

Modele generowane przez domeny kompaktowe mogą być eksportowane do lokalnego uruchamiania. Wydajność modelu różni się w zależności od wybranej domeny. W poniższej tabeli zgłaszamy rozmiar modelu i czas wnioskowania na procesorach Intel Desktop CPU i NVidia GPU \[1\]. 

> [!NOTE]
> Te numery nie obejmują przetwarzania wstępnego i czasu dostosujesz.

|Zadanie|Domain|Rozmiar modelu|Czas wnioskowania procesora CPU|Czas wnioskowania procesora GPU|
|---|---|---|---|---|
|Klasyfikacja|Ogólne (Compact)|5 MB|13 MS|5 ms|
|Wykrywanie obiektów|Ogólne (Compact)|45 MB|35 MS|5 ms|
|Wykrywanie obiektów|Ogólne (kompaktowe) [S1]|14 MB|27 MS|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision Kit

W przypadku wybrania domeny kompaktowej dostępna jest dodatkowa opcja "możliwości eksportu" umożliwiająca rozróżnienie między elementami "podstawowe platformy" i "wizja".

W obszarze _możliwości eksportu_ są dostępne dwie opcje:

- Platformy podstawowe (Tensorflow, CoreML, ONNX itp.)
- Vision Kit.

W przypadku wybrania _zestawu AI_ , _terenu_i _sprzedaży detalicznej_ , ale nie _domen kompaktowych_ w postaci kompaktowej, są dostępne dla klasyfikacji _obrazów, podczas_gdy zarówno _Ogólne (kompaktowe)_ , jak i _Ogólne (kompaktowe) [S1]_ są dostępne do wykrywania obiektów.

>[!NOTE]
>__Ogólna (kompaktowa)__ domena do wykrywania obiektów wymaga specjalnej logiki dostosujesz. Aby uzyskać szczegółowe informacje, zobacz przykładowy skrypt w wyeksportowanym pakiecie zip. Jeśli potrzebujesz modelu bez logiki dostosujesz, użyj __ogólnego (kompaktowego) [S1]__ .

>[!IMPORTANT]
>Nie ma żadnej gwarancji, że eksportowane modele dają dokładnie ten sam wynik, co w przypadku interfejsu API przewidywania w chmurze. Niewielka różnica w działającej platformie lub implementacja przetwarzania wstępnego może spowodować większą różnicę w danych wyjściowych modelu. Szczegóły logiki przetwarzania wstępnego można znaleźć w [tym dokumencie](python-tutorial.md).

\[1\] Intel Xeon E5-2690 CPU i NVIDIA Tesla M60
