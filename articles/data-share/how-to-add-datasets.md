---
title: Dodawanie zestawów danych do istniejącej usługi Azure Data Share
description: Dowiedz się, jak dodawać zestawy danych do istniejącego udziału danych w usłudze Azure Data Share i udostępniać je tym samym odbiorcom.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490545"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Jak dodać zestawy danych do istniejącego udziału w usłudze Azure Data Share

W tym artykule wyjaśniono, jak dodać zestawy danych do istniejącego udziału danych przy użyciu usługi Azure Data Share. Pozwala to na udostępnianie większej ilości danych tym samym odbiorcom bez konieczności tworzenia nowego udziału.

Aby uzyskać informacje na temat dodawania zestawów danych podczas tworzenia udziału, zobacz samouczek [udostępnianie danych](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Przejdź do wysłanego udziału danych

W udziale danych platformy Azure przejdź do wysłanego udziału i wybierz kartę **DataSets** . kliknij przycisk **+ Dodaj zestawy danych** , aby dodać więcej zestawów danych.

![Dodaj zestawy danych](./media/how-to/how-to-add-datasets/add-datasets.png)

W panelu po prawej stronie wybierz typ zestawu danych, który chcesz dodać, a następnie kliknij przycisk **dalej**. Wybierz subskrypcję i grupę zasobów danych, które chcesz dodać. Za pomocą strzałek listy rozwijanej Znajdź, a następnie zaznacz pole wyboru obok danych do dodania.

![Dodaj zestawy danych](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknięciu przycisku **Dodaj zestawy**danych zestawy danych zostaną dodane do udziału. Uwaga: aby można było zobaczyć nowe zestawy danych, migawka musi być wyzwalana przez odbiorców. Jeśli ustawienia migawki zostały skonfigurowane, użytkownicy będą widzieli nowe zestawy danych po zakończeniu następnej zaplanowanej migawki. Bez skonfigurowanych ustawień migawek konsument musi ręcznie wyzwolić pełną lub przyrostową kopię danych w celu uzyskania aktualizacji. Aby uzyskać więcej informacji [na temat migawek, zobacz](terminology.md)snapshots.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [dodawania odbiorców do istniejącego udziału danych](how-to-add-recipients.md).