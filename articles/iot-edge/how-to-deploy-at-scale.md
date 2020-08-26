---
title: Wdrażanie modułów na dużą skalę Azure Portal — Azure IoT Edge
description: Użyj Azure Portal, aby utworzyć automatyczne wdrożenia dla grup urządzeń IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0c1d83c2dac0163cd9b9cbc07969103381e85471
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855393"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Wdrażanie modułów IoT Edge na dużą skalę przy użyciu Azure Portal

Utwórz **IoT Edge Automatyczne wdrażanie** w Azure Portal, aby zarządzać trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach, śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identyfikowanie urządzeń przy użyciu tagów

Przed utworzeniem wdrożenia należy mieć możliwość określania, które urządzenia mają mieć wpływ. Azure IoT Edge identyfikuje urządzenia przy użyciu **tagów** z sznurka urządzenia. Każde urządzenie może mieć wiele tagów zdefiniowanych w dowolny sposób, który ma sens dla danego rozwiązania.

Na przykład, Jeśli zarządzasz kampusami z inteligentnymi budynkami, możesz dodać do urządzenia następujące znaczniki lokalizacji, typu pokoju i środowiska:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

IoT Edge oferuje dwa różne typy wdrożeń automatycznych, których można użyć do dostosowania scenariusza. Można utworzyć standardowe *wdrożenie*, które obejmuje moduły środowiska uruchomieniowego systemu oraz dodatkowe moduły i trasy. Każde urządzenie może zastosować tylko jedno wdrożenie. Można też utworzyć *wdrożenie warstwowe*, które obejmuje tylko niestandardowe moduły i trasy, a nie środowisko uruchomieniowe systemu. Wiele wdrożeń warstwowych można łączyć na urządzeniu na podstawie standardowego wdrożenia. Aby uzyskać więcej informacji o tym, jak dwa typy wdrożeń automatycznych współpracują ze sobą, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

Kroki związane z tworzeniem wdrożenia i wdrożeniem warstwowym są bardzo podobne. Wszelkie różnice są wywoływane w poniższych krokach.

