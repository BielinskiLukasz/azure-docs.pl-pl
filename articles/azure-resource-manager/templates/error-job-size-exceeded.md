---
title: Błąd przekroczenia rozmiaru zadania
description: Opisuje sposób rozwiązywania problemów, gdy rozmiar zadania lub szablon jest zbyt duży.
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.openlocfilehash: 638bdef246fc908ab997bfb99e7526febdb3792e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822143"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Rozwiązano błędy w przypadku przekroczenia rozmiaru zadania

W tym artykule opisano sposób rozwiązywania błędów **JobSizeExceededException** i **DeploymentSizeExceededException** .

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu pojawia się błąd informujący o przekroczeniu limitów wdrożenia.

## <a name="cause"></a>Przyczyna

Ten błąd można uzyskać, gdy rozmiar szablonu przekracza 4 MB. Limit 4 MB ma zastosowanie do końcowego stanu szablonu po jego rozszerzeniu na definicje zasobów używające [kopiowania](copy-resources.md) do tworzenia wielu wystąpień. Ostatni stan zawiera również rozpoznane wartości zmiennych i parametrów.

Zadanie wdrażania zawiera również metadane dotyczące żądania. W przypadku dużych szablonów metadane połączone z szablonem mogą przekraczać dozwolony rozmiar zadania.

Inne limity dla szablonu są następujące:

* parametry 256
* 256 zmienne
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

## <a name="solution-1---simplify-template"></a>Rozwiązanie 1 — uproszczenie szablonu

Pierwszą opcją jest uproszczenie szablonu. Ta opcja działa, gdy szablon wdraża wiele różnych typów zasobów. Rozważ podzielenie szablonu na [połączone szablony](linked-templates.md). Podziel typy zasobów na grupy logiczne i Dodaj połączony szablon dla każdej grupy. Na przykład, jeśli konieczne jest wdrożenie wielu zasobów sieciowych, można przenieść te zasoby do połączonego szablonu.

Możesz ustawić inne zasoby jako zależne od szablonu połączonego i [uzyskać wartości z danych wyjściowych połączonego szablonu](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Rozwiązanie 2 — zmniejszanie rozmiaru nazwy

Spróbuj skrócić długość nazw używanych dla [parametrów](template-parameters.md), [zmiennych](template-variables.md)i danych [wyjściowych](template-outputs.md). Gdy te wartości są powtarzane przez pętle kopiowania, duża nazwa jest przemnożona wiele razy.

## <a name="solution-3---use-serial-copy"></a>Rozwiązanie 3 — Korzystanie z kopii seryjnej

Drugą opcją jest zmiana pętli kopiowania z [równoległe na przetwarzanie szeregowe](copy-resources.md#serial-or-parallel). Tej opcji należy używać tylko wtedy, gdy podejrzewasz, że błąd pochodzi z wdrażania dużej liczby zasobów za pośrednictwem kopiowania. Ta zmiana może znacząco zwiększyć czas wdrożenia, ponieważ zasoby nie są wdrażane równolegle.
