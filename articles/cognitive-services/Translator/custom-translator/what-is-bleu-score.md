---
title: Co to jest wynik BLEU? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: BLEU to pomiar różnic między translacją maszyn i utworzonymi przez człowieka tłumaczeniami odwołań tego samego zdania źródła.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: 61c1efb7337bee5fe329c9d548e23f3931e6ce3c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510780"
---
# <a name="what-is-a-bleu-score"></a>Co to jest wynik BLEU?

[Bleu (Podbadanie przy ocenie dwujęzycznej)](https://en.wikipedia.org/wiki/BLEU) to pomiar różnic między automatycznym translacją a jednym lub większą liczbą odniesień przez człowieka z tego samego zdania źródłowego.

## <a name="scoring-process"></a>Proces oceniania

Algorytm BLEU porównuje kolejne frazy automatycznego tłumaczenia z następującymi frazami znalezionymi w tłumaczeniu referencyjnym i liczy liczbę dopasowań w sposób ważony. Te dopasowania są niezależne od pozycji. Wyższy stopień dopasowania wskazuje wyższy stopień podobieństwa przy użyciu translacji odwołań i wyższy wynik. Intelligibility i poprawności gramatyki nie są brane pod uwagę.

## <a name="how-bleu-works"></a>Jak działa BLEU?

Siła BLEUa polega na tym, że jest ona skorelowane z osądem ludzkim poprzez przeciętnie błędy rozstrzygania poszczególnych zdań w teście korpus, a nie próby opracowania dokładnego osądu człowieka na każde zdanie.

Bardziej obszerne omówienie wyników BLEU jest [tutaj](https://youtu.be/-UqDljMymMg).

Wyniki BLEU zależą od szerokiej szerokości domeny, spójności danych testowych z danymi szkoleniowymi i dostrajania oraz ilości danych dostępnych do uczenia się. Jeśli modele zostały przeszkolone w domenie wąskiej, a Twoje dane szkoleniowe są spójne z danymi testowymi, możesz oczekiwać o wysoką BLEU.

>[!NOTE]
>Porównanie wyników BLEU jest uzasadnione tylko wtedy, gdy wyniki BLEU są porównywane z tym samym zestawem testów, tą samą parą językową i ten sam MT Engine. Wynik BLEU z innego zestawu testów jest powiązany z innym.
