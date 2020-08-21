---
ms.openlocfilehash: ce5611a92e9899d64ff2117385af008c37c22c5e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682322"
---
W ramach wymagań wstępnych dotyczących tego przewodnika Szybki Start pobrano przykładowy kod do folderu. Wykonaj następujące kroki, aby przeanalizować i edytować przykładowy kod.

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *ENV* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego, gdzie zmienne są używane dla niektórych właściwości. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.js* pliku i kilku innych plików:
    * ***operations.jsna*** liście operacji, które program ma uruchomić.
    * ***Main.py*** — przykładowy kod programu. Ten kod:

        * Ładuje ustawienia aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](../../../direct-methods.md). 
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
        * Wywołuje bezpośrednie metody czyszczenia zasobów.

1. Edytuj *operations.jsw* pliku:
    * Zmień łącze do topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edytuj adres URL RTSP, aby wskazywał na plik wideo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

        `"name": "EVRToFilesOnMotionDetection"`