1. W [Azure Portal](https://portal.azure.com)przejdź do IoT Hub.
1. W menu w lewym okienku wybierz pozycję **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**.
1. Na górnym pasku wybierz pozycję **Utwórz wdrożenie** lub **Utwórz wdrożenie warstwowe**.

Istnieje pięć kroków, które należy wykonać, aby utworzyć wdrożenie. W poniższych sekcjach omówiono każdy z nich.

### <a name="step-1-name-and-label"></a>Krok 1. nazwa i etykieta

1. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` .
1. Etykiety można dodawać jako pary klucz-wartość, aby ułatwić śledzenie wdrożeń. Na przykład **HostPlatform** i **Linux**, lub **wersja** i **3.0.1**.
1. Wybierz kolejno pozycje **Następny: moduły** , aby przejść do kroku 2.

### <a name="step-2-modules"></a>Krok 2. moduły

Do wdrożenia można dodać do 50 modułów. Jeśli utworzysz wdrożenie bez modułów, spowoduje to usunięcie wszystkich bieżących modułów z urządzeń docelowych.

W obszarze wdrożenia można zarządzać ustawieniami agenta IoT Edge i IoT Edge modułów centrów. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** , aby skonfigurować dwa moduły środowiska uruchomieniowego. W przypadku wdrażania warstwowego moduły środowiska uruchomieniowego nie są uwzględniane, więc nie można ich skonfigurować.

Można dodać trzy typy modułów:

* Moduł IoT Edge
* Moduł portalu Marketplace
* Moduł Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Dodawanie modułu IoT Edge

Aby dodać niestandardowy kod jako moduł lub ręcznie dodać moduł usługi platformy Azure, wykonaj następujące kroki:

1. W sekcji **poświadczenia Container Registry** na stronie Podaj nazwy i poświadczenia dla prywatnych rejestrów kontenerów, które zawierają obrazy modułów dla tego wdrożenia. Agent IoT Edge zgłosi błąd 500, jeśli nie można znaleźć poświadczenia rejestru kontenera dla obrazu platformy Docker.
1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł IoT Edge** z menu rozwijanego.
1. Nadaj modułowi **IoT Edge nazwę modułu**.
1. W polu **Identyfikator URI obrazu** wprowadź obraz kontenera dla modułu.
1. Użyj menu rozwijanego, aby wybrać **zasady ponownego uruchamiania**. Wybierz jedną z następujących opcji:
   * **zawsze** — moduł zawsze jest uruchamiany ponownie, jeśli z jakiegoś powodu zostanie zamknięty.
   * **nigdy** — moduł nigdy nie jest ponownie uruchamiany, jeśli z jakiegoś powodu zostanie zamknięty.
   * w przypadku niepowodzenia — moduł jest uruchamiany ponownie w przypadku awarii, ale nie w przypadku jego **nieprawidłowego** zamknięcia.
   * **w złej kondycji** — moduł jest uruchamiany ponownie, jeśli ulegnie awarii lub zwróci stan złej kondycji. Do każdego modułu jest zaimplementowana funkcja stanu kondycji.
1. Użyj menu rozwijanego, aby wybrać **żądany stan** modułu. Wybierz jedną z następujących opcji:
   * **uruchomiona** jest opcja domyślna. Moduł zacznie działać natychmiast po wdrożeniu.
   * **zatrzymano** — po wdrożeniu moduł pozostanie bezczynny do momentu wywołania przez użytkownika lub innego modułu.
1. Określ wszelkie **Opcje tworzenia kontenera** , które powinny być przesyłane do kontenera. Aby uzyskać więcej informacji, zobacz [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Wybierz pozycję **Ustawienia sznurka modułu** , jeśli chcesz dodać Tagi lub inne właściwości do sznurka modułu.
1. Wprowadź **zmienne środowiskowe** dla tego modułu. Zmienne środowiskowe udostępniają informacje o konfiguracji do modułu.
1. Wybierz pozycję **Dodaj** , aby dodać moduł do wdrożenia.

#### <a name="add-a-module-from-the-marketplace"></a>Dodawanie modułu z portalu Marketplace

Aby dodać moduł z portalu Azure Marketplace, wykonaj następujące kroki:

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł Marketplace** z menu rozwijanego.
1. Wybierz moduł na stronie **witryny Marketplace modułu IoT Edge** . Wybrany moduł jest automatycznie konfigurowany dla Twojej subskrypcji, grupy zasobów i urządzenia. Zostanie ona wyświetlona na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów z witryny Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Dodaj moduł Stream Analytics

Aby dodać moduł z Azure Stream Analytics, wykonaj następujące kroki:

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj**.
1. Wybierz **moduł Azure Stream Analytics** z menu rozwijanego.
1. W prawym okienku wybierz swoją **subskrypcję**.
1. Wybierz zadanie IoT **Edge**.
1. Wybierz pozycję **Zapisz** , aby dodać moduł do wdrożenia.

#### <a name="configure-module-settings"></a>Konfigurowanie ustawień modułu

Po dodaniu modułu do wdrożenia można wybrać jego nazwę, aby otworzyć stronę **modułu aktualizacji IoT Edge** . Na tej stronie można edytować ustawienia modułu, zmienne środowiskowe, opcje tworzenia i sznurki modułowe. Jeśli dodano moduł z portalu Marketplace, może on mieć już wypełnione niektóre parametry.

W przypadku tworzenia wdrożenia warstwowego można skonfigurować moduł, który istnieje w innych wdrożeniach przeznaczonych dla tych samych urządzeń. Aby zaktualizować splot modułu bez zastępowania innych wersji, Otwórz kartę **Ustawienia sznurka modułu** . Utwórz nową **Właściwość sznurka modułu** o unikatowej nazwie dla podsekcji w ramach żądanych właściwości sznurka `properties.desired.settings` modułu. Jeśli zdefiniujesz właściwości w tylko `properties.desired` polu, spowoduje to zastąpienie żądanych właściwości modułu zdefiniowanego we wdrożeniach o niższym priorytecie.

![Ustaw właściwość sznurka modułu dla wdrożenia warstwowego](./media/how-to-deploy-monitor/module-twin-property.png)

Aby uzyskać więcej informacji na temat konfiguracji sznurka modułu w przypadku wdrożeń warstwowych, zobacz [wdrażanie warstwowe](module-deployment-monitoring.md#layered-deployment).

Po skonfigurowaniu wszystkich modułów dla wdrożenia wybierz pozycję **Dalej: trasy** , które mają zostać przeniesione do kroku 3.

### <a name="step-3-routes"></a>Krok 3. trasy

Trasy definiują, jak moduły komunikują się ze sobą w ramach wdrożenia. Domyślnie Kreator udostępnia trasę o nazwie **nadrzędny** i zdefiniowany jako **od/messages/ \* do $upstream**, co oznacza, że wszystkie komunikaty wyjściowe przez wszystkie moduły są wysyłane do centrum IoT Hub.  

Dodaj lub zaktualizuj trasy z informacjami z [deklaracji trasy](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd.

Wybierz pozycję **Dalej: metryki**.

### <a name="step-4-metrics"></a>Krok 4. metryki

Metryki zawierają podsumowanie liczb różnych stanów, które urządzenie może zgłosić z powrotem w wyniku zastosowania zawartości konfiguracji.

1. Wprowadź nazwę dla **nazwy metryki**.

1. Wprowadź zapytanie dla **kryteriów metryki**. Zapytanie jest oparte na IoT Edge [raportowanych właściwościach](module-edgeagent-edgehub.md#edgehub-reported-properties)modułu centrum. Metryka reprezentuje liczbę wierszy zwracanych przez zapytanie.

   Na przykład:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Wybierz pozycję **Dalej: urządzenia docelowe**.

### <a name="step-5-target-devices"></a>Krok 5. urządzenia docelowe

Użyj właściwości Tags z urządzeń, aby określić urządzenia docelowe, które powinny otrzymać to wdrożenie.

Ponieważ wiele wdrożeń może wskazywać na to samo urządzenie, należy nadać każdemu wdrożeniu numer priorytetu. Jeśli kiedykolwiek wystąpi konflikt, wdrożenie o najwyższym priorytecie (większe wartości wskazują wyższy priorytet) WINS. Jeśli dwa wdrożenia mają taki sam numer priorytetu, ten, który został utworzony ostatnio jako usługa WINS.

Jeśli wiele wdrożeń jest przeznaczonych dla tego samego urządzenia, stosowany jest tylko ten z wyższym priorytetem. Jeśli wiele wdrożeń warstwowych jest przeznaczonych dla tego samego urządzenia, zostaną one zastosowane. Jednakże jeśli wszystkie właściwości są zduplikowane, na przykład jeśli istnieją dwie trasy o tej samej nazwie, to jeden z wyższych priorytetów wdrożenia warstwowego zastępuje resztę.

Każde wdrożenie warstwowe ukierunkowane na urządzenie musi mieć wyższy priorytet niż wdrożenie podstawowe, aby można je było zastosować.

1. Wprowadź dodatnią liczbę całkowitą dla **priorytetu**wdrożenia.
1. Wprowadź **warunek docelowy** , aby określić, które urządzenia będą ukierunkowane na to wdrożenie.Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia.Na przykład: `tags.environment='test'` lub `properties.reported.devicemodel='4000x'`.

Wybierz pozycję **Dalej: Przejrzyj i Utwórz** , aby przejść do ostatniego kroku.

### <a name="step-6-review-and-create"></a>Krok 6. przegląd i tworzenie

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

Aby monitorować wdrożenie, zobacz [monitorowanie wdrożeń IoT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modyfikowanie wdrożenia

Po zmodyfikowaniu wdrożenia zmiany są natychmiast replikowane na wszystkie urządzenia objęte usługą. Istnieje możliwość zmodyfikowania następujących ustawień i funkcji dla istniejącego wdrożenia:

* Warunki docelowe
* Metryki niestandardowe
* Etykiety
* Tagi
* Żądane właściwości

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modyfikowanie warunków docelowych, metryk niestandardowych i etykiet

1. W centrum IoT wybierz pozycję **IoT Edge** w menu po lewej stronie.
1. Wybierz kartę **wdrożenia IoT Edge** a następnie wybierz wdrożenie, które chcesz skonfigurować.
1. Wybierz kartę **warunek docelowy** . Zmień **warunek docelowy** , aby wskazać docelowe urządzenia. Możesz również dostosować **priorytet**.  Wybierz pozycję **Zapisz**.

    W przypadku zaktualizowania warunku docelowego następujące aktualizacje są wykonywane:

    * Jeśli urządzenie nie spełnia starego warunku docelowego, ale spełnia warunki nowego elementu docelowego, a to wdrożenie ma najwyższy priorytet dla tego urządzenia, to wdrożenie jest stosowane na urządzeniu.
    * Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego, Odinstalowuje to wdrożenie i podejmuje kolejne wdrożenie o najwyższym priorytecie.
    * Jeśli urządzenie, na którym jest uruchomione to wdrożenie, już nie spełnia warunku docelowego i nie spełnia warunku docelowego innych wdrożeń, nie nastąpi żadne zmiany na urządzeniu. Urządzenie kontynuuje uruchamianie bieżących modułów w bieżącym stanie, ale nie jest już zarządzane w ramach tego wdrożenia. Po spełnieniu warunku docelowego dowolnego innego wdrożenia Odinstalowuje to wdrożenie i przyjmuje nowe.

1. Wybierz kartę **metryki** i kliknij przycisk **Edytuj metryki** . Dodawanie lub modyfikowanie metryk niestandardowych przy użyciu składni przykładowej jako przewodnika. Wybierz pozycję **Zapisz**.

    ![Edytowanie metryk niestandardowych w ramach wdrożenia](./media/how-to-deploy-monitor/metric-list.png)

1. Wybierz kartę **etykiety** i wprowadź żądane zmiany i wybierz pozycję **Zapisz**.

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Po usunięciu wdrożenia wszystkie wdrożone urządzenia przyjmą kolejne wdrożenie o najwyższym priorytecie. Jeśli urządzenia nie spełniają warunku docelowego innego wdrożenia, moduły nie zostaną usunięte po usunięciu wdrożenia.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do IoT Hub.
1. Wybierz **IoT Edge**.
1. Wybierz kartę **wdrożenia IoT Edge** .

   ![Wyświetl IoT Edge wdrożenia](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Użyj pola wyboru, aby wybrać wdrożenie, które chcesz usunąć.
1. Wybierz pozycję **Usuń**.
1. Zostanie wyświetlony monit z informacją o tym, że ta akcja spowoduje usunięcie tego wdrożenia i przywrócenie poprzedniego stanu dla wszystkich urządzeń.Zostanie zastosowane wdrożenie z niższym priorytetem.Jeśli żadne inne wdrożenie nie jest wskazywane, moduły nie zostaną usunięte. Jeśli chcesz usunąć wszystkie moduły z urządzenia, Utwórz wdrożenie z zerowymi modułami i wdróż je na tych samych urządzeniach.Wybierz przycisk **Tak**, aby kontynuować.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
